---
title: "Final Quiz"
description: "Test what you have learned across all four levels of Selenium Mastery Path with a 19-question final quiz."
---

# Final Quiz

19 questions drawn from all four levels of this course. You need **70% (14 out of 19)** to pass —
you can retake it as many times as you like. Your best result is saved in this browser.

<div id="mp-quiz-root"></div>

<script type="application/json" id="mp-quiz-data">
[
  {
    "question": "What does this print?\n```python\n# page (loaded with driver.get('data:text/html,...')):\n# <button id=b disabled>go<\/button><p id=out><\/p><script>setTimeout(()=>b.disabled=false,300);b.onclick=()=>setTimeout(()=>out.textContent='ok',200)<\/script>\nw = WebDriverWait(driver, 3)\nw.until(EC.element_to_be_clickable((By.ID, 'b'))).click()\nprint(w.until(EC.text_to_be_present_in_element((By.ID, 'out'), 'ok')))\n```\n",
    "options": [
      "False",
      "True",
      "ok",
      "None"
    ],
    "correctIndex": 1,
    "explanation": "An explicit wait polls the condition until it is truthy; text_to_be_present_in_element returns True. Verified by running it with Selenium and headless Chrome."
  },
  {
    "question": "What does this print?\n```python\n# page (loaded with driver.get('data:text/html,...')):\n# <p>top<\/p><iframe srcdoc=\"<p>a<\/p><p>b<\/p>\"><\/iframe>\nprint(len(driver.find_elements(By.CSS_SELECTOR, 'p')))\ndriver.switch_to.frame(driver.find_element(By.TAG_NAME, 'iframe'))\nprint(len(driver.find_elements(By.CSS_SELECTOR, 'p')))\n```\n",
    "options": [
      "3\n2",
      "1\n1",
      "2\n2",
      "1\n2"
    ],
    "correctIndex": 3,
    "explanation": "Elements inside an iframe are only visible after switching into it. Verified by running it with Selenium and headless Chrome."
  },
  {
    "question": "What does this print?\n```python\n# page (loaded with driver.get('data:text/html,...')):\n# <div id=c><b id=x>old<\/b><\/div><button id=r onclick=\"c.innerHTML='<b id=x>new<\/b>'\">r<\/button>\ne = driver.find_element(By.ID, 'x')\ndriver.find_element(By.ID, 'r').click()\nprint(driver.find_element(By.ID, 'x').text)\ntry:\n    e.click()\nexcept StaleElementReferenceException:\n    print('stale')\n```\n",
    "options": [
      "new\nnew",
      "new\nstale",
      "stale\nnew",
      "old\nstale"
    ],
    "correctIndex": 1,
    "explanation": "Re-rendering replaces the DOM node; the old reference goes stale. Verified by running it with Selenium and headless Chrome."
  },
  {
    "question": "What does this print?\n```python\n# page (loaded with driver.get('data:text/html,...')):\n# <ul><li>a<\/li><li>b<\/li><li>c<\/li><\/ul>\nprint(driver.execute_script('return arguments[0] * 2', 21))\nprint(driver.execute_script('return document.querySelectorAll(\"li\").length'))\n```\n",
    "options": [
      "21\n3",
      "None\n3",
      "42\n3",
      "42\n0"
    ],
    "correctIndex": 2,
    "explanation": "execute_script returns JSON-serialisable results and takes arguments. Verified by running it with Selenium and headless Chrome."
  },
  {
    "question": "What does this print?\n```python\n# page (loaded with driver.get('data:text/html,...')):\n# <p>x<\/p>\nprint(len(driver.find_elements(By.ID, 'nope')))\ntry:\n    driver.find_element(By.ID, 'nope')\nexcept NoSuchElementException:\n    print('raised')\n```\n",
    "options": [
      "0\n0",
      "0\nraised",
      "raised\n0",
      "None\nraised"
    ],
    "correctIndex": 1,
    "explanation": "find_elements returns an empty list; find_element raises. Verified by running it with Selenium and headless Chrome."
  },
  {
    "question": "Why is time.sleep(5) after a click a poor fix for a flaky test?",
    "options": [
      "Sleep is illegal",
      "It is either too short (still flaky) or too long (slow) and never checks the real condition",
      "It stops the driver",
      "It clears cookies"
    ],
    "correctIndex": 1,
    "explanation": "Wait for the condition the next step needs."
  },
  {
    "question": "A test passes alone but fails in the full suite. What is the likeliest cause?",
    "options": [
      "The browser version",
      "The monitor",
      "Shared state or ordering dependence between tests",
      "The Python version"
    ],
    "correctIndex": 2,
    "explanation": "Make each test create and clean its own data."
  },
  {
    "question": "Why keep locators in page objects rather than in tests?",
    "options": [
      "It is faster",
      "Selenium requires it",
      "It avoids waits",
      "A UI change needs one edit instead of changing every test"
    ],
    "correctIndex": 3,
    "explanation": "Tests state intent; pages know the UI."
  },
  {
    "question": "Why not keep WebElement objects as long-lived page-object fields?",
    "options": [
      "They use memory",
      "They are private",
      "Selenium forbids it",
      "They go stale when the DOM re-renders; store locators and look up on use"
    ],
    "correctIndex": 3,
    "explanation": "Locators survive re-renders."
  },
  {
    "question": "What does driver.quit() do that close() does not?",
    "options": [
      "Clears cookies only",
      "Ends the whole session and driver process, closing all windows",
      "Closes one tab",
      "Takes a screenshot"
    ],
    "correctIndex": 1,
    "explanation": "Always quit in teardown."
  },
  {
    "question": "Why avoid mixing implicit and explicit waits?",
    "options": [
      "Their timeouts can combine unpredictably, making failures slow and confusing",
      "They conflict in Python only",
      "Implicit waits are removed",
      "Explicit waits stop working"
    ],
    "correctIndex": 0,
    "explanation": "Keep implicit at zero."
  },
  {
    "question": "Which locator is usually the most stable?",
    "options": [
      "An index like (//div)[7]",
      "A dedicated id or data-testid attribute",
      "Visible text that marketing edits",
      "An absolute XPath"
    ],
    "correctIndex": 1,
    "explanation": "Agree stable test hooks with developers."
  },
  {
    "question": "How do you handle a JavaScript confirm() dialog?",
    "options": [
      "Ignore it",
      "find_element",
      "click the OK button in the DOM",
      "switch_to.alert then accept() or dismiss()"
    ],
    "correctIndex": 3,
    "explanation": "Alerts are not part of the DOM."
  },
  {
    "question": "How do you reach into an open shadow root?",
    "options": [
      "XPath //shadow",
      "driver.find_element only",
      "execute_script is forbidden",
      "element.shadow_root, then find elements within it"
    ],
    "correctIndex": 3,
    "explanation": "Normal lookups cannot cross the boundary."
  },
  {
    "question": "What do you pass to a file input to upload a file?",
    "options": [
      "send_keys with the file's absolute path",
      "Click the OS dialog",
      "execute_script only",
      "drag and drop"
    ],
    "correctIndex": 0,
    "explanation": "Do not automate the native dialog."
  },
  {
    "question": "What does Selenium Grid add?",
    "options": [
      "A faster browser",
      "A test runner",
      "Parallel sessions across machines and browsers behind one endpoint",
      "Visual diffing"
    ],
    "correctIndex": 2,
    "explanation": "Tests use webdriver.Remote."
  },
  {
    "question": "Why capture screenshots and page source on failure?",
    "options": [
      "They fix flakiness",
      "They speed up tests",
      "Without them CI failures are hard to diagnose after the browser is gone",
      "Required by Selenium"
    ],
    "correctIndex": 2,
    "explanation": "Attach artefacts to the report."
  },
  {
    "question": "What is the honest use of automatic test retries?",
    "options": [
      "A permanent fix",
      "Faster tests",
      "A temporary measure while tracking and fixing the root cause",
      "A way to hide failures"
    ],
    "correctIndex": 2,
    "explanation": "Retries mask flakiness."
  },
  {
    "question": "Which test is best pushed down from the UI to an API or unit test?",
    "options": [
      "Login happy path",
      "Checkout smoke test",
      "Cross-browser rendering",
      "Business-rule logic with many input combinations"
    ],
    "correctIndex": 3,
    "explanation": "UI tests are slow; keep them for journeys."
  }
]
</script>

## Coding challenges

Try these on your own computer, then compare your output. Nothing you write is sent anywhere.

### A page object with a synchronised search

Build a ShopPage page object for a small search page whose results appear 400 ms after you click Search. Locators live in the class, search() waits for the status text instead of sleeping, and tests only call page methods. This runs against real headless Chrome with Selenium, on a local data: page that this exercise provides.

**Starter code**

```python
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

**Expected output**

```text
['apple pie', 'apple tart']
['apple pie', 'cherry pie']
[]
4
```

<details><summary>Show solution and explanation</summary>

<pre><code>import urllib.parse
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

HTML = &quot;&quot;&quot;
&lt;h1&gt;Shop&lt;/h1&gt;
&lt;input id=search placeholder=&#x27;Search&#x27;&gt;
&lt;button id=go&gt;Search&lt;/button&gt;
&lt;p id=st&gt;idle&lt;/p&gt;
&lt;ul id=res&gt;&lt;/ul&gt;
&lt;script&gt;
const ITEMS = [&#x27;apple pie&#x27;, &#x27;apple tart&#x27;, &#x27;banana bread&#x27;, &#x27;cherry pie&#x27;];
go.onclick = () =&gt; {
  st.textContent = &#x27;loading&#x27;;
  res.innerHTML = &#x27;&#x27;;
  setTimeout(() =&gt; {
    const q = search.value.toLowerCase();
    ITEMS.filter(i =&gt; i.includes(q)).forEach(i =&gt; { const li = document.createElement(&#x27;li&#x27;); li.className = &#x27;item&#x27;; li.textContent = i; res.appendChild(li); });
    st.textContent = &#x27;done&#x27;;
  }, 400);
};
&lt;/script&gt;
&quot;&quot;&quot;

class ShopPage:
    SEARCH = (By.ID, &#x27;search&#x27;)
    GO = (By.ID, &#x27;go&#x27;)
    STATUS = (By.ID, &#x27;st&#x27;)
    ITEMS = (By.CSS_SELECTOR, &#x27;#res li.item&#x27;)

    def __init__(self, driver):
        self.d = driver
        self.wait = WebDriverWait(driver, 5)

    def open(self):
        self.d.get(&#x27;data:text/html;charset=utf-8,&#x27; + urllib.parse.quote(HTML))
        return self

    def search(self, term):
        box = self.d.find_element(*self.SEARCH)
        box.clear()
        box.send_keys(term)
        self.d.find_element(*self.GO).click()
        self.wait.until(EC.text_to_be_present_in_element(self.STATUS, &#x27;done&#x27;))
        return self

    def results(self):
        return [e.text for e in self.d.find_elements(*self.ITEMS)]

opts = webdriver.ChromeOptions()
opts.add_argument(&#x27;--headless=new&#x27;)
driver = webdriver.Chrome(options=opts)
try:
    page = ShopPage(driver).open()
    print(page.search(&#x27;apple&#x27;).results())
    print(page.search(&#x27;pie&#x27;).results())
    print(page.search(&#x27;zzz&#x27;).results())
    print(len(page.search(&#x27;&#x27;).results()))
finally:
    driver.quit()
</code></pre>

<p>The click sets the status to &#x27;loading&#x27; immediately, so waiting for &#x27;done&#x27; with an explicit wait is race-free. Locators as (By, value) tuples work with both find_element(*loc) and expected conditions. Verified by running it with Selenium 4 and headless Chrome.</p>
</details>

### A resilient custom wait

Write wait_until, a helper that polls a condition, tolerates stale-element errors during re-renders and raises a clear TimeoutException with context. Use it to wait for a toast and for a list to reach three items after an asynchronous re-render.

**Starter code**

```python
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

**Expected output**

```text
first went stale
['one', 'two', 'three']
timeout: nine items
```

<details><summary>Show solution and explanation</summary>

<pre><code>import time, urllib.parse
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.common.exceptions import StaleElementReferenceException, TimeoutException

HTML = &quot;&quot;&quot;
&lt;ul id=list&gt;&lt;li&gt;one&lt;/li&gt;&lt;li&gt;two&lt;/li&gt;&lt;/ul&gt;
&lt;button id=add&gt;add&lt;/button&gt;
&lt;div id=toast style=&#x27;display:none&#x27;&gt;saved&lt;/div&gt;
&lt;script&gt;
add.onclick = () =&gt; setTimeout(() =&gt; {
  list.innerHTML = list.innerHTML + &#x27;&lt;li&gt;&#x27; + [&#x27;three&#x27;,&#x27;four&#x27;,&#x27;five&#x27;][list.children.length - 2] + &#x27;&lt;/li&gt;&#x27;;
  toast.style.display = &#x27;block&#x27;;
}, 300);
&lt;/script&gt;
&quot;&quot;&quot;

def wait_until(driver, condition, timeout=3.0, poll=0.1, message=&#x27;condition not met&#x27;):
    end = time.monotonic() + timeout
    last = None
    while time.monotonic() &lt; end:
        try:
            value = condition(driver)
            if value:
                return value
        except StaleElementReferenceException as e:
            last = e
        time.sleep(poll)
    raise TimeoutException(message + (f&#x27; (last error: {type(last).__name__})&#x27; if last else &#x27;&#x27;))

def texts(driver):
    return [e.text for e in driver.find_elements(By.CSS_SELECTOR, &#x27;#list li&#x27;)]

opts = webdriver.ChromeOptions()
opts.add_argument(&#x27;--headless=new&#x27;)
driver = webdriver.Chrome(options=opts)
try:
    driver.get(&#x27;data:text/html;charset=utf-8,&#x27; + urllib.parse.quote(HTML))
    first = driver.find_element(By.CSS_SELECTOR, &#x27;#list li&#x27;)
    driver.find_element(By.ID, &#x27;add&#x27;).click()
    wait_until(driver, lambda d: d.find_element(By.ID, &#x27;toast&#x27;).is_displayed())
    try:
        first.text
        print(&#x27;first still attached&#x27;)
    except StaleElementReferenceException:
        print(&#x27;first went stale&#x27;)
    print(wait_until(driver, lambda d: len(texts(d)) == 3 and texts(d)))
    try:
        wait_until(driver, lambda d: len(texts(d)) == 9, timeout=0.5, message=&#x27;nine items&#x27;)
    except TimeoutException as e:
        print(&#x27;timeout:&#x27;, e.msg)
finally:
    driver.quit()
</code></pre>

<p>Selenium&#x27;s own WebDriverWait does the same job; writing it once shows the mechanics: poll, swallow expected transient errors, and explain the timeout. The re-render replaces the list, so the original element goes stale. Verified by running it with Selenium 4 and headless Chrome.</p>
</details>
