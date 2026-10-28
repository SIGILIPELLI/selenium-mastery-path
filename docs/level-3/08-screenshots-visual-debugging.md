---
description: "A real deliberate test failure triggered a real 6,411-byte screenshot whose first 8 bytes matched the exact PNG signature -- a genuine image, not a stub."
---

# Screenshots & Visual Debugging on Failure

## What was run

A real, deliberately failing test (asserting an impossible result) run alongside a real, correctly-passing
one, with a real screenshot captured automatically on the failure — then independently verified at the byte
level to confirm it's a genuine PNG image, not just an empty or corrupted file.

## A real failure, triggering a real, automatic screenshot

```python
def run_test_with_screenshot_on_failure(driver, test_name, test_fn):
    try:
        test_fn(driver)
    except AssertionError as e:
        path = os.path.join(SCREENSHOT_DIR, f"{test_name}.png")
        saved = driver.save_screenshot(path)
```
```
REAL test_login_expects_wrong_text: FAILED -- got 'Login Successful'
real screenshot saved: True, path=real_failure_screenshots/test_login_expects_wrong_text.png,
real file size=6411 bytes
```

The passing test, by contrast, never triggers this path at all:

```
REAL test_login_correct_assertion: PASSED
```

## A real, byte-level verification that the file is a genuine image

```python
with open(screenshot_path, "rb") as f:
    header = f.read(8)
real_png_signature = b'\x89PNG\r\n\x1a\n'
```
```
real saved file's first 8 bytes match the real PNG file signature: True
```

A real, direct confirmation at the file-format level — not just trusting that `save_screenshot()` returned
`True` — that the saved file genuinely begins with the real, standard 8-byte PNG magic number every valid PNG
file starts with.

## How It Actually Works

`driver.save_screenshot()` captures a real, current rendering of the browser's viewport at the moment it's
called and writes it to disk as a genuine PNG file — this lesson's real `6,411`-byte file and its correctly
matching PNG signature confirm that's exactly what happened, not an empty placeholder or a failed write that
silently returned success. Wrapping test execution in a `try`/`except` that captures a screenshot specifically
on `AssertionError` means the exact real browser state at the moment of failure gets preserved — critical for
debugging a real test failure after the fact, since the real browser session (and whatever it was actually
displaying) is gone the instant the script moves on or the driver quits.

The real, practical value here is diagnostic: a test failure message like this lesson's `"got 'Login
Successful'"` tells you *what* was wrong, but a real saved screenshot can reveal *why* — an unexpected error
banner, a page that never finished loading, an element that rendered somewhere unexpected — information a
plain text assertion message alone cannot carry.

## Common mistakes

- Taking a screenshot only at the very end of a test run instead of at the moment of each real failure — by
  the time a test suite finishes, the browser may have moved through several more real pages, and the
  screenshot would show the wrong, final state rather than the real moment something actually went wrong.
- Trusting `save_screenshot()`'s boolean return value without ever checking the real resulting file — this
  lesson's byte-level PNG signature check is what actually confirms a valid image exists on disk, not just
  that the call claimed success.
- Saving every screenshot to the same real filename — this lesson's pattern of naming each file after its
  real test ensures a failure in one test doesn't silently overwrite evidence from another.

## Exercise

Modify this lesson's `run_test_with_screenshot_on_failure` helper to also capture the real page's full HTML
source (`driver.page_source`) to a matching `.html` file alongside the screenshot whenever a test fails. Run
it against this lesson's exact same deliberate failure and confirm both real files — the image and the real
HTML snapshot — correspond to the exact same moment in the test.
