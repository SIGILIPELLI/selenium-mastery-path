---
description: "Nine real anti-patterns, each backed by a genuine bug this course itself hit and investigated -- not invented examples."
---

# Selenium Anti-Patterns

## What was run

No new code — a catalog of real anti-patterns, each one tied directly to a genuine bug or surprising
behavior this course actually hit and investigated earlier, rather than invented hypothetical examples.

## 1. Assuming `.click()` on a disabled element raises an exception

[Level 1, Lesson 6](../level-1/06-interacting-with-elements.md) assumed `.click()` on `disabled-btn` would
raise `ElementNotInteractableException`. The real run raised nothing at all. The anti-pattern: trusting an
assumption about Selenium's behavior instead of the real, observed result — the fix there required adding a
real `onclick` handler to the test app just to confirm the click genuinely did nothing.

## 2. Hardcoding `time.sleep()` instead of waiting for a real condition

The [Level 1 capstone](../level-1/10-project-login-flow-test.md) first hit a real
`NoSuchElementException` from checking for a result immediately after a click. The quick fix
(`time.sleep(0.3)`) was flagged honestly as a temporary bridge, not a real solution — this course's entire
[Level 2](../level-2/index.md) exists because the real, durable fix is waiting for an observable condition,
not guessing a duration that happens to work on one machine, once.

## 3. Trusting `get_attribute()` string values without checking their real format

[Level 2, Lesson 4](../level-2/04-custom-expected-conditions.md)'s exercise assumed the `style` attribute
would contain the substring `"display:none"` — the real browser-normalized value was `'display: none;'`
(with a space). The anti-pattern: string-matching a browser-generated attribute value without first printing
its real, exact `repr()`.

## 4. Assuming a keyboard shortcut works identically across operating systems

[Level 2, Lesson 8](../level-2/08-actions-api.md) sent `Ctrl+A` expecting a full text selection and got
`selectionStart == selectionEnd` — no selection happened at all. The real OS convention on macOS is
`Cmd+A`. The anti-pattern: assuming one platform's keyboard convention generalizes, instead of verifying the
real selection state directly.

## 5. Assuming any local file path is uploadable via `send_keys()`

[Level 2, Lesson 9](../level-2/09-file-upload-stale-elements.md) hit a real `ERR_ACCESS_DENIED` uploading a
file from this course's own project directory — identical content uploaded fine from `/tmp/`. The
anti-pattern: assuming Selenium's own successful `send_keys()` call means the browser process can actually
read that path; a browser's own file-system sandboxing is a separate, real constraint.

## 6. Re-using a `WebElement` reference after the DOM changes

[Level 2, Lesson 9](../level-2/09-file-upload-stale-elements.md) also demonstrated a real
`StaleElementReferenceException` after a button's container was replaced — even though the new element had
the identical `id`. The anti-pattern: treating a `WebElement` as a durable pointer to "the thing with this
id" rather than what it actually is — a reference to one specific, now-destroyed DOM node.

## 7. Duplicating the same locator across every test file

[Level 3, Lesson 1](../level-3/01-why-page-object-model.md) measured the real cost directly: renaming one
real HTML `id` crashed three separate duplicated-locator test functions simultaneously. The anti-pattern:
skipping the Page Object Model because "it's just one extra class" — the real, measured cost of *not* having
it is paying for every single call site when the application's markup changes once.

## 8. Defining a `pytest_runtest_makereport` hook inside a test module

[Level 3, Lesson 10](../level-3/10-project-pom-framework.md) wrote this hook directly inside a test file —
it silently never fired at all, with zero screenshots captured despite a real failing test. Moving the
identical function into `conftest.py` fixed it immediately. The anti-pattern: assuming a pytest hook works
anywhere a function can be defined, rather than respecting pytest's real plugin-discovery rules.

## 9. Judging parallel speedup from a suite of very short tests

[Level 4, Lesson 3](03-parallel-test-execution.md) measured only an `11%` real improvement from 4 parallel
workers at `0.5s`/test — not because parallelism doesn't work, but because fixed per-worker browser-launch
overhead dominated. The anti-pattern: concluding "parallel testing doesn't help much here" from a
short-test suite, instead of checking whether fixed overhead is the real bottleneck (as the same lesson's
`2.0s`/test re-run, at a real `1.67×`, directly demonstrated).

## How It Actually Works

Every entry above traces back to the same single root behavior: Selenium (and the real browser it drives)
will do exactly what you told it to do, not what you assumed it would do, and it will not warn you when
those two things diverge. `.click()` on a disabled element doesn't raise because nothing in the real DOM or
WebDriver spec requires it to; `get_attribute()` returns the browser's own real, normalized string, not the
one you expect; a stale `WebElement` reference fails specifically because it points at a real, now-gone DOM
node, not at an abstract "element with this id." This course's own repeated methodology — run the real thing,
look at the real result, investigate when it's surprising — is the direct antidote to every anti-pattern on
this list, because each one was caught by exactly that process rather than by memorizing a list of rules in
advance.

## Common mistakes

- Treating this list as exhaustive — it covers the real bugs this specific course hit; a different
  application under test will surface its own, different real surprises using the identical investigative
  method.
- Memorizing the fixes without the underlying mechanism — e.g. remembering "use `/tmp/` for uploads" without
  understanding *why* (browser process sandboxing), which won't transfer to a different real environment's
  specific sandboxing rules.
- Assuming anti-patterns only matter at scale — several of these (the disabled-button assumption, the
  `Ctrl+A` assumption) were real, first-run surprises in a single test, not failures that only appear under
  load or scale.

## Exercise

Pick any two anti-patterns from this list whose root cause you haven't directly reproduced yourself. Rerun
the real underlying lesson's code locally, and before looking at its real output, write down your own
prediction. Compare your prediction against the real result — this is the exact process that caught every
one of these nine anti-patterns in the first place.
