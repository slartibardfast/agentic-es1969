# The es1969 build is not yet byte-reproducible

- Status: accepted
- Scope: software
- Date: 2026-06-21

## Context and Problem Statement

Software initiated under this methodology must have reproducible builds: the
deployed artifact must be byte-reproducible from the pinned source plus a recorded
build recipe, so the `.host-software` pin is a true production anchor. `es1969` is
**migrated** software, not greenfield: a reconstruction of the ESS Technology
ES1969 (Solo-1) `es1969.sys` audio driver, plus a generic MPU-401 gameport driver.
It builds with the Windows DDK/WDK (`SOURCES` / `DIRS` files, NT4 and Win2K trees,
and a `vs2019` `.sln`) and ships as a self-signed kernel driver. That toolchain
does not run on the Linux host where this project is materialized, and the upstream
project records no pinned, hash-attested build recipe.

## Decision

Embed `es1969` under the documented **escape clause for migrated software**: the
`[software "es1969"]` stanza in `.host-software` carries `repro-exempt = call/0001`
citing this decision, rather than a `build`/`toolchain`/`artifact` recipe. The pin
(`5ab89a4`, reachable on `origin`/`upstream` as tag `20240404`) remains the source
anchor; `host-lifecycle software --verify-build` warns and skips the rebuild
comparison while `--check` still requires this citation to resolve.

Interim provenance: the canonical worktree is materialized from
`github.com/slartibardfast/es1969` at the recorded pin, which matches `origin/main`,
`upstream/main`, and the signed release tag `20240404`.

## Consequences

- Good: the migrated driver is embedded honestly, with its non-reproducibility
  recorded and citable rather than silently ignored.
- Bad / follow-up: the exemption is meant to be **retired**. A later milestone
  should pin the WDK build (a `[build "es1969" "windows"]` subsection with an
  `attest-host = windows`) so `--verify-build` can prove the shipped `.sys`.
- Neutral: the exemption is never available to greenfield software added later.
