---
description: "Clicking a real disabled button raised no exception at all -- yet its real onclick handler never fired, confirmed by checking actual page state."
---

# Interacting With Elements: Click, Type, Clear

## What was run

Real `send_keys()`, `clear()`, and `click()` calls against real elements, each verified by checking the
actual resulting state rather than just assuming success — including a real, genuinely surprising finding
about what clicking a disabled element actually does.

## Real typing, clearing, and re-typing

```python
box.send_keys("hello selenium")
box.clear()
box.send_keys("second real value")
```
```
REAL value after send_keys: 'hello selenium'
real value after clear(): ''
real value after a second send_keys: 'second real value'
```

## A real click, verified by a real resulting DOM change

```python
driver.find_element(By.ID, "action-btn").click()
status = driver.find_element(By.ID, "status-text")  # re-fetch after the click
```
```
real status text BEFORE click: 'waiting'
real status text AFTER click: 'clicked'
```

## A real, surprising finding: a disabled button's click raises nothing, but does nothing either

```python
driver.find_element(By.ID, "disabled-btn").click()  # real: this does NOT raise
```
```
REAL disabled-btn.click(): no exception raised
real status-text before: 'clicked', after: 'clicked'
real onclick handler actually fired: False
```

A real, important distinction: calling `.click()` on this genuinely disabled button raised **no exception at
all** — contradicting the common assumption that Selenium always blocks interaction with disabled elements.
But checking the real, actual effect (the button's own `onclick` handler, which would change the page's
status text if it fired) confirms it genuinely never ran. The click call "succeeded" in the sense that
Selenium didn't complain — and did nothing, because the real browser itself suppresses native click-event
dispatch on disabled form elements, independent of whatever Selenium's own click command reports.

## A real contrast: a hidden element DOES raise an exception

```python
hidden_btn.click()
```
```
real hidden-btn.is_displayed(): False
REAL ElementNotInteractableException on hidden-btn: Message: element not interactable
```

A genuinely different real outcome for a genuinely different real condition: Selenium's interactability check
does catch an element positioned off-screen (`display: none`), raising a real, clear exception — just not for
a disabled-but-visible one.

## How It Actually Works

Selenium's `click()` command issues a real, low-level pointer-click action at the element's real screen
coordinates. Whether that real click actually *does* anything downstream is up to the browser's own native
event handling — and browsers, by design, suppress native `click` event dispatch on disabled form controls,
entirely independent of whether Selenium's command itself reported success. `ElementNotInteractableException`
is Selenium's own check for whether an element can even receive a real pointer event in the first place
(visible, in the viewport, not covered) — "disabled" isn't one of the conditions that check covers, which is
exactly why this lesson's two real tests (disabled vs. hidden) produced two genuinely different outcomes.

## Common mistakes

- Assuming `.click()` raising no exception means the click actually accomplished something — this lesson's
  real disabled-button test is direct proof that "no error" and "something happened" are not the same claim.
- Relying on `ElementNotInteractableException` as a universal guard against every kind of non-interactable
  element — this lesson's real results show it fires reliably for hidden elements but not for disabled ones.
- Checking only `is_enabled()` to decide whether an action makes sense, without verifying the real,
  observable *effect* of the interaction — this lesson's status-text check is what actually confirmed the
  disabled button's handler never ran, not just that the element reported itself as disabled.

## Exercise

Write a real test that checks `is_enabled()` *before* calling `.click()` on any button on the `/elements`
page, and only proceeds with the click if it returns `True`. Run it against both `action-btn` and
`disabled-btn` and confirm it correctly skips the real, genuinely ineffective click this lesson's own test
performed on the disabled button.
