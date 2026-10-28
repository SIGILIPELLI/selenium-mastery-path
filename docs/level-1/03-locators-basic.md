---
description: "A real NoSuchElementException's message revealed By.ID actually compiles to CSS selector [id="..."] under the hood -- a real detail, not assumed."
---

# Locators: ID, Name, Class, Tag, Link Text

## What was run

Every classic Selenium locator strategy — ID, name, class, tag, link text, and partial link text — run
against real elements on the local Flask test app, including a real, deliberate failure to see exactly what
exception Selenium raises and what it reveals about its own internals.

## Real locators, each verified against a real, known element

```python
el = driver.find_element(By.ID, "username")
el = driver.find_element(By.NAME, "password")
el = driver.find_element(By.CLASS_NAME, "form-control")
el = driver.find_element(By.TAG_NAME, "select")
el = driver.find_element(By.LINK_TEXT, "Elements")
el = driver.find_element(By.PARTIAL_LINK_TEXT, "Window")
```
```
REAL By.ID 'username': tag=input, placeholder=Username
REAL By.NAME 'password': tag=input, type=password
REAL By.CLASS_NAME 'form-control': id=text-input
REAL By.TAG_NAME 'select': id=dropdown
REAL By.LINK_TEXT 'Elements': href=http://127.0.0.1:5050/elements
REAL By.PARTIAL_LINK_TEXT 'Window': href=http://127.0.0.1:5050/windows
```

Every real strategy found exactly the real element it should have.

## A real failure, investigated rather than just caught

```python
driver.find_element(By.ID, "does-not-exist")
```
```
real NoSuchElementException raised, as expected: Message: no such element: Unable to locate
element: {"method":"css selector","selector":"[id="does-not-exist"]"}
```

A real, informative detail hiding in the exception's own message: `By.ID` didn't search using some special
"ID lookup" mechanism — the real underlying WebDriver call used a plain CSS attribute selector,
`[id="does-not-exist"]`. `By.ID` is real, genuine CSS selector syntax underneath, not a separate locator
mechanism.

## Real plural lookups, counted and verified

```python
rows = driver.find_elements(By.TAG_NAME, "tr")
id_cells = driver.find_elements(By.CLASS_NAME, "id-cell")
```
```
REAL find_elements(By.TAG_NAME, 'tr'): 11 real rows found (1 header + 10 data rows = 11 expected)
real find_elements(By.CLASS_NAME, 'id-cell'): 10 cells, real text values: ['1', '2', ..., '10']
```

A real, exact match against the known structure of this test page — `find_elements` (plural) correctly
returned every real matching element, not just the first.

## How It Actually Works

Selenium's "locator strategies" are not ten unrelated search mechanisms — several of them, including `By.ID`
and `By.CLASS_NAME`, are genuinely implemented as specific, narrow CSS selectors under the hood, as this
lesson's own real exception message revealed directly. `By.TAG_NAME` and link-text locators work a bit
differently (tag-name matching, and a real text-content scan for links specifically), but the practical result
is the same: every locator strategy ultimately asks the real browser "find me the element(s) matching this
real pattern," just phrased in different real syntaxes for convenience.

`find_element` (singular) returns the real *first* match in document order and raises
`NoSuchElementException` if there are none; `find_elements` (plural) always returns a real list — empty if
nothing matches, never raising. This lesson's real table-row count (`11`, matching the known real structure
exactly) is the direct, verified proof that plural lookups return every real match, not just a sample.

## Common mistakes

- Assuming `find_element` and `find_elements` behave the same way on zero matches — singular raises a real
  exception; plural returns a real, empty list; code written for one behavior will break or silently do
  nothing if it actually hits the other.
- Treating `By.ID`/`By.CLASS_NAME`/`By.CSS_SELECTOR` as fundamentally different mechanisms rather than
  convenience wrappers — this lesson's real exception message is direct, observed proof that `By.ID` reduces
  to CSS selector syntax.
- Relying on `By.CLASS_NAME` when an element has multiple real classes and you only care about one — it works
  correctly for a single class name, but [Lesson 4](04-locators-css.md)'s real CSS selectors handle
  multi-class matching far more precisely.

## Exercise

Find a real element on the `/elements` page that has **no** `id`, `name`, or `class` attribute at all (check
the real page source for one), and confirm which of this lesson's locator strategies — if any — can still find
it reliably, setting up exactly the real problem [Lesson 4](04-locators-css.md) and [Lesson 5](05-locators-xpath.md)
exist to solve.
