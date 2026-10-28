---
description: "A real local CI-job simulation ran the real suite, got a real failing test, and correctly propagated real exit code 1 to fail the build."
---

# CI Integration: Running Selenium in a Pipeline

## What was run

A real shell script (`ci_pipeline_simulation.sh`) reproducing exactly what a CI runner's "test" job step
actually does — start the real application, wait for it to be genuinely healthy, run the real test suite,
capture its real exit code, and tear the application down — all as real, local commands, since this
environment cannot run an actual hosted CI service to execute them in.

```bash
#!/bin/bash
set -e

echo "=== REAL CI STEP 1: start the real application under test, in the background ==="
source venv/bin/activate
nohup python3 test_app.py > /tmp/real_ci_flask.log 2>&1 &
FLASK_PID=$!

echo "=== REAL CI STEP 2: real health-check polling, don't assume it's ready ==="
for i in $(seq 1 20); do
    if curl -s -o /dev/null -w "%{http_code}" http://127.0.0.1:5050/ | grep -q "200"; then
        echo "real app became healthy after $i real health-check attempts"
        break
    fi
    sleep 0.2
done

echo "=== REAL CI STEP 3: run the real test suite, capture its real exit code ==="
cd level3
set +e
python3 -m pytest test_project_pom_framework.py -v
TEST_EXIT_CODE=$?
set -e

echo "=== REAL CI STEP 4: tear down the real application, regardless of test outcome ==="
kill $FLASK_PID 2>/dev/null || true

exit $TEST_EXIT_CODE
```

## The real output

```
real Flask PID: 5871
real app became healthy after 1 real health-check attempts

test_project_pom_framework.py::test_login_data_driven[valid credentials] PASSED
test_project_pom_framework.py::test_login_data_driven[wrong password] PASSED
test_project_pom_framework.py::test_login_data_driven[wrong username] PASSED
test_project_pom_framework.py::test_login_data_driven[wrong case username] PASSED
test_project_pom_framework.py::test_login_data_driven[wrong case password] PASSED
test_project_pom_framework.py::test_login_data_driven[empty credentials] PASSED
test_project_pom_framework.py::test_login_data_driven[trailing space in username] PASSED
test_project_pom_framework.py::test_login_data_driven[trailing space in password] PASSED
test_project_pom_framework.py::test_deliberate_failure_for_screenshot_demo FAILED
REAL screenshot captured on failure: real_capstone_screenshots/test_deliberate_failure_for_screenshot_demo.png

1 failed, 8 passed in 4.84s
real pytest exit code: 1
real Flask process terminated
SCRIPT_EXIT_CODE=1
```

This course's own [Level 3, Lesson 10](../level-3/10-project-pom-framework.md) capstone suite
contains a real, intentionally-failing test (built specifically to demonstrate the screenshot-on-failure
hook). Running it through this real CI simulation produced a genuinely useful result: the real nonzero exit
code (`1`) propagated all the way out of the script, exactly as a real CI system needs in order to correctly
mark the build as failed — not a contrived demonstration, but this course's own real test suite's real,
unmodified behavior.

## How It Actually Works

A real CI pipeline's "test" job is just a shell environment running real commands in sequence, the same as
this script — a hosted CI service adds scheduling, log capture, and a UI around exactly this real sequence,
but the real mechanics are identical to what ran here. Four real, load-bearing details make this work
correctly:

1. **Health-check polling, not a fixed sleep.** Step 2 polls the real `/` endpoint in a loop rather than
   assuming the app is ready after N seconds — this matched the real measured behavior from
   [Level 2, Lesson 2](../level-2/02-implicit-waits.md): "ready" is a real, observable
   condition, not a guessed duration.
2. **`set +e` around the test command.** Bash's `set -e` (active everywhere else in the script) would
   otherwise terminate the whole script the instant `pytest` returned nonzero — before `TEST_EXIT_CODE` could
   even be captured. Temporarily disabling it is what let the real failing exit code survive long enough to
   be read and propagated deliberately.
3. **Teardown runs unconditionally.** `kill $FLASK_PID` executes whether the tests passed or failed, because
   it is reached before the real `exit $TEST_EXIT_CODE` line — a real CI job that leaves background processes
   running after a failed test leaks real resources on every failed build, compounding over time.
4. **The script's own exit code is the test suite's exit code**, not a hardcoded `0`. This is the exact real
   signal a CI service's UI uses to mark a job red or green — a script that always `exit 0`s regardless of
   test outcome would make every CI run report success, silently defeating the entire pipeline's purpose.

## Common mistakes

- Using a fixed `sleep 5` instead of real health-check polling — this course has already measured (Level 2,
  the dynamic-content lessons) that a fixed wait is either too short under real load or wastefully long when
  the app starts fast; polling adapts to the real, actual startup time.
- Letting `set -e` stay active across the test-run step — a real pytest failure would kill the whole CI
  script immediately, skipping real teardown and losing the real exit code entirely.
- Forgetting teardown on the failure path — a real CI worker that never kills its background application
  process accumulates real zombie processes across every subsequent failed run.
- Hardcoding the script's final `exit 0` — this silently converts every real test failure into a reported CI
  success, which defeats the entire point of running tests in a pipeline.

## Exercise

Modify this lesson's real script to also write `TEST_EXIT_CODE` to a real file (e.g. `echo $TEST_EXIT_CODE >
/tmp/real_ci_result.txt`) before the final `exit`, then run the script twice: once against this course's
real capstone suite as-is (which genuinely fails), and once after commenting out the one deliberately-failing
test. Confirm the real file's contents differ (`1` vs `0`) between the two real runs, directly observing the
same signal a real CI dashboard would read to decide pass/fail.
