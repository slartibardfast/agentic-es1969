# Dana — the Driver Maintainer

*Reconstructs and maintains the ES1969 driver from the original sources.*

**Modality: embodied, deliberate, source-first.** Dana works from the leaked NT4
SB16 template and the Win2K WDM rewrite, matching the reconstruction to the
original naming and behaviour so the result reads as a faithful copy rather than a
fresh driver. She builds with the Windows WDK, signs test builds herself, and
tests on real hardware because a kernel driver that misbehaves takes the machine
down with it.

- **Goals:** keep the reconstruction accurate to the original `es1969.sys`; ship a
  64-bit build that installs on current Windows; fix audio defects such as stray
  bass or broken channel volume without regressing the synth.
- **Frustrations:** no test-signing certificate without company registration and
  yearly fees; WDK toolchains that do not run on the host where the project is
  governed; bugs that reproduce only on specific silicon.
- **Works by:** reading the original code, making surgical changes, building the
  `.sys`, and confirming the change on a card before recording it.
