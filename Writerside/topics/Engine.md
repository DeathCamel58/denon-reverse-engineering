# Engine

This is the main DJ application that the user interacts with. This does all the mixing, cloud file loading, and almost
everything else the user sees.

> Change diffs are available on [my private GitLab instance](https://gitlab.randomcpu.com/engine-os/engine-app). Contact
> me to get access.

## Application Data

Path: `/usr/Engine/Engine`

### Available in firmware for

| Hardware                                                             | Hardware ID | Available          |
|----------------------------------------------------------------------|-------------|--------------------|
| [Mixstream Pro](https://www.numark.com/mixstream-pro)                | `NH08`      | :white_check_mark: |
| [Mixstream Pro GO](https://www.numark.com/product/mixstream-pro-go)  | `NH10`      | :white_check_mark: |
| [Mixstream Pro +](https://www.numark.com/product/mixstream-pro-plus) | `NH08S`     | :white_check_mark: |
| [Prime 2](https://www.denondj.com/prime-2-prime2xus)                 | `JC16`      | :white_check_mark: |
| [Prime 4](https://www.denondj.com/prime-4-prime4xus)                 | `JC11`      | :white_check_mark: |
| [Prime 4 Plus](https://www.denondj.com/prime-4-plus.html)            | `JC11S`     | :white_check_mark: |
| [Prime Go](https://www.denondj.com/prime-go.html)                    | `JP11`      | :white_check_mark: |
| [Prime Go Plus](https://www.denondj.com/prime-go-plus.html)          | `JP11S`     | :white_check_mark: |
| [SC5000](https://www.denondj.com/sc5000%20prime-sc5000primexus)      | `JP07`      | :white_check_mark: |
| SC5000M (No Product Page)                                            | `JP08`      | :white_check_mark: |
| [SC6000](https://www.denondj.com/sc6000-prime.html)                  | `JP13`      | :white_check_mark: |
| [SC6000M](https://www.denondj.com/sc6000m-prime.html)                | `JP14`      | :white_check_mark: |
| [SC Live 2](https://www.denondj.com/sclive2.html)                    | `JP20`      | :white_check_mark: |
| [SC Live 4](https://www.denondj.com/sclive4.html)                    | `JP21`      | :white_check_mark: |

> Verified against the `5.0.4` release images — every one of these devices ships this application.
> Devices that share an update image share a single root filesystem, so presence is really a property of the
> image rather than the individual device; see [](Firmware.md) for the groupings. Note the binaries are *not*
> shared between images: the `AZ01` and `AZ0x` builds have matching sizes but different checksums.
> {style="note"}

## Binary Overview

Measured against `/usr/Engine/Engine` from the **SC6000 5.0.4** firmware:

| Property       | Value                                                         |
|----------------|---------------------------------------------------------------|
| Format         | ELF 32-bit, `ARM:LE:32:v8`                                    |
| Size           | ~44 MB (`.text` alone is ~30 MB)                              |
| Image base     | `0x00010000`                                                  |
| `.text`        | `0016d840` - `01d7a667`                                       |
| Real functions | ~46,500 (per `.ARM.exidx`)                                    |
| Stripped       | Yes, but RTTI, `.ARM.exidx` and assertion strings survive     |
| Hardening      | Stack canaries, `_FORTIFY_SOURCE`                             |
| Debug link     | `.gnu_debuglink` present: an unstripped build exists upstream |

### Internal codename: `planck`

Assertion strings retain full build paths, which exposes the source tree layout and confirms the
`air.planck.*` logging categories seen in the runtime output below:

```
/usr/src/debug/planck/0.0.0+git/App/src/Planck/gui/Views/BeatGridEditView/BeatGridEditViewController.cpp:253
/usr/src/debug/planck/0.0.0+git/Libraries/private/Planck/Librarian/src/Database/...
/usr/src/debug/planck/0.0.0+git/Libraries/Streaming/Providers/Eaas/src/GRPCWebAccessor.cpp:473
```

The two top-level trees are `App/src/` (`Planck`, `PlanckEngine`) and `Libraries/` (split into
`private/`, `shared/` and `Streaming/`).

### Third-party libraries

All dynamically linked, so versions can be read from the runtime's `LD_LIBRARY_PATH`:

| Library                      | Use                                                               |
|------------------------------|-------------------------------------------------------------------|
| Qt (QtQuick/QML, Qt Network) | Entire UI and event system                                        |
| gRPC (`libgrpc.so.37`)       | Device-to-device and cloud RPC                                    |
| Protocol Buffers             | Wire format for all RPC                                           |
| OpenSSL                      | Digests, RSA, EVP                                                 |
| CryptoPP                     | Additional hashing, bundled with Widevine                         |
| Widevine CDM                 | Streaming DRM (`wvcdm::`, `video_widevine::`)                     |
| TagLib                       | Audio file metadata parsing                                       |
| zplane Elastique             | Time-stretch / pitch-shift                                        |
| Boost                        | Assorted utilities, incl. the UUID SHA-1 in [](Engine-Library.md) |
| libudev                      | Hotplug detection for USB media                                   |
| spdlog                       | Logging                                                           |
| Crashpad                     | Crash reporting (`.note.crashpad.info`)                           |

### Embedded QML

The Qt resource system stores QML **as plaintext** in `.rodata`: component source, property
bindings, and JavaScript are all directly readable with `strings`. These call into a `Planck` bridge
object exposed to the QML engine, for example:

```
Planck.getProperty("/Engine/Sync/Network/SyncType")
Planck.engineConnect.numberOfMixerChannels
Planck.networkInformation()
Planck.isRadxa()
```

The `/Some/Slash/Path` strings are keys into a global state map (`StateMap`), which is also how
remote devices' properties are addressed: `/RemoteState/%1/RemoteProperties/...`.

### Network services

`Engine` runs gRPC servers as well as clients, exposing `enginelibrary.v1`, `enginesync.v1`,
`networktrust.v1` and `remotehostscreen.v1`. Discovery is a custom UDP scheme
(`airNetworkDiscoverer` / `airNetworkExchange`) rather than mDNS. See [](Engine-Networking.md) for
the full method inventory.

### Reverse engineering notes

Ghidra's stock auto-analysis finds only about half the functions in this binary.
[](Engine-Ghidra-Analysis.md) documents how to recover the rest from `.ARM.exidx` and how to name
them from RTTI vtables and assertion strings.

## Runtime Console Output {collapsible="true"}

`LD_LIBRARY_PATH=/usr/qt/lib:${LD_LIBRARY_PATH} /usr/Engine/Engine -d0`

- `-d0` flag is the debug logging flag

```
```
{ src="applications/engine/runtime" }

## Related Files

- [](Controller-Assignments.md) covers how the hardware controls different functions of this application.
- [](Engine-MIDI-Devices.md) covers how this application finds its control surface, wheel displays and mixer over MIDI,
  and where their MCU firmware and versions live.
- [](Engine-Ghidra-Analysis.md) covers getting this binary into a navigable state in Ghidra.
- [](Engine-Networking.md) covers the gRPC services, discovery, and cloud endpoints.
