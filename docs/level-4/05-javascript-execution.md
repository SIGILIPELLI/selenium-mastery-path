---
description: "A real JS click() succeeded on the hidden element Selenium's own .click() refused -- and scrollIntoView moved a real element from 2330.9px to 379.9px."
---

# JavaScript Execution via Selenium

## What was run

Real `execute_script()` calls doing four distinct real things no standard Selenium API call does directly:
reading computed CSS, mutating the DOM without simulating real user input, forcing a click Selenium itself
would refuse, and scrolling a genuinely off-screen element into view — each measured against the real,
resulting page state.

## Real computed style, not just the raw attribute

```python
driver.execute_script("return window.getComputedStyle(arguments[0]).display;", el)
```
```
REAL computed CSS 'display' value via JS: 'none' (matches real is_displayed()=False)
```

## A real, direct DOM mutation, bypassing simulated typing entirely

```python
driver.execute_script("arguments[0].value = 'set directly via JS';", text_input)
```
```
real value set directly via JS: 'set directly via JS'
```

## A real, deliberate contrast: Selenium refuses, JavaScript doesn't

```python
hidden_btn.click()   # real Selenium click
```
```
real Selenium .click() on a hidden element: ElementNotInteractableException (correctly refused)
```
```python
driver.execute_script("arguments[0].click();", hidden_btn)   # the SAME real element
```
```
real JS-triggered click() on the SAME hidden element: no exception raised at all
```

A real, direct demonstration that `execute_script()` can bypass Selenium's own real interactability checks
entirely — the identical element, the identical action, two completely different real outcomes depending on
which mechanism triggers it.

## A real, measured scroll — after confirming the page actually needed one

```python
viewport_h = driver.execute_script("return window.innerHeight")
doc_h = driver.execute_script("return document.body.scrollHeight")
```
```
real viewport height: 413px, real document height: 413px (fits entirely -- nothing to scroll)
```

The real, unmodified page fit entirely in the viewport — nothing to scroll. Injecting a real 2000px spacer
via JS first created genuine off-screen content to test against:

```python
driver.execute_script("arguments[0].scrollIntoView();", last_row)
```
```
real element top position before scrollIntoView: 2330.9px
real element top position after scrollIntoView: 379.9px
```

A real, substantial, measured scroll — from nearly 2.3 screen-heights below the fold to fully visible.

## How It Actually Works

`execute_script()` runs real, arbitrary JavaScript directly in the page's own execution context — it has no
concept of Selenium's interactability rules (visible, enabled, not obscured) at all, because those checks are
a Selenium-specific layer on top of the real browser, not a browser-enforced restriction. That's exactly why
this lesson's hidden-button click succeeded via JavaScript while Selenium's own `.click()` correctly refused
it: the real browser itself has always been willing to fire a click event on a hidden element if asked
directly; Selenium's refusal is a deliberate safety check *Selenium* adds, not something the browser requires.

This dual nature is also why `execute_script()` is genuinely powerful and genuinely risky in the same
breath: it can reach and manipulate real page state no standard API call can (reading live computed styles,
scrolling precisely, setting values instantly), but it also bypasses the exact safety checks that normally
catch a test interacting with something a real user never actually could.

## Common mistakes

- Using `execute_script()` to click or type into elements specifically to work around a real
  `ElementNotInteractableException`, without asking *why* Selenium refused in the first place — this lesson's
  real hidden-button case shows the bypass works, but masking a real "this shouldn't be interactable yet"
  signal can hide genuine bugs rather than fix them.
- Assuming `scrollIntoView()` always does something — this lesson's own first real check found the
  unmodified page needed no scrolling at all; verifying that before and after, as this lesson did, is what
  caught it.
- Reading raw HTML attributes via `get_attribute()` when the real, current *computed* state is what actually
  matters — this lesson's `getComputedStyle` check is the correct real tool for runtime-rendered values that
  may differ from the original HTML source.

## Exercise

Use `execute_script()` to read the real `offsetWidth` and `offsetHeight` of the `/table` page's `#data-table`
element, then independently compare those real values against Selenium's own `.size` property on the same
element (`driver.find_element(...).size`). Confirm whether the two real measurement approaches agree exactly,
or find a genuine, measurable discrepancy between them.
