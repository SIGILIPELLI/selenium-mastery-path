---
title: "Selenium Interview Questions and Answers (179 Questions)"
description: "179 Selenium interview questions with answers and explanations, from beginner to advanced, organised by level. Free, from the Selenium Mastery Path."
---

# Selenium Interview Questions and Answers

**179 questions** with the correct answer and a short explanation, organised from beginner (Level 1) to advanced (Level 4). Every question comes from a lesson in the free Selenium Mastery Path, so you can open the lesson to go deeper.

!!! tip "Practise, don't just read"
    Each lesson also ends with an interactive quiz, and the [final quiz](quiz.md) tests all four levels. Try to answer each question below before reading the answer.

## Level 1 · WebDriver Fundamentals & Locators

### [What Selenium WebDriver Actually Does](level-1/01-what-webdriver-does.md)

**Q1.** What does the WebDriver protocol let a test do?

**Answer:** Send standard HTTP commands to a browser driver, which controls a real browser

Selenium's client libraries speak the W3C WebDriver protocol to a driver such as chromedriver.

**Q2.** Which component sits between your test code and Chrome?

**Answer:** chromedriver (a browser driver process)

Each browser has its own driver, managed automatically by Selenium Manager in recent versions.

**Q3.** Is Selenium a testing framework?

**Answer:** No, it is a browser automation library; you pair it with a runner such as pytest

Assertions and test structure come from pytest or unittest.

**Q4.** What is Selenium Manager?

**Answer:** A tool bundled with Selenium that finds or downloads the matching browser driver

It removes the need to manually install chromedriver.

### [Setting Up a Real WebDriver Session](level-1/02-webdriver-session-setup.md)

**Q5.** Why should every test quit its driver in a finally block or fixture teardown?

**Answer:** Otherwise browser and driver processes leak and accumulate

driver.quit() ends the session; close() only closes a window.

**Q6.** What is the difference between driver.close() and driver.quit()?

**Answer:** close() closes the current window; quit() ends the session and all windows

Always quit at the end.

**Q7.** What does the --headless=new Chrome argument do?

**Answer:** Runs Chrome without a visible window

Useful on CI machines.

**Q8.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <title>Hello page</title><p>x</p>
print(driver.title)
print(driver.current_url.startswith('data:'))
```

**Answer:** Hello page
True

driver.title is the document title and current_url is the data URL. Verified by running it with Selenium and headless Chrome.

### [Locators: ID, Name, Class, Tag, Link Text](level-1/03-locators-basic.md)

**Q9.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <h1 id=a>One</h1><ul><li class=item>x</li><li class=item>y</li></ul><input name=q placeholder=Search>
print(driver.find_element(By.ID, 'a').text)
print(len(driver.find_elements(By.CLASS_NAME, 'item')))
print(driver.find_element(By.NAME, 'q').get_attribute('placeholder'))
```

**Answer:** One
2
Search

find_elements returns a list; find_element returns the first match. Verified by running it with Selenium and headless Chrome.

**Q10.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <p>nothing here</p>
try:
    driver.find_element(By.ID, 'missing')
except NoSuchElementException:
    print('none')
print(driver.find_elements(By.ID, 'missing'))
```

**Answer:** none
[]

find_element raises NoSuchElementException; find_elements returns an empty list. Verified by running it with Selenium and headless Chrome.

**Q11.** Why are IDs usually the best locators?

**Answer:** They are meant to be unique and are the fastest and most stable

Fall back to CSS or XPath when there is no stable ID.

**Q12.** What does By.LINK_TEXT match?

**Answer:** Anchor elements whose visible text matches exactly

PARTIAL_LINK_TEXT matches substrings.

### [Locators: CSS Selectors](level-1/04-locators-css.md)

**Q13.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <ul class=menu><li class=active><a href=/>Home</a></li><li><a href=/about>About</a></li><li><a href=/g.pdf>Guide</a></li></ul>
print(driver.find_element(By.CSS_SELECTOR, 'ul.menu > li.active a').text)
print(len(driver.find_elements(By.CSS_SELECTOR, 'li:not(.active)')))
print(driver.find_element(By.CSS_SELECTOR, 'a[href$=".pdf"]').text)
```

**Answer:** Home
2
Guide

Child combinator, :not() and attribute suffix selectors. Verified by running it with Selenium and headless Chrome.

**Q14.** What does #login .btn[type=submit] select?

**Answer:** A submit element with class btn inside the element with id login

Space is the descendant combinator.

**Q15.** Why is CSS often preferred over XPath for simple lookups?

**Answer:** Shorter, readable and fast in browsers

XPath can still do things CSS cannot, like text and upward traversal.

**Q16.** What can CSS NOT do that XPath can?

**Answer:** Select a parent element or match by text content

:has() is available in modern browsers, but the text matching still needs XPath.

### [Locators: XPath](level-1/05-locators-xpath.md)

**Q17.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <table><tr><td class=n>Ana</td><td>25</td></tr><tr><td class=n>Bo</td><td>30</td></tr><tr><td class=n>Cy</td><td>41</td></tr></table><label>Email </label><input id=em>
print(driver.find_element(By.XPATH, "//tr[td[text()='Bo']]/td[2]").text)
print(driver.find_element(By.XPATH, "//label[normalize-space()='Email']/following-sibling::input").get_attribute('id'))
print(len(driver.find_elements(By.XPATH, '//td[contains(@class,"n")]')))
```

**Answer:** 30
em
3

XPath can select by text, siblings and attribute substrings. Verified by running it with Selenium and headless Chrome.

**Q18.** What does // at the start of an XPath mean?

**Answer:** Search anywhere in the document

A single / means from the root.

**Q19.** What does the XPath function normalize-space() help with?

**Answer:** Matching text ignoring extra whitespace

Useful for labels with stray spaces.

**Q20.** What is a risk of absolute XPaths such as /html/body/div[2]/div[1]?

**Answer:** They break whenever the page structure changes

Prefer relative paths anchored on stable attributes.

### [Interacting With Elements: Click, Type, Clear](level-1/06-interacting-with-elements.md)

**Q21.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <input id=name><button id=go onclick="document.getElementById('out').textContent='clicked'">Go</button><p id=out>nothing</p>
box = driver.find_element(By.ID, 'name')
box.send_keys('Ana')
box.send_keys(' Lee')
print(box.get_attribute('value'))
box.clear()
print(repr(box.get_attribute('value')))
driver.find_element(By.ID, 'go').click()
print(driver.find_element(By.ID, 'out').text)
```

**Answer:** Ana Lee
''
clicked

send_keys appends; clear() empties the field; click triggers handlers. Verified by running it with Selenium and headless Chrome.

**Q22.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <form onsubmit="document.getElementById('out').textContent='submitted '+document.getElementById('f').value;return false"><input id=f></form><p id=out></p>
f = driver.find_element(By.ID, 'f')
f.send_keys('hi' + Keys.ENTER)
print(driver.find_element(By.ID, 'out').text)
```

**Answer:** submitted hi

Keys.ENTER in a form field submits via the page's own handler. Verified by running it with Selenium and headless Chrome.

**Q23.** What does element.text return?

**Answer:** The visible, rendered text of the element

Hidden text is not returned.

**Q24.** How do you read the current value typed in an input?

**Answer:** element.get_attribute('value') (or get_property('value'))

text is empty for inputs.

### [Dropdowns, Checkboxes &amp; Radio Buttons](level-1/07-dropdowns-checkboxes-radios.md)

**Q25.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <select id=c><option value=r>Red</option><option value=b>Blue</option></select>
s = Select(driver.find_element(By.ID, 'c'))
s.select_by_visible_text('Blue')
print(s.first_selected_option.get_attribute('value'))
s.select_by_value('r')
print(s.first_selected_option.text)
print([o.text for o in s.options])
```

**Answer:** b
Red
['Red', 'Blue']

Select can choose by visible text, value or index. Verified by running it with Selenium and headless Chrome.

**Q26.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <input type=checkbox id=cb><input type=radio name=size value=s checked><input type=radio name=size value=l>
cb = driver.find_element(By.ID, 'cb')
print(cb.is_selected())
cb.click()
print(cb.is_selected())
radio = driver.find_element(By.CSS_SELECTOR, 'input[name=size][value=l]')
radio.click()
print(driver.find_element(By.CSS_SELECTOR, 'input[name=size][value=s]').is_selected(), radio.is_selected())
```

**Answer:** False
True
False True

Radios in a group are mutually exclusive. Verified by running it with Selenium and headless Chrome.

**Q27.** Which condition should you check before clicking a checkbox to make the state explicit?

**Answer:** is_selected(), click only if it is not already in the desired state

Clicking blindly toggles.

**Q28.** What does Select.deselect_all() require?

**Answer:** A multi-select element (otherwise it raises)

Single selects cannot be deselected.

### [Browser Navigation: Back, Forward, Refresh](level-1/08-browser-navigation.md)

**Q29.** After driver.get(A), driver.get(B), what does driver.back() do?

**Answer:** Returns to page A (the previous history entry); forward() would return to B

back and forward move through the session history, and element references from the old page become stale.

**Q30.** What does driver.refresh() do?

**Answer:** Reloads the current page

Element references become stale.

**Q31.** What does driver.get(url) wait for by default?

**Answer:** The page load event (readyState complete)

Dynamic content may still need explicit waits.

**Q32.** How can you set the page load strategy?

**Answer:** With the pageLoadStrategy capability (normal, eager, none)

eager returns after DOMContentLoaded.

### [Reading Element State: Text, Attributes, Visibility](level-1/09-element-state.md)

**Q33.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <p id=a style='display:none'>secret</p><button id=b disabled>hidden</button><button id=c disabled style='color:red'>x</button>
e = driver.find_element(By.ID, 'a')
print(e.is_displayed(), repr(e.text))
b = driver.find_element(By.ID, 'b')
print(b.is_displayed(), b.is_enabled(), b.get_attribute('textContent'))
c = driver.find_element(By.ID, 'c')
print(c.is_enabled(), c.value_of_css_property('color'))
```

**Answer:** False ''
True False hidden
False rgba(255, 0, 0, 1)

Hidden elements report empty text; disabled controls are not enabled; CSS values are normalised. Verified by running it with Selenium and headless Chrome.

**Q34.** What is the difference between is_displayed and is_enabled?

**Answer:** Displayed is about visibility; enabled is about whether a control can be interacted with

Both are needed before some actions.

**Q35.** Why prefer get_attribute('textContent') for hidden text?

**Answer:** text returns empty for non-visible elements, textContent returns the raw content

Rarely needed in tests of visible UI.

**Q36.** What does get_dom_attribute differ in?

**Answer:** It returns the attribute as written in the HTML, not the live property

get_attribute tries property first.

### [Project: a Real Login Flow Test](level-1/10-project-login-flow-test.md)

**Q37.** What is a good first assertion in a login test?

**Answer:** That the expected landing page or element appears after login

Assert on outcomes the user sees.

**Q38.** How should test credentials be handled?

**Answer:** Read from environment or a secrets store, never committed

Use test accounts.

**Q39.** What should a negative login test check?

**Answer:** That an error message is shown and the user stays on the login page

Test failure paths too.

**Q40.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <input id=u><input id=p type=password><button id=go onclick="document.getElementById('msg').textContent=document.getElementById('p').value=='secret'?'Welcome '+document.getElementById('u').value:'Invalid credentials'">Login</button><p id=msg></p>
driver.find_element(By.ID, 'u').send_keys('ana')
driver.find_element(By.ID, 'p').send_keys('bad')
driver.find_element(By.ID, 'go').click()
print(driver.find_element(By.ID, 'msg').text)
```

**Answer:** Invalid credentials

A small page model: wrong password shows the error. Verified by running it with Selenium and headless Chrome.

## Level 2 · Waits, Synchronization & Robust Interactions

### [The Real Race Condition: Why Tests Fail Without Waits](level-2/01-the-real-race-condition.md)

**Q41.** What does this print? (the text is added 300 ms after load)

```python
# page (loaded with driver.get('data:text/html,...')):
# <div id=box></div><script>setTimeout(()=>{box.innerHTML='<p id=late>hi</p>'},300)</script>
import time
print(len(driver.find_elements(By.ID, 'late')))
time.sleep(0.8)
print(len(driver.find_elements(By.ID, 'late')))
```

**Answer:** 0
1

find_elements returns immediately; the element only exists after the delay, which is the race condition. Verified by running it with Selenium and headless Chrome.

**Q42.** Why is time.sleep a poor synchronisation strategy?

**Answer:** It is either too short (flaky) or too long (slow) and never adapts to the real state

Wait for a condition instead.

**Q43.** What causes most 'flaky' UI test failures?

**Answer:** Race conditions between the test and asynchronous page updates

Synchronise on observable conditions.

**Q44.** What is the right way to fix a race?

**Answer:** Wait for the specific condition the next step depends on

Explicit waits.

### [Implicit Waits](level-2/02-implicit-waits.md)

**Q45.** What does this print with an implicit wait of 1 s?

```python
# page (loaded with driver.get('data:text/html,...')):
# <p id=here>ok</p>
import time
driver.implicitly_wait(1)
t = time.time()
try:
    driver.find_element(By.ID, 'nope')
except NoSuchElementException:
    print('missing', 1 <= round(time.time()-t, 1) <= 2)
driver.implicitly_wait(0)
print(driver.find_element(By.ID, 'here').text)
```

**Answer:** missing True
ok

With an implicit wait, a failed lookup keeps polling until the timeout. Verified by running it with Selenium and headless Chrome.

**Q46.** What is the problem with mixing implicit and explicit waits?

**Answer:** Timeouts can combine unpredictably and make failures slower and confusing

Prefer explicit waits and keep implicit at 0.

**Q47.** What does an implicit wait apply to?

**Answer:** Every find_element call in the session

It is global.

**Q48.** What does implicit wait NOT wait for?

**Answer:** Conditions like visibility, clickability or text

It only waits for presence.

### [Explicit Waits &amp; WebDriverWait](level-2/03-explicit-waits.md)

**Q49.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <div id=box></div><script>setTimeout(()=>{box.innerHTML='<p id=late>hi</p>'},400)</script>
w = WebDriverWait(driver, 3)
el = w.until(EC.visibility_of_element_located((By.ID, 'late')))
print(el.text)
try:
    WebDriverWait(driver, 0.5).until(EC.presence_of_element_located((By.ID, 'never')))
except TimeoutException:
    print('timeout')
```

**Answer:** hi
timeout

WebDriverWait polls until the condition is truthy or raises TimeoutException. Verified by running it with Selenium and headless Chrome.

**Q50.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <button id=b disabled>Go</button><p id=out></p><script>setTimeout(()=>{b.disabled=false},300);b.onclick=()=>setTimeout(()=>{out.textContent='done'},200)</script>
w = WebDriverWait(driver, 3)
btn = w.until(EC.element_to_be_clickable((By.ID, 'b')))
btn.click()
print(w.until(EC.text_to_be_present_in_element((By.ID, 'out'), 'done')))
print(driver.find_element(By.ID, 'out').text)
```

**Answer:** True
done

element_to_be_clickable waits until the control is visible and enabled; text_to_be_present returns True. Verified by running it with Selenium and headless Chrome.

**Q51.** What is the polling interval default of WebDriverWait?

**Answer:** 0.5 seconds

You can change it with poll_frequency.

**Q52.** Which exceptions can WebDriverWait ignore while polling by default?

**Answer:** NoSuchElementException

Add others with ignored_exceptions.

### [Custom Expected Conditions](level-2/04-custom-expected-conditions.md)

**Q53.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <p id=n>0</p><script>let i=0;let t=setInterval(()=>{n.textContent=++i;if(i==3)clearInterval(t)},150)</script>
class text_is_exactly:
    def __init__(self, locator, text): self.locator, self.text = locator, text
    def __call__(self, driver):
        el = driver.find_element(*self.locator)
        return el if el.text == self.text else False
el = WebDriverWait(driver, 3).until(text_is_exactly((By.ID, 'n'), '3'))
print(el.text)
```

**Answer:** 3

A custom condition is any callable that returns a truthy value (here the element) when satisfied. Verified by running it with Selenium and headless Chrome.

**Q54.** What should a custom condition return when it is not satisfied?

**Answer:** A falsy value such as False or None (not raise for normal absence)

WebDriverWait keeps polling.

**Q55.** What is a benefit of lambda conditions like lambda d: d.title == 'x'?

**Answer:** Quick one-off waits without a class

until accepts any callable taking the driver.

**Q56.** What should custom waits include for debugging?

**Answer:** A helpful message= argument so timeouts explain themselves

until(cond, message='...').

### [Handling Alerts, Confirms &amp; Prompts](level-2/05-alerts-confirms-prompts.md)

**Q57.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <button id=b onclick="out.textContent=confirm('Proceed?')?'confirmed':'cancelled'">x</button><p id=out></p>
driver.find_element(By.ID, 'b').click()
al = WebDriverWait(driver, 2).until(EC.alert_is_present())
print(al.text)
al.accept()
print(driver.find_element(By.ID, 'out').text)
```

**Answer:** Proceed?
confirmed

accept() clicks OK, which makes the page's confirm() return true. Verified by running it with Selenium and headless Chrome.

**Q58.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <button id=b onclick="out.textContent='Hello '+prompt('Name?')">x</button><p id=out></p>
driver.find_element(By.ID, 'b').click()
al = driver.switch_to.alert
al.send_keys('Ana')
al.accept()
print(driver.find_element(By.ID, 'out').text)
```

**Answer:** Hello Ana

send_keys fills a prompt(). Verified by running it with Selenium and headless Chrome.

**Q59.** What happens if an alert is open and you call find_element?

**Answer:** UnexpectedAlertPresentException (handle the alert first)

Switch to the alert and accept or dismiss it.

**Q60.** Which method clicks Cancel on a confirm?

**Answer:** alert.dismiss()

accept clicks OK.

### [Handling IFrames](level-2/06-handling-iframes.md)

**Q61.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <p id=outer>in page</p><iframe id=f srcdoc="<p id='inner'>in frame</p>"></iframe>
print(len(driver.find_elements(By.ID, 'inner')))
frame = driver.find_element(By.ID, 'f')
driver.switch_to.frame(frame)
print(driver.find_element(By.ID, 'inner').text)
driver.switch_to.default_content()
print(driver.find_element(By.ID, 'outer').text)
```

**Answer:** 0
in frame
in page

Elements inside an iframe are invisible to the top-level document until you switch into the frame. Verified by running it with Selenium and headless Chrome.

**Q62.** How do you leave an iframe and return to the main document?

**Answer:** driver.switch_to.default_content()

parent_frame() goes up one level.

**Q63.** What error occurs if you search for an iframe's content without switching?

**Answer:** NoSuchElementException

Frame context matters.

**Q64.** Which wait helps with iframes that load later?

**Answer:** EC.frame_to_be_available_and_switch_to_it

It waits and switches in one step.

### [Handling Multiple Windows &amp; Tabs](level-2/07-multiple-windows-tabs.md)

**Q65.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <h1>First</h1>
orig = driver.current_window_handle
driver.switch_to.new_window('tab')
driver.get('data:text/html,<h1>Second</h1>')
print(len(driver.window_handles), driver.find_element(By.TAG_NAME, 'h1').text)
driver.close()
driver.switch_to.window(orig)
print(len(driver.window_handles), driver.find_element(By.TAG_NAME, 'h1').text)
```

**Answer:** 2 Second
1 First

new_window opens and focuses a new tab; after close() you must switch back to a remaining handle. Verified by running it with Selenium and headless Chrome.

**Q66.** Why must you switch back after closing a window?

**Answer:** The driver does not automatically choose a remaining window

Otherwise commands fail with NoSuchWindowException.

**Q67.** What does driver.switch_to.new_window('tab') do?

**Answer:** Opens and switches to a new tab

Selenium 4 API.

**Q68.** What identifies a window?

**Answer:** A window handle string

Titles are not unique.

### [Actions API: Mouse &amp; Keyboard](level-2/08-actions-api.md)

**Q69.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <div id=d ondblclick="out.textContent='double'">d</div><div id=h onmouseover="out.textContent='hover'" style='height:30px'>h</div><input id=f><p id=out></p>
a = ActionChains(driver)
a.double_click(driver.find_element(By.ID, 'd')).perform()
print(driver.find_element(By.ID, 'out').text)
ActionChains(driver).move_to_element(driver.find_element(By.ID, 'h')).perform()
print(driver.find_element(By.ID, 'out').text)
ActionChains(driver).click(driver.find_element(By.ID, 'f')).key_down(Keys.SHIFT).send_keys('ab').key_up(Keys.SHIFT).perform()
print(driver.find_element(By.ID, 'f').get_attribute('value'))
```

**Answer:** double
hover
AB

ActionChains compose double clicks, hovers and modified key presses. Verified by running it with Selenium and headless Chrome.

**Q70.** When do you need the Actions API?

**Answer:** For complex input like hover, drag and drop, double click or key chords

Simple clicks use click().

**Q71.** What does perform() do on an ActionChains?

**Answer:** Executes the queued actions

Nothing runs until perform().

**Q72.** Why can hover menus be flaky?

**Answer:** The pointer movement and rendering race; wait for the submenu to be visible

Use waits after the hover.

### [File Upload &amp; Stale Elements](level-2/09-file-upload-stale-elements.md)

**Q73.** What is a StaleElementReferenceException?

**Answer:** The referenced element was removed or replaced in the DOM since it was found

Re-find the element.

**Q74.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <div id=c><p id=x>old</p></div><button id=r onclick="c.innerHTML='<p id=x>new</p>'">r</button>
el = driver.find_element(By.ID, 'x')
driver.find_element(By.ID, 'r').click()
try:
    el.text
except StaleElementReferenceException:
    print('stale')
print(driver.find_element(By.ID, 'x').text)
```

**Answer:** stale
new

Replacing innerHTML detaches the old node. Verified by running it with Selenium and headless Chrome.

**Q75.** How do you upload a file with Selenium?

**Answer:** send_keys the absolute file path to the &lt;input type=file&gt; element

Do not interact with the OS dialog.

**Q76.** Why not store WebElements long-term in page objects?

**Answer:** They go stale after DOM updates; store locators and find on use

Locators are stable.

### [Project: a Real Dynamic-Content Test Suite](level-2/10-project-dynamic-content-suite.md)

**Q77.** What is the best structure for a dynamic-content test suite?

**Answer:** Reusable waits and helpers with clear assertions, no sleeps

Consistent synchronisation.

**Q78.** How do you test content that appears after an AJAX call?

**Answer:** Wait for the specific element or text rather than a fixed time

Explicit waits.

**Q79.** What makes tests independent?

**Answer:** Each creates its own state and does not rely on order

Run in any order or in parallel.

**Q80.** Why print or attach diagnostics on failure?

**Answer:** Screenshots and page source speed up debugging

Use fixtures or hooks.

## Level 3 · Page Object Model & Test Architecture

### [Why Page Object Model: the Problem Without It](level-3/01-why-page-object-model.md)

**Q81.** What problem does the Page Object Model solve?

**Answer:** Duplicated locators and UI steps spread across tests

Centralise changes.

**Q82.** What should tests contain in a POM design?

**Answer:** Intent and assertions, not locators

Pages hold locators and actions.

**Q83.** What should a page object NOT contain?

**Answer:** Assertions about test expectations (usually)

Keep assertions in tests.

**Q84.** What does a change in the login button's selector require with POM?

**Answer:** One edit in the page object

Maintainability.

### [Building a Real Page Object](level-3/02-building-a-page-object.md)

**Q85.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <input id=u><button id=go onclick="msg.textContent='hi '+u.value">go</button><p id=msg></p>
class LoginPage:
    USER = (By.ID, 'u')
    GO = (By.ID, 'go')
    MSG = (By.ID, 'msg')
    def __init__(self, d): self.d = d
    def login(self, user):
        self.d.find_element(*self.USER).send_keys(user)
        self.d.find_element(*self.GO).click()
        return self
    def message(self): return self.d.find_element(*self.MSG).text
print(LoginPage(driver).login('ana').message())
```

**Answer:** hi ana

Locators are class attributes and the page object exposes user-level actions. Verified by running it with Selenium and headless Chrome.

**Q86.** Why store locators as tuples (By, value)?

**Answer:** They can be unpacked with * in find_element and reused by waits

EC conditions accept tuples.

**Q87.** What should a page object method return when navigation happens?

**Answer:** The next page object

Enables fluent flows.

**Q88.** Why pass the driver into the page object?

**Answer:** So tests control browser lifetime and pages stay reusable

Dependency injection.

### [Fluent Page Objects](level-3/03-fluent-page-objects.md)

**Q89.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <a id=a href='#x' onclick="document.body.innerHTML='<h1>About us</h1>'">about</a>
class Home:
    def __init__(self, d): self.d = d
    def open_about(self):
        self.d.find_element(By.ID, 'a').click()
        return About(self.d)
class About:
    def __init__(self, d): self.d = d
    def heading(self): return self.d.find_element(By.TAG_NAME, 'h1').text
print(Home(driver).open_about().heading())
```

**Answer:** About us

Returning the next page object lets calls chain. Verified by running it with Selenium and headless Chrome.

**Q90.** What is a fluent interface?

**Answer:** Methods return objects so calls chain readably

page.login().open_settings().

**Q91.** What is a risk of fluent page objects?

**Answer:** Long chains hide which step failed unless failures are reported clearly

Keep steps meaningful.

**Q92.** Should every page method return self?

**Answer:** No, return the page the user lands on after the action

Model navigation honestly.

### [Test Data Management](level-3/04-test-data-management.md)

**Q93.** What is a good source for test data?

**Answer:** Factories or fixtures creating unique data per test (API or DB setup)

Avoid cross-test coupling.

**Q94.** Why avoid sharing one user across parallel tests?

**Answer:** Concurrent changes conflict and cause flakiness

Use unique users.

**Q95.** Where should test data cleanup happen?

**Answer:** In teardown (or by creating disposable data)

Leave the system clean.

**Q96.** Why generate unique values (uuid) for names and emails?

**Answer:** Avoid collisions with data from earlier runs

Unique per run.

### [pytest Fixtures for Selenium](level-3/05-pytest-fixtures-for-selenium.md)

**Q97.** What does this pytest-style fixture return?

```python
import contextlib
@contextlib.contextmanager
def driver_fixture():
    d = webdriver.Chrome(options=opts)
    try:
        yield d
    finally:
        d.quit()
with driver_fixture() as d2:
    d2.get('data:text/html,<title>fx</title>')
    print(d2.title)
```

**Answer:** fx

A fixture is a yield-based setup/teardown; here the context manager mimics what pytest does. Verified by running it with Selenium and headless Chrome.

**Q98.** What does scope='session' do for a driver fixture?

**Answer:** Shares one browser across all tests (faster but less isolated)

Trade isolation for speed.

**Q99.** What does yield do in a pytest fixture?

**Answer:** Provides the value to the test and runs the code after it as teardown

Teardown runs even after failures.

**Q100.** What does a conftest.py file hold?

**Answer:** Shared fixtures and hooks available to tests in its directory

No imports needed.

### [Cross-Browser Testing](level-3/06-cross-browser-testing.md)

**Q101.** What are the main browsers Selenium supports?

**Answer:** Chrome, Firefox, Edge and Safari

Each has its own driver.

**Q102.** How can you parametrise tests across browsers?

**Answer:** pytest parametrize over a browser name feeding a driver factory

Keep tests identical.

**Q103.** What kind of bugs do cross-browser runs catch?

**Answer:** Rendering, CSS, JavaScript API and timing differences

Sample the matrix wisely.

**Q104.** What is a sensible cross-browser strategy?

**Answer:** Run the full suite on one browser and a smoke subset on others

Balance time and risk.

### [Headless vs Headed: What Actually Differs](level-3/07-headless-vs-headed.md)

**Q105.** What does headless mode change?

**Answer:** The browser runs without a visible window, which is faster and works on servers

Layout can differ slightly.

**Q106.** Why set an explicit window size in headless mode?

**Answer:** Responsive layouts depend on viewport size

--window-size=1280,800.

**Q107.** What does this print in headless Chrome with a 800x600 window?

```python
# page (loaded with driver.get('data:text/html,...')):
# <p>x</p>
driver.set_window_size(800, 600)
print(driver.execute_script('return [window.innerWidth > 0, navigator.webdriver]'))
```

**Answer:** [True, True]

navigator.webdriver is true under automation; innerWidth is positive. Verified by running it with Selenium and headless Chrome.

**Q108.** When should you run headed?

**Answer:** When debugging visually

Switch with an environment flag.

### [Screenshots &amp; Visual Debugging on Failure](level-3/08-screenshots-visual-debugging.md)

**Q109.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <div id=e style='width:100px;height:40px;background:#09f'>e</div>
png = driver.get_screenshot_as_png()
print(png[:8] == b'\x89PNG\r\n\x1a\n', len(png) > 100)
el = driver.find_element(By.ID, 'e')
print(len(el.screenshot_as_png) > 50)
```

**Answer:** True True
True

Screenshots are PNG bytes, for the page or a single element. Verified by running it with Selenium and headless Chrome.

**Q110.** When should a screenshot be taken automatically?

**Answer:** On test failure, attached to the report

pytest hooks can do this.

**Q111.** What else is useful to capture on failure?

**Answer:** Page source, console logs and URL

Context speeds triage.

**Q112.** Why can screenshots differ between runs?

**Answer:** Fonts, animations and viewport size

Visual diffing needs stable environments.

### [Flaky Test Diagnosis: Stale Elements &amp; Friends](level-3/09-flaky-test-diagnosis.md)

**Q113.** What is the first step when diagnosing a flaky test?

**Answer:** Reproduce it repeatedly and capture evidence (logs, screenshots, timing)

Do not guess.

**Q114.** What is the most common cause of flakiness?

**Answer:** Missing synchronisation with asynchronous UI

Replace sleeps with explicit waits.

**Q115.** What does retrying a flaky test automatically do?

**Answer:** Hides the problem and should be a last resort with tracking

Fix the cause.

**Q116.** What other causes produce flakiness?

**Answer:** Shared data, ordering, timing, environment and unstable locators

Isolate tests.

### [Project: a Real POM Test Framework](level-3/10-project-pom-framework.md)

**Q117.** What does a POM framework add on top of tests?

**Answer:** Page objects, fixtures, configuration, reporting and logging

Structure for growth.

**Q118.** Where should environment config (base URL, browser) live?

**Answer:** In config files or environment variables, not in tests

One place to change.

**Q119.** What belongs in a base page class?

**Answer:** Common waits and helper methods

Reduce duplication.

**Q120.** What does good reporting include?

**Answer:** Pass/fail with screenshots and logs for failures

Useful to humans.

## Level 4 · Selenium Grid, Scaling & Production Practices

### [Selenium Grid Architecture: Hub &amp; Nodes](level-4/01-selenium-grid-architecture.md)

**Q121.** What does Selenium Grid do?

**Answer:** Distributes browser sessions across machines and browser types

Hub and nodes (or standalone).

**Q122.** What is the role of the router/hub in Selenium 4 Grid?

**Answer:** Accept new session requests and route them to a node with a matching capability

Components: Router, Distributor, Session Map, Node.

**Q123.** Why use Grid?

**Answer:** Parallel runs and cross-browser coverage

Scale test execution.

**Q124.** How does a test use Grid?

**Answer:** With webdriver.Remote pointing at the Grid URL and desired options

The same test code works.

### [Running a Real Local Selenium Grid](level-4/02-running-a-real-grid.md)

**Q125.** What is the quickest way to run a local Grid?

**Answer:** The standalone Selenium server or docker images (selenium/standalone-chrome)

Standalone bundles all components.

**Q126.** What does the /status endpoint of a Grid report?

**Answer:** Whether it is ready and its node and slot capacity

Check before running.

**Q127.** What happens if all Grid slots are busy?

**Answer:** New sessions queue until a slot is free (or time out)

Size nodes to your parallelism.

**Q128.** Why run browsers in containers?

**Answer:** Reproducible environments with matching browser and driver versions

Docker images.

### [Parallel Test Execution](level-4/03-parallel-test-execution.md)

**Q129.** What makes tests safe to run in parallel?

**Answer:** Independence: separate data, separate browser sessions and no shared state

pytest-xdist.

**Q130.** What does pytest -n 4 do with pytest-xdist?

**Answer:** Runs tests across four worker processes

Each worker needs its own driver.

**Q131.** Why can shared files and databases break parallel tests?

**Answer:** Concurrent writes conflict

Use per-test data.

**Q132.** What should drive session creation in parallel runs?

**Answer:** A fixture creating a new driver per test or worker

Avoid globals.

### [Shadow DOM &amp; Modern Web Components](level-4/04-shadow-dom.md)

**Q133.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <div id=host></div><script>host.attachShadow({mode:'open'}).innerHTML='<p>inside</p>'</script>
host = driver.find_element(By.ID, 'host')
root = host.shadow_root
print(root.find_element(By.CSS_SELECTOR, 'p').text)
print(len(driver.find_elements(By.CSS_SELECTOR, 'p')))
```

**Answer:** inside
0

Normal lookups cannot see into a shadow root; use element.shadow_root first. Verified by running it with Selenium and headless Chrome.

**Q134.** What is Shadow DOM?

**Answer:** An encapsulated DOM subtree attached to an element, hidden from normal selectors

Used by web components.

**Q135.** Which Selenium 4 API reaches into a shadow root?

**Answer:** element.shadow_root

Then find within it.

**Q136.** What can't you do with a closed shadow root?

**Answer:** Access it via shadow_root (use JavaScript or application hooks)

Design for testability.

### [JavaScript Execution via Selenium](level-4/05-javascript-execution.md)

**Q137.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <p id=a>old</p>
print(driver.execute_script('return 2 + 3'))
el = driver.find_element(By.ID, 'a')
driver.execute_script("arguments[0].textContent = arguments[1]", el, 'changed')
print(el.text)
print(driver.execute_script('return document.readyState'))
```

**Answer:** 5
changed
complete

execute_script runs JavaScript in the page and returns serialisable values; arguments pass elements. Verified by running it with Selenium and headless Chrome.

**Q138.** When is execute_script appropriate?

**Answer:** For things WebDriver cannot do directly, such as scrolling into view or reading JS state

Prefer real user interactions for tested behaviour.

**Q139.** Why is clicking via JavaScript risky in tests?

**Answer:** It bypasses real user checks like visibility and overlays, hiding bugs

Use it only as a diagnosed workaround.

**Q140.** What does arguments[0] refer to in execute_script?

**Answer:** The first argument passed after the script

Elements are converted to DOM nodes.

### [Performance Insights via Selenium](level-4/06-performance-insights.md)

**Q141.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <p>x</p>
t = driver.execute_script('return performance.getEntriesByType("navigation")[0].type')
print(t)
print(driver.execute_script('return typeof performance.now()'))
```

**Answer:** navigate
number

The Navigation Timing API is available through execute_script. Verified by running it with Selenium and headless Chrome.

**Q142.** What does the Navigation Timing API give you?

**Answer:** Timings of page load phases

Useful for coarse performance checks.

**Q143.** Why is browser-based performance testing noisy?

**Answer:** Variability in machine, network and scheduling

Use dedicated tools for load testing.

**Q144.** What can Chrome DevTools Protocol add in Selenium 4?

**Answer:** Network, performance and console events (Chromium browsers)

driver.execute_cdp_cmd.

### [CI Integration: Running Selenium in a Pipeline](level-4/07-ci-integration.md)

**Q145.** What should a CI run of Selenium tests do on failure?

**Answer:** Upload screenshots, logs and reports as artefacts

Debug without access to the machine.

**Q146.** Why pin browser and driver versions in CI?

**Answer:** Reproducible results

Use containers or Selenium Manager with pinned versions.

**Q147.** How do you keep CI Selenium suites fast?

**Answer:** Parallelise, run smoke on every commit and full suites nightly

Tiered suites.

**Q148.** Where should secrets for test accounts live in CI?

**Answer:** In the CI secret store

Never commit.

### [Selenium vs. Playwright: An Honest Comparison](level-4/08-selenium-vs-playwright.md)

**Q149.** What is a notable advantage of Playwright over Selenium?

**Answer:** Built-in auto-waiting and tracing, with a single API across browsers

Selenium has a wider language and browser ecosystem.

**Q150.** What is a notable advantage of Selenium?

**Answer:** Mature, standard W3C protocol with broad language and Grid support

Many organisations already use it.

**Q151.** Does Selenium auto-wait for elements before actions?

**Answer:** No, you must synchronise explicitly

Playwright does by default.

**Q152.** What decides which tool to use?

**Answer:** Team skills, existing assets, browser/language needs and maintenance cost

No universal winner.

### [Selenium Anti-Patterns](level-4/09-selenium-antipatterns.md)

**Q153.** What is the sleep antipattern?

**Answer:** Using time.sleep instead of waiting for a condition

Flaky and slow.

**Q154.** What is wrong with XPath indexes like (//div)[7]?

**Answer:** Brittle; any layout change breaks them

Use semantic attributes.

**Q155.** Why avoid tests that depend on each other?

**Answer:** Failures cascade and parallel runs break

Independent tests.

**Q156.** Why not test everything through the UI?

**Answer:** UI tests are slow and brittle; push logic checks down to API or unit tests

Test pyramid.

### [Capstone: A Complete Test Framework](level-4/10-capstone-complete-test-framework.md)

**Q157.** What is the capstone's goal?

**Answer:** A complete Selenium framework with POM, fixtures, reporting, parallel runs and CI

Show maintainable engineering.

**Q158.** What belongs in the README?

**Answer:** Setup, running tests, configuration and design decisions

Reviewers start there.

**Q159.** What evidence proves the framework is reliable?

**Answer:** Repeated green runs, no sleeps and failure diagnostics in CI

Measure flakiness.

**Q160.** What should you log for each test?

**Answer:** Steps, URL and artefacts on failure

Helpful but safe.

## Mixed questions across all levels

**Q161.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <button id=b disabled>go</button><p id=out></p><script>setTimeout(()=>b.disabled=false,300);b.onclick=()=>setTimeout(()=>out.textContent='ok',200)</script>
w = WebDriverWait(driver, 3)
w.until(EC.element_to_be_clickable((By.ID, 'b'))).click()
print(w.until(EC.text_to_be_present_in_element((By.ID, 'out'), 'ok')))
```

**Answer:** True

An explicit wait polls the condition until it is truthy; text_to_be_present_in_element returns True. Verified by running it with Selenium and headless Chrome.

**Q162.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <p>top</p><iframe srcdoc="<p>a</p><p>b</p>"></iframe>
print(len(driver.find_elements(By.CSS_SELECTOR, 'p')))
driver.switch_to.frame(driver.find_element(By.TAG_NAME, 'iframe'))
print(len(driver.find_elements(By.CSS_SELECTOR, 'p')))
```

**Answer:** 1
2

Elements inside an iframe are only visible after switching into it. Verified by running it with Selenium and headless Chrome.

**Q163.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <div id=c><b id=x>old</b></div><button id=r onclick="c.innerHTML='<b id=x>new</b>'">r</button>
e = driver.find_element(By.ID, 'x')
driver.find_element(By.ID, 'r').click()
print(driver.find_element(By.ID, 'x').text)
try:
    e.click()
except StaleElementReferenceException:
    print('stale')
```

**Answer:** new
stale

Re-rendering replaces the DOM node; the old reference goes stale. Verified by running it with Selenium and headless Chrome.

**Q164.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <ul><li>a</li><li>b</li><li>c</li></ul>
print(driver.execute_script('return arguments[0] * 2', 21))
print(driver.execute_script('return document.querySelectorAll("li").length'))
```

**Answer:** 42
3

execute_script returns JSON-serialisable results and takes arguments. Verified by running it with Selenium and headless Chrome.

**Q165.** What does this print?

```python
# page (loaded with driver.get('data:text/html,...')):
# <p>x</p>
print(len(driver.find_elements(By.ID, 'nope')))
try:
    driver.find_element(By.ID, 'nope')
except NoSuchElementException:
    print('raised')
```

**Answer:** 0
raised

find_elements returns an empty list; find_element raises. Verified by running it with Selenium and headless Chrome.

**Q166.** Why is time.sleep(5) after a click a poor fix for a flaky test?

**Answer:** It is either too short (still flaky) or too long (slow) and never checks the real condition

Wait for the condition the next step needs.

**Q167.** A test passes alone but fails in the full suite. What is the likeliest cause?

**Answer:** Shared state or ordering dependence between tests

Make each test create and clean its own data.

**Q168.** Why keep locators in page objects rather than in tests?

**Answer:** A UI change needs one edit instead of changing every test

Tests state intent; pages know the UI.

**Q169.** Why not keep WebElement objects as long-lived page-object fields?

**Answer:** They go stale when the DOM re-renders; store locators and look up on use

Locators survive re-renders.

**Q170.** What does driver.quit() do that close() does not?

**Answer:** Ends the whole session and driver process, closing all windows

Always quit in teardown.

**Q171.** Why avoid mixing implicit and explicit waits?

**Answer:** Their timeouts can combine unpredictably, making failures slow and confusing

Keep implicit at zero.

**Q172.** Which locator is usually the most stable?

**Answer:** A dedicated id or data-testid attribute

Agree stable test hooks with developers.

**Q173.** How do you handle a JavaScript confirm() dialog?

**Answer:** switch_to.alert then accept() or dismiss()

Alerts are not part of the DOM.

**Q174.** How do you reach into an open shadow root?

**Answer:** element.shadow_root, then find elements within it

Normal lookups cannot cross the boundary.

**Q175.** What do you pass to a file input to upload a file?

**Answer:** send_keys with the file's absolute path

Do not automate the native dialog.

**Q176.** What does Selenium Grid add?

**Answer:** Parallel sessions across machines and browsers behind one endpoint

Tests use webdriver.Remote.

**Q177.** Why capture screenshots and page source on failure?

**Answer:** Without them CI failures are hard to diagnose after the browser is gone

Attach artefacts to the report.

**Q178.** What is the honest use of automatic test retries?

**Answer:** A temporary measure while tracking and fixing the root cause

Retries mask flakiness.

**Q179.** Which test is best pushed down from the UI to an API or unit test?

**Answer:** Business-rule logic with many input combinations

UI tests are slow; keep them for journeys.

## Practical coding questions

### A page object with a synchronised search

Build a ShopPage page object for a small search page whose results appear 400 ms after you click Search. Locators live in the class, search() waits for the status text instead of sleeping, and tests only call page methods. This runs against real headless Chrome with Selenium, on a local data: page that this exercise provides.

Starter code:

```
import urllib.parse
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

HTML = """
<h1>Shop</h1>
<input id=search placeholder='Search'>
<button id=go>Search</button>
<p id=st>idle</p>
<ul id=res></ul>
<script>
const ITEMS = ['apple pie', 'apple tart', 'banana bread', 'cherry pie'];
go.onclick = () => {
  st.textContent = 'loading';
  res.innerHTML = '';
  setTimeout(() => {
    const q = search.value.toLowerCase();
    ITEMS.filter(i => i.includes(q)).forEach(i => { const li = document.createElement('li'); li.className = 'item'; li.textContent = i; res.appendChild(li); });
    st.textContent = 'done';
  }, 400);
};
</script>
"""

# TODO: class ShopPage (page object)
#   locators as (By, value) tuples: SEARCH #search, GO #go, STATUS #st, ITEMS '#res li.item'
#   open()        loads the data URL and returns self
#   search(term)  clear, type, click Search, then WAIT until the status text contains "done" (no sleeps); return self
#   results()     list of the visible result texts

opts = webdriver.ChromeOptions()
opts.add_argument('--headless=new')
driver = webdriver.Chrome(options=opts)
try:
    page = ShopPage(driver).open()
    print(page.search('apple').results())
    print(page.search('pie').results())
    print(page.search('zzz').results())
    print(len(page.search('').results()))
finally:
    driver.quit()
```

Expected output:

```text
['apple pie', 'apple tart']
['apple pie', 'cherry pie']
[]
4
```

??? success "Show a solution"

    ```
    import urllib.parse
    from selenium import webdriver
    from selenium.webdriver.common.by import By
    from selenium.webdriver.support.ui import WebDriverWait
    from selenium.webdriver.support import expected_conditions as EC

    HTML = """
    <h1>Shop</h1>
    <input id=search placeholder='Search'>
    <button id=go>Search</button>
    <p id=st>idle</p>
    <ul id=res></ul>
    <script>
    const ITEMS = ['apple pie', 'apple tart', 'banana bread', 'cherry pie'];
    go.onclick = () => {
      st.textContent = 'loading';
      res.innerHTML = '';
      setTimeout(() => {
        const q = search.value.toLowerCase();
        ITEMS.filter(i => i.includes(q)).forEach(i => { const li = document.createElement('li'); li.className = 'item'; li.textContent = i; res.appendChild(li); });
        st.textContent = 'done';
      }, 400);
    };
    </script>
    """

    class ShopPage:
        SEARCH = (By.ID, 'search')
        GO = (By.ID, 'go')
        STATUS = (By.ID, 'st')
        ITEMS = (By.CSS_SELECTOR, '#res li.item')

        def __init__(self, driver):
            self.d = driver
            self.wait = WebDriverWait(driver, 5)

        def open(self):
            self.d.get('data:text/html;charset=utf-8,' + urllib.parse.quote(HTML))
            return self

        def search(self, term):
            box = self.d.find_element(*self.SEARCH)
            box.clear()
            box.send_keys(term)
            self.d.find_element(*self.GO).click()
            self.wait.until(EC.text_to_be_present_in_element(self.STATUS, 'done'))
            return self

        def results(self):
            return [e.text for e in self.d.find_elements(*self.ITEMS)]

    opts = webdriver.ChromeOptions()
    opts.add_argument('--headless=new')
    driver = webdriver.Chrome(options=opts)
    try:
        page = ShopPage(driver).open()
        print(page.search('apple').results())
        print(page.search('pie').results())
        print(page.search('zzz').results())
        print(len(page.search('').results()))
    finally:
        driver.quit()
    ```

    The click sets the status to 'loading' immediately, so waiting for 'done' with an explicit wait is race-free. Locators as (By, value) tuples work with both find_element(*loc) and expected conditions. Verified by running it with Selenium 4 and headless Chrome.

### A resilient custom wait

Write wait_until, a helper that polls a condition, tolerates stale-element errors during re-renders and raises a clear TimeoutException with context. Use it to wait for a toast and for a list to reach three items after an asynchronous re-render.

Starter code:

```
import time, urllib.parse
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.common.exceptions import StaleElementReferenceException, TimeoutException

HTML = """
<ul id=list><li>one</li><li>two</li></ul>
<button id=add>add</button>
<div id=toast style='display:none'>saved</div>
<script>
add.onclick = () => setTimeout(() => {
  list.innerHTML = list.innerHTML + '<li>' + ['three','four','five'][list.children.length - 2] + '</li>';
  toast.style.display = 'block';
}, 300);
</script>
"""

# TODO: wait_until(driver, condition, timeout=3.0, poll=0.1, message='condition not met')
#   call condition(driver) until it returns something truthy and return that value;
#   treat StaleElementReferenceException as "not yet" and remember it;
#   on timeout raise TimeoutException with the message (and the last stale error's class name, if any)
def texts(driver):
    return [e.text for e in driver.find_elements(By.CSS_SELECTOR, '#list li')]

opts = webdriver.ChromeOptions()
opts.add_argument('--headless=new')
driver = webdriver.Chrome(options=opts)
try:
    driver.get('data:text/html;charset=utf-8,' + urllib.parse.quote(HTML))
    first = driver.find_element(By.CSS_SELECTOR, '#list li')
    driver.find_element(By.ID, 'add').click()
    wait_until(driver, lambda d: d.find_element(By.ID, 'toast').is_displayed())
    try:
        first.text
        print('first still attached')
    except StaleElementReferenceException:
        print('first went stale')
    print(wait_until(driver, lambda d: len(texts(d)) == 3 and texts(d)))
    try:
        wait_until(driver, lambda d: len(texts(d)) == 9, timeout=0.5, message='nine items')
    except TimeoutException as e:
        print('timeout:', e.msg)
finally:
    driver.quit()
```

Expected output:

```text
first went stale
['one', 'two', 'three']
timeout: nine items
```

??? success "Show a solution"

    ```
    import time, urllib.parse
    from selenium import webdriver
    from selenium.webdriver.common.by import By
    from selenium.common.exceptions import StaleElementReferenceException, TimeoutException

    HTML = """
    <ul id=list><li>one</li><li>two</li></ul>
    <button id=add>add</button>
    <div id=toast style='display:none'>saved</div>
    <script>
    add.onclick = () => setTimeout(() => {
      list.innerHTML = list.innerHTML + '<li>' + ['three','four','five'][list.children.length - 2] + '</li>';
      toast.style.display = 'block';
    }, 300);
    </script>
    """

    def wait_until(driver, condition, timeout=3.0, poll=0.1, message='condition not met'):
        end = time.monotonic() + timeout
        last = None
        while time.monotonic() < end:
            try:
                value = condition(driver)
                if value:
                    return value
            except StaleElementReferenceException as e:
                last = e
            time.sleep(poll)
        raise TimeoutException(message + (f' (last error: {type(last).__name__})' if last else ''))

    def texts(driver):
        return [e.text for e in driver.find_elements(By.CSS_SELECTOR, '#list li')]

    opts = webdriver.ChromeOptions()
    opts.add_argument('--headless=new')
    driver = webdriver.Chrome(options=opts)
    try:
        driver.get('data:text/html;charset=utf-8,' + urllib.parse.quote(HTML))
        first = driver.find_element(By.CSS_SELECTOR, '#list li')
        driver.find_element(By.ID, 'add').click()
        wait_until(driver, lambda d: d.find_element(By.ID, 'toast').is_displayed())
        try:
            first.text
            print('first still attached')
        except StaleElementReferenceException:
            print('first went stale')
        print(wait_until(driver, lambda d: len(texts(d)) == 3 and texts(d)))
        try:
            wait_until(driver, lambda d: len(texts(d)) == 9, timeout=0.5, message='nine items')
        except TimeoutException as e:
            print('timeout:', e.msg)
    finally:
        driver.quit()
    ```

    Selenium's own WebDriverWait does the same job; writing it once shows the mechanics: poll, swallow expected transient errors, and explain the timeout. The re-render replaces the list, so the original element goes stale. Verified by running it with Selenium 4 and headless Chrome.

---

More free practice: the [Selenium Mastery Path](index.md) curriculum, the [Interview Prep Pack](https://learnmasterypath.com/interview-pack/) and the [free resume ATS check](https://learnmasterypath.com/resume/).
