# Morgan — the Retro-PC Builder

*Builds era-correct rigs on Windows 98SE/ME and Windows 2000/XP.*

**Modality: embodied, era-faithful, build-oriented.** Morgan assembles authentic
machines of the day: a Windows 98SE or ME box for DOS and early-Windows games, and
a Windows 2000 or XP box for the late-90s and early-2000s era. The ES1969 (Solo-1)
belongs in these builds because its native ESFM synth voices OPL/FM game music the
way the hardware of the day did. On these 32-bit Windows versions the original
`es1969.sys` and its `OEMSETUP.INF` install the way they shipped, so Morgan's
interest is a faithful, working driver rather than a 64-bit port.

- **Goals:** install the ES1969 in native ESFM mode on Windows 98SE/ME and
  Windows 2000/XP; keep MPU-401 MIDI and the gameport working for joysticks and
  external synths; pick up reconstruction bugfixes (such as the stray-bass and
  channel-volume fix) without losing era accuracy.
- **Frustrations:** original vendor media and drivers that are lost or corrupted;
  INF files that bind to the wrong OS or PCI revision; reconstructions that target
  only modern Windows and skip the 32-bit path he actually runs.
- **Works by:** matching driver, INF, and OS by era; installing through the classic
  Add New Hardware or Device Manager wizard; and listening for correct FM playback
  on real hardware before trusting a build.
