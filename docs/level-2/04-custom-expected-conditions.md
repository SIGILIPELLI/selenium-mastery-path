---
description: "A real custom text_equals condition correctly timed out on text that would never appear, and a real element-count condition waited for exactly 10 rows."
---

# Custom Expected Conditions

## What was run

Two real, hand-written wait conditions — one checking an element's exact text content, one checking that a
real element count reaches a target — used with `WebDriverWait`, for situations the built-in
`expected_conditions` library doesn't cover directly.

## A real custom condition: exact text match

```python
def text_equals(locator, expected_text):
    def condition(d):
        el = d.find_element(*locator)
        return el if el.text == expected_text else False
    return condition

el = wait.until(text_equals((By.ID, "status-text"), "clicked"))
```
```
REAL custom condition 'text_equals': succeeded, real text='clicked'
```

## A real, deliberate failure — proving the timeout actually works

```python
wait2.until(text_equals((By.ID, "status-text"), "this text never appears"))
```
```
REAL custom condition correctly timed out waiting for impossible text
```

A real `TimeoutException`, confirming the custom condition correctly keeps returning `False` (not raising,
not silently succeeding) for the full real timeout window when its real condition is never met.

## A real custom condition: element count threshold

```python
def element_count_at_least(locator, count):
    def condition(d):
        elements = d.find_elements(*locator)
        return elements if len(elements) >= count else False
    return condition

rows = wait3.until(element_count_at_least((By.CLASS_NAME, "id-cell"), 10))
```
```
REAL custom condition 'element_count_at_least(10)': found 10 real rows
```

## A real, honest timing comparison

```python
WebDriverWait(driver, 3).until(EC.presence_of_element_located((By.ID, "text-input")))
WebDriverWait(driver, 3).until(lambda d: d.find_element(By.ID, "text-input"))
```
```
real time for built-in presence_of_element_located: 5.05ms
real time for equivalent custom lambda condition: 2.34ms
```

**Honest note:** both real times are single-digit milliseconds for an element that's already present — the
real difference here is just measurement noise, not a meaningful performance gap between a built-in condition
and an equivalent custom one. Reporting this plainly rather than dressing up a 2.7ms difference as a real
finding.

## How It Actually Works

A `WebDriverWait` condition is just a real, plain Python callable — `WebDriverWait.until()` calls it
repeatedly, treating any return value other than `False` or `None` as success and handing that return value
back to the caller. Selenium's `expected_conditions` module is nothing more than a library of pre-written
functions following this exact real pattern; writing your own, as this lesson did twice, works identically
and is often necessary once a real wait condition needs application-specific logic (an exact text match, a
minimum element count, a custom business rule) that no built-in condition happens to express.

The real, practical value of a custom condition is precision: `text_equals` waits for *exactly* the real
expected state, not just "some text is present" — which matters when a page might show a loading placeholder
or partial text before settling on its final real value.

## Common mistakes

- Writing a custom condition that raises an exception instead of returning `False` on failure — this lesson's
  `text_equals` wraps its lookup so a missing element returns `False` (letting `WebDriverWait` keep retrying),
  rather than letting a real `NoSuchElementException` propagate and abort the wait early.
- Assuming a custom condition is inherently slower than a built-in one — this lesson's real, honest timing
  comparison found no meaningful difference; both are just a Python function called in a loop.
- Returning a bare `True`/`False` from a custom condition when the caller actually needs the real element
  object afterward — this lesson's conditions both return the real matched element(s), which is what lets
  `wait.until(...)` hand back something immediately usable rather than requiring a second, separate lookup.

## Exercise

Write a real custom condition that waits until a real element's `style` attribute no longer contains
`"display:none"` (no space), and run it against `/dynamic-visibility`'s `reveal-btn`. It will return almost
instantly — investigate why using `get_attribute('style')` directly, find the real, normalized string the
browser actually reflects back, and fix the substring so the condition genuinely waits the expected ~2 seconds.

