# 0001 — Windows driver CI builds

Bring up a GitHub Actions build for the ES1969 driver across **three lanes**, one
per Windows family it must serve. The driver is the WDM/portcls audio miniport
under `src/win2k`; its `es1969.inf` already carries both the `$CHICAGO$` (Win9x)
and `NTamd64` install sections, so one source tree targets all three.

| Lane | Target OS | Bitness | Build |
|------|-----------|---------|-------|
| **Win9x** | Windows 98SE / ME | 32-bit (x86) | Win9x-capable WDM build |
| **NT x86** | Windows 2000 RTM, Windows XP RTM (and later 32-bit) | 32-bit (x86) | legacy WinDDK, `W2K` target |
| **NT x64** | Windows XP x64 / Server 2003 x64 (and later 64-bit) | 64-bit (x64) | legacy WinDDK, `WNET AMD64` target |

## Hard constraint — Windows 2000 RTM and Windows XP RTM

The NT x86 driver **must load and run on Windows 2000 RTM and Windows XP RTM** (no
service packs). That rules out the modern WDK10 toolset (`WindowsKernelModeDriver10.0`,
`TargetVersion=Windows10`) used by the `vs2019` project: it stamps a high PE
subsystem/OS version that the Win2K and XP RTM loaders reject, and links kernel
imports those builds lack. The lane therefore builds with the **legacy WinDDK**
(Server 2003 / build 3790) using its `W2K` target, which produces an `es1969.sys`
with subsystem version `5.00` — accepted by Win2K RTM upward. This is exactly what
`src/win2k/sources` (a DDK `build` recipe: `portcls.lib`, `stdunk.lib`,
`libcntpr.lib`) is set up for.

- **Who** — Morgan (98SE/ME and 2000/XP, 32-bit), Rex (x64 and later), and Dana
  (a green build gate per lane).
- **What** — workflows **in the `es1969` repo** building all three lanes and
  uploading each `es1969.sys` + `es1969.inf` as a named artifact. No `.allium` /
  `.tla` spec is in scope yet.
- **Why** — `call/0002` (adoption) and `call/0001` (a pinned, attested per-lane
  build is the path to recording a `[build "es1969" "windows"]` recipe and
  retiring `repro-exempt`).

## Done when

- CI builds all three lanes green on push and pull request.
- Each lane uploads its `es1969.sys` (+ `es1969.inf`) as an artifact.
- A PE-header check asserts each binary's subsystem/OS version matches its target
  (Win9x ≈ 4.x; NT x86 = 5.00; NT x64 = 5.02) — the necessary, CI-checkable
  condition for RTM loadability.
- The driver is confirmed to load on **Windows 2000 RTM** and **Windows XP RTM**
  (VM or hardware; likely a manual acceptance step owned by Dana, since automating
  a Win2K RTM VM in CI is impractical).
- The host advances the `.host-software` pin to the green `es1969` commit.

## Progress (2026-06-21)

- **NT x64 lane — done.** `nt-x64-wnet` builds `es1969.sys` green (subsystem 5.02,
  verified in CI) and uploads it.
- **NT x86 lane — done.** `nt-x86-win2k` builds green with **subsystem 5.00**
  (verified in CI), the load condition for Windows 2000 RTM and XP RTM. Required:
  lowering the makefile OS guard to `0x500`, providing `stdunk.lib` for the W2K
  lib dir, guarding the XP-only `IDrmAudioStream` DRM path behind `ES_NT_TARGET`,
  and aliasing the Win2K `IAdapterPowerManagement` IID typo. The driver is built
  with the self-contained legacy WinDDK 3790.1830 (no Visual Studio).
- **Win9x lane — deferred (tracked).** Needs a Win9x-capable toolchain (VC6 + the
  Win2K DDK, per the WDMHDA recipe), which is fragile on modern hosted CI runners.
  Deferred by decision on 2026-06-21 to keep it a tracked open item rather than
  block the two green NT lanes. Revisit once the toolchain approach is chosen (or
  test whether the existing subsystem-5.00 x86 binary loads on 98SE/ME, since the
  `es1969.inf` already carries the `$CHICAGO$` Win9x sections).
- **Runtime acceptance — pending.** CI confirms the build and the PE subsystem
  version; loading on real Windows 2000 RTM / XP RTM is still a manual VM/hardware
  step (Dana).

## Open questions to resolve during the work

- **Win9x lane toolchain.** Which DDK builds a 98SE/ME-loadable WDM driver (the
  Win98/Me DDK, or the Win2K DDK targeting Win9x), and whether `portcls` on
  98SE/ME exports everything the miniport uses. Whether one subsystem-5.0 binary
  can serve both Win9x and NT x86, or Win9x needs its own build.
- **Legacy WinDDK acquisition in CI.** A stable source for the 3790 DDK ISO (and
  the Win9x DDK), and how to set up its `build` environment on a modern runner.
- **Runtime acceptance.** How far CI can go (build + PE check) versus what must be
  a manual load test on Win2K RTM / XP RTM.
