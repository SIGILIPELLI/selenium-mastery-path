---
description: "The real chromedriver's own --port argument matched Selenium's client command_executor URL -- confirming WebDriver is a real local HTTP protocol."
---

# What Selenium WebDriver Actually Does

## What was run

A real Selenium session, with its underlying HTTP wire protocol traffic logged directly, cross-checked against
a real, independent OS-level process listing — to see, concretely, what "WebDriver" actually is underneath the
Python API: a real local HTTP server, a real separate browser-driver process, and a real, documented wire
protocol between them.

## A real HTTP request, logged directly

```python
logging.getLogger("selenium.webdriver.remote.remote_connection").setLevel(logging.DEBUG)
driver = webdriver.Chrome(options=options)
```
```
POST http://localhost:54092/session {'capabilities': {'firstMatch': [{}], 'alwaysMatch':
{'browserName': 'chrome', 'pageLoadStrategy': 'normal', ...}}}
Remote response: status=200 | data={"value":{"capabilities":{...},"sessionId":"061283dd..."}}
```

A real, literal HTTP `POST` request to a real local server, asking it to start a new browser session — and a
real HTTP `200` response containing a real session ID. This is genuinely all `webdriver.Chrome()` does under
the hood: send this exact real request and get this exact real response back.

## A real, independent confirmation at the OS level

```python
result = subprocess.run(["pgrep", "-fl", "chromedriver"], capture_output=True, text=True)
```
```
=== REAL session identity ===
real session_id: 7e2fd4427aef6f41b9fe75bb9671a69a
real command_executor URL: http://localhost:54211

=== REAL OS-level process check ===
76751 /Users/.../chromedriver --enable-chrome-logs --port=54211
```

The real port number Selenium's Python client is talking to (`54211`) exactly matches the real `--port=54211`
argument the actual `chromedriver` process was launched with — independently confirmed via `pgrep`, not just
read from inside the Python client itself.

## A real, verified clean shutdown

```python
driver.quit()
result_after = subprocess.run(["pgrep", "-fl", "chromedriver"], ...)
```
```
real chromedriver process(es) found AFTER driver.quit(): '(none)'
```

A real, confirmed cleanup — the actual OS process that was running chromedriver is genuinely gone after
`quit()`, not just disconnected or orphaned.

## How It Actually Works

Every Selenium command — find an element, click it, type text, navigate — is translated by the Python client
into a real HTTP request to a real local server run by the browser-specific driver (`chromedriver` for Chrome),
following the W3C WebDriver specification: a standardized, documented wire protocol. `chromedriver` itself then
talks to the real browser using its own separate real protocol (Chrome DevTools Protocol, for Chrome). Python
→ WebDriver HTTP protocol → chromedriver → Chrome's own control protocol → the actual browser is the real,
complete real chain, and this lesson's logged HTTP traffic is a direct, observed slice of the first hop.

This matters practically: Selenium's real architecture means every single action — however simple it looks in
Python (`element.click()`) — is a real network round trip to a real local server, with real, measurable latency.
That's the real, structural reason this entire course's later lessons on waits and synchronization are
necessary: between any two of these real round trips, the actual browser is free to keep doing its own real
work (loading a page, running JavaScript) completely independently of what the Python script currently believes
is true.

## Common mistakes

- Treating Selenium as "a Python library that controls Chrome" without realizing a real separate process
  (`chromedriver`) and a real local HTTP server are involved — this lesson's exact matching port number between
  the client and the independently-checked OS process is direct proof of that real architecture.
- Assuming `driver.quit()` is just a Python object being garbage collected — this lesson's real `pgrep` check
  before and after confirms it's an actual OS-level process termination.
- Not understanding that every Selenium command is a real network round trip — this is the real reason
  Selenium scripts can behave differently under different real network/system load, and why
  [Level 2](../level-2/index.md) treats waits as a first-class topic rather than an afterthought.

## Exercise

Enable the same real DEBUG-level logging this lesson used, then run any five Selenium commands of your choice
(navigate, find an element, click, type, read an attribute) and count the real number of distinct
`POST`/`GET` HTTP requests logged for each — confirming directly whether any of Selenium's higher-level Python
methods secretly issue more than one real wire-protocol request.
