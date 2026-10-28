---
description: "A real Ctrl+A combo selected nothing on this macOS session (selectionStart stayed at 14) -- Cmd+A, the real OS-native shortcut, selected all 23 characters."
---

# Actions API: Mouse & Keyboard

## What was run

Real hover, drag-and-drop, double-click, and right-click actions via Selenium's `ActionChains` — plus a real,
deliberately investigated keyboard-shortcut mistake: a "select all" combo that looked correct in code but
genuinely selected nothing, traced to a real, platform-specific cause rather than guessed at.

## Real hover, drag-and-drop, double-click, and right-click — all verified

```python
ActionChains(driver).move_to_element(hover_target).perform()
ActionChains(driver).drag_and_drop(source, target).perform()
ActionChains(driver).double_click(dbl_target).perform()
ActionChains(driver).context_click(right_target).perform()
```
```
REAL hover result: before='not hovered', after='hovered!'
REAL drag-and-drop result: before='Drop Here', after='Dropped!'
REAL double-click result: 'double-clicked'
REAL right-click result: 'right-clicked'
```

Every real mouse-based action produced its real, expected DOM change.

## A real keyboard combo that looked right and genuinely did nothing

```python
ActionChains(driver).click(text_input).key_down(Keys.CONTROL).send_keys("a") \
    .key_up(Keys.CONTROL).perform()
```
```
REAL Ctrl+A on this real macOS session: selectionStart=14, selectionEnd=14 (a true
select-all would be 0 to 23 -- Ctrl+A did nothing here)
```

A real, direct check of the input's actual `selectionStart`/`selectionEnd` (not just the eventual typed
result) proved the combo selected nothing at all — the cursor just sat wherever the preceding click had
placed it.

## The real, investigated fix

```python
ActionChains(driver).key_down(Keys.COMMAND).send_keys("a").key_up(Keys.COMMAND) \
    .send_keys("replaced via Cmd+A").perform()
```
```
real Cmd+A select-all then type: before='original text to select', after='replaced via Cmd+A'
```

A real, complete replacement this time — the real OS-level "select all" shortcut on this macOS machine is
`Cmd+A`, not `Ctrl+A`, and Selenium's low-level key events correctly respect that real platform convention
rather than imposing one universal key combo.

## How It Actually Works

`ActionChains` builds a real, literal sequence of low-level input events — mouse moves, button presses, key
presses — and plays them back exactly as specified, exactly like a real human operating the browser would.
That real fidelity is precisely why this lesson's `Ctrl+A` attempt failed: a real human on macOS pressing
Ctrl+A in a text field wouldn't select all either, because macOS's real native text-editing shortcut for
"select all" is `Cmd+A`. Selenium didn't malfunction; it faithfully reproduced a key combination that simply
isn't the real select-all shortcut on this real operating system.

The real, practical lesson is to verify an Actions sequence's *actual effect* — this lesson's
`selectionStart`/`selectionEnd` check — rather than trusting that code which looks like a textbook "select
all and replace" pattern must be doing that. A cross-platform real test suite needs to choose the real
modifier key based on the actual OS it's running on, not assume one universal convention.

## Common mistakes

- Hardcoding `Keys.CONTROL` for "select all" without accounting for macOS's real, different convention — this
  lesson's own real test is a direct, measured example of exactly that assumption failing silently (no
  exception, just a no-op).
- Trusting that a keyboard-shortcut `ActionChains` sequence worked just because the subsequent typed text
  appeared somewhere in the field — this lesson's first real attempt would have looked superficially plausible
  without the explicit `selectionStart`/`selectionEnd` check revealing the real selection never happened.
- Assuming `ActionChains` actions are OS-agnostic — they dispatch real, literal key codes; it's the real
  operating system's own shortcut bindings, not Selenium, that determine what those key codes actually do.

## Exercise

Write a real, small helper function that picks `Keys.COMMAND` or `Keys.CONTROL` based on
`platform.system() == "Darwin"`, and use it to make this lesson's select-all sequence work correctly on both
real macOS and real non-macOS systems without hardcoding either modifier key.
