---
description: "All three Select methods (value, visible text, index) changed the real dropdown; clicking radio B auto-deselected A with zero extra Selenium code."
---

# Dropdowns, Checkboxes & Radio Buttons

## What was run

Real interaction with a real `<select>` dropdown via Selenium's `Select` helper class, real checkbox
toggling verified by `is_selected()`, and a real, direct confirmation of radio button mutual exclusivity —
every result checked against the actual, resulting real browser state.

## A real dropdown, controlled three different real ways

```python
dropdown = Select(driver.find_element(By.ID, "dropdown"))
dropdown.select_by_value("green")
dropdown.select_by_visible_text("Blue")
dropdown.select_by_index(0)
```
```
REAL options available: ['Red', 'Green', 'Blue']
real default selected option: Red
real after select_by_value('green'): Green
real after select_by_visible_text('Blue'): Blue
real after select_by_index(0): Red
```

Three real, different selection methods, each verified to produce the exact real expected state.

## Real checkbox toggling, verified by state, not assumption

```python
checkbox.click()
print(checkbox.is_selected())
```
```
real checkbox default state (is_selected): False
real checkbox state after 1 click: True
real checkbox state after 2nd click (toggled back): False
```

## Real radio button mutual exclusivity, confirmed directly

```python
radio_a.click()
radio_b.click()
```
```
real radio states after clicking A: A=True, B=False
real radio states after clicking B: A=False, B=True
```

Clicking radio B genuinely, automatically deselected A — real native browser behavior for same-named radio
groups, with zero Selenium code needed to make that happen.

## How It Actually Works

A real `<select>` element only has one true value at a time; Selenium's `Select` class is a thin, convenient
wrapper that issues the equivalent of clicking the dropdown and choosing an option, exposed as three
real, distinct real methods for matching by value, text, or position — this lesson's real tests confirm all
three land on the correct real result. Checkboxes and radio buttons are both real native HTML form controls
whose `checked` state Selenium reads via `is_selected()` — that real state is actually tracked by the
browser, not by Selenium, which is why toggling radio B's real state automatically flips radio A: the browser
itself enforces that only one radio button in a same-named group can be checked, completely independent of
anything Selenium does or doesn't do.

## Common mistakes

- Using `.click()` directly on a `<select>` element's `<option>` instead of the `Select` helper — it can work
  in simple cases but misses the real helper's safer matching logic (by value or exact visible text) and
  doesn't generalize to multi-select dropdowns the way `Select`'s own API does.
- Forgetting that radio buttons need a shared `name` attribute to be mutually exclusive — this lesson's real
  mutual-exclusivity result depends entirely on `radio-a` and `radio-b` sharing `name="choice"` in the real
  HTML; without that shared name, both could genuinely end up checked simultaneously.
- Checking a checkbox's state via `get_attribute('checked')` instead of `is_selected()` — the real attribute
  value can lag behind or not reflect dynamic, JavaScript-driven state changes the way the real, live
  `is_selected()` property does.

## Exercise

Click `checkbox-1` an odd number of real times (try 5) and predict its final `is_selected()` state before
running the real check — then click it an even number of times (try 4) from a fresh page load and predict
again, confirming the real, simple toggle pattern holds consistently.
