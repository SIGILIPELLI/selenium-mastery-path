---
description: "Level 2: the real race condition, implicit/explicit waits, custom expected conditions, alerts, iframes, windows, the Actions API, and file upload/stale elements."
---

# Level 2 · Waits, Synchronization & Robust Interactions

The single most common reason a Selenium test fails isn't a wrong locator — it's a real race between your
script and the browser's own, variable loading time. This level builds the real fix: implicit and explicit
waits, custom wait conditions, and the specific real techniques for alerts, iframes, multiple windows, mouse
and keyboard actions via the Actions API, and the stale-element problem that catches almost everyone at least
once.

By the end of this level you'll have a real, direct, reproduced demonstration of the race condition itself,
and you'll know exactly which real waiting strategy fixes which real situation — plus a complete, working test
suite against the local app's dynamic-content pages.
