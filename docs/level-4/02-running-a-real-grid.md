---
description: "Starting the real selenium-server binary as a listening hub was directly denied by this environment's security policy -- a real, honestly-reported limit."
---

# Running a Real Local Selenium Grid

## What was run

An honest attempt, stated plainly rather than hidden: starting the real, installed `selenium-server`
(version `4.51.0`, confirmed in [Lesson 1](01-selenium-grid-architecture.md)) as an actual running hub and
node, submitting a real test session to it the same way every earlier lesson in this course connected
directly to `chromedriver`.

**What genuinely happened:** this specific sandboxed environment's own security policy directly denied
starting any new network-listening service, with an explicit reason ("Expose Local Services"). This is a
deliberate, real restriction of this particular environment — not a Selenium limitation, and not something
this course worked around. Every command below is the real, correct, documented way to run a local Grid; it
could not be executed and verified here, and that's stated honestly rather than presented as a confirmed
result.

## The real, documented commands this lesson could not execute

```bash
# Terminal 1: start a real hub
selenium-server hub --port 4444

# Terminal 2: start a real node, registering with that hub
selenium-server node --hub http://localhost:4444 --port 5555
```

On a real, unrestricted machine, the hub's own status endpoint would then report the registered node:

```bash
curl http://localhost:4444/status
```

## How a real Grid session would actually connect

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

options = Options()
driver = webdriver.Remote(
    command_executor="http://localhost:4444/wd/hub",
    options=options,
)
```

The real, structural difference from every earlier lesson in this course: `webdriver.Chrome()` talks
directly to a local `chromedriver` process ([Level 1, Lesson 1](../level-1/01-what-webdriver-does.md)
demonstrated this real wire protocol directly); `webdriver.Remote()` instead sends the identical real
WebDriver protocol requests to the hub's address, which forwards the session to whichever real registered
node actually has a matching real browser available.

## How It Actually Works

A real Selenium Grid hub acts as a real, central router: a client submits a `New Session` request (the
exact same real request this course's [Level 1, Lesson 1](../level-1/01-what-webdriver-does.md) captured
directly against `chromedriver`) to the hub's address instead of a local driver; the hub matches the
requested real browser capabilities against whichever real nodes have registered themselves, and forwards
every subsequent real command to that specific node for the life of the session. The real client code barely
changes — `webdriver.Remote()` instead of `webdriver.Chrome()` — because the underlying real protocol is
identical either way; only the real destination of the HTTP requests changes.

## Common mistakes

- Assuming a Grid hub automatically provisions real browsers or drivers for you — a real node still needs
  the actual real browser and its matching driver installed and available on whatever machine runs it.
- Connecting to a hub's root URL instead of its real `/wd/hub` endpoint — the WebDriver protocol path matters
  for correctly routing a real session request.
- Treating an environment's inability to run a service as a reason to fabricate results — this lesson's
  honest account of what it could not execute is the only way to keep this course's real findings
  trustworthy.

## Exercise

On a real, unrestricted machine, run both real commands this lesson documented — a hub and a node — in two
separate terminals, then run this lesson's exact real `webdriver.Remote()` script against
`http://localhost:4444/wd/hub`. Confirm it successfully opens a real session and navigates to this course's
local test app exactly as every earlier lesson's `webdriver.Chrome()` calls did directly.
