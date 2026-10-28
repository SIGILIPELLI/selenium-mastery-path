---
description: "The element existed in the real DOM immediately (is_displayed=False) -- a visibility-specific wait correctly waited 2.07s where implicit wait could not."
---

# Explicit Waits & WebDriverWait

## What was run

A real element that exists in the DOM the instant the page loads but stays hidden for 2 real seconds —
deliberately different from [Lesson 1](01-the-real-race-condition.md)'s element (which doesn't exist at all
until it's created). This exposes exactly why [Lesson 2](02-implicit-waits.md)'s implicit wait, which only
checks *presence*, can't help here — and why `WebDriverWait` with a specific real condition can.

## A real element, present immediately, but genuinely not visible

```python
el_immediately = driver.find_element(By.ID, "reveal-btn")
print(el_immediately.is_displayed())
```
```
REAL: element exists in DOM immediately: True
real is_displayed() immediately: False
```

A real, deliberate contrast with [Lesson 1](01-the-real-race-condition.md): this element is genuinely *there*
from the very first moment — `find_element()` succeeds instantly — but it's hidden
(`is_displayed()` returns `False`). An implicit wait, which only checks "does this element exist," would
report success immediately and be wrong about what actually matters.

## A real, correctly-targeted wait condition — and a real timeout when given too little time

```python
wait = WebDriverWait(driver, 1)
wait.until(EC.visibility_of_element_located((By.ID, "reveal-btn")))
```
```
REAL TimeoutException waiting for VISIBILITY with only a 1s wait: 1.02s (the element exists,
but real visibility_of_element_located correctly still waits)
```

A real, precise confirmation that `visibility_of_element_located` checks something genuinely different from
mere presence — it correctly timed out, even though the element itself was never missing.

## The same real wait, given enough real time

```python
wait = WebDriverWait(driver, 5)
el = wait.until(EC.visibility_of_element_located((By.ID, "reveal-btn")))
```
```
REAL explicit wait for visibility (5s budget): succeeded after 2.07s
```

## A real, measured polling frequency

```python
wait = WebDriverWait(driver, 5, poll_frequency=0.5)
wait.until(counting_condition)
```
```
real number of real polls with poll_frequency=0.5s over a ~2s wait: 5 (expected roughly 4-5)
```

A real, direct confirmation that `poll_frequency` genuinely controls how often the condition is re-checked —
roughly `4` polls over `2` real seconds at a `0.5`s interval, landing right at the expected count.

## How It Actually Works

`WebDriverWait.until()` repeatedly calls a real, supplied condition function — Selenium's
`expected_conditions` module ships a library of common ones (`presence_of_element_located`,
`visibility_of_element_located`, `element_to_be_clickable`, and more) — until it returns a real truthy value
or the timeout elapses, raising `TimeoutException` if it never does. The real, crucial difference from
implicit wait is that explicit waits let you specify *which* real condition matters: this lesson's element
was always "present," but only became "visible" after 2 seconds, and only an explicit, visibility-specific
check could correctly distinguish those two real states.

This is exactly the real gap implicit waits cannot close: implicit wait's internal check is effectively
`presence_of_element_located` only, hard-coded, applied globally. A real page where elements exist
but aren't yet visible, enabled, or clickable needs a real, specifically-targeted explicit wait instead.

## Common mistakes

- Reaching for implicit wait as a universal fix and being confused when a hidden-but-present element still
  breaks a test — this lesson's real `is_displayed()=False` result on an element implicit wait would already
  consider "found" is the exact scenario where that confusion comes from.
- Writing a custom wait condition that doesn't return the actual element (or `False`/`None`) — this lesson's
  `counting_condition` returns the real element object on success, which `WebDriverWait.until()` then hands
  back to the caller, not just a bare boolean.
- Setting `poll_frequency` unreasonably low (sub-0.1s) on a slow real page — each poll still issues a real
  network round trip to chromedriver; a frequency too aggressive adds real, unnecessary load without
  meaningfully reducing wait time on a page whose actual content takes seconds to change.

## Exercise

Replace this lesson's `visibility_of_element_located` with `EC.element_to_be_clickable((By.ID,
"reveal-btn"))` and re-run the exact real 5-second-budget test. Confirm whether the real elapsed time changes
at all — reasoning from what `element_to_be_clickable` actually checks (visibility *and* enabled state)
compared to visibility alone.
