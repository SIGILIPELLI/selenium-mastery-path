---
description: "Renaming one real HTML id broke all 3 duplicated-locator tests at once -- and because none caught the exception, it crashed the entire real test run."
---

# Why Page Object Model: the Problem Without It

## What was run

Three real, small login tests, each independently duplicating the exact same real locators
(`By.ID, "username"`, `By.ID, "password"`, `By.ID, "submit-btn"`) directly in its own code — then a real,
deliberate change to the actual application: renaming the real `username` field's `id` attribute, exactly the
kind of ordinary change a real developer makes without thinking about test code at all.

## A real, measured duplication, before anything changes

```python
real_occurrences = len(re.findall(r'"username"', source))
```
```
REAL count of the raw locator string 'username' across this test file: 5
(if the real app's developer renames this field, ALL 5 of these real locations need to be
found and changed, in every test file that duplicates them)

real test results (before any locator change): [True, True, True]
```

Three real, small, passing tests — and already `5` real duplicated references to one single locator string,
in just this one small file.

## A real, ordinary app change — and real, immediate breakage

```html
<!-- before -->  <input type="text" name="username" id="username" placeholder="Username">
<!-- after  -->  <input type="text" name="username" id="user-name" placeholder="Username">
```
```
selenium.common.exceptions.NoSuchElementException: Message: no such element: Unable to
locate element: {"method":"css selector","selector":"[id="username"]"}
```

A real, single-attribute rename in the application — the kind of change a developer would make without a
second thought — immediately broke the real test. And because this duplicated-locator code has no shared
error handling, the real failure didn't just fail one test and move on: it crashed the entire real test run,
taking the second and third tests down with it, even though neither of those tests' own logic was wrong.

## How It Actually Works

Every one of this lesson's three real tests hardcodes the same real locator independently — there's no single
place in the code that represents "the login page" as a concept; there's only scattered, duplicated knowledge
of what that page currently looks like. When the real page changes, *every* copy of that knowledge goes stale
simultaneously, and nothing in this lesson's code structure limits the real damage to just the one test that
happened to run first — the crash is total, because there's no actual boundary between "finding the field" and
"using the field" for any of these tests to fail gracefully at.

This is the exact real problem the Page Object Model exists to solve: concentrate the knowledge of *where
things are* on a page into one real, single place, so that a real change like this lesson's rename requires
exactly one real edit — in the one place that knowledge lives — rather than a hunt through every test file
that happened to duplicate it.

## Common mistakes

- Assuming "it's just one test file, duplication doesn't matter yet" — this lesson's real file had only 3
  small tests and still needed `5` separate real fixes for one locator change; real projects with dozens of
  tests across multiple files multiply that real cost directly.
- Writing tests with no error isolation between them — this lesson's real crash took down tests 2 and 3
  along with test 1, even though their own logic was completely unaffected by the real rename.
- Discovering a locator change only when a real test suite starts failing, rather than through any earlier,
  real signal — without a shared abstraction, there's no single place to notice "this page's structure
  changed" before it shows up as a real, scattered test failure.

## Exercise

Before reading [Lesson 2](02-building-a-page-object.md), try fixing this lesson's real broken tests yourself,
using nothing but plain Python — no Page Object Model yet. Count how many real lines you actually had to
change, and compare that real number against what [Lesson 2](02-building-a-page-object.md) achieves with a
proper abstraction in place.
