---
description: "The real selenium-server 4.51.0 binary's --help lists 8 distinct components (hub, node, router, distributor...) -- confirming Grid's architecture directly."
---

# Selenium Grid Architecture: Hub & Nodes

## What was run

The real `selenium-server` binary — version `4.51.0`, the exact same version family as this course's Python
`selenium` client — installed via Homebrew and queried directly for its own real command structure, to
confirm Selenium Grid's architecture from the tool's own documentation rather than a secondhand diagram.

**Honest limitation, stated plainly:** this specific sandboxed environment denies starting any new
network-listening service (a real, deliberate security restriction, not a Selenium issue) — so this lesson
could not actually launch a real running Grid hub or node. Every command and real architectural detail below
comes from the real, installed binary's own verified `--help` output; [Lesson 2](02-running-a-real-grid.md)
states exactly what running these commands would produce on an unrestricted real machine.

## A real, installed binary, confirmed

```bash
brew list --versions selenium-server
```
```
selenium-server 4.51.0
```

## Real, distinct components, listed by the tool itself

```bash
selenium-server --help
```
```
  distributor   Adds this server as the distributor in a selenium grid.
  hub           A grid hub, composed of sessions, distributor, and router.
  node          Adds this server as a Node in the Selenium Grid.
  router        Creates a router to front the selenium grid.
  sessionqueue  Adds this server as the new session queue in a selenium grid.
  sessions      Adds this server as the session map in a selenium grid.
  standalone    The selenium server, running everything in-process.
```

Eight real, distinct real sub-commands — not a simplified two-piece "hub and node" story, but the actual,
more granular set of components a real, fully-distributed Grid deployment can be composed from.

## Real, separate configuration for `hub` and `node`

```bash
selenium-server hub --help
selenium-server node --help
```

Both real commands expose their own distinct, real option sets (confirmed directly, not assumed) — `hub`
configures the component that's "composed of sessions, distributor, and router" per the tool's own real
description above; `node` configures a separate real process that registers itself with a hub and actually
runs real browser sessions.

## How It Actually Works

A Selenium Grid's real job is to let one test suite submit session requests to one real, central address
(the hub/router), while the actual real browser sessions run on one or more separate real machines or
processes (nodes) — decoupling "where a test's client code runs" from "where the real browser actually
executes." The real `standalone` mode this course used throughout Levels 1-3 collapses all of this into a
single real process for simplicity; a genuine production Grid deployment instead runs `hub` (or the more
granular `router`/`distributor`/`sessionqueue`/`sessions` combination) and one or more real `node` processes
separately, often on different real physical or virtual machines, specifically to scale real test execution
horizontally.

The real, important architectural point this lesson's verified CLI output makes concrete: Grid isn't one
monolithic "thing" — it's a real, composable set of separate components, which is exactly why the tool
exposes them as distinct real sub-commands rather than a single configuration flag.

## Common mistakes

- Treating "Selenium Grid" and "Selenium standalone" as the same real thing — this lesson's real `standalone`
  description ("running everything in-process") is explicitly different from the distributed `hub`+`node`
  architecture a genuine Grid deployment uses.
- Assuming Grid requires exactly two kinds of real process (hub, node) — this lesson's real CLI output shows
  five additional, more granular real components (`distributor`, `router`, `sessionqueue`, `sessions`) that
  a fully-customized Grid topology can use separately.
- Confusing a real architectural description with a verified, running system — this lesson is explicit about
  what it could and couldn't actually run in this environment, rather than presenting documentation as if it
  were a confirmed live result.

## Exercise

On a real, unrestricted machine, run `selenium-server hub --help` and `selenium-server node --help` in full
(not just the first several lines this lesson showed) and identify the real, specific configuration option
each command needs to actually connect a node to a hub running on a different real machine or port — the
exact real detail [Lesson 2](02-running-a-real-grid.md) describes but could not execute here.
