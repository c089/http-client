## [v4.0.1]

- Renamed `handleResponse` to `onResponse`
- Fixed logic error with response handlers

[v4.0.1]: https://github.com/mjackson/http-client/compare/v4.0.0...v4.0.1

## [v4.0.0]
> Apr 28, 2015

- Updated docs

[4.0.0]: https://github.com/mjackson/http-client/compare/v4.0.0-0...v4.0.0

## [v4.0.0-0]
> Apr 27, 2016

- `handleResponse` runs response handlers top to bottom, just like Express
- Added `options.responseHandlers` array to specify an array of transforms to
  run on the response after it is received
- Added `enhanceFetch` to top-level exports for adding `options.responseHandlers`
  support to an arbitrary `fetch` function
- Added `options.fetch` to fetch functions created using `createFetch` so users
  can swap out usage of the "global" fetch function, which makes testing easier
- Removed callback API to preserve `fetch` method signature

[v4.0.0-0]: https://github.com/mjackson/http-client/compare/v3.1.0...v4.0.0-0

## [v3.1.0]
> Mar 28, 2016

- Added the `init` middleware

[v3.1.0]: https://github.com/mjackson/http-client/compare/v3.0.0...v3.1.0

## [v3.0.0]
> Mar 22, 2016

- Require consumers to provide their own global `fetch` function

[v3.0.0]: https://github.com/mjackson/http-client/compare/v2.4.2...v3.0.0

## [v2.4.2]
> Mar 22, 2016

- Fix bundling with Browserify

[v2.4.2]: https://github.com/mjackson/http-client/compare/v2.4.0...v2.4.2

## [v2.4.0]
> Mar 18, 2016

- Export `handleResponse` helper for building middleware
- Fixed `browser` field in package config

[v2.4.0]: https://github.com/mjackson/http-client/compare/v2.3.0...v2.4.0

## [v2.3.0]
> Mar 14, 2016

- Add callback support to all `fetch` methods
- **Breakage:** Use `requestInput` property instead of `requestURL` in `requestInfo`
  middleware

[v2.3.0]: https://github.com/mjackson/http-client/compare/v2.2.1...v2.3.0

## [v2.2.1]
> Mar 13, 2016

- Move UMD build from `umd/HTTPClient.js` to `umd/http-client.js`
- **Bugfix:** Do not append empty URL when using `base` middleware

[v2.2.1]: https://github.com/mjackson/http-client/compare/v2.2.0...v2.2.1
