---
description: "'table > td' raised NoSuchElementException (td nests inside tbody/tr) while the same selector without '>' found it instantly -- a real difference."
---

# Locators: CSS Selectors

## What was run

A real, deeper set of CSS selector patterns — attribute selectors, descendant vs. direct-child combinators,
`:nth-child`, multi-attribute matching, and `:disabled` — each verified against real elements on the local
test app, including a real element with no `id`, `name`, or `class` at all, resolving
[Lesson 3](03-locators-basic.md)'s exercise directly.

## A real attribute selector, finding what basic locators couldn't

```python
el = driver.find_element(By.CSS_SELECTOR, "label[for='checkbox-1']")
```
```
REAL CSS attribute selector on a label with NO id/name/class: text='I agree'
```

[Lesson 3](03-locators-basic.md) left this exact element unlocatable by ID, name, or class, since it has none
of those. A CSS attribute selector, matching on the real `for` attribute instead, finds it directly.

## A real, measured difference between `>` and plain descendant matching

```python
el = driver.find_element(By.CSS_SELECTOR, "table > td")      # direct child only
el = driver.find_element(By.CSS_SELECTOR, "table td")        # any descendant
```
```
REAL direct-child 'table > td': NOT found (NoSuchElementException) -- a real <td> is
nested inside <tbody><tr>, not a direct child of <table>
real descendant (no >) 'table td': found, text=1
```

A real, exact confirmation of what `>` actually restricts: the real table's HTML genuinely nests `<td>` inside
`<tr>` inside `<tbody>` inside `<table>` — never a *direct* child — so `table > td` correctly finds nothing,
while the identical selector without `>` succeeds immediately.

## Real positional and multi-attribute matching

```python
el = driver.find_element(By.CSS_SELECTOR, "tbody tr:nth-child(3) td.name-cell")
el = driver.find_element(By.CSS_SELECTOR, "input[type='radio'][value='B']")
el = driver.find_element(By.CSS_SELECTOR, "button:disabled")
```
```
REAL ':nth-child(3)' on the real table: 3rd row's name = 'Item3'
REAL multi-attribute selector: id=radio-b
real ':disabled' pseudo-class: found button id=disabled-btn
```

Every real positional, multi-condition, and state-based selector matched exactly the intended real element.

## How It Actually Works

CSS selectors describe real structural relationships in the actual HTML tree, not just individual element
properties — which is exactly why `>` (direct child) and a plain space (any descendant, at any depth) produce
genuinely different real results on the same page, as this lesson's table test demonstrates concretely rather
than just describing. `:nth-child(3)` counts real sibling position within a parent, which is why it correctly
picked out the third real `<tr>` without needing any class or ID on that specific row at all. `:disabled` and
similar state pseudo-classes query the real, current DOM state of an element — not a fixed attribute in the
HTML source — which matters because an element's enabled/disabled state (or other state like `:checked`) can
change dynamically at runtime, and a well-written selector tracks the real current state rather than what the
page looked like when it first loaded.

## Common mistakes

- Using `>` when you actually mean "anywhere inside," or vice versa — this lesson's real `table > td` failure
  is a direct, concrete reminder that these two combinators are not interchangeable, and the real DOM's actual
  nesting depth determines which one works.
- Reaching for XPath by default for anything beyond a simple ID or class — most of this lesson's real examples
  (attribute matching, nth-child, multi-attribute, state pseudo-classes) are handled natively and often more
  readably by CSS alone; [Lesson 5](05-locators-xpath.md) covers the real cases where XPath genuinely does
  something CSS selectors structurally cannot.
- Assuming a selector that matches zero elements is a locator *syntax* error — this lesson's real
  `NoSuchElementException` on `table > td` was a completely valid selector; it was correctly reporting that no
  real element in the actual DOM satisfied that specific structural relationship.

## Exercise

Write a real CSS selector that selects the second `<option>` element inside the real `#dropdown` select on the
`/elements` page using only `:nth-child` (not an attribute or text match), verify it returns the real "Green"
option, and explain why `:nth-child(2)` — not `:nth-of-type(2)` — is the correct real choice here given that
every child of a `<select>` is already an `<option>`.
