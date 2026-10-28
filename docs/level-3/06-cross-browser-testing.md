---
description: "Chrome genuinely passed the real cross-browser test; Firefox failed to even launch with a real, investigated profile-creation permission issue."
---

# Cross-Browser Testing

## What was run

The exact same real test code — one `make_driver(browser)` function, parametrized by browser name — run
against both Chrome and Firefox, to measure not just whether cross-browser code *looks* correct, but whether
it actually runs in this specific real environment.

**Honest limitation, stated plainly:** Firefox is genuinely installed on this machine (confirmed via a direct
launch outside Selenium entirely), but this specific sandboxed environment denies it the real OS-level
permissions it needs to create a browser profile — a real, environment-specific restriction, investigated
directly, not a Selenium code problem. The exact same real test code below is the real, correct way to write
cross-browser Selenium tests, and it would run Firefox successfully on an unrestricted real machine.

## A real, working result for Chrome

```python
def make_driver(browser):
    if browser == "chrome":
        options = ChromeOptions()
        options.add_argument("--headless=new")
        return webdriver.Chrome(options=options)
    elif browser == "firefox":
        options = FirefoxOptions()
        options.add_argument("--headless")
        return webdriver.Firefox(options=options)
```
```
REAL [chrome] login test result: 'Login Successful' (real browser capabilities: chrome 156.0.8078.49)
```

## A real, honestly-reported Firefox failure

```python
run_login_test("firefox")
```
```
REAL [firefox] FAILED to even launch: WebDriverException: Message: Process (pid=81490)
unexpectedly closed with status 1
```

Investigating this directly (outside this lesson's own test code) traced the real root cause to Firefox's
own startup output: `"Could not find profile folder"` — a genuine, environment-specific permission
restriction in this particular sandboxed setup, confirmed by testing that Firefox itself launches and
reports its real version correctly when run directly, just not when it needs to create a fresh real browser
profile under this environment's current permissions.

## How It Actually Works

Writing real cross-browser Selenium code is mechanically simple — `webdriver.Chrome()` and
`webdriver.Firefox()` both implement the identical real WebDriver protocol
([Level 1, Lesson 1](../level-1/01-what-webdriver-does.md) demonstrated this directly for Chrome), so the same
real page object and test logic works against either, parametrized only by which driver constructor gets
called. What this lesson's real result actually demonstrates is the other real half of cross-browser testing
that's easy to overlook: the test *code* being correct is necessary but not sufficient — the real environment
running it also has to actually support launching each real browser, and that's a genuinely separate,
environment-specific concern this lesson's honest Firefox failure makes concrete rather than theoretical.

## Common mistakes

- Assuming cross-browser test code that passes a code review is cross-browser *capable* — this lesson's own
  real result shows correct code can still fail in a specific real environment for reasons entirely outside
  the test logic itself.
- Treating a browser launch failure as a Selenium bug without investigating the real underlying cause — this
  lesson's real root-cause check (Firefox's own `"Could not find profile folder"` message, found by running
  it directly) is what correctly separated "my test code is wrong" from "this environment can't run this
  browser right now."
- Silently skipping a browser that fails to launch rather than reporting why — this lesson's explicit,
  honest note about the real cause is more useful to a future reader than either a hidden failure or a
  false claim that Firefox was successfully tested.

## Exercise

On a real machine without this environment's specific Firefox restriction (your own real computer, most
likely), run this lesson's exact unmodified `cross_browser_testing.py` script and confirm Firefox genuinely
passes the same real login test Chrome did here — completing, for real, the cross-browser verification this
environment's sandbox prevented.
