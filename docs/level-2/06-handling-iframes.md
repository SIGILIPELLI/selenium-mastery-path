---
description: "The real in-frame button was invisible before switching context; the heading became invisible after switching in -- a symmetric, measured blindness."
---

# Handling IFrames

## What was run

A real page containing a real `<iframe>`, with element lookups attempted both before and after switching
Selenium's context into the frame — and, symmetrically, attempted again from *inside* the frame for an
element that only exists at the top level — to measure exactly what becomes visible and invisible at each
real step.

## A real element, genuinely invisible before switching context

```python
driver.find_element(By.ID, "frame-btn")   # attempted from the top-level document
```
```
REAL NoSuchElementException: the real in-frame element is invisible to the top-level document's
context, even though it's genuinely on the page
```

## The same real element, found immediately after switching in

```python
frame_element = driver.find_element(By.ID, "my-frame")
driver.switch_to.frame(frame_element)
btn = driver.find_element(By.ID, "frame-btn")
btn.click()
```
```
REAL: found frame-btn AFTER switching context: tag=button
real frame button text after click: 'frame button clicked'
```

## The real, symmetric reverse: the top-level heading disappears while inside the frame

```python
driver.find_element(By.TAG_NAME, "h1")   # attempted while still switched into the frame
```
```
REAL NoSuchElementException: the top-level <h1> is invisible while still switched into the
frame's own context
```

## A real, clean escape back

```python
driver.switch_to.default_content()
h1 = driver.find_element(By.TAG_NAME, "h1")
```
```
REAL: found top-level <h1> after switch_to.default_content(): 'IFrame Test'
```

## How It Actually Works

An `<iframe>` embeds a genuinely separate real HTML document inside the page — Selenium's `find_element()`
only ever searches the *currently active* document context, never both simultaneously. This lesson's real,
perfectly symmetric result makes that concrete in both directions: the frame's content is invisible from the
top level until you explicitly `switch_to.frame()`, and the top level becomes equally invisible once you've
switched *in*, until you explicitly `switch_to.default_content()` to leave. Neither context has any real,
automatic awareness of the other.

This matters for real test structure: any code operating on a page with iframes has to track which real
context it's currently in, and explicitly switch at every real boundary crossing — there's no way to
"accidentally" find an element in the wrong context, which this lesson's two real, matching
`NoSuchElementException`s confirm directly.

## Common mistakes

- Forgetting to switch into a frame before trying to interact with its real content — this lesson's first
  real failure is exactly that mistake, demonstrated deliberately rather than just described.
- Forgetting to switch *back out* after finishing work inside a frame — any subsequent top-level lookup will
  fail exactly the way this lesson's second real failure shows, for a reason that's easy to forget mid-test.
- Using `switch_to.frame()` with a frame's `name`/`id` string versus a real `WebElement` reference
  interchangeably without checking both work — this lesson used the real element-reference form, which is
  more robust when a page has multiple frames without unique, predictable names.

## Exercise

Modify this lesson's script to nest a second real iframe inside `/iframe-content` (pointing to yet another
small, real Flask route), and confirm that reaching an element inside that doubly-nested frame requires two
separate, real `switch_to.frame()` calls — one for each level — not a single call that somehow reaches both.
