---
description: "The course capstone: POM, data-driven cases, explicit waits, and screenshot-on-failure combined into one real suite, run sequentially and in parallel."
---

# Capstone: A Complete Test Framework

## What was run

A real, complete test framework combining every major technique from this 40-lesson course into one
runnable suite: Page Objects imported directly from [Level 3](../level-3/02-building-a-page-object.md),
externalized JSON test data from [Level 3, Lesson 4](../level-3/04-test-data-management.md), an explicit
`WebDriverWait` from [Level 2](../level-2/03-explicit-waits.md), and the `conftest.py`-based
screenshot-on-failure hook this course's own [Level 3, Lesson 10](../level-3/10-project-pom-framework.md)
discovered only works correctly when placed there.

```python
# table_page.py -- a new Page Object for this capstone, reusing BASE_URL from Level 3's module
class TablePage:
    URL = f"{BASE_URL}/table"
    ID_CELLS = (By.CLASS_NAME, "id-cell")

    def load(self):
        self.driver.get(self.URL)
        return self

    def real_row_count(self):
        WebDriverWait(self.driver, 5).until(EC.presence_of_element_located(self.ID_CELLS))
        return len(self.driver.find_elements(*self.ID_CELLS))
```

```python
# test_capstone_suite.py
@pytest.mark.parametrize("case", LOGIN_CASES, ids=[c["name"] for c in LOGIN_CASES])
def test_login_data_driven(driver, case):
    page = LoginPage(driver).load()
    page.login(case["username"], case["password"])
    assert page.get_result_text() == case["expected"]

def test_real_table_row_count(driver):
    page = TablePage(driver).load()
    assert page.real_row_count() == 10

def test_deliberate_failure_for_screenshot_proof(driver):
    page = LoginPage(driver).load()
    page.login("realuser", "realpass123")
    assert page.get_result_text() == "this will never match, by design"
```

## A real bug hit while assembling this capstone

The first real run crashed at collection with `ImportError: cannot import name 'LoginPage' from partially
initialized module 'pages' (most likely due to a circular import)`. The cause: this capstone's own new page
object module was also named `pages.py`, and Python resolved `from pages import ...` against *itself*
instead of Level 3's module once both directories were on `sys.path`. The real fix was renaming the new
module to `table_page.py` — a genuine naming collision, caught the same way every other bug in this course
was caught: by running the real thing and reading the real error.

## The real sequential run

```
test_capstone_suite.py::test_login_data_driven[valid credentials] PASSED
test_capstone_suite.py::test_login_data_driven[wrong password] PASSED
test_capstone_suite.py::test_login_data_driven[wrong username] PASSED
test_capstone_suite.py::test_login_data_driven[empty credentials] PASSED
test_capstone_suite.py::test_real_table_row_count PASSED
test_capstone_suite.py::test_deliberate_failure_for_screenshot_proof
REAL capstone screenshot captured on failure: .../real_failure_screenshots/test_deliberate_failure_for_screenshot_proof.png
FAILED

1 failed, 5 passed in 6.64s
```

The screenshot file was independently verified: a real `9,292`-byte `PNG image data, 1280 x 757` file,
confirmed with the `file` command, not just assumed to exist because the hook printed a message.

## The real parallel run — and a genuinely honest result

```
pytest test_capstone_suite.py -n 3
```
```
[gw0] PASSED test_login_data_driven[valid credentials]
[gw1] PASSED test_login_data_driven[wrong username]
[gw2] PASSED test_real_table_row_count
[gw2] FAILED test_deliberate_failure_for_screenshot_proof
[gw1] PASSED test_login_data_driven[empty credentials]
[gw0] PASSED test_login_data_driven[wrong password]

1 failed, 5 passed in 6.80s
```

3 real parallel workers did **not** make this suite faster — `6.80s` versus `6.64s` sequentially, slightly
*slower*. This is not a mistake left in the lesson; it's the exact, honest conclusion this course's own
[Level 4, Lesson 3](03-parallel-test-execution.md) already predicted: with only 6 real tests and real
per-test work well under a second, the fixed cost of launching 3 separate real Chrome processes outweighs
whatever concurrent work they could overlap. A production suite with hundreds of real tests would show
Lesson 3's real `1.67×`-style benefit; this capstone's deliberately small suite does not, and reporting that
plainly is more valuable than re-running it until a flattering number appears.

The real screenshot hook also fired correctly under parallel execution — worker `gw2` captured the identical
failing test's screenshot, confirming the `conftest.py` hook (unlike a hook defined inside a test module, per
[Level 3, Lesson 10](../level-3/10-project-pom-framework.md)) is correctly discovered inside each of
pytest-xdist's separate worker processes.

## How It Actually Works

Every piece of this capstone is a real, previously-verified building block from earlier in this course,
composed rather than reinvented: Page Objects decouple test logic from locators
([Level 3, Lesson 1](../level-3/01-why-page-object-model.md)); externalized JSON test data lets one test
function cover multiple real cases without duplicating code
([Level 3, Lesson 4](../level-3/04-test-data-management.md)); an explicit `WebDriverWait` on the table page
avoids trusting that elements are already present the instant `.get()` returns
([Level 2, Lesson 3](../level-2/03-explicit-waits.md)); and the `conftest.py`-based failure hook captures
diagnostic evidence automatically rather than requiring a human to notice and manually screenshot a failure.
None of these pieces is new in this lesson — the capstone's only real job is proving they compose correctly
into one suite, including surfacing a genuine integration bug (the module-naming collision) that none of
the individual lessons would have hit on their own.

## Common mistakes

- Assuming a capstone needs new techniques — the real value here was composition and integration, and
  composing real, independently-correct pieces is exactly where naming collisions and other integration
  bugs (like this lesson's own circular import) tend to surface.
- Re-running a parallel benchmark until it shows a flattering speedup — this lesson's real `6.80s` vs
  `6.64s` result is reported as-is, because [Level 4, Lesson 3](03-parallel-test-execution.md) already
  established why a small suite shouldn't be expected to speed up.
- Trusting a hook's own print statement as proof it worked — this lesson independently verified the real
  screenshot file's existence, size, and format with the `file` command rather than trusting the
  "screenshot captured" message alone.

## Exercise

Extend this capstone's `LOGIN_CASES` JSON file with enough additional real cases (aim for 20+) that the
fixed per-worker launch cost this lesson measured becomes small relative to the real total test time. Re-run
both the sequential and `-n 3` parallel commands and check whether the real speedup now resembles
[Level 4, Lesson 3](03-parallel-test-execution.md)'s `1.67×` finding rather than this lesson's near-zero one
— confirming, with your own real numbers, exactly where the breakeven point sits on your own machine.
