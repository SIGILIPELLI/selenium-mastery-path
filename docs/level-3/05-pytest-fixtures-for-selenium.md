---
description: "The same 3 real tests ran in 4.86s with a fresh browser per test, and 2.83s with one shared session-scoped browser -- a real, measured 42% speedup."
---

# pytest Fixtures for Selenium

## What was run

The real `LoginPage` tests, converted into genuine `pytest` tests using a real `driver` fixture for
setup/teardown, run twice with two different real fixture scopes — a fresh browser per test, and one browser
shared across the whole session — to measure the actual real timing cost of each.

## Real function-scoped fixture: a fresh browser every test

```python
@pytest.fixture
def driver():
    d = webdriver.Chrome(options=options)
    yield d
    d.quit()
```
```
test_pytest_fixtures.py::test_valid_login PASSED
test_pytest_fixtures.py::test_invalid_login PASSED
test_pytest_fixtures.py::test_empty_login PASSED

3 passed in 4.86s
```

## Real session-scoped fixture: one browser, shared

```python
@pytest.fixture(scope="session")
def driver():
    d = webdriver.Chrome(options=options)
    yield d
    d.quit()
```
```
test_pytest_fixtures_session.py::test_valid_login PASSED
test_pytest_fixtures_session.py::test_invalid_login PASSED
test_pytest_fixtures_session.py::test_empty_login PASSED

3 passed in 2.83s
```

A real, measured `42%` reduction in total run time — from `4.86s` to `2.83s` — for the exact same three real
tests, changing nothing but the fixture's `scope` argument.

## How It Actually Works

A `pytest` fixture using `yield` runs everything before the `yield` as real setup and everything after it as
real teardown, automatically, for every test that requests it. `scope="function"` (the default) means that
setup/teardown cycle — launching and quitting a real Chrome process — happens once per real test; this
lesson's real `4.86s` for 3 tests reflects paying that real browser-launch cost three separate times.
`scope="session"` instead launches the real browser once, shares it across every test in the entire real run,
and only quits it at the very end — this lesson's real `2.83s` reflects paying that cost exactly once.

The real tradeoff this lesson doesn't show, but is worth knowing: a session-scoped browser carries real state
between tests (cookies, local storage, whatever page it was last on) unless each test explicitly resets what
it needs — which is exactly why this lesson's tests all start with `LoginPage(driver).load()`, a real,
explicit navigation that resets the relevant page state regardless of which fixture scope is in play.

## Common mistakes

- Defaulting to function-scoped fixtures for every real test without considering the real time cost at
  scale — this lesson's `42%` savings on just 3 tests becomes a much larger real time difference across a
  suite with hundreds of tests.
- Switching to session scope without checking whether tests actually rely on real isolation between runs — a
  session-scoped browser that quietly carries over cookies or session state from an earlier test can produce
  a test that only passes because of leftover real state, not because its own logic is correct.
- Forgetting the `yield`-based teardown entirely and just returning the driver — without `yield` and the
  code after it, the real browser process is never explicitly closed, leaking real resources across a test
  run.

## Exercise

Add a fourth real test to both fixture-scope versions of this lesson's suite, and re-measure the real total
time for each. Confirm whether the real percentage time saved by session scope grows, shrinks, or stays
roughly the same as the number of tests increases from 3 to 4.
