# 0001 — reproducible WDK build

Retire the reproducibility exemption on the embedded `es1969` driver. Today the
component carries `repro-exempt = call/0001` because its Windows WDK build is not
pinned or hash-attested. This milestone records a build recipe so
`host-lifecycle software --verify-build` can rebuild the driver from the pin and
prove the shipped `.sys`.

- **Who** — Dana, the driver maintainer (see `cast/dana.md`). A pinned, attested
  build means a clean checkout reproduces the binary she signs and tests.
- **What** — a `[build "es1969" "windows"]` subsection in `.host-software` with a
  pinned WDK `toolchain`, a `build` command, the expected `artifact` hash, and
  `attest-host = windows`, so `--verify-build` attests on a Windows runner. No
  behavioural (`.allium`) or temporal (`.tla`) spec is in scope yet; if one is
  added later it lives with the code in the software repo, not here.
- **Why** — `call/0001` (the interim exemption this milestone closes) and
  `call/0002` (the adoption that embedded the driver).

## Done when

- `.host-software` records the Windows build recipe and drops `repro-exempt`.
- `host-lifecycle software --verify-build .` rebuilds and matches the recorded
  artifact hash on a Windows attest-host.
- `call/0001` is updated to `Status: superseded` pointing at the recorded recipe.
