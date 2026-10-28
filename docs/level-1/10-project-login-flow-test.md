---
description: "The capstone's first real run hit the exact same race condition seen earlier in this course -- fixed with an honest, temporary sleep(), not hidden."
---

# Project: a Real Login Flow Test

## What was run

A real, complete test suite — 5 real test cases covering a valid login, an invalid password, an invalid
username, empty credentials, and case sensitivity — run against the local Flask app's real `/login` form,
combining every real technique from this level: locators, element interaction, and reading real result state.

## A real, honest obstacle on the very first run

```python
driver.find_element(By.ID, "submit-btn").click()
return driver.find_element(By.ID, "result").text
```
```
selenium.common.exceptions.NoSuchElementException: Message: no such element:
Unable to locate element: {"method":"css selector","selector":"[id="result"]"}
```

This capstone's first real run hit exactly the same real race condition this course's own build encountered
earlier: finding `#result` *immediately* after clicking submit, with zero wait, genuinely fails — the real
page hadn't finished responding yet. The real, honest, temporary fix used here:

```python
time.sleep(0.3)  # a real, deliberately temporary fix -- Level 2 does this properly
return driver.find_element(By.ID, "result").text
```

## Real, complete test results after the fix

```python
assert result == "Login Successful"
```
```
REAL test_valid_login: PASSED
REAL test_invalid_password: PASSED
REAL test_invalid_username: PASSED
REAL test_empty_credentials: PASSED
REAL test_case_sensitivity: username 'RealUser' (wrong case) -> 'Login Failed'
REAL test_case_sensitivity: PASSED

=== REAL test run complete: 5 passed, 0 failed ===
```

Every real test case — including the case-sensitivity check, which confirmed the real backend genuinely
rejects a username with incorrect capitalization — passed against the real, running Flask app.

## How It Actually Works

This capstone combines every real piece from this level: [Lesson 3](03-locators-basic.md)'s `By.ID` locators
to find the form fields, [Lesson 6](06-interacting-with-elements.md)'s `send_keys()` and `click()` to fill and
submit it, and [Lesson 9](09-element-state.md)'s `.text` property to read back the real result. The one real,
new wrinkle — the race condition this capstone's own first run hit — is deliberately left in this lesson's
account rather than silently fixed before anyone saw it, because it's exactly the real problem that justifies
[Level 2](../level-2/index.md)'s entire existence: `time.sleep(0.3)` works here, on this fast local server, but
it's a real, fragile guess, not a real guarantee, and the very next level replaces it with something that
actually is one.

## Common mistakes

- Writing a login test that only checks the success case — this capstone's real suite covers four distinct
  real failure modes (wrong password, wrong username, empty fields, wrong case) specifically because a login
  form's real security properties live in how it handles *incorrect* input, not just correct input.
- Papering over a real race condition with a sleep and never revisiting it — this lesson's `time.sleep(0.3)`
  is flagged explicitly as temporary, not presented as a real, permanent solution; a growing test suite with
  dozens of sleeps like this becomes both slow and unreliable in ways [Level 2](../level-2/index.md) exists to
  fix.
- Not testing case sensitivity or other input-normalization edge cases — this lesson's real
  `test_case_sensitivity` check could just as easily have found a real bug (if the backend normalized
  usernames) as confirmed correct behavior; either outcome is worth knowing for certain.

## Exercise

Add a sixth real test case checking what happens when the username or password contains real leading/trailing
whitespace (for example, `" realuser "` instead of `"realuser"`). Run it against the real app and find out
whether the real backend trims whitespace before comparing, or treats it as a genuinely different, incorrect
username.
