---
description: "Opening a real new window did not change driver.current_window_handle -- title stayed 'Original Window' until an explicit switch_to.window() call."
---

# Handling Multiple Windows & Tabs

## What was run

A real link that opens a genuinely new browser window (`target="_blank"`), tracked through
`driver.window_handles`, with a direct, real check of whether Selenium's "current" context automatically
follows a newly-opened window — and a real close-and-switch-back sequence afterward.

## A real new window, opened — but not automatically switched to

```python
driver.find_element(By.ID, "new-window-link").click()
WebDriverWait(driver, 3).until(lambda d: len(d.window_handles) == 2)
```
```
real window_handles after clicking the new-window link: 2
real current_window_handle still equals original: True
real current title (still the OLD window's title, confirming no auto-switch): 'Original Window'
```

A real, important confirmation: opening a second real window did not change which window Selenium is actually
"looking at." The title still reads the *original* window's real title, even though a second real window now
genuinely exists.

## A real, explicit switch — and proof it landed on the right window

```python
new_window = [h for h in driver.window_handles if h != original_window][0]
driver.switch_to.window(new_window)
```
```
REAL after explicit switch_to.window(): title='New Window', heading='This is a new real window'
```

## A real close, and a real switch back

```python
driver.close()
driver.switch_to.window(original_window)
```
```
real window_handles after closing the new window: 1
real current title after switching back to original: 'Original Window'
```

## How It Actually Works

Opening a new real browser window or tab doesn't automatically move Selenium's point of control to it — this
lesson's real check (`current_window_handle == original_window` staying `True` right after the click) proves
that directly. Every real window or tab a session has opened gets its own handle, tracked in
`driver.window_handles`, and `switch_to.window()` is the real, explicit action required to point subsequent
commands at a different one. `driver.close()` closes only the *currently active* real window — this lesson's
sequence had to switch into the new window first, then close *that* one specifically, rather than closing the
original by accident.

This matters practically: a test that clicks something opening a new tab and immediately tries to interact
with "the new content" will, without an explicit switch, keep operating on the real original window — exactly
the kind of silent, confusing failure this lesson's direct title check was built to catch.

## Common mistakes

- Assuming Selenium automatically follows a newly-opened window or tab — this lesson's real, verified result
  (`current_window_handle` unchanged) is the direct disproof of that assumption.
- Calling `driver.close()` without first confirming which window is actually active — closing the wrong real
  window (for example, the original one while still "in" it) can leave a session with no way back to content
  you still needed.
- Comparing window handles as if they were predictable or orderable — this lesson used a real set-difference
  (`[h for h in handles if h != original]`) specifically because handle values are opaque identifiers with no
  guaranteed order or format.

## Exercise

Open two *additional* new windows from the original (three real windows total), and write real code that
switches to each one in turn, collects each real title into a list, and confirms the final list contains
exactly `['Original Window', 'New Window', 'New Window']` — handling the real case where multiple windows
happen to share the same title.
