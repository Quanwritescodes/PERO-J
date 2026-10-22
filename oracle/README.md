# Oracle adapters

`src/adapters/freshness.js` provides `fetchFreshFromAdapters(adapters, options)`
for ordered oracle fallback. Each adapter is an object with a `fetch()` method
that returns an API response containing `last_updated` or `timestamp`.

The first response with a parseable timestamp less than 10 minutes old is
returned. ISO date strings, `Date` values, and Unix timestamps in seconds or
milliseconds are supported. Missing, invalid, stale, or future timestamps are
rejected, and the next adapter is tried. If no adapter returns fresh data, the
function throws an `AggregateError`; callers must not submit an outcome when
that happens.

```js
const response = await fetchFreshFromAdapters([
  { name: "primary", fetch: () => primaryApi.getOutcome(marketId) },
  { name: "secondary", fetch: () => secondaryApi.getOutcome(marketId) },
]);
```

The maximum age can be overridden with `maxAgeMs`; the default is 600,000 ms.
