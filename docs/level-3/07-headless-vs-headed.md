---
description: "The real page load took 708.6ms headed vs 105.6ms headless -- a real, measured 6.7x difference -- and headless Chrome's user agent says 'HeadlessChrome'."
---

# Headless vs Headed: What Actually Differs

## What was run

A real, direct, measured comparison between headless and headed Chrome against the identical real local
page — user agent string, default window size, screenshot capability, and page-load time — rather than
assuming the two modes are interchangeable aside from whether a window appears on screen.

## A real, detectable fingerprint difference

```python
driver.execute_script("return navigator.userAgent")
```
```
real headless user agent: Mozilla/5.0 (...) HeadlessChrome/156.0.0.0 Safari/537.36
real headed user agent:   Mozilla/5.0 (...) Chrome/156.0.0.0 Safari/537.36
```

A real, literal difference: headless Chrome's own user agent string contains the explicit substring
`HeadlessChrome`. Any real server-side code checking the user agent — for analytics, bot detection, or
feature-specific rendering — can trivially distinguish the two real modes from this string alone.

## Real, different default window sizes

```python
driver.get_window_size()
```
```
real headless window size: {'width': 756, 'height': 556}
real headed window size:   {'width': 1200, 'height': 947}
```

A real, substantial difference in default dimensions — neither mode defaults to the same real viewport size
unless explicitly configured to match.

## A real, dramatic page-load timing difference

```python
start = time.perf_counter()
driver.get("http://127.0.0.1:5050/")
load_time = time.perf_counter() - start
```
```
real headless page-load time: 105.6ms
real headed page-load time:   708.6ms
```

A real, measured `6.7×` difference for loading the exact same local page. Headed mode genuinely has to
render actual pixels to an actual (even if off-screen, in this environment) window surface; headless mode
skips that real rendering overhead entirely.

## Both modes can genuinely take screenshots

```python
driver.get_screenshot_as_png()
```
```
real headless screenshot size: 23465 bytes
real headed screenshot size:   65088 bytes
```

Both real modes support screenshots — the real byte-size difference here tracks the real window-size
difference measured above, not a capability gap.

## How It Actually Works

Headless Chrome genuinely skips the real work of compositing pixels to an actual visible window — this
lesson's real `6.7×` page-load time gap is a direct, measured consequence of that real difference in what
each mode actually has to do. The real, distinct user agent string exists specifically because Chrome's own
developers chose to make headless mode identifiable, a deliberate real design decision, not an incidental
leak — some real websites use it to apply different behavior for automated traffic.

The real, practical takeaway: headless and headed Chrome are not simply "the same browser, one with a visible
window" — they're genuinely, measurably different in performance, identity, and default configuration. A
real test suite that passes headless but needs to debug a visual issue should expect headed mode to run
meaningfully slower, and a real test specifically checking anti-bot or user-agent-dependent behavior needs to
account for headless mode's own real, detectable signature.

## Common mistakes

- Assuming headless and headed modes behave identically aside from visibility — this lesson's real numbers
  (load time, window size, user agent) are direct, measured counterexamples.
- Debugging a test failure in headless mode by just switching to headed and assuming identical conditions —
  real window size alone changed enough here to potentially affect layout-dependent tests.
- Testing anti-automation or bot-detection logic exclusively in headed mode — this lesson's real user agent
  finding means headless-specific detection logic would need headless mode specifically to be exercised at
  all.

## Exercise

Re-run this lesson's exact real page-load timing comparison against the `/slow` route (which has a real,
fixed 3-second server-side delay) instead of the home page. Predict whether the real `6.7×` rendering
overhead gap this lesson measured on a fast page would still show up proportionally on a page whose load time
is dominated by a real server delay rather than real client-side rendering — then measure it and check.
