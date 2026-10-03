# Sulaiman Dauda

**I build production software with AI agents, and I measure whether it actually works.**

Customer-facing AI whose rules are enforced in code rather than in a prompt, MCP
servers that propose changes for a person to approve, and evals that run in CI.
Underneath that, self-hosted infrastructure in Go. Nine years shipping to
production, an MSc in Applied Data Science, and a habit of not believing a change
works until something has measured it.

Based in Essex, England.

---

## AI in production

Most of my code is now written with Claude Code and Codex: 2,241 of 2,937 commits
across 44 repositories carry a Claude co-author line, and Codex adds none. That
makes code cheap to write, not cheap to be wrong, so the work is in what surrounds
the model. The work for my employer lives in private repositories; the case
studies are at **[sulaimandauda.com](https://sulaimandauda.com/#ai)**.

**A customer assistant that cannot be talked into a price.** I replaced LiveChat
with an in-house live chat and AI support assistant, answering customers on its
own since August 2026. It never states a price, stock level or delivery date from
its own knowledge, and that rule runs in code before the model is called.
Retrieval is PostgreSQL full-text search with pgvector, and an eval suite with a
judge model runs in CI. Elixir, Phoenix, PostgreSQL.

**An MCP server that proposes and never commits**, for a product and pricing
system synced with a live WooCommerce shop. Five tools let Claude search the
catalogue and propose changes; the only write is a draft that a person approves.

**Now: a business platform built by a governed team of agents.** Quoting, field
service, purchasing, a double-entry ledger with VAT, HR and marketing email, in
one Go application on one PostgreSQL database. A principal-engineer agent leads
specialist agents; anything touching money, permissions, security or a migration
is design-reviewed before it is built, and only I can merge it. The foundations
phase merged 49 pull requests in its first eight days.

**One working setup for Claude Code and Codex.** Shared standards, six skills I
wrote, per-project memory and a worklog both CLIs read, a secret scan that blocks
every commit carrying anything resembling a key, and deny rules on the commands
that would make a repository public.

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
| Cached throughput, 500 connections | **9,280 req/s** | 2,259 req/s |
| p99 at that load | **85.5 ms**, 1 timed out | 385.9 ms, 319 timed out |
| 2,000-connection flood | **8,018 req/s**, 0 errors | 455 req/s, 1,619 timeouts |
| Install, bare server to running panel | **105 s** | 460 s |
| Uncacheable WooCommerce shop listing | 5.48 req/s | **10.75 req/s** |

Measured 3 September 2026, Slipstream v0.2.0 against CloudPanel CE 2.5.4.
That last row is the one it loses, by 2 times on the same machine and 4 times
over a network, and it stays on the page. The cause is
measured rather than guessed: every site runs inside an `open_basedir` jail,
which cost 72 ms of a 301 ms render when I removed it and put it back.
CloudPanel sets no `open_basedir` and isolates tenants by Unix user alone. A
panel that is faster on uncached renders and lets one compromised site read
another's files is not a trade worth making, so the jail stays.

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

**Sportsafe UK.** Lead technical delivery on a national B2B commerce platform.
**Eleven paid tools became code the business owns, about £3,000 a year in
subscriptions** at list price: an in-house live chat and AI assistant in place of
LiveChat, an integration service in place of Zapier (134 of 134 web orders
checked reached the CRM), and our own code in place of nine Pro plugins. The
internal tools, including a quoting tool and a product and pricing system for
4,268 products, are self-hosted on Windlass. Time to first byte
went from **983 ms to 288 ms** on a cache miss and 110 to 160 ms on a hit, and
reporting is reconciled against the order database to the penny every month.

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
TypeScript, Elixir and Postgres, and since 2026 building with AI agents. The data side is why the benchmarks exist: I am more
interested in what a change measurably did than in what it was meant to do.

---

**[sulaimandauda.com](https://sulaimandauda.com)** ·
**[LinkedIn](https://www.linkedin.com/in/sulaiman-dauda/)**
