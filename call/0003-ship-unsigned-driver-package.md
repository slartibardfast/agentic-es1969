# Ship the driver distribution package unsigned

- Status: accepted
- Scope: software
- Date: 2026-06-21

## Context and Problem Statement

Milestone 0002 packages the ES1969 driver into per-architecture `.zip`s. A Windows
kernel driver normally ships with a catalog (`es1969.cat`) signed by a code-signing
certificate, plus the public certificate (`es1969.cer`) for the user to trust. The
upstream project is self-signed: the maintainer has no purchased signature, signs
locally, and ships an `es1969.cer` from their own certificate. The repository holds
no certificate, and CI has no signing key.

## Decision

Ship the distribution package **unsigned**: each `.zip` contains the built
`es1969.sys`, `es1969.inf`, a short install `README`, and (64-bit only) the gameport
driver — but **no `es1969.cat` and no `es1969.cer`**, and not `install_cert.cmd`
(which has nothing to install without a certificate). Users enable test-signing
(`bcdedit /set testsigning on`) and accept the unsigned-publisher prompt when
installing.

Rejected alternatives: a per-build self-signed certificate (the cert and the `.cer`
users install would change every build); a stable certificate held as a CI secret
(needs the operator to mint a cert and store a key). Either can be adopted later
without changing the package layout — they only add `es1969.cat` + `es1969.cer`.

## Consequences

- Good: the package builds in CI with no secrets and no signing toolchain; nothing
  sensitive is stored.
- Bad: installation needs test-signing enabled and shows an unsigned-driver prompt;
  there is no catalog, so Windows cannot verify package integrity.
- Neutral: revisiting this is additive — adopting a signing approach later just adds
  the `.cat`/`.cer` to the existing zip.
