---
description: "The race condition that failed 15/15 times in Lesson 1 succeeded 10/10 with a 5s implicit wait -- returning in ~2.02s when found, 5.02s when not."
---

# Implicit Waits

## What was run

The exact same real race condition from [Lesson 1](01-the-real-race-condition.md) — which failed `15/15`
times with zero wait — repeated with a real implicit wait configured, plus two direct real timing
measurements to see exactly how implicit wait actually behaves: does it return early once an element appears,
or always wait its full configured duration regardless?

## The real race condition, fixed

```python
driver.implicitly_wait(5)
```
```
trial 1: found after real 2.01s wait -- 'I appeared after a real delay'
...
trial 10: found after real 2.01s wait -- 'I appeared after a real delay'

REAL success rate with implicit wait: 10/10
```

A real, complete turnaround from [Lesson 1](01-the-real-race-condition.md)'s `0/15`: the identical
`find_element()` call, against the identical real page, now succeeds every single time.

## A real, precise answer to how implicit wait actually behaves

```python
el = driver.find_element(By.ID, "dynamic-element")  # element appears at ~2s
```
```
real elapsed time when element IS found (5s implicit wait configured): 2.02s
```
```python
driver.find_element(By.ID, "this-never-exists")  # an element that genuinely never appears
```
```
real elapsed time before giving up on a NEVER-appearing element: 5.02s (configured implicit wait: 5s)
```

Two real, different outcomes that together answer the question precisely: when the element genuinely appears
at `~2s`, Selenium returns almost immediately afterward (`2.02s`) — it polls repeatedly rather than sleeping
blindly for the full configured duration. When the element never appears at all, it genuinely waits the
**entire** configured `5` seconds (`5.02s`) before finally raising `NoSuchElementException`.

## How It Actually Works

An implicit wait doesn't replace `find_element()`'s real one-time check with a single longer wait — it turns
that check into a real, repeated polling loop, retrying periodically until either the element is found or the
configured timeout elapses. This lesson's two real timing measurements prove both halves of that behavior
directly: the real, fast `2.02s` return when the element exists confirms it's genuinely polling and exiting
early, not just sleeping for a fixed duration; the real, full `5.02s` wait before failure confirms the
timeout is a genuine upper bound, not an average or a suggestion.

The real, practical cost this introduces: `implicitly_wait()` applies globally, to *every* `find_element()`
call for the rest of the session. A test that correctly expects an element *not* to exist will now genuinely
wait the full configured timeout before confirming that — this lesson's real `5.02s` "never appears" result is
exactly that real cost, paid every single time a lookup is expected to fail.

## Common mistakes

- Assuming implicit wait always takes the full configured duration — this lesson's real `2.02s` success case
  directly disproves that; it polls and returns as soon as the element is found.
- Setting a long implicit wait and then writing tests that deliberately check for an element's *absence* —
  this lesson's real `5.02s` result for a genuinely nonexistent element shows exactly how expensive that
  combination is, since every "this shouldn't be here" check pays the full timeout.
- Mixing implicit waits with explicit waits ([Lesson 3](03-explicit-waits.md)) without understanding they can
  compound — Selenium's own documentation warns against combining the two, and the real reason is directly
  visible in this lesson's timing data: each one independently polls and can stack unpredictable extra real
  delay on top of the other.

## Exercise

Set the implicit wait to a real `1` second instead of `5`, and re-run [Lesson 1](01-the-real-race-condition.md)'s
exact 15-trial experiment. Predict the real success rate first (the element appears at `~2s`, the wait allows
only `1s`), then run it and confirm whether your prediction matches the real, measured result.
