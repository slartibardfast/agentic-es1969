# Adopt the host methodology, embedding es1969 as the Where room

- Status: accepted
- Scope: project
- Date: 2026-06-21

## Context and Problem Statement

The `es1969` driver reconstruction is a working software repository with its own
history and remotes (`slartibardfast/es1969`, upstream `leecher1337/es1969`). We
want it governed as an agentic project — an operating manual, the five rooms, and
the verification tools — without turning the code repository into the host. The
host methodology is explicit: **never adopt a software repository in place**; a host
is a separate meta-repo that embeds the software as its *Where* room.

## Decision

Create this host meta-repo, `agentic-es1969`, and adopt the `host-template`
methodology at revision `b6aa7de26db77d7707264c74461df44355896500` (recorded in the
`.host` stamp). Embed `es1969` as a bare store with worktrees under `software/`,
recorded in `.host-software` (see `call/0001` for its reproducibility status), and
keep the two repositories independently versioned.

Adoption followed the procedure at `github.com/connollydavid/host`: classify, then
adopt (rooms plus stamp), then embed (`.host-software` and `software
--materialize`), then wire the verification tools as pinned submodules, then record
and verify.

## Consequences

- Good: `es1969`'s thought (plans, decisions, personas, specs) and its action (the
  code) stay separate and independently versioned; the project can upgrade as the
  methodology moves, via `host-lifecycle upgrade`.
- Neutral: this records the project's adoption, not a methodology rule. Methodology
  lives in the spine (`CLAUDE.md` + `STRUCTURE.md`), inherited by copy-at-version
  and propagated by `upgrade` — not re-litigated here (anti-ouroboros).
