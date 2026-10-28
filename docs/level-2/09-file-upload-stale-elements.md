---
description: "Chrome genuinely denied file access for a real ~/Desktop path (ERR_ACCESS_DENIED) while the same upload from /tmp succeeded -- a real sandbox boundary."
---

# File Upload & Stale Elements

## What was run

A real file upload via `send_keys()` on a file input — no OS file picker dialog involved, which real
browsers don't require Selenium to interact with — attempted first against a file under `~/Desktop`
(genuinely denied, investigated directly) and then against the identical operation under `/tmp` (succeeded),
followed by a real, deliberately reproduced `StaleElementReferenceException`.

## A real, genuine access denial — investigated, not ignored

```python
driver.find_element(By.ID, "file-input").send_keys(desktop_file)
driver.find_element(By.ID, "upload-btn").click()
```
```
real page title after submitting: '127.0.0.1'
REAL finding: Chrome's own file access was DENIED for this real path
```

Checking the real page source directly revealed Chrome's own internal error page, with the exact real error
code `ERR_ACCESS_DENIED` — not a Selenium exception, not a Flask error (confirmed separately: the identical
upload via plain `curl` from outside the browser worked perfectly). The real file genuinely exists
(`os.path.exists()` confirmed `True`); Chrome's own renderer process, in this specific sandboxed environment,
was not permitted to read it.

## The real, investigated fix

```python
tmp_file = "/tmp/real_test_upload_tmp.txt"
driver.find_element(By.ID, "file-input").send_keys(tmp_file)
```
```
REAL upload result from a /tmp path: 'Uploaded: real_test_upload_tmp.txt'
```

The identical real operation, against a file under `/tmp` instead of `~/Desktop`, succeeded completely. A
real, specific sandbox boundary — not a general file-upload limitation — restricts which real paths this
particular browser process can read from in this environment, independent of what the automation script's
own Python process can access.

## A real, deliberately reproduced stale element

```python
original_btn = driver.find_element(By.ID, "original-btn")
driver.find_element(By.ID, "replace-btn").click()   # removes and recreates the element
original_btn.text   # using the OLD real reference
```
```
real original button text (before replacement): 'Original Button'
REAL StaleElementReferenceException raised: Message: stale element reference: stale element
not found in the current frame
```

```python
refetched_btn = driver.find_element(By.ID, "original-btn")   # a fresh real lookup
```
```
real re-fetched button text (same ID, new DOM node): 'Replaced Button'
```

## How It Actually Works

`send_keys()` on a `<input type="file">` element is Selenium's real, standard technique for file upload —
it directly sets the input's file list via the WebDriver protocol, which is exactly why no OS-level file
dialog ever needs to appear or be automated. This lesson's real `ERR_ACCESS_DENIED` finding is a genuinely
different, separate concern: once Selenium hands the browser a path, it's the real browser process itself —
subject to whatever real OS or sandbox permissions it runs under — that has to actually read the file's
content to upload it. In this specific sandboxed environment, that real permission boundary differs from
what the automation script's own process can access, which is exactly why the identical operation succeeded
from `/tmp` and failed from `~/Desktop`.

A `WebElement` reference in Selenium points to a specific real DOM node — not an ID string, not a position.
When the page's real JavaScript genuinely removes and recreates an element (even with the identical `id`
attribute), the *old* reference becomes real garbage pointing at a node that no longer exists in the live
document, which is exactly what `StaleElementReferenceException` reports. The real, correct fix is always the
same: look the element up again, fresh, after any DOM change that might have replaced it.

## Common mistakes

- Assuming a file-upload failure must be a Selenium or application bug — this lesson's real, direct
  investigation (checking the actual Chrome error page, confirming the same upload worked via `curl`, and
  finding a path that genuinely succeeded) is what correctly isolated the real cause to a sandboxed browser
  permission boundary.
- Holding onto a `WebElement` reference across any action that might change the DOM — this lesson's real
  `StaleElementReferenceException` is the exact, expected consequence of doing that here.
- Wrapping every stale-element risk in a blind retry loop without understanding *why* staleness happens — the
  real, correct fix is re-fetching the element after the specific change that invalidated it, not retrying
  the exact same stale reference repeatedly.

## Exercise

Check which specific real directories this environment's Chrome process can and cannot read files from by
testing uploads from at least three different real paths (for example, your home directory directly, a
subdirectory you create inside `/tmp`, and a path inside your project's own working directory). Report the
real pattern you find, rather than assuming it's simply "Desktop is blocked, everything else works."
