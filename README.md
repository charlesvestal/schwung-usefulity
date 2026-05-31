# Usefulity

A stereo utility audio effect for [Move Anything](https://github.com/charlesvestal) on the Ableton Move. Think of it as a Swiss army knife for signal routing — an Ableton Utility–style plugin offering channel selection, stereo width, bass mono, gain, balance, phase inversion, mute, and a DC filter, all in one chainable `audio_fx` module.

## Features

- **Channel select** — Stereo, Left, Right, or Swap
- **Stereo width** — mid/side width control from 0× (mono) to 2× (extra wide)
- **Mono** — collapse the signal to mono
- **Bass mono** — sum everything below an adjustable crossover (50–500 Hz) to mono
- **Bass audition** — solo the bass range to hear what's being summed
- **Gain** — −100 dB (−∞) to +35 dB
- **Balance / pan** — −1.0 (left) to +1.0 (right)
- **Phase invert** — independent phase flip for L and R
- **DC filter** — 2nd-order high-pass (~5 Hz) to remove DC offset
- **Mute** — kill switch

### Signal flow

```
Input → DC Filter → Phase Invert → Channel Select → Width →
Mono → Bass Mono → Gain → Balance → Mute → Output
```

## Controls

The eight knobs map to the most-used parameters; the remaining controls are available through the parameter menu.

| Knob | Parameter   | Range / Options                  |
|------|-------------|----------------------------------|
| 1    | Channel     | Stereo, Left, Right, Swap        |
| 2    | Width       | 0–2× (0 = mono, 1 = normal)      |
| 3    | Mono        | Off / On                         |
| 4    | Bass Mono   | Off / On                         |
| 5    | Bass Freq   | 50–500 Hz                        |
| 6    | Gain        | −100 to +35 dB                   |
| 7    | Balance     | −1.0 to +1.0                     |
| 8    | Mute        | Off / On                         |

Menu-only parameters: **Phase L**, **Phase R**, **DC Filter**, **Bass Audition**.

## Building

The module is a shared library (`usefulity.so`) cross-compiled for the Move's ARM64 (Cortex-A72) target. The build runs inside Docker so you don't need an ARM toolchain installed locally:

```bash
./scripts/build.sh
```

This builds the `move-anything-builder` Docker image on first run, compiles the DSP plugin, and packages the module into `dist/usefulity/` along with a `dist/usefulity-module.tar.gz` tarball.

To build without Docker, set `CROSS_PREFIX` to your toolchain prefix (defaults to `aarch64-linux-gnu-`):

```bash
CROSS_PREFIX=aarch64-linux-gnu- ./scripts/build.sh
```

## Installing

With the module built, copy it to a Move on your network over SSH:

```bash
./scripts/install.sh
```

This installs to `/data/UserData/move-anything/modules/audio_fx/usefulity/` on `move.local`. Restart Move Anything to load the new module.

## Project layout

```
src/
  module.json          Module manifest (params, knob mapping, UI hierarchy)
  help.json            On-device help text
  dsp/
    usefulity.c        DSP implementation (Audio FX API v2)
    audio_fx_api_v2.h  Plugin interface
    plugin_api_v1.h    Host interface
scripts/
  build.sh             Docker-based ARM64 cross-compile + package
  install.sh           Deploy to Move over SSH
  Dockerfile           ARM64 build environment
.github/workflows/
  release.yml          Tag-triggered release build
```

## Releases

Pushing a `v*` tag (e.g. `v0.1.2`) triggers the release workflow, which verifies the tag matches the version in `src/module.json`, builds the module, attaches the tarball to a GitHub release, and updates `release.json`.

## License

MIT © 2025–2026 Charles Vestal. See [LICENSE](LICENSE).
