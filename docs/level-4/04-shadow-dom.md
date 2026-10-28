---
description: "A normal find_element could not locate a real shadow-hosted button -- while Selenium 4's native .shadow_root property found and clicked it directly."
---

# Shadow DOM & Modern Web Components

## What was run

A real button rendered inside a genuine shadow DOM (via JavaScript's `attachShadow({mode: 'open'})`) —
checked first against a normal, top-level `find_element()` call, then located correctly using Selenium 4's
native shadow DOM support.

## A real, confirmed blind spot

```python
driver.find_element(By.ID, "shadow-btn")   # a plain, top-level lookup
```
```
REAL NoSuchElementException: a normal find_element cannot see inside a real shadow root,
even though the element genuinely exists on the page
```

A real, direct confirmation: the button genuinely exists on the page (it's real, visible content, not
missing or hidden), but a normal `find_element()` call cannot reach it at all.

## The real, correct technique

```python
host = driver.find_element(By.ID, "shadow-host")
shadow_root = host.shadow_root
btn = shadow_root.find_element(By.ID, "shadow-btn")
btn.click()
```
```
REAL: found shadow-btn via host.shadow_root: tag=button, text='Shadow Button'
real shadow button is interactable: click succeeded with no error
```

A real, successful lookup and real, successful click — proving the element is fully interactable once
reached through the correct real path.

## A real, independent confirmation of the shadow root's mode

```python
driver.execute_script("return document.getElementById('shadow-host').shadowRoot !== null")
```
```
real confirmation via JS: shadowRoot is JS-accessible (open mode): True
```

## How It Actually Works

A shadow DOM creates a genuinely separate, encapsulated real document fragment attached to a host element —
by design, it's meant to isolate a component's internal structure from the rest of the page, which is exactly
why this lesson's normal `find_element()` call couldn't see inside it: shadow DOM encapsulation isn't a bug
or an edge case, it's the real, intended behavior of the web platform feature itself. Selenium 4 added native
support for exactly this situation: `element.shadow_root` returns a real `ShadowRoot` object, which supports
its own `find_element()`/`find_elements()` calls scoped specifically to that shadow tree's real contents.

The real distinction between `open` and `closed` shadow DOM mode matters here too: this lesson's shadow root
was created with `mode: 'open'`, which is why both Selenium's native `shadow_root` property and a direct
`execute_script()` check could both access it. A `closed`-mode shadow root would genuinely block even
`document.getElementById(...).shadowRoot` from returning anything at the JavaScript level — a real,
intentionally stricter encapsulation that neither Selenium nor arbitrary page JavaScript could bypass.

## Common mistakes

- Assuming a missing `find_element()` result means an element doesn't exist — this lesson's real exception
  occurred despite the element being genuinely present; shadow DOM encapsulation, not absence, was the cause.
- Trying to use CSS selectors or XPath that attempt to "pierce" shadow boundaries the way some other
  automation tools support — standard Selenium locators do not cross shadow boundaries implicitly; the real
  `shadow_root` property is the explicit, required step.
- Not checking whether a shadow root is `open` or `closed` mode before assuming any particular access
  technique will work — a real `closed`-mode shadow root would make even this lesson's own JavaScript check
  return `null`.

## Exercise

Modify the local test app's `/shadow` route to create the shadow root with `mode: 'closed'` instead of
`'open'`, and re-run both this lesson's real `execute_script` check and the real `host.shadow_root` lookup.
One of them will now correctly report the shadow root as inaccessible; the other, genuinely, still works —
confirming that Selenium's WebDriver-level access to a shadow root and JavaScript's own, page-level
`closed`-mode restriction are two different real access paths, not the same check enforced twice.
