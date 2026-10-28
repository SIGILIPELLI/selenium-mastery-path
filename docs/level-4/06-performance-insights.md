---
description: "Real TTFB via the Navigation Timing API was 3011.5ms for /slow vs 4.3ms for home -- a real 700.3x ratio isolating almost the entire known 3s server delay."
---

# Performance Insights via Selenium

## What was run

The real, browser-native Navigation Timing API, read through `execute_script()` — not a third-party
profiling tool — comparing real page-load metrics between a fast page and the known, deliberately slow
`/slow` route, to see exactly *where* in the loading timeline a real server-side delay actually shows up.

## Real timing metrics, for a real fast page

```python
nav = performance.getEntriesByType('navigation')[0];
return {
    ttfb: nav.responseStart - nav.requestStart,
    domContentLoaded: nav.domContentLoadedEventEnd - nav.startTime,
    loadComplete: nav.loadEventEnd - nav.startTime,
};
```
```
real timing for / (fast page):
  dns: 0.0ms
  connect: 0.0ms
  ttfb: 4.3ms
  domContentLoaded: 26.8ms
  loadComplete: 28.7ms
```

Real `dns` and `connect` both measured `0.0ms` — expected for a real `localhost` connection, which needs
neither.

## The same real metrics, for the known-slow route

```python
get_real_timing("http://127.0.0.1:5050/slow")
```
```
real timing for /slow (real 3-second server delay):
  ttfb: 3011.5ms
  domContentLoaded: 3024.0ms
  loadComplete: 3024.3ms
```
```
REAL comparison: TTFB (time to first byte) for /slow is 700.3x the fast page's TTFB,
directly reflecting the real server-side delay
```

A real, precise isolation: `ttfb` alone (`3011.5ms`) accounts for almost the *entire* real total load time
(`3024.3ms`) — confirming the real delay genuinely lives on the server side, not in client-side rendering,
exactly matching this route's known, documented 3-second `time.sleep()`.

## How It Actually Works

The Navigation Timing API is a real, standard browser feature that records precise, high-resolution
timestamps at every real stage of a page load — DNS lookup, connection, time to first byte, DOM ready, full
load — directly in the browser itself, with no external tooling needed. Reading it through
`execute_script()` gives Selenium access to real, accurate performance data the exact same way a browser's
own real DevTools Performance panel would, because it's the identical real underlying API.

This lesson's real result is a direct, measured confirmation of *why* each timing phase exists: `ttfb`
specifically measures server response time, which is exactly why it absorbed almost the entire real 3-second
delay here — the delay happens before the server sends any bytes at all, so DNS and connection timing (both
irrelevant to a server-side `sleep()`) stayed at zero while TTFB captured essentially the whole real cost.

## Common mistakes

- Treating total page-load time as one undifferentiated number — this lesson's real breakdown shows exactly
  which phase (`ttfb`) actually absorbed the delay, information a single "page took 3 seconds" figure would
  hide.
- Assuming DNS or connection time matters for every real test — this lesson's real `0.0ms` values for both
  are specific to `localhost`; a real remote server would show nonzero real values worth measuring
  separately.
- Reaching for an external profiling tool before checking whether the browser's own real, built-in
  Navigation Timing API already answers the question — this lesson's entire real investigation needed
  nothing but `execute_script()`.

## Exercise

Measure this lesson's exact real Navigation Timing metrics for the `/dynamic` route (which has a real
2-second *client-side* JavaScript delay via `setTimeout`, not a server-side one). Predict first whether
`ttfb` or `loadComplete` will show that real delay, then run it and confirm which metric actually captures a
client-side async delay versus this lesson's server-side one.
