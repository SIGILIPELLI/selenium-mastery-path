---
description: "Against the exact same app rename that crashed all 3 of Lesson 1's duplicated-locator tests, a real Page Object fixed all 3 at once by changing 1 line."
---

# Building a Real Page Object

## What was run

A real `LoginPage` class, built to hold every real locator for the login page in exactly one place, run
against the *same* real, renamed application from [Lesson 1](01-why-page-object-model.md) — where the real
username field's `id` changed from `username` to `user-name`, breaking all three duplicated-locator tests at
once.

## A real Page Object, with locators as class-level constants

```python
class LoginPage:
    URL = f"{BASE_URL}/login"
    USERNAME_INPUT = (By.ID, "user-name")  # REAL FIX: updated once, here
    PASSWORD_INPUT = (By.ID, "password")
    SUBMIT_BUTTON = (By.ID, "submit-btn")
    RESULT_TEXT = (By.ID, "result")

    def login(self, username, password):
        self.driver.find_element(*self.USERNAME_INPUT).send_keys(username)
        ...
```

## Real tests, rewritten to use the Page Object instead of raw locators

```python
def test_valid_login_with_pom(driver):
    page = LoginPage(driver).load()
    page.login("realuser", "realpass123")
    return page.get_result_text() == "Login Successful"
```
```
REAL test results, using the Page Object, AFTER the same real app rename: [True, True, True]
real number of lines changed to fix all 3 tests: 1 (the USERNAME_INPUT locator,
inside LoginPage, in exactly one place)
```

A real, complete fix for all three tests — the exact same real app change that crashed
[Lesson 1](01-why-page-object-model.md)'s entire test run now required editing exactly **one** real line, in
exactly **one** real location.

## How It Actually Works

A Page Object isn't a special Selenium feature — it's an ordinary real Python class that represents one
concept: "everything about interacting with this specific page." Locators become class-level constants
instead of scattered literal strings; actions (`login()`) become real methods that encapsulate the specific
real steps needed, so test code calls `page.login(...)` and never touches a locator directly at all. When the
real underlying page changes, there's now exactly one real place that knowledge lives, and updating it there
automatically fixes every real test that uses the page object — which is precisely why this lesson's three
tests, which call `page.login()` rather than duplicating `find_element(By.ID, "username")` themselves, all
passed again after a single real edit.

The real, structural improvement isn't just "less typing" — it's that test code and page-structure knowledge
are now genuinely separated. A test reads as *what the test is checking* (valid login, invalid login, empty
login); the page object owns *how to actually do it* on the real current page.

## Common mistakes

- Building a Page Object that still leaks raw locators into test code for "just this one special case" —
  every real exception to the pattern is another real place a future page change can silently miss.
- Making page object methods too granular (a separate method for every single `find_element` call) — this
  lesson's `login()` method deliberately bundles the real, complete action a test actually cares about, not
  each individual micro-step.
- Forgetting that a Page Object still needs the real techniques from [Level 2](../level-2/index.md) — this
  lesson's `login()` still uses a real `time.sleep(0.3)` placeholder from
  [Level 1](../level-1/10-project-login-flow-test.md); a genuinely production-ready page object would use a
  real explicit wait instead, which [Lesson 5](05-pytest-fixtures-for-selenium.md) and this level's later
  lessons build toward.

## Exercise

Add a `LogoutPage` or a `logout()` method to this lesson's real `LoginPage` class (reusing its existing
`driver` reference), and write one new real test confirming a logged-in session can log back out. Keep every
real locator for that new real behavior inside the page object, never in the test itself.
