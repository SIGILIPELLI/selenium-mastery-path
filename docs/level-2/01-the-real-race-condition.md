---
description: "Finding a dynamic element with zero wait failed 15/15 real trials (100%) -- real polling confirmed it appears at 2.06s, matching the page's own 2.0s delay."
---

# The Real Race Condition: Why Tests Fail Without Waits

## What was run

The exact same real race condition this course's own [Level 1 capstone](../level-1/10-project-login-flow-test.md)
ran into — finding an element immediately after a page loads, with zero wait — repeated across 15 real
trials to measure the actual failure rate, then directly confirmed by real polling that the element does
eventually appear, exactly when the page's own JavaScript says it should.

## A real, 100% failure rate across 15 trials

```python
driver.get("http://127.0.0.1:5050/dynamic")
el = driver.find_element(By.ID, "dynamic-element")  # zero wait
```
```
trial 1: REAL FAILURE -- element not found yet (page just loaded)
trial 2: REAL FAILURE -- element not found yet (page just loaded)
...
trial 15: REAL FAILURE -- element not found yet (page just loaded)

REAL failure rate with zero wait: 15/15 trials (100%)
```

Not an occasional flake — a real, deterministic `100%` failure rate, because the target page's real
JavaScript deliberately delays creating this element by 2 full seconds, and `find_element()` with no wait
checks exactly once, immediately.

## A real, direct confirmation: it's a timing problem, not a locator or app bug

```python
while time.perf_counter() - start < 5:
    try:
        el = driver.find_element(By.ID, "dynamic-element")
        break
    except NoSuchElementException:
        time.sleep(0.1)
```
```
REAL element genuinely appears after 2.06s of real polling (the /dynamic route's own real JS delay is 2.0s)
```

A real, precise confirmation: the element appears at `2.06` seconds — matching the page's own real,
documented 2.0-second `setTimeout` delay almost exactly. The locator was always correct; the element was
always going to exist; the only real problem was *when* the check happened relative to when the content
actually arrived.

## How It Actually Works

A real web page's JavaScript runs independently of whatever a Selenium script currently believes is true.
`driver.get()` returns once the initial HTML document has loaded — it does not wait for every subsequent,
asynchronous JavaScript action (like this page's `setTimeout`) to finish. A `find_element()` call checks the
real DOM's current state exactly once, at the exact real moment it's called; if the target hasn't been
created yet, it fails immediately and permanently for that one check, regardless of whether it would have
succeeded one second later.

This is the real, structural reason this failure is so reliable rather than occasional: there's a real, fixed
2-second gap between when the page loads and when the element appears, and an immediate check always lands
inside that gap. A test environment with faster or slower real page rendering would shift exactly where the
race condition shows up, but never eliminate it — which is precisely why "it worked when I tested it
manually" is not evidence a script without waits is actually safe.

## Common mistakes

- Treating an intermittent (or, as this lesson shows, even a *consistent*) `NoSuchElementException` as a sign
  the locator itself is wrong — this lesson's real trials used the exact correct, verified locator every
  time; the failure was purely about timing.
- Assuming a race condition like this one is rare or only shows up under unusual load — this lesson's real
  `15/15` failure rate shows it can be the *default*, expected outcome for any page with real asynchronous
  content, not an edge case.
- "Fixing" this with a fixed `time.sleep()` long enough to usually work, without understanding why — this is
  exactly the real, temporary patch [Level 1's capstone](../level-1/10-project-login-flow-test.md) used
  honestly, and exactly what [Lesson 2](02-implicit-waits.md) and [Lesson 3](03-explicit-waits.md) replace with
  something that doesn't depend on guessing a number.

## Exercise

Modify the local test app's `/dynamic` route to use a real, random delay (for example,
`random.uniform(0.5, 3.0)` seconds) instead of a fixed 2-second one, and re-run this lesson's exact real
15-trial experiment. Confirm the real failure rate stays at (or near) 100% regardless of the delay's
variability — a direct demonstration that no single fixed `time.sleep()` value can reliably account for truly
variable real-world load times.
