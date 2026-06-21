# 0002 — Distribution package (with the gameport driver)

Produce installable distribution `.zip`s in CI, one per architecture, that bundle
the built driver plus everything a user needs to install it — and add the 64-bit
MPU-401 **gameport driver** to the 64-bit package.

The gameport driver is a prebuilt, Microsoft-signed inbox enumerator
(`src/gameport/GameEnum.sys`, machine type **x64**) with its own `gameport.inf`
(`NTamd64` sections only). It exists because 64-bit Windows ships no gameport
driver; 32-bit Windows has one inbox, so the gameport belongs in the **x64 package
only**.

## Package manifest (answers "any others?")

Per-architecture `es1969-<arch>.zip`:

| File | Source | x86 (Win2K/XP) | x64 (XP/2003) |
|------|--------|:--:|:--:|
| `es1969.sys` | built by milestone 0001 CI | ✓ | ✓ |
| `es1969.inf` | repo (`src/win2k`) | ✓ | ✓ |
| `es1969.cat` | **generated** (`inf2cat`) + signed | ✓ | ✓ |
| `es1969.cer` | the public self-signing certificate | ✓ | ✓ |
| `install_cert.cmd` | repo (`release/`) | ✓ | ✓ |
| `README` / install guide | repo (trimmed) | ✓ | ✓ |
| `GameEnum.sys` | repo (`src/gameport`, x64, MS-signed) | — | ✓ |
| `gameport.inf` | repo (`src/gameport`) | — | ✓ |

The `.cat` and `.cer` are the items beyond the `.inf` that a self-signed driver
needs: Windows validates the driver against the catalog, and the certificate is
what `install_cert.cmd` adds to `TrustedPublisher` so the self-signed catalog is
trusted.

- **Who** — Rex (x64, wants the gameport on modern Windows), Morgan (32-bit), and
  Dana, who cuts a ready-to-install download instead of assembling it by hand.
- **What** — a packaging step in the `es1969` CI (extending milestone 0001's
  `build-driver` workflow) that assembles each `.zip` and uploads it; the 64-bit
  `.zip` includes the gameport driver.
- **Why** — `call/0002` (the adoption); a downloadable package is what the personas
  actually install.

## Done when

- CI produces `es1969-x86.zip` and `es1969-x64.zip` artifacts with the manifest
  files above; the x64 zip includes `GameEnum.sys` + `gameport.inf`.
- The driver `.sys` and a generated `es1969.cat` are test-signed, and `es1969.cer`
  (the matching public cert) installs cleanly via `install_cert.cmd`.
- A short install `README` ships inside each zip.

## Open questions to resolve during the work

- **Signing / certificate strategy** (the main decision). The repo ships no cert —
  the upstream maintainer signs locally. Options: (a) CI generates a per-build
  self-signed cert (`New-SelfSignedCertificate`), signs the `.sys`/`.cat`, and
  exports the `.cer` — no secrets, but the cert changes each build; (b) a stable
  self-signed cert with the `.pfx` held as a CI secret and the `.cer` committed; or
  (c) ship unsigned and document enabling test-signing.
- **Catalog tooling.** `inf2cat` ships with the modern WDK, not the legacy DDK used
  for the build; the packaging step may need a separate WDK/SDK install on the runner.
- **`sbemul.sys`.** `es1969.inf` lists it under `NTMPDriver` (SB DOS-box emulation);
  confirm whether it is an inbox driver or must be bundled.
