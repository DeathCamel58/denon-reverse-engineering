# Engine OS

[Engine OS](https://enginedj.com/software/enginedj-os) is inMusic's DJ operating system, with some additional
custom software on top of a stock embedded Linux distribution.

## Build System

Engine OS was built with [](Buildroot.md) from the beginning up to and including `4.3.4`, and
switched to Yocto/OpenEmbedded (`scarthgap`) in **`5.0.0`**.

The table below covers every Prime Go (`JP11`) release from `1.6.2` to `5.0.4`. Consecutive
releases sharing a build are grouped. Other `RK3288` devices ship the same platform, so the
distribution and kernel should match version-for-version, but I've only walked the full history
on the Prime Go.

| Firmware        | `ID`        | `VERSION`                      | Kernel                               |
|-----------------|-------------|--------------------------------|--------------------------------------|
| `1.6.2`         | `buildroot` | `2020.02.3-311-g89f300ef23`    | `4.19.150-inmusic-2020-12-03-rt64`   |
| `2.0.0`-`2.1.2` | `buildroot` | `2021.02.3-12-g23d424b4ea`     | `5.4.131-inmusic-2021-07-13-rt61`    |
| `2.2.0`-`2.2.2` | `buildroot` | `2021.02.9-83-g1f864943a0`     | `5.10.109-inmusic-2022-03-30-rt64`   |
| `2.3.0`-`2.3.2` | `buildroot` | `2021.02.9-204-g1da559a23d`    | `5.10.120-inmusic-2022-06-06-rt67`   |
| `2.4.0`         | `buildroot` | `2022.02-rc1-1197-g5ea8014829` | `5.15.71-inmusic-2022-09-28-rt50`    |
| `3.0.0`         | `buildroot` | `2022.02.7-7-g934a5c01a3`      | `5.15.82-inmusic-2022-12-08-rt54`    |
| `3.0.1`         | `buildroot` | `2022.02.7-7-g934a5c01a3`      | `5.15.82-inmusic-2022-12-08-p1-rt54` |
| `3.1.0`         | `buildroot` | `2022.02.11-6-g7da9ea3140`     | `5.15.110-inmusic-2023-05-05-rt62`   |
| `3.1.1`         | `buildroot` | `2022.02.11-6-g7da9ea3140`     | `5.15.110-inmusic-2023-05-05-p1-rt62`|
| `3.2.0`         | `buildroot` | `2022.02.12-6-gf52eddeb39`     | `5.15.115-inmusic-2023-06-05-rt64`   |
| `3.3.0`-`3.4.0` | `buildroot` | `2023.02.3-4-g189b0a5875`      | `6.1.49-inmusic-2023-08-29-p3-rt13`  |
| `4.0.0`-`4.0.1` | `buildroot` | `2023.02.9-5-gc80b63ff32`      | `6.1.78-inmusic-2024-02-16-p2-rt24`  |
| `4.1.0`         | `buildroot` | `2023.02.11-5-gfbf213aa23`     | `6.1.92-inmusic-2024-05-28-rt32`     |
| `4.2.0`-`4.3.4` | `buildroot` | `2023.02.11-5-g140647e9b3`     | `6.1.111-inmusic-2024-09-19-rt41`    |
| `5.0.0`-`5.0.4` | `az01`      | `5.0.14 (scarthgap)`           | `6.6.119-az01-2025-12-17-rt67`       |

On `5.x` the `ID` is the platform name rather than the distribution, so it varies by hardware
generation: `az01` on `RK3288`, `az04` on the `RK3588` [Rane System One](Rane.md). The
`CPE_NAME` gives the distribution away:

```
ID=az01
VERSION="5.0.14 (scarthgap)"
VERSION_CODENAME="scarthgap"
CPE_NAME="cpe:/o:openembedded:az01:5.0.14"
```

inMusic's own code treats the two as distinct. The test app launcher reads `/etc/os-release` and
branches on `ID` to decide which field holds the OS version:

| `ID`                        | Field read         | Example      |
|-----------------------------|--------------------|--------------|
| `buildroot`                 | `VERSION_ID`       | `2023.02.11` |
| `az01` / `az04` / `az05`... | `VERSION_CODENAME` | `scarthgap`  |

Anything else is logged as `Unknown embedded platform`. This matters when writing a
[test app catalog](HARDWARE-ID-Test-App.md), because the `osVersionID` in `manifest.yaml` has to
match whichever of those two fields applies to the target firmware.

Note that `VERSION_ID` is **not** the `VERSION` string in the table above - it drops the git
describe suffix, and it doesn't always track it. `1.6.2` reports `VERSION=2020.02.3-311-...` but
`VERSION_ID=2020.02.6`. These are the values a manifest has to match:

| Firmware        | `osVersionID` to use |
|-----------------|----------------------|
| `1.6.2`         | `2020.02.6`          |
| `2.0.0`-`2.1.2` | `2021.02.3`          |
| `2.2.0`-`2.2.2` | `2021.02.10`         |
| `2.3.0`-`2.3.2` | `2021.02.12`         |
| `2.4.0`         | `2022.02.6`          |
| `3.0.0`-`3.0.1` | `2022.02.7`          |
| `3.1.0`-`3.1.1` | `2022.02.11`         |
| `3.2.0`         | `2022.02.12`         |
| `3.3.0`-`3.4.0` | `2023.02.3`          |
| `4.0.0`-`4.0.1` | `2023.02.9`          |
| `4.1.0`-`4.3.4` | `2023.02.11`         |
| `5.0.0`-`5.0.4` | `scarthgap`          |

The comparison tries an exact string match first and only then falls back to splitting on `.` and
matching major and minor. `scarthgap` has no dots, so on `5.x` the value has to be exactly right.

### Other Layout Changes

`5.x` also made the root filesystem properly read-only, with writable `/etc` and `/var` provided
by overlays backed by the `/data` partition (`etc.mount`, `var.mount`, `overlayfs-setup.service`).
On `4.x` there are no overlays, and a `mount -o remount,rw /` is needed before anything under
`/etc` can be modified.

## Custom Applications

This is additional custom software that I've noticed in the firmware, along with documentation pages for them.

> My research has focused on the [Denon Prime Go](https://www.denondj.com/prime-go), so additional software may exist in
> other firmwares.
> {style="note"}

| Software name                              | Software Path                   | Description                                                    | Page                        |
|--------------------------------------------|---------------------------------|----------------------------------------------------------------|-----------------------------|
| Engine                                     | `/usr/Engine/Engine`            | The main software that the user interacts with.                | [](Engine.md)               |
| Firmware Updater                           | `/usr/Engine/FirmwareUpdater`   | TODO                                                           | [](Firmware-Updater.md)     |
| MIDI Device Scanner                        | `/usr/Engine/MidiDeviceScanner` | TODO                                                           | [](MIDI-Device-Scanner.md)  |
| Offline Analyzer                           | `/usr/Engine/OfflineAnalyzer`   | TODO                                                           | [](Offline-Analyzer.md)     |
| Reporter                                   | `/usr/Engine/Reporter`          | TODO                                                           | [](Reporter.md)             |
| [SoundSwitch](https://www.soundswitch.com) | `/usr/SoundSwitch/SoundSwitch`  | The software that provides the lighting control functionality. | [](SoundSwitch.md)          |
| [HARDWARE_ID] Test App                     | `/bin/[HARDWARE_ID]TestApp`     | Debug software for testing switches, LEDs, etc.                | [](HARDWARE-ID-Test-App.md) |
