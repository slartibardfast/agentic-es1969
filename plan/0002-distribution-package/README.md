# 0002 — Distribution package (with the gameport driver)

Produce installable distribution `.zip`s in CI, one per architecture, bundling the
built driver plus what a user needs to install it — and add the 64-bit MPU-401
**gameport driver** to the 64-bit package.

The package ships **unsigned** (`call/0003`): no catalog or certificate; users
enable test-signing and accept the unsigned-publisher prompt.

## The gameport driver

`src/gameport/GameEnum.sys` is a **reconstruction of Microsoft's generic Game Port
Enumerator (`gameenum.sys`)**, rebuilt for **x64** (its version info carries
Microsoft's copyright; the embedded PDB path is
`…essaudio\legacy\gameenum\…objfre_wnet_AMD64\amd64`). It is **not** ESS-specific and
sets **no** ESS registers — it only enumerates the standard gameport so a joystick
attaches. The ESS gameport/MPU-401 hardware is driven by `es1969.sys` itself
(`FLAG_NOGAMEPORT`, `m_pJoystickBase` / `m_pMPU401Base`, the `MPU401_REG_*` writes).

It is **x64-only on purpose**: 32-bit Windows (2000/XP) ships `gameenum.sys` inbox,
so the 32-bit package needs no gameport driver; 64-bit Windows dropped it, which is
why the reconstruction exists. Because the binary is Microsoft-derived, the 64-bit
zip carries a `NOTICE` recording its provenance (`call/0003`-style honesty; bundling
chosen as upstream does).

## Package manifest

Per-architecture `es1969-<arch>.zip`:

| File | Source | x86 (Win2K/XP) | x64 (XP/2003) |
|------|--------|:--:|:--:|
| `es1969.sys` | built by milestone 0001 CI | ✓ | ✓ |
| `es1969.inf` | repo (`src/win2k`) | ✓ | ✓ |
| `INSTALL.txt` | generated (test-signing install guide) | ✓ | ✓ |
| `GameEnum.sys` | repo (`src/gameport`, x64, MS-derived) | — | ✓ |
| `gameport.inf` | repo (`src/gameport`) | — | ✓ |
| `NOTICE` | generated (gameport provenance) | — | ✓ |

No `es1969.cat` / `es1969.cer` / `install_cert.cmd` — the package is unsigned
(`call/0003`). Adopting a signing approach later only adds the `.cat` + `.cer`.

- **Who** — Rex (x64, wants the gameport on modern Windows), Morgan (32-bit), and
  Dana, who cuts a ready-to-install download instead of assembling it by hand.
- **What** — a packaging step in the `es1969` CI (extending milestone 0001's
  `build-driver` workflow) that assembles each `.zip` and uploads it; the 64-bit
  `.zip` includes the gameport driver.
- **Why** — `call/0002` (adoption) and `call/0003` (ship unsigned).

## Done when

- CI produces `es1969-x86.zip` and `es1969-x64.zip` artifacts with the manifest
  files above; the x64 zip includes `GameEnum.sys` + `gameport.inf` + `NOTICE`.
- Each zip carries an `INSTALL.txt` describing the test-signing install path.

## Resolved / notes

- **Signing** — ship unsigned (`call/0003`).
- **Gameport licensing** — bundle the Microsoft-derived `GameEnum.sys` with a
  `NOTICE`, as upstream does.
- **32-bit gameport** — not needed; inbox on 32-bit Windows.
- **`sbemul.sys`** — referenced by `es1969.inf` for SB DOS-box emulation; it is a
  Windows-provided component, not bundled.
