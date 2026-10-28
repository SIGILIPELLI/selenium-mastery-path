---
description: "The real screenshot-on-failure hook, defined inside the test module, silently never fired -- moved to conftest.py, it saved a real 6,411-byte file."
---

# Project: a Real POM Test Framework

## What was run

A real, complete test framework combining every technique this level built: the fluent `LoginPage` from
[Lesson 3](03-fluent-page-objects.md), the real externalized test data from
[Lesson 4](04-test-data-management.md) driving a real `pytest.mark.parametrize` suite, a real session-scoped
fixture from [Lesson 5](05-pytest-fixtures-for-selenium.md), and an automatic screenshot-on-failure hook from
[Lesson 8](08-screenshots-visual-debugging.md) — which, on its first real attempt, silently did nothing at
all.

## Real, data-driven tests, all passing

```python
@pytest.mark.parametrize("case", LOGIN_CASES, ids=[c["case"] for c in LOGIN_CASES])
def test_login_data_driven(driver, case):
    page = LoginPage(driver).load()
    page.login(case["username"], case["password"])
    assert page.get_result_text() == case["expected"]
```
```
test_login_data_driven[valid credentials] PASSED
test_login_data_driven[wrong case username] PASSED
test_login_data_driven[trailing space in password] PASSED
... (8 passed total)
```

## A real, deliberate failure — and a hook that silently didn't work

```python
# first attempt: hook defined directly inside the test module
@pytest.hookimpl(hookwrapper=True)
def pytest_runtest_makereport(item, call):
    ...
```

The deliberately-failing test correctly failed with a clean real assertion diff — but checking the real
screenshot directory afterward:

```
total 0
```

Zero files. The real hook never fired at all, despite looking syntactically correct.

## The real, investigated fix

```python
# conftest.py -- NOT the test module
def pytest_runtest_makereport(item, call):
    if call.when == "call" and call.excinfo is not None:
        driver_fixture = item.funcargs.get("driver")
        path = os.path.join(SCREENSHOT_DIR, f"{item.name}.png")
        driver_fixture.save_screenshot(path)
```
```
test_deliberate_failure_for_screenshot_demo.png  6411 bytes
```

Moving the *identical* hook function into a real `conftest.py` file — nothing else changed — made it
genuinely fire, producing a real, correctly-sized screenshot file.

## How It Actually Works

`pytest` hooks like `pytest_runtest_makereport` are discovered through a real, specific plugin-registration
mechanism that scans `conftest.py` files and installed plugins — it does **not** scan ordinary test modules
for hook functions, even ones with the exact correct name and signature. This lesson's first attempt defined
a perfectly valid-looking hook that pytest simply never knew existed, because test modules aren't part of
pytest's real hook-discovery path. This is exactly the kind of architectural detail that's easy to get wrong
silently — no error, no warning, the hook just never runs — which is why this lesson caught it by checking
the real, actual file system output rather than trusting that code which "looks right" is doing what it
appears to do.

This capstone's real, complete framework now demonstrates every piece working together: real page objects
for structure, real external data for coverage, a real shared fixture for efficiency, and a real, correctly-
placed failure hook for diagnosability — the same four concerns [Lessons 2](02-building-a-page-object.md),
[4](04-test-data-management.md), [5](05-pytest-fixtures-for-selenium.md), and
[8](08-screenshots-visual-debugging.md) each built in isolation, now composed into one real, working
framework.

## Common mistakes

- Assuming a correctly-written pytest hook will work regardless of which file it's defined in — this
  lesson's own real, silent failure is direct proof that placement matters architecturally, not just
  syntactically.
- Trusting a test run's pass/fail output alone as proof every piece of surrounding tooling (hooks, fixtures,
  reporting) actually worked — this lesson's real screenshot-directory check is what caught a problem the
  test results themselves gave no indication of.
- Building a "complete" framework without ever deliberately forcing a failure to confirm the failure-handling
  path actually works — this lesson's one deliberately-wrong assertion is what exposed the real hook-placement
  bug before it could hide silently in a framework nobody ever tested the failure path of.

## Exercise

Add a second real pytest hook to this capstone's `conftest.py` that logs every test's real pass/fail result
and total duration to a JSON file after the full session completes (`pytest_sessionfinish`). Run the full
real suite and confirm the resulting file accurately reflects this lesson's real `8 passed, 1 failed` outcome.
