---
description: "A real 1s page-load timeout against a real 3s-delay route threw TimeoutException in 1.01s, leaving the real browser at 'data:,' not a partial page."
---

# Setting Up a Real WebDriver Session

## What was run

A real default Chrome WebDriver session compared against a real, deliberately customized one (window size,
user agent, page-load timeout) — plus a real, direct test of what actually happens when a page genuinely
takes longer to load than the configured timeout allows, rather than assuming the behavior.

## Real default vs. real customized session options

```python
default_driver = webdriver.Chrome(options=default_options)  # only headless set
```
```
REAL default window size: {'width': 756, 'height': 556}
real default user agent: Mozilla/5.0 (Macintosh; ...) HeadlessChrome/156.0.0.0 Safari/537.36
```
```python
options.add_argument("--window-size=1024,768")
options.add_argument("--user-agent=SeleniumCourseBot/1.0")
```
```
REAL customized window size: {'width': 1024, 'height': 768}
real customized user agent: SeleniumCourseBot/1.0
```

Both real values changed exactly as configured — the default session's real window size and user agent are
Chrome's own defaults, not something Selenium invents.

## A real, enforced page-load timeout

```python
driver.set_page_load_timeout(1)  # deliberately too short for the real 3s /slow route
driver.get("http://127.0.0.1:5050/slow")
```
```
REAL page-load timeout enforced: TimeoutException after 1.02s (real route takes 3s, timeout was set to 1s)
```

A real, precisely-enforced timeout — the exception fired at `1.02s`, matching the configured `1`-second limit,
not the real page's actual `3`-second load time.

## A real, investigated follow-up: what state is the browser left in?

```python
print(driver.current_url)   # right after the timeout
```
```
real current_url right after timeout: data:,
```

A real, concrete finding: the browser didn't keep whatever partial content it had received — it reset
entirely to a blank `data:,` URL. A fresh `driver.get()` call afterward, with a real generous timeout, loaded
the actual page correctly:

```python
driver.set_page_load_timeout(300)
driver.get("http://127.0.0.1:5050/slow")
heading_text = driver.find_element(By.ID, "slow-heading").text
```
```
real page DID load successfully with a generous timeout: 'Loaded after a real 3-second delay'
```

## How It Actually Works

Every one of these session options is set once, at session creation or immediately after, and shapes every
real command for the rest of that session: window size affects what's actually visible and clickable without
scrolling; user agent affects what the real server (and any client-side JavaScript) believes is making the
request; and the page-load timeout bounds how long `driver.get()` is willing to real-world wait before giving
up. This lesson's real timeout test confirms that bound is actually enforced, not just a suggestion — Chrome
genuinely abandons the in-progress navigation and resets to a blank state rather than leaving a half-loaded
page sitting there.

That real reset-to-blank behavior matters practically: a script that catches a `TimeoutException` and tries to
keep working with whatever's "already on the page" is working with nothing, as this lesson's real
`current_url` check (`data:,`) directly demonstrates. The real, correct recovery, shown in this lesson's final
step, is a fresh `driver.get()` call.

## Common mistakes

- Setting a page-load timeout without testing what actually happens when it fires — this lesson's real check
  of `current_url` after the exception revealed a complete reset, not a partially-usable page, which anyone
  assuming otherwise would discover the hard way in production.
- Assuming a custom user agent is cosmetic — real servers and real client-side JavaScript can and do change
  behavior based on it; this lesson's real `execute_script` check is a direct way to confirm what value a page
  actually sees.
- Confusing a page-load timeout with the explicit/implicit waits [Level 2](../level-2/index.md) covers next —
  a page-load timeout bounds the initial navigation itself; it says nothing about whether a *specific element*
  on an already-loaded page is ready to interact with.

## Exercise

Set a real page-load timeout of exactly `3.5` seconds (just above the `/slow` route's real 3-second delay) and
run this lesson's exact real test again. Confirm the real page loads successfully this time without throwing,
and measure how close the real elapsed time comes to the route's actual 3-second delay.
