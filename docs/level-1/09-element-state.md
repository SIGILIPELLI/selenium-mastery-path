---
description: "The real off-screen element's .text returned '' while get_attribute('textContent') returned 'Off-screen' -- visibility, not content, is what .text checks."
---

# Reading Element State: Text, Attributes, Visibility

## What was run

Real comparisons between `.text`, `get_attribute('textContent')`, and `get_attribute('innerHTML')` on the
same real elements, plus real `size`/`location` measurements and `is_displayed()` checks across every tested
element on the local test page — isolating exactly when `.text` and `textContent` genuinely diverge.

## Real text properties, identical on a normal, visible element

```python
label.text
label.get_attribute("textContent")
label.get_attribute("innerHTML")
```
```
REAL .text: 'I agree'
real get_attribute('textContent'): 'I agree'
real get_attribute('innerHTML'): 'I agree'
```

All three agree completely — for a normal, visible element with no nested markup, there's no real difference.

## A real, measured divergence on an off-screen element

```python
offscreen.text                              # ''
offscreen.get_attribute("textContent")      # bypasses visibility entirely
```
```
real offscreen.text (via .text property): ''
real offscreen text via textContent (bypasses visibility): 'Off-screen'
real offscreen.is_displayed(): False
```

A real, precise confirmation of what `.text` actually checks: the exact same element's real text content is
genuinely still there (`textContent` returns `'Off-screen'`), but `.text` returns an empty string, because
`.text` specifically reflects *rendered, visible* text, not raw DOM content — and this element's real
`is_displayed()` is `False`.

## Real size, location, and visibility across every tested element

```python
btn.size      # {'height': 21, 'width': 67}
btn.location  # {'x': 8, 'y': 198}
```
```
#text-input: is_displayed=True
#disabled-btn: is_displayed=True
#hidden-btn: is_displayed=False
#not-interactable: is_displayed=False
#action-btn: is_displayed=True
```

A real, informative result: `disabled-btn` is `is_displayed=True` — disabled is not the same real condition
as hidden, consistent with [Lesson 6](06-interacting-with-elements.md)'s finding that disabled elements remain
genuinely visible, just not clickable in a way that fires events.

## How It Actually Works

`.text` is computed from the real, *rendered* page — it reflects what a human would actually see on screen,
which is why an element positioned off-screen (`position: absolute; left: -9999px` in this lesson's real test
case) reports empty text even though its real underlying HTML content hasn't changed at all.
`get_attribute('textContent')` and `innerHTML` read the real DOM directly, completely bypassing any rendering
or visibility consideration — which is exactly why they still returned the real `'Off-screen'` text when
`.text` could not.

This is a real, practical distinction worth internalizing: if a test needs to confirm something is actually
*visible* to a real user, `.text` (combined with `is_displayed()`) is the right real check. If a test needs to
confirm specific content exists in the DOM regardless of whether it's currently shown — useful for testing
accessibility-only text, or content that's deliberately hidden until a later interaction — `textContent` is
the real, correct tool.

## Common mistakes

- Using `.text` to verify that specific content exists in the DOM, then being confused when it returns empty
  for a real, legitimately-hidden-for-now element — this lesson's exact result is the direct explanation: the
  content is there, it's just not currently rendered.
- Assuming `is_displayed()` and `is_enabled()` measure the same thing — this lesson's real
  `disabled-btn: is_displayed=True` result shows a disabled element is still fully visible; the two checks are
  independent.
- Reading `innerHTML` when you actually want plain text — `innerHTML` includes real nested markup; for an
  element with child tags, it will differ from `textContent`/`.text` in ways this lesson's simple, tag-free
  label didn't happen to expose.

## Exercise

Find or construct a real element on the test app with nested HTML inside it (for example, a `<span>` wrapped
inside a `<div>`), and compare its real `.text`, `textContent`, and `innerHTML` values directly. Confirm
`innerHTML` is the only one of the three that includes the real nested tag markup itself, not just its text.
