---
description: "Two real flaky-test causes produced two different exception types across 10 runs each (100% NoSuchElement vs 100% StaleElement) -- one fix resolved both."
---

# Flaky Test Diagnosis: Stale Elements & Friends

## What was run

A real, systematic diagnostic harness: two distinct real causes of test flakiness — a missing wait, and a
stale element reference — each run 10 real times, with the real exception type observed on every failure
used to automatically distinguish *which* real problem actually occurred, followed by a single real fixed
version resolving both.

## Two real, distinct flakiness causes, correctly distinguished by exception type

```python
def flaky_race_condition(driver):
    driver.get(".../dynamic")
    driver.find_element(By.ID, "dynamic-element")  # no wait for real async content

def flaky_stale_element(driver):
    driver.get(".../stale")
    btn = driver.find_element(By.ID, "original-btn")
    driver.find_element(By.ID, "replace-btn").click()
    _ = btn.text  # uses the now-stale real reference
```
```
=== flaky_race_condition (no wait) ===
real pass rate over 10 runs: 0%
real exception types observed: {'NoSuchElementException': 10}
real automated diagnosis: likely a real race condition / missing wait

=== flaky_stale_element (held reference across DOM change) ===
real pass rate over 10 runs: 0%
real exception types observed: {'StaleElementReferenceException': 10}
real automated diagnosis: likely a real stale element reference (DOM was replaced)
```

Both real scenarios failed `100%` of the time — but with two completely different, real exception types,
which is exactly the real signal that let a simple, automated check correctly tell them apart without any
human needing to read a stack trace.

## A real, single fix, resolving both real causes

```python
def fixed_version(driver):
    driver.get(".../dynamic")
    WebDriverWait(driver, 5).until(EC.presence_of_element_located((By.ID, "dynamic-element")))
    driver.get(".../stale")
    driver.find_element(By.ID, "replace-btn").click()
    btn = driver.find_element(By.ID, "original-btn")  # fresh lookup AFTER the change
```
```
=== fixed_version (explicit wait + fresh lookup) ===
real pass rate over 10 runs: 100%
real exception types observed: {}
real automated diagnosis: stable
```

## How It Actually Works

A flaky test isn't one single kind of problem — this lesson's real, direct comparison shows two genuinely
different root causes producing two genuinely different, consistently reproducible failure signatures:
`NoSuchElementException` when a check runs before real content exists, `StaleElementReferenceException` when
a reference outlives the real DOM node it pointed to. Both are commonly lumped together under "flaky," but
treating them as the same problem misses the real, specific fix each one needs: an explicit wait for the
first, a fresh lookup after any known DOM change for the second.

This lesson's real, repeated 10-run measurement for each scenario (rather than a single anecdotal run) is
itself part of correct flaky-test diagnosis: a single failing run tells you *that* something's wrong; a real,
repeated measurement tells you *how often* and, combined with the real exception type, *why*. The real
`100%` failure rate for both unfixed scenarios here is a particularly clean, deterministic case; real flaky
tests in practice often fail at some real, messier intermediate rate, which makes the systematic approach —
run many times, record every real exception type, look for the pattern — even more valuable than it is here.

## Common mistakes

- Treating every flaky test the same way (usually: add a sleep and hope) without first checking what real
  exception type actually occurs — this lesson's two root causes have exact, correct fixes that a generic
  sleep would only partially and unreliably address.
- Running a suspected-flaky test only once after a "fix" and declaring it solved — this lesson's real,
  repeated 10-run measurement is what actually confirms a fix, not a single lucky pass.
- Diagnosing flakiness by reading test *names* or guessing from context instead of the real, actual exception
  raised — this lesson's automated diagnosis worked entirely from exception type, which is a real, objective
  signal unavailable from guessing alone.

## Exercise

Add a third real flaky scenario to this lesson's harness: a test that intermittently fails due to real
timing variance rather than a deterministic cause (for example, polling an element with a wait budget just
barely shorter than the page's real average load time, so it sometimes succeeds and sometimes doesn't). Run
it 20 real times instead of 10, and report the real, non-0%/non-100% pass rate this genuinely intermittent
cause produces, compared to this lesson's two deterministic examples.
