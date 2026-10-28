---
description: "XPath's parent:: axis found Item5's real containing row by climbing UP from matched text -- something CSS selectors have no standard syntax to do at all."
---

# Locators: XPath

## What was run

Real XPath expressions exercising exactly the capabilities [Lesson 4](04-locators-css.md)'s CSS selectors
structurally cannot provide: matching on visible text content, and traversing *upward* or *sideways* through
the real DOM tree from a text match — each verified against real elements, including one real, honest
off-by-one mistake caught and fixed mid-investigation.

## Real text-based matching — no CSS equivalent exists

```python
el = driver.find_element(By.XPATH, "//a[text()='Login']")
el = driver.find_element(By.XPATH, "//a[contains(text(), 'Window')]")
```
```
REAL XPath exact text match: href=http://127.0.0.1:5050/login
real XPath contains(text()) match: href=http://127.0.0.1:5050/windows
```

Standard CSS has no selector for "an element whose visible text equals/contains X" — this is real,
genuine XPath-only territory.

## Real upward traversal — structurally impossible in standard CSS

```python
el = driver.find_element(By.XPATH, "//td[text()='Item5']/parent::tr")
```
```
REAL XPath parent:: axis: found the real <tr> containing 'Item5', full row text='5 Item5 50'
```

A real, direct demonstration of something CSS selectors cannot do at all: starting from a matched *child*
(`<td>Item5</td>`) and climbing *up* to its real parent `<tr>`. CSS selectors only ever describe a target
relative to its ancestors or preceding siblings — never the reverse.

## A real, honest mistake, caught and fixed

```python
el = driver.find_element(By.XPATH, "//td[text()='Item7']/following-sibling::td[2]")
```
```
selenium.common.exceptions.NoSuchElementException: ... selector":"//td[text()='Item7']/following-sibling::td[2]"
```

This real query failed — investigated directly: `Item7` lives in the *second* column (`name-cell`), so it has
only **one** real following sibling (`price-cell`), not two. `following-sibling::td[2]` was asking for a
second sibling that doesn't exist. The real, corrected fix:

```python
el = driver.find_element(By.XPATH, "//td[text()='Item7']/following-sibling::td[1]")
```
```
real XPath following-sibling:: (1st sibling after Item7's name cell): price='70'
```

## A real, direct equivalence check where both languages CAN express the same query

```python
css_el = driver.find_element(By.CSS_SELECTOR, "tbody tr:nth-child(5) td.price-cell")
xpath_el = driver.find_element(By.XPATH, "//tbody/tr[5]/td[3]")
```
```
real CSS: 50
real XPath: 50
real values match: True
```

## How It Actually Works

XPath models the real HTML document as a navigable tree with explicit *axes* — `parent::`, `ancestor::`,
`following-sibling::`, `preceding-sibling::`, and more — that can move in any real direction from a starting
point, including backward and upward. CSS selectors only describe a target element's relationship to things
that come *before* it in the tree (ancestors, preceding siblings) — there's no standard CSS way to select "the
parent of the thing I just matched," which is exactly the real gap this lesson's `parent::` example fills.

This lesson's own real mistake (`following-sibling::td[2]` instead of `td[1]`) is worth sitting with: XPath's
sibling-counting axes count only in the specified direction, starting fresh from `1` — they don't use the
element's absolute position in its parent, which is a real, common source of off-by-one errors until you've
made (and fixed) one yourself, as this lesson's script just demonstrated directly.

## Common mistakes

- Reaching for XPath's `parent::`/`ancestor::`/`following-sibling::` axes when a simple CSS selector would
  already do the job — this lesson's final equivalence check shows CSS and XPath landing on the identical
  real result when the query doesn't actually need XPath's extra directional power.
- Miscounting sibling position with `following-sibling::` or `preceding-sibling::` — this lesson's own real,
  caught mistake is a direct, concrete example of exactly this error, and how to actually debug it (check what
  real siblings exist relative to the match, not just guess at an index).
- Writing XPath expressions that start from the document root (`/html/body/...`) instead of relative text or
  attribute matches — those are fragile against any structural change anywhere above the real target; this
  lesson's `//td[text()='Item7']` pattern is far more robust because it anchors to real, meaningful content.

## Exercise

Using only the `preceding-sibling::` axis (the mirror image of this lesson's `following-sibling::`), write a
real XPath expression that starts from the `<td class="price-cell">` containing `'30'` and finds the real
`<td class="name-cell">` in the same row. Verify it returns `'Item3'`, and confirm you get the real index
right on the first try this time.
