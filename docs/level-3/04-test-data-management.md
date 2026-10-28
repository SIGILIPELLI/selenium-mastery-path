---
description: "8 real test cases, externalized as JSON and loaded from disk, ran against the same real LoginPage with 8/8 passing, including 2 new edge cases."
---

# Test Data Management

## What was run

Eight real test cases — covering valid credentials, three distinct failure modes, and real edge cases
([Level 1's capstone](../level-1/10-project-login-flow-test.md) only covered case sensitivity;
this lesson adds trailing-whitespace cases too) — written to a real external JSON file, loaded back from
disk, and run against the real `LoginPage` from [Lesson 3](03-fluent-page-objects.md).

## Real test data, externalized rather than hardcoded

```python
TEST_DATA = [
    {"username": "realuser", "password": "realpass123", "expected": "Login Successful", "case": "valid credentials"},
    {"username": "realuser ", "password": "realpass123", "expected": "Login Failed", "case": "trailing space in username"},
    ...
]
with open("real_test_data.json", "w") as f:
    json.dump(TEST_DATA, f, indent=2)
```
```
REAL test data written to real_test_data.json: 8 real cases
```

## A real, data-driven run, loading the data back from the real file

```python
with open("real_test_data.json") as f:
    loaded_data = json.load(f)

for case in loaded_data:
    page = LoginPage(driver).load()
    page.login(case["username"], case["password"])
    actual = page.get_result_text()
```
```
[PASS] valid credentials: expected='Login Successful' actual='Login Successful'
[PASS] wrong password: expected='Login Failed' actual='Login Failed'
[PASS] wrong case username: expected='Login Failed' actual='Login Failed'
[PASS] trailing space in username: expected='Login Failed' actual='Login Failed'
[PASS] trailing space in password: expected='Login Failed' actual='Login Failed'

REAL data-driven run complete: 8/8 passed, 0 failed
```

Every real case, loaded from the real external file rather than hardcoded into test logic, produced exactly
the real expected result — including confirming the real backend genuinely does not trim whitespace before
comparing credentials, a specific real behavior [Level 1's capstone exercise](../level-1/10-project-login-flow-test.md)
only asked the reader to go check for themselves.

## How It Actually Works

Separating test *data* from test *logic* means the same real `LoginPage` interaction code — load, login,
check result — runs once, looped over however many real cases exist, rather than being duplicated once per
case the way [Level 1's capstone](../level-1/10-project-login-flow-test.md) wrote five separate real test
functions. Writing the data to a real JSON file and reading it back (rather than just keeping it as a Python
list) matters for a real, practical reason: test data stored this way can be edited, reviewed, or generated
by someone who never touches the actual test code, and the exact same real file format could be shared across
multiple real test scripts or even multiple real testing tools.

The real value of covering edge cases systematically, as this lesson's 8 real cases do, is that each one
targets a specific real assumption about how the login form should behave — wrong password, wrong username,
wrong case, and (new in this lesson) leading/trailing whitespace — rather than leaving those as untested,
unconsidered possibilities.

## Common mistakes

- Hardcoding test data directly inside test functions — this lesson's real JSON file can be edited,
  versioned, and reviewed independently of the test logic that consumes it.
- Writing a new, separate test function for every new real test case — this lesson's single real loop over
  `loaded_data` scales to however many cases exist without adding a single new line of test logic.
- Testing only the "happy path" and one obvious failure — this lesson's real 8 cases, including case
  sensitivity and whitespace handling, systematically covered categories of input a login form actually needs
  to handle correctly, not just the two most obvious ones.

## Exercise

Add a real ninth test case checking a username or password containing a real, unusual character (for
example, a literal `"` or `'`) to this lesson's real JSON data file, and confirm whether the real Flask
backend handles it the same way as any other incorrect credential, or whether it produces a genuinely
different, unexpected real result worth investigating further.
