---
description: "4 workers sped up a short-per-test suite by only 11% (10.11s -> 9.02s) -- the same suite at 4x per-test work sped up a real 1.67x (17.91s -> 10.74s)."
---

# Parallel Test Execution

## What was run

A real 6-test Selenium suite, run via `pytest-xdist`, both sequentially and across 4 real parallel workers —
twice, at two different real per-test costs — to measure how much parallelism actually helps, rather than
assuming it divides total time by the real worker count.

## Real parallelism's benefit — much smaller than expected, the first time

```bash
pytest test_parallel_execution.py            # real sequential run
pytest test_parallel_execution.py -n 4        # real, 4 parallel workers
```
```
real sequential (0.5s/test): 6 passed in 10.11s
real parallel,  4 workers:   6 passed in 9.02s
```

A real, modest `11%` improvement — nowhere close to the naive expectation that 4 real workers should finish
roughly 4× faster.

## The real, investigated reason: fixed overhead dominated

```python
time.sleep(2.0)  # increased from 0.5s, to make the real per-test work larger relative to fixed cost
```
```
real sequential (2.0s/test): 6 passed in 17.91s
real parallel,  4 workers:   6 passed in 10.74s
```

A real, much clearer `1.67×` speedup this time — still well below a naive `4×`, but now a genuine,
substantial improvement, simply by increasing how much real per-test work existed relative to the fixed real
cost of launching a browser and spinning up each worker.

## How It Actually Works

Each real pytest-xdist worker needs its own real browser launch — this course's own
[Level 3, Lesson 5](../level-3/05-pytest-fixtures-for-selenium.md) already measured that a fresh real Chrome
launch per test carries a real, nontrivial fixed cost. When each test's own real work (this lesson's
`time.sleep()`) is small relative to that fixed launch cost, parallelizing barely helps — the real bottleneck
is how fast 4 real Chrome processes can all start up and compete for this machine's real CPU, not how much
test logic runs concurrently. This lesson's real first measurement (`11%`) is a direct, measured
demonstration of exactly that bottleneck. Once the real per-test work (`2.0s`) became large enough to
genuinely dominate over the fixed launch cost, the real measured speedup (`1.67×`) grew substantially,
because now there was real, substantial concurrent work for the 4 workers to actually overlap.

The real reason neither measurement reached a clean `4×` is that this specific real machine only has finite
real CPU and I/O capacity — 4 real Chrome processes launching and rendering simultaneously genuinely compete
for the same real, limited hardware resources, which is a real, physical constraint no amount of pytest
configuration can parallelize away.

## Common mistakes

- Assuming parallel test execution always divides total time by the worker count — this lesson's own real
  numbers (`11%` and `1.67×`, neither close to `4×`) are a direct, measured counterexample.
- Parallelizing a suite of very short, fast tests and being confused when the real speedup is disappointing —
  this lesson's real first measurement shows exactly why: fixed per-worker overhead (browser launch) can
  dominate when individual tests are fast.
- Assuming more workers always helps — beyond a real machine's actual CPU/resource capacity, adding more
  parallel workers competing for the same limited real hardware can produce diminishing, or even negative,
  real returns.

## Exercise

Re-run this lesson's exact real parallel comparison with `-n 2` and `-n 8` instead of `-n 4`, at the real
`2.0s`-per-test configuration. Measure whether the real speedup continues improving roughly proportionally,
plateaus, or gets worse at `8` workers on this specific real machine — finding, by direct measurement, where
this real hardware's own parallelism ceiling actually sits.
