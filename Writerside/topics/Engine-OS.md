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

## Emulation

[QEngine](https://github.com/Peyton-C/QEngine) boots Engine OS under `QEMU` on an ordinary host,
with no inMusic hardware involved. It covers `4.3.0`, `4.6.0` and `5.0.4` across the
`armv7` / `RK3288` lineup and the `arm64` / `RK3588` [System One](Rane.md), and  reaches a running
`Engine` UI with working audio playback and MIDI from real USB controllers passed through to the
guest.

It works by `LD_PRELOAD`ing shims that fake the hardware `Engine` refuses to start without:

| Shim                                                | What it provides                                                                                                                                                         |
|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `dtshim.c`, `dtshim_rmz2.c`                         | The `inmusic,*` devicetree properties (`inmusic,product-code`, `serial-number`, `inmusic,az01-pcb-rev`), a synthetic `/proc/interrupts`, and faked `smp_affinity` writes |
| `drmatomic_rmz2.c`                                  | Rewrites `eglfs`'s `ARGB8888` framebuffers and legacy modeset ioctls into the `XRGB8888` / atomic-commit form `virtio-gpu` accepts                                       |
| `alsashim_rmz2.c`                                   | Gets the emulated card past `Engine`'s card-name allowlist, and routes PCM opens through ALSA's `plug` layer                                                             |
| `touchsim.c`, `vnctouchbridge.c`, `touchbridge_*.c` | Synthetic `uinput` multitouch - Engine OS has no mouse support at all                                                                                                    |

### Findings Worth Carrying Over

- **`inmusic,product-code` is the entire product identity.** Faking it changes which UI screens
  appear, the screen-size assumptions, and whether the battery gate applies, so a Prime Go image
  spoofed as `JC11` boots as a Prime 4. [](Software.md) covers where the property lives on real
  hardware.
- **`Engine` aborts outright without it**, logging
  `air.planck.config: Unable to find product "" in config map!`, then crash-looping through
  `engine.service`'s `Restart=on-failure`.
- **The accepted audio devices are a compiled-in allowlist.** `ALSADeviceEnumerator::scanDevices()`
  matches `snd_ctl_card_info_get_name()` against the `AudioDevices` key of the per-product config
  map inside the binary, and closes any card that isn't listed *before* looking at its PCM devices
  or formats at all. Nothing on the rootfs contains that list.
- **Battery-powered units gate startup on a touch gesture.** The Prime Go shows its
  "touch and hold the logo" prompt and quits after 30 seconds if nothing holds it. The Prime 4 has
  no battery and skips it entirely.
- **Telemetry is on by default** on every version tested, sending crash reports and anonymous
  analytics to `o230257.ingest.sentry.io`. Its `docs/BLOCKING_TELEMETRY.md` blocks this with an
  `/etc/hosts` entry, which is worth doing before experimenting on a unit so that inMusic isn't
  sent noise from research. See [](Reporter.md).

> Several of its conclusions were reached independently of this repository and agree: the `panthor`
> plus Mesa graphics stack and `Qt 6.7.2` on the System One ([](Rane.md)), `Qt`'s image-format
> plugins depending on the proprietary `libmali` ([](Mali-T764.md)), and the `/etc` + `/var` overlay
> split that arrived in `5.x` (above). It goes further on the `RK3288` battery gate, an
> `evdevtouch` defect in `2.4.0`-era builds where the plugin loads but is never instantiated, and
> the ALSA card-name allowlist.
> {style="note"}

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
