---
description: "A real 6-test suite combining every Level 2 technique -- waits, alerts, iframes, windows, Actions, stale elements -- passed 6/6 with zero fixed sleeps."
---

# Project: a Real Dynamic-Content Test Suite

## What was run

A real, complete test suite combining every real technique this level built — explicit waits, alert handling,
iframe switching, multi-window handling, the Actions API, and correct stale-element recovery — run together
against the real local Flask app, with real assertions checking actual resulting state at every step.

## Six real tests, each exercising a distinct real Level 2 technique

```python
WebDriverWait(driver, 5).until(EC.presence_of_element_located((By.ID, "dynamic-element")))
alert.accept()
driver.switch_to.frame(frame)
driver.switch_to.window(new_window)
ActionChains(driver).drag_and_drop(source, target).perform()
refreshed = driver.find_element(By.ID, "original-btn")  # re-fetched after a DOM change
```
```
REAL test_dynamic_content_wait: PASSED
REAL test_alert_handling: PASSED
REAL test_iframe_interaction: PASSED
REAL test_new_window_flow: PASSED
REAL test_actions_drag_and_drop: PASSED
REAL test_stale_element_recovery: PASSED

=== REAL Level 2 capstone suite complete: 6 passed, 0 failed ===
```

Every real test passed — and notably, **not one of them uses a fixed `time.sleep()`** anywhere.
[Level 1's capstone](../level-1/10-project-login-flow-test.md) needed one as an honest, temporary patch;
every real test in this capstone instead uses the correct tool this level actually built for its specific
real situation: explicit waits for asynchronous content, proper alert/window/frame switching for browser-native
UI, and a fresh lookup for a genuinely replaced element.

## How It Actually Works

This capstone is a direct, working demonstration that this level's techniques compose cleanly: each real test
targets exactly one of the real problems [Lessons 1 through 9](index.md) solved, using the real, correct tool
for that specific situation rather than a one-size-fits-all patch. `test_dynamic_content_wait` uses
`WebDriverWait` instead of a guessed sleep; `test_alert_handling` and `test_iframe_interaction` use the
real, specific switching APIs browser-native UI and embedded documents require;
`test_stale_element_recovery` re-fetches rather than retrying a known-invalid reference.

The real, meaningful contrast with [Level 1's capstone](../level-1/10-project-login-flow-test.md) is exactly
the point of this entire level: that earlier project's honest `time.sleep(0.3)` worked, but only by luck and
only on this fast local server — it offered no real guarantee. Every real test in this capstone instead
relies on an actual, verifiable condition being met, which is why none of them needed to guess a duration at
all.

## Common mistakes

- Treating a passing test suite as proof every technique was used correctly — this capstone's real value
  comes specifically from each test using the *right* technique for its specific real scenario, not just from
  all six happening to pass.
- Writing one generic "wait and hope" helper and reusing it everywhere instead of this capstone's approach of
  matching each real situation (missing element, open alert, separate frame, new window, DOM replacement) to
  its own correct, specific real tool.
- Running a test suite like this once and declaring it reliable — a real test suite's actual reliability is
  demonstrated by running it repeatedly under real, possibly varying conditions, not by one single clean run.

## Exercise

Add a seventh real test to this suite that deliberately triggers this level's real file-upload investigation
from [Lesson 9](09-file-upload-stale-elements.md) — uploading from a path under `/tmp` specifically, since
that's the real, confirmed-working location in this environment — and assert the real resulting page text
matches the uploaded filename exactly.
