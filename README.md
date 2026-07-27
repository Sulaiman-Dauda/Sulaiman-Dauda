# Sulaiman Dauda

**I build self-hosted infrastructure in Go, and I measure what it actually does.**

Control planes, backend platforms and deploy tooling. The layer underneath the
application, where a wrong assumption costs someone their production server.
Nine years shipping to production, an MSc in Applied Data Science, and a habit of
not believing a change works until something has measured it.

Based in Essex, England.

---

## What I'm building

### [Slipstream](https://github.com/Sulaiman-Dauda/slipstream) · Go · AGPL-3.0

A hosting control panel that treats slow as broken. Sites arrive cached, tuned
and isolated, and a deployment that makes a site slower is **refused** rather
than shipped.

Benchmarked against CloudPanel on the **same physical server**, one panel at a
time, with the OS reinstalled in between and both tuned to their best:

| | Slipstream | CloudPanel |
| --- | --- | --- |
| Cached throughput, 500 connections | **9,840 req/s** | 2,495 req/s |
| p99 latency at that load | **83 ms** | 6.46 s |
| 2,000-connection flood | **8,051 req/s**, 0 errors | 163 req/s, 100 timeouts |
| Install, bare server to running panel | **79 s** | 410 s |
| Uncacheable WooCommerce render | 189 ms | **168 ms** |

That last row is the one it loses, and it stays on the page. The gap is the
measured cost of running every site inside an `open_basedir` jail: 72 ms of a
301 ms render, found by stracing a worker under load. The jail stays.

Two processes, an unprivileged API and a root agent, talking over a typed RPC on
a Unix socket. Commands are built as argv arrays, never shell strings. Released
binaries carry signed build provenance, because the installer runs as root via
`curl | sudo bash`.

**[slipstreampanel.com](https://slipstreampanel.com)**

### [Gresbase](https://github.com/Sulaiman-Dauda/gresbase) · Go · MIT

A backend platform in a single binary. Collections, auth, realtime and file
storage, with **PostgreSQL as the only infrastructure**. Anything a larger
platform does with a sidecar service, this does with a Postgres feature.

Collections are **locked when you create them**. Every access rule starts as
superuser only, and you open what should be public on purpose. Rules are checked
on every read path: list, view, search, batch, file downloads and realtime
delivery. A rule enforced on four paths out of five is not enforced.

### [Windlass](https://github.com/Sulaiman-Dauda/windlass) · Go · Apache-2.0

A Docker Compose control plane that wraps the real `docker compose` instead of
replacing it with its own runtime. The project filesystem stays authoritative,
so editing `compose.yaml` by hand and running `docker compose up -d` keeps
working.

The rule I care most about: **your containers keep running if Windlass stops or
gets removed.** It is a control plane, not something your stack depends on to
stay up. Privileged work is confined to one package, and a `depguard` lint rule
enforces that boundary at build time rather than in code review.

---

## Measured outcomes, not activity

**Sportsafe UK.** Led technical delivery on the rebuild of a national B2B commerce
platform: **50% uplift in organic and campaign traffic, 35% increase in
conversions**, through UX, navigation and performance work. Rebuilt the GA4 and
GTM setup and fixed the technical SEO first, so the reporting was worth trusting
before anyone acted on it.

**Work I measured and then threw away.** Kernel TLS is standard advice and cost
**28% of cached throughput** on a page-cache workload, so it is not shipped.
Flattening the release docroot gained about 8% on uncacheable renders and was
declined because the risk to the rollback model was not worth it. Both decisions
sit in the code with their numbers attached, so nobody quietly adds them back.

---

## How I work

**Verify on the wire, not in the file.** A directive present in a config is not a
directive in effect. I have shipped nginx security headers that were silently
dropped by inheritance rules, and a capability probe that read stdout for a tool
that writes to stderr. Both looked correct in review.

**Change one variable at a time.** Copying someone else's tuning is a hypothesis,
not an improvement.

**Prove the hardware matches before you compare.** Two supposedly identical VPS
of the same spec measured 2.5 times apart on the same fixed workload. A benchmark
run across two machines is measuring the machines.

**Say what did not work.** The rejected experiments are usually more useful to the
next person than the successful ones.

---

## Background

**MSc Applied Data Science**, University of Essex. **BSc Business
Administration**, University of Lagos.

Nine years across web engineering and data. WordPress and PHP at depth, then Go,
TypeScript and Postgres. The data side is why the benchmarks exist: I am more
interested in what a change measurably did than in what it was meant to do.

---

**[sulaimandauda.com](https://sulaimandauda.com)** ·
**[LinkedIn](https://www.linkedin.com/in/sulaiman-dauda/)**
