# http-client [![Travis][build-badge]][build] [![npm package][npm-badge]][npm]

[build-badge]: https://img.shields.io/travis/mjackson/http-client/master.svg?style=flat-square
[build]: https://travis-ci.org/mjackson/http-client

[npm-badge]: https://img.shields.io/npm/v/http-client.svg?style=flat-square
[npm]: https://www.npmjs.org/package/http-client

[http-client](https://www.npmjs.com/package/http-client) lets you compose HTTP clients using JavaScript's [fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API). This library has the following goals:

  - Preserve the full capabilities of the fetch API
  - Provide an extendable middleware API
  - Use the same API on both client and server

## Installation

Using [npm](https://www.npmjs.com/):

    $ npm install --save http-client

http-client requires you to bring your own [global `fetch`](https://developer.mozilla.org/en-US/docs/Web/API/GlobalFetch/fetch) function. [isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) is a great polyfill.

Then, use as you would anything else:

```js
// using ES6 modules
import { fetch, createFetch } from 'http-client'

// using CommonJS modules
var fetch = require('http-client').fetch
var createFetch = require('http-client').createFetch
```

The UMD build is also available on [npmcdn](https://npmcdn.com):

```html
<script src="https://npmcdn.com/http-client/umd/http-client.min.js"></script>
```

You can find the library on `window.HTTPClient`.

## Usage

http-client simplifies the process of creating flexible HTTP clients that work in both node and the browser. You create your own `fetch` function using the `createFetch` method, optionally passing [middleware](#middleware) as arguments.

```js
import { createFetch, base, accept, parseJSON } from 'http-client'

const fetch = createFetch(
  base('https://api.stripe.com/v1'),  // Prefix all request URLs
  accept('application/json'),         // Set "Accept: application/json" in the request headers
  parseJSON()                         // Read the response as JSON and put it in response.jsonData
)

fetch('/customers/5').then(response => {
  console.log(response.jsonData)
})
```

http-client also exports a base `fetch` function if you need it (i.e. don't want middleware).

## Top-level API

#### `createFetch(...middleware)`

Creates an [enhanced](#enhancefetchfetch) `fetch` function that is fronted by some middleware.

#### `createStack(...middleware)`

Combines several middleware into one, in the same order they are provided as arguments. Use this function to create re-usable [middleware stacks](#stacks).

#### `enhanceFetch(fetch)`

Returns an "enhanced" version of the given `fetch` function that uses an array of transforms in `options.responseHandlers` to modify the response after it is received.

#### `fetch([input], [options])`

An [enhanced](#enhancefetchfetch) `fetch` function. Use this directly if you don't need any middleware.

#### `onResponse(handler)`

A helper for creating middleware that enhances the `response` object in some way. The `handler` function should return the new response value, or a promise for it. Response handlers run in the order they are defined.

## Middleware

http-client provides a variety of middleware that may be used to extend the functionality of the client. Out of the box, http-client ships with the following middleware:

#### `accept(contentType)`

Adds an `Accept` header to the request.

```js
import { createFetch, accept } from 'http-client'

const fetch = createFetch(
  accept('application/json')
)
```

#### `auth(value)`

Adds an `Authorization` header to the request.

```js
import { createFetch, auth } from 'http-client'

const fetch = createFetch(
  auth('Bearer ' + oauth2Token)
)
```

#### `base(baseURL)`

Adds the given `baseURL` to the beginning of the request URL.

```js
import { createFetch, base } from 'http-client'

const fetch = createFetch(
  base('https://api.stripe.com/v1')
)

fetch('/customers/5') // GET https://api.stripe.com/v1/customers/5
```

#### `body(content, contentType)`

Sets the given `content` string as the request body.

```js
import { createFetch, body } from 'http-client'

const fetch = createFetch(
  body(JSON.stringify(data), 'application/json')
)
```

#### `header(name, value)`

Adds a header to the request.

```js
import { createFetch, header } from 'http-client'

const fetch = createFetch(
  header('Content-Type', 'application/json')
)
```

#### `init(propertyName, value)`

Sets the value of an arbitrary property in the options object.

```js
import { createFetch, init } from 'http-client'

const fetch = createFetch(
  init('credentials', 'include')
)
```

#### `json(object)`

Adds the data in the given object as JSON to the request body.

#### `method(verb)`

Sets the request method.

```js
import { createFetch, method } from 'http-client'

const fetch = createFetch(
  method('POST')
)
```

#### `params(object)`

Adds the given object to the query string of `GET`/`HEAD` requests and as a `x-www-form-urlencoded` payload on all others.

```js
import { createFetch, method, params } from 'http-client'

// Create a client that will append hello=world to the URL in the query string
const fetch = createFetch(
  params({ hello: 'world' })
)

// Create a client that will send hello=world as POST data
const fetch = createFetch(
  method('POST'),
  params({ hello: 'world' })
)
```

#### `parseJSON(propertyName = 'jsonData')`

Reads the response body as JSON and puts it on `response.jsonData`.

```js
import { createFetch, parseJSON } from 'http-client'

const fetch = createFetch(
  parseJSON()
)

fetch(input).then(response => {
  console.log(response.jsonData)
})
```

#### `parseText(propertyName = 'textString')`

Reads the response body as text and puts it on `response.textString`.

```js
import { createFetch, parseText } from 'http-client'

const fetch = createFetch(
  parseText()
)

fetch(input).then(response => {
  console.log(response.textString)
})
```

#### `query(object)`

Adds the data in the given object (or string) to the query string of the request URL.

#### `requestInfo()`

Adds `requestURL` and `requestOptions` properties to the response (or error) object so you can inspect them. Mainly useful for testing/debugging (should be put last in the list of middleware).

```js
import { createFetch, requestInfo } from 'http-client'

const fetch = createFetch(
  // ...
  requestInfo()
)

fetch(input).then(response => {
  console.log(response.requestURL, response.requestOptions)
})
```

## Stacks

Middleware may be combined together into re-usable middleware "stacks" using `createStack`. A stack is itself a middleware that is composed of one or more other pieces of middleware.

This is useful when you have a common set of functionality that you'd like to share between several different `fetch` methods, e.g.:

```js
import { createStack, createFetch, header, base, parseJSON } from 'http-client'

const commonStack = createStack(
  header('X-Auth-Key', key),
  header('X-Auth-Email', email),
  base('https://api.cloudflare.com/client/v4'),
  parseJSON()
)

// This fetch function can be used standalone...
const fetch = createFetch(commonStack)

// ...or we can add further middleware to create another fetch function!
const fetchSinceBeginningOf2015 = createFetch(
  commonStack,
  query({ since: '2015-01-01T00:00:00Z' })
)
```
