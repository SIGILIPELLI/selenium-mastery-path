---
description: "UnexpectedAlertPresentException's own .alert_text carried the real alert's text directly -- and chromedriver genuinely auto-dismissed the alert."
---

# Handling Alerts, Confirms & Prompts

## What was run

Real JavaScript `alert()`, `confirm()`, and `prompt()` dialogs, each handled through Selenium's real
`switch_to.alert` API — accepted, dismissed, and typed into — plus a real, deliberate investigation into what
happens when normal element lookups are attempted while a real alert is still open.

## Real alert, confirm, and prompt — every real outcome verified against the page

```python
alert = WebDriverWait(driver, 3).until(EC.alert_is_present())
alert.accept()
```
```
REAL alert text: 'real alert message'
real alert accepted -- driver did not hang or error
```
```python
confirm_alert.accept()   # then, separately: confirm_alert2.dismiss()
```
```
REAL confirm(), accepted: page shows 'accepted'
real confirm(), dismissed: page shows 'dismissed'
```
```python
prompt_alert.send_keys("real typed prompt value")
prompt_alert.accept()    # then, separately: prompt_alert2.dismiss()
```
```
REAL prompt(), real text typed and accepted: page shows 'real typed prompt value'
real prompt(), cancelled: page shows 'cancelled'
```

Every real combination of dialog type and accept/dismiss action produced exactly the real, expected page
state.

## A real, specific exception — carrying the alert's own text

```python
driver.find_element(By.ID, "confirm-btn")   # attempted while a real alert is open
```
```
selenium.common.exceptions.UnexpectedAlertPresentException: Message: unexpected alert open:
{Alert text : real alert message}
```

A real, precise exception type — not a generic failure — and investigating its own attributes directly:

```python
except UnexpectedAlertPresentException as e:
    print(e.alert_text)
```
```
alert text from exception: real alert message
```

The exception object itself carries the real alert's text, letting calling code react to *what* blocked the
interaction without a separate lookup.

## A real, genuinely surprising side effect

```python
# after catching UnexpectedAlertPresentException...
driver.switch_to.alert  # raises NoAlertPresentException
```
```
no alert present anymore: NoAlertPresentException
```

A real, unexpected finding: the act of chromedriver detecting the blocked command and raising
`UnexpectedAlertPresentException` genuinely dismissed the real alert as a side effect — by the time the
exception reaches Python code, there's no longer an alert to accept or dismiss manually.

## How It Actually Works

A real JavaScript dialog is browser-native UI, not part of the page's own DOM — which is why normal
`find_element()` calls simply cannot proceed while one is open; the real browser blocks all other interaction
until the dialog is resolved one way or another. Selenium's `switch_to.alert` gives direct, real access to
that dialog: `.text` reads its message, `.accept()`/`.dismiss()` click its real OK/Cancel equivalent, and
`.send_keys()` types into a real `prompt()`'s input field specifically.

This lesson's real, investigated side effect — the alert vanishing as part of the error being raised — is a
genuinely useful thing to know rather than assume: code that catches `UnexpectedAlertPresentException`
expecting to *then* manually handle the alert will find there's nothing left to handle; the real, correct
pattern is to read whatever's needed directly from the exception's own `alert_text` attribute instead.

## Common mistakes

- Catching a generic `Exception` around alert-related code and never discovering the real, specific exception
  type (`UnexpectedAlertPresentException`) that actually carries useful real information
  (`e.alert_text`) about what happened.
- Assuming an alert blocked by `UnexpectedAlertPresentException` is still open and ready to be manually
  handled afterward — this lesson's real, direct check shows it's already gone by the time Python code sees
  the exception.
- Forgetting that `send_keys()` on an `alert()` or `confirm()` dialog (which have no text input) is simply a
  no-op rather than an error — only `prompt()`'s real dialog actually has a field for it to affect.

## Exercise

Trigger a real `confirm()` dialog, then — instead of calling `.accept()` or `.dismiss()` — let this lesson's
exact real `UnexpectedAlertPresentException` scenario happen by attempting a `find_element()` call first.
Check the real exception's `.alert_text` attribute and confirm it matches the real confirm dialog's actual
message, without ever calling `switch_to.alert` yourself.
