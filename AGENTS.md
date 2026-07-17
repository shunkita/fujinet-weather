# AGENTS.md

## Cursor Cloud specific instructions

This is a **cross-platform 6502/retro build** (Apple II, C64, Atari, ADAM, CoCo, …)
driven by the MekkoGX makefiles. Standard build commands are in `README.md`
(`make <platform>`, output lands in `r2r/<platform>/`). Notes below are the
non-obvious things that are easy to get wrong on a Linux cloud VM.

### Apple II (verified working on the cloud VM)

- Build: `make apple2` → produces `r2r/apple2/weather.a2s` (AppleSingle binary)
  and `r2r/apple2/weather.po` (bootable 140 KB ProDOS disk).
- Inspect the disk with `ac -ls r2r/apple2/weather.po`.
- The build downloads `fujinet-lib` (version pinned in the `Makefile`) and a
  ProDOS system disk on first run, so **network access is required** for a clean
  build. Downloads are cached under `_cache/`.

### Toolchain gotchas

- **cc65 must be built from source (git), not the distro `cc65` package.** The
  prebuilt `fujinet-lib` references the runtime symbol `c_sp`, which only exists
  in modern cc65. The old apt `cc65` (2.19-1) fails at link with
  `Unresolved external 'c_sp'`. The cloud VM has cc65 built from git at
  `/usr/local` (`cl65 --version` → `V2.19 - Git …`).
- A source-built cc65 does not locate its data dir on its own, so the driver
  binaries in `/usr/local/bin` are wrapped to export
  `CC65_HOME=/usr/local/share/cc65`. If you reinstall cc65, either keep those
  wrappers or export `CC65_HOME` yourself, otherwise you get
  `cl65: error - could not determine target path`.
- The Apple II disk target needs **AppleCommander** `ac` and `acx` on `PATH`
  (native Linux build, installed in `/usr/local/bin`). Java (JRE) is also present
  but the native `ac`/`acx` binaries do not require it.

### Other platforms

Non-Apple2 targets need extra cross toolchains that are **not** installed on the
cloud VM (see `README.md`): Mad-Pascal for `atari`, `z88dk` for `adam`, and
`CMOC`/`lwtools` for `coco`. Install those before building those platforms.

### Running

The build products are Apple II / retro-machine disk images; they run on real
hardware or an emulator (e.g. AppleWin, Virtual ][), not on the VM itself.
A successful `make apple2` producing a valid `.po` disk is the meaningful
end-to-end check here.
