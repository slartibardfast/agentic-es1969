# 0001 — Windows driver CI builds

Bring up a GitHub Actions build for the ES1969 driver covering both target
architectures:

- **32-bit (x86)** — for Windows 2000 and Windows XP.
- **64-bit (x64)** — for Windows XP x64 and Windows Server 2003 x64 (and later
  64-bit Windows).

The buildable component is the WDM driver under `src/win2k` (its `vs2019`
solution and `es1969.vcxproj`), which uses the `WindowsKernelModeDriver10.0`
platform toolset and already declares `Win32` and `x64` project configurations.
CI drives `msbuild` with the WDK on a Windows runner, once per architecture, and
publishes the resulting `es1969.sys` (with its `.inf`) as a build artifact.

- **Who** — Morgan (Windows 2000/XP, 32-bit) and Rex (XP/2003 x64 and later
  64-bit), who get tested, downloadable driver builds per architecture; and Dana,
  who gets a green build gate on every change instead of a manual local build.
- **What** — a workflow **in the `es1969` software repo** (CI for the software
  lives with the software, verified by that repo's own CI), building
  `Release|Win32` and `Release|x64` and uploading both `.sys` artifacts. No
  behavioural (`.allium`) or temporal (`.tla`) spec is in scope yet; if one is
  added it lives with the code, not here.
- **Why** — `call/0002` (the adoption) and `call/0001` (the reproducibility
  exemption this milestone moves toward retiring: a pinned, attested CI build is
  the path to recording a `[build "es1969" "windows"]` recipe in `.host-software`
  and dropping `repro-exempt`). The 64-bit build is the project's original reason
  for existing — no signed 64-bit ESS driver ships for modern Windows.

## Done when

- A workflow in the `es1969` repo builds `Release|Win32` and `Release|x64` green
  on push and pull request (WDK + `msbuild`, matrix over the two platforms).
- Each build uploads its `es1969.sys` and `es1969.inf` as a named artifact.
- The host advances the `.host-software` pin to the `es1969` commit that carries
  the workflow (software-discipline: push the worktree first, then re-pin).

## Open questions to resolve during the work

- Which WDK / Visual Studio version the GitHub `windows-` runner provides, and how
  to install the `WindowsKernelModeDriver10.0` toolset there.
- Whether the WDK10 toolchain can target Windows 2000/XP down-level, or whether
  the 32-bit path needs the older `src/win2k` DDK `SOURCES` build instead of the
  `vs2019` project.
- Whether to also build the MPU-401 gameport driver (`src/gameport`) and whether
  to test-sign artifacts in CI.
