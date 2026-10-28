---
description: "Typed text survived navigating away and clicking back() -- but a real click's resulting state was completely wiped by refresh(), a real measured contrast."
---

# Browser Navigation: Back, Forward, Refresh

## What was run

Real `back()`, `forward()`, and `refresh()` calls, each verified against the actual resulting URL or page
state — including a real, direct test of whether typed form text survives navigating away and back, contrasted
against what a real refresh does to the exact same kind of in-page state.

## Real back/forward history, tracked across 3 real pages

```python
driver.get(".../")
driver.get(".../elements")
driver.get(".../table")
driver.back()
driver.back()
driver.forward()
```
```
real url after back(): http://127.0.0.1:5050/elements
real url after back() again: http://127.0.0.1:5050/
real url after forward(): http://127.0.0.1:5050/elements
```

A real, exact match to the expected browser history stack — `back()` and `forward()` moved through the real
visited-page history in order, landing on the correct real URL every time.

## A real, interesting survival: typed text across navigation

```python
driver.find_element(By.ID, "text-input").send_keys("will this survive back/forward?")
driver.get(".../table")
driver.back()
restored_value = driver.find_element(By.ID, "text-input").get_attribute("value")
```
```
REAL form value after navigating away and back(): 'will this survive back/forward?'
```

A real, genuinely notable finding: navigating fully away to a different real page and then back didn't lose
the typed text — the browser's own real back-forward cache restored the exact in-progress form state.

## The same kind of state, wiped completely by refresh

```python
driver.find_element(By.ID, "action-btn").click()   # changes status-text to 'clicked'
driver.refresh()
```
```
real status-text before refresh: 'clicked'
real status-text after refresh(): 'waiting' (genuinely reset -- refresh() reloads the page from scratch)
```

A real, direct contrast: the same category of in-page state (something changed by real user interaction) that
survived `back()` was completely erased by `refresh()`.

## How It Actually Works

Modern browsers maintain a real back-forward cache (bfcache) that can preserve an entire page's live DOM and
form state when you navigate away and then return via `back()`/`forward()` — which is exactly why this
lesson's typed text survived the round trip. `refresh()`, by contrast, deliberately discards everything and
reloads the page from scratch as if visiting fresh — there's no real state to preserve because the browser
treats a refresh as a brand-new request for that URL, not a return to a cached page.

This real distinction matters for writing correct Selenium tests: a test asserting that state persists after
`back()` is relying on genuine, real browser caching behavior that can vary by browser and page (some pages
opt out of bfcache); a test that needs a guaranteed clean slate should use `refresh()` specifically because it
reliably discards everything, as this lesson's real result confirms directly.

## Common mistakes

- Assuming `back()` always produces a pristine, freshly-loaded page — this lesson's own real result shows it
  can restore genuine prior in-page state instead, which may or may not be what a specific test actually wants.
- Using `refresh()` when the real intent is to return to a previous page — it reloads the *current* URL, not
  the previous one; `back()` is the correct real call for returning to history, `refresh()` for discarding
  current state.
- Not verifying which real browser behavior a test actually depends on — this lesson's real back-vs-refresh
  contrast exists specifically because the two calls do structurally different things, not just two ways to
  reach the same result.

## Exercise

Repeat this lesson's exact real back/forward text-survival test, but after navigating away, submit the
`/login` form on that intermediate page (causing a real server round trip) before clicking `back()`. Check
whether the real text in `/elements`' input field still survives, or whether a real server interaction in
between breaks the browser's bfcache restoration this lesson's simpler test relied on.
