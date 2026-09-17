# MPC

This is the main application the user interacts with on [](MPC.md) devices — the sampler, sequencer
and mixer UI. It is the MPC-family counterpart to [](Engine.md), but it is **not** a relative of it:
different codebase, different UI toolkit, different command-line parser.

## Application Data

Path: `/usr/bin/MPC`

Everything below was measured against `/usr/bin/MPC` from the **MPC `3.9.1`** update image
(`MPC-3.9.1-Gen1-update.img`, HWIDs `ACV5`/`ACV5S`/`ACV8`/`ACVA`/`ACVA2`/`ACVB`/`ACVM`/`ACVR`).
The binary reports itself as `3.9.1.2 (f3af35cdc95)`.

### Available in firmware for

One image serves the whole family; the `.dtb` shipped in `/boot` is what distinguishes the units.
Product codes and models read directly from those `.dtb`s:

| Product code | Model                           | Board             |
|--------------|---------------------------------|-------------------|
| `ACV5`       | (generic `InMusic MPC ACV5`)    | `az01`            |
| `ACV5S`      | Akai Professional MPC X SE      | `az01`            |
| `ACV8`       | (generic `InMusic MPC ACV8`)    | `az01`            |
| `ACVA`       | Akai Professional MPC One       | `az01`            |
| `ACVA2`      | Akai Professional MPC One+      | `az05`            |
| `ACVB`       | Akai Professional MPC Live Mk 2 | `az01`            |
| `ACVM`       | Akai Professional MPC Key 61    | `az01` and `az05` |
| `ACVR`       | Akai Professional MPC Key 37    | `az05`            |

> `ACV5` and `ACV8` keep generic `InMusic MPC …` model strings where the newer entries carry real
> product names. Each code also has a `-c` `.dtb` variant, which is the `rockchip,rk3288-c` SoC
> revision rather than a different product.
> {style="note"}

## Binary Overview

| Property       | Value                                                         |
|----------------|---------------------------------------------------------------|
| Format         | ELF 32-bit PIE, `ARM:LE:32:v8`, RK3288                        |
| Size           | ~108 MB (`.text` alone is ~73 MB)                             |
| Image base     | `0x00010000`                                                  |
| `.text`        | `004aa080`, `0x45d61e8` bytes                                 |
| `.rodata`      | `04a80280`, `0x1b493b8` bytes                                 |
| `.data.rel.ro` | `068c4570`, `0x1c50f4` bytes (RTTI and vtables)               |
| Real functions | 91,459 (per `.ARM.exidx`)                                     |
| Stripped       | Yes; 9,266 `.dynsym` entries survive                          |
| Debug link     | `.gnu_debuglink` present: an unstripped build exists upstream |

For scale, this is roughly **2.5× the code and 2× the functions** of `Engine` at the same
firmware generation. The same caveats in [](Engine-Ghidra-Analysis.md) apply — stock Ghidra
auto-analysis finds a fraction of the functions, and `.ARM.exidx` is the way to recover the rest.

### Internal codename: `acvs-sme0`

Debug paths left in the binary expose the build tree:

```
/usr/src/debug/acvs-sme0/0.0.2.0+git-alpha/Software/External/Libraries/sentry-native
/usr/src/debug/acvs-sme0/0.0.2.0+git-alpha/fetchcontent-dependencies/software-unlock
```

which matches the Yocto build tag in `/etc/az01-release`:
`jenkins-ACVS-MPC3-SME0-MPC3-Yocto-563`. So `ACVS` is the platform, `SME0`/`MPC3` the product
generation. Compare `planck` for [](Engine.md).

### Embedded frameworks

| Component                    | Evidence                                                                                                                                                 |
|------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **JUCE**                     | `juce::String`, `juce::File`; the UI and app framework                                                                                                   |
| **Pure Data**                | 179 `pd_*` symbols and a full Pd option set (`-alsa`, `-jack`, `-audiobuf`, `-nogui`, `-loadbang`, `-schedlib`, …) — the synth/DSP engine is embedded Pd |
| **sentry-native + Crashpad** | 129 `crashpad::` symbols compiled in, plus a standalone `/usr/bin/crashpad_handler`                                                                      |
| SQLite                       | `sqlite3` references (project/library storage)                                                                                                           |
| libcurl                      | `libcurl.so.4`, which links `libcares.so.2` (c-ares 1.27.0)                                                                                              |

> MPC uses **JUCE**, not Qt. Nothing in [](Engine.md)'s `QCommandLineParser` or QML machinery
> applies here, and the command-line conventions differ (see below).
> {style="warning"}

The Pd options are worth flagging because a naive scan for `-`-prefixed strings in this binary turns
up well over a hundred of them; almost all belong to embedded Pd, to an ARM CPU-feature table
(`-neon`, `-vfp4`, `-fp16`, `-sha2`) or to Crashpad (`--no-rate-limit`,
`--shared-client-connection`). They are not MPC's own command line.

## Command-line arguments

MPC parses `argv` itself — there is no `QCommandLineParser` equivalent. The convention is
**`--longName` with a single-dash short alias**, which is the opposite of [](Engine.md), where long
names take a single dash.

Its built-in `--help` output advertises only six options:

```
usage: <prog> <params>
  where params are:
    --version           (-v)
    --settings
    --sentryTags
    --messageFile
    --help              (-h)
    --update <url>      (-u)
```

There are **fifteen** in the binary. All fifteen are referenced from the argument-matching code, so
none of them is inert — unlike [](Engine.md), where 11 of 44 registered options are never read. That
difference is structural: MPC compares `argv` against the literal directly, so the literal existing
*is* the read.

| Option                | Short | Value   | Description / observed effect                                                                                                                         | In `--help` |
|-----------------------|-------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------------|-------------|
| `--help`              | `-h`  |         | Prints the usage text above                                                                                                                           | yes         |
| `--version`           | `-v`  |         | Prints the version                                                                                                                                    | yes         |
| `--settings`          |       | path    | Settings file to use                                                                                                                                  | yes         |
| `--sentryTags`        |       | path    | JSON file of extra Sentry tags; errors include `Sentry tags file … cannot be parsed as JSON` and `Can't write Sentry tags file without a JSON parser` | yes         |
| `--messageFile`       |       | path    | Purpose not established                                                                                                                               | yes         |
| `--update`            | `-u`  | `<url>` | Update from the given URL                                                                                                                             | yes         |
| `--remotescreen`      | `-r`  |         | Opens the `MPC Remote Screen` window                                                                                                                  | **no**      |
| `--touchtest`         |       |         | Opens the `MPC Touch Test` window                                                                                                                     | **no**      |
| `--painttest`         |       |         | Opens the `MPC Paint Test` window (`BLUE BOX`, `RED BOX`, `GREEN BOX`, `OPAQUE COMPONENTS`, `REPAINT RED & BLUE`)                                     | **no**      |
| `--script`            | `-s`  | path    | `Startup script:` — runs a script at launch                                                                                                           | **no**      |
| `--scriptLibs`        | `-l`  | path    | Script library path                                                                                                                                   | **no**      |
| `--scriptOutputPath`  | `-o`  | path    | `Script Output Path:`                                                                                                                                 | **no**      |
| `--terminateOnFinish` | `-t`  |         | `Terminate on script complete.`                                                                                                                       | **no**      |
| `--project`           | `-p`  | path    | `Startup project:` — opens a project at launch                                                                                                        | **no**      |
| `--skipStartupChecks` | `-k`  |         | `Skip startup checks.`                                                                                                                                | **no**      |

Nine of the fifteen are undocumented, and the scripting group (`--script`, `--scriptLibs`,
`--scriptOutputPath`, `--terminateOnFinish`) plus `--project` and `--skipStartupChecks` are the
interesting ones — together they amount to an unattended/automation mode.

> The **Value** column is inferred from each option's own description strings, except `--update`,
> whose `<url>` is explicit in the usage text. The parser does validate: it carries a
> `… requires an argument` error. Which options are strictly mandatory-argument was not read from
> the code.
> {style="note"}

Two separate matching sites handle these, which is why the literals sit in two clusters in
`.rodata`:

| Site                       | Options handled                    |
|----------------------------|------------------------------------|
| `0x0157a33c`–`0x0157b0c4`  | everything except the two below    |
| `0x00e27668`, `0x00e27738` | `--project`, `--skipStartupChecks` |

## Device identity

MPC does not hard-code which unit it is; it reads that at runtime.

| Datum             | Source                                               | Populated by                                             |
|-------------------|------------------------------------------------------|----------------------------------------------------------|
| **Product code**  | `/sys/firmware/devicetree/base/inmusic,product-code` | static, compiled into the `.dtb`                         |
| **Serial number** | `/sys/firmware/devicetree/base/serial-number`        | **U-Boot at runtime** — absent from every shipped `.dtb` |

The product code is declared in the DTS source, e.g. `inmusic,product-code = "ACV5S";` in
[](rk3288-az01-acv5s-dts.md). `serial-number` appears in **none** of the 14 `.dtb`s in `/boot` and in
none of the DTS sources, so the node exists only because the bootloader creates it — the usual
Rockchip pattern of filling it from OTP/eFuse. On an emulated guest it is there only if whatever
replaces U-Boot fabricates it.

Reads go through **`libaz0x-info.so`** rather than raw `open()` alone: MPC imports `az0x_info_get`,
`az0x_info_datum_at`/`_key`/`_value`/`_category` and `az0x_info_category_name`. Only `MPC`,
`/usr/bin/az0x-info` and `/usr/bin/az01-info` use that API. The library exposes keyed data well
beyond identity:

- `serial`, `serial-sanitized`, `product-code`, `board`, `bootloader`, `platform`, `hardware`,
  `secure-boot`
- `cpuid`, `cpuid-sanitized` — from the RK3288 eFuse (`/sys/devices/platform/ffb10000.efuse`,
  `ffb40000.efuse`)
- `emmc-serial`, `emmc-name`, `emmc-manfid`, `emmc-oemid`, `emmc-prv`, `emmc-date`, `emmc-fwrev`,
  `emmc-size`, `emmc-lifetime`, `emmc-pre-eol-status`
- `sd-serial`, `sd-cid`, `sd-cid-sanitized`, and the same `sd-*` family
- `touch-panel`, `touch-firmware`, `wireless-chip` (it distinguishes AP6255/AP6256/AP6335, and has an
  `AP6255 / AP6256 (ambiguous!)` case), `usb-hub`, `gpu-driver`, `typec`, `panel`

> There are **three** distinct "serial" concepts here: the unit serial from devicetree (`serial`),
> the eMMC serial, and the SD serial. `/usr/bin/az0x-info` dumps all of them as `key="value"` pairs,
> which is the quickest way to see what the app sees on a running device.
> {style="note"}

MPC also has a serial *write* path, separate from reading — `ProgramSerialNumber unimplemented!`,
`control_surface.serialnumber_reprogram`, `com.akaipro.hasTriedProgrammingSerialNumber`, and a
`SerialNumber` listener on `AcvxHardwareIO`. That is a serial held in the control-surface MCU rather
than the devicetree one, the same idea as `midifirmup`'s `WriteSerialNumber` in
[](Engine-MIDI-Devices.md). The `unimplemented!` string suggests at least one branch is unfinished.

### Recognised product codes {id="recognised-product-codes"}

The product code read from the device tree is matched against a fixed table of **26 entries** at
`0x068b3898` (`.data.rel.ro.local`, stride `0x50`) — the `AcvxHardware::Type` enum. Verified entry
layout:

| Offset          | Meaning                                                        |
|-----------------|----------------------------------------------------------------|
| `+0x00`         | `char*` product code, e.g. `ACV5`                              |
| `+0x04`         | `u32` numeric id — purpose not established, and **not unique** |
| `+0x0c`         | `char*` display name, e.g. `MPC X`                             |
| `+0x10`         | `char*` family, e.g. `MPC`, `Force`, `DrumModule`              |
| `+0x18`…`+0x4c` | small integers, presumably capability flags                    |

| idx | Code    | id       | Display name                 | Family       |
|-----|---------|----------|------------------------------|--------------|
| 0   | `none`  | —        | *(no name — the fallback)*   | —            |
| 1   | `ACV0`  | `0x017f` | MPC Renaissance              | `MPC`        |
| 2   | `ACV1`  | `0x0113` | MPC Studio                   | `MPC`        |
| 3   | `ACV3`  | `0x0121` | MPC Element                  | `MPC`        |
| 4   | `ACV5`  | `0x013a` | MPC X                        | `MPC`        |
| 5   | `ACV5S` | `0x013a` | MPC X SE                     | `MPC`        |
| 6   | `ACV6`  | `0x0000` | *(name slot holds the code)* | `MPC`        |
| 7   | `ACV7`  | `0x0137` | MPC Touch                    | `MPC`        |
| 8   | `ACV8`  | `0x013b` | MPC Live                     | `MPC`        |
| 9   | `ACV9`  | `0x013d` | MPC Studio Black             | `MPC`        |
| 10  | `ACVA`  | `0x0146` | MPC One                      | `MPC`        |
| 11  | `ACVA2` | `0x0156` | MPC One+                     | `MPC`        |
| 12  | `ACVA3` | `0x0100` | MPC One                      | `MPC`        |
| 13  | `ACVB`  | `0x0147` | MPC Live Mk II               | `MPC`        |
| 14  | `ACVC`  | `0x015a` | MPC XL                       | `MPC`        |
| 15  | `ACVF`  | `0x014a` | MPC Studio 2                 | `MPC`        |
| 16  | `ACVG`  | `0x0158` | MPC Live III                 | `MPC`        |
| 17  | `ACVM`  | `0x014b` | MPC Key 61                   | `MPC`        |
| 18  | `ACVM2` | `0x0102` | *(name slot holds `ACVM`)*   | `ACVM2`      |
| 19  | `ACVR`  | `0x0157` | MPC Key 37                   | `MPC`        |
| 20  | `ACVR2` | `0x0101` | MPC Key 37                   | `MPC`        |
| 21  | `ADA2`  | `0x0140` | Force                        | `Force`      |
| 22  | `ADA2S` | `0x0104` | Force                        | `Force`      |
| 23  | `ACVK`  | `0x0141` | *(name slot holds the code)* | `ACVK`       |
| 24  | `LDMD`  | `0x014a` | Alesis Prime Drum Module     | `DrumModule` |
| 25  | `LDMF`  | `0x014b` | Alesis Core Drum Module      | `DrumModule` |

> This is **what the application recognises**, which is a larger set than
> [what this firmware ships device trees for](MPC.md#hardware-and-product-codes). The presence of a
> code here is not evidence that the device runs this image — `ACVC`, `ACVF`, `ACVG`, `ACVA3`,
> `ACVM2`, `ACVR2`, `ACV6` and `ACVK` have no `.dtb` in any image examined.
> {style="warning"}

Three things the table shows beyond the codes themselves:

- **It spans product lines.** `ADA2`/`ADA2S` (Force) and `LDMD`/`LDMF` (Alesis drum modules) sit in
  the MPC binary's own table, so one hardware abstraction covers MPC, Force and Alesis.
- **It spans two hardware eras.** `ACV0`, `ACV1`, `ACV3`, `ACV7` and `ACV9` are the desktop-controller
  generation — USB controllers driven by desktop MPC software rather than standalone Linux units — so
  some codes could never appear in `inmusic,product-code` on a standalone device.
- **Three entries have no real display name**: `ACV6`, `ACVM2` and `ACVK` carry a code string in the
  name slot. `ACVA3` and `ACVR2` reuse the names of `ACVA` and `ACVR`.

### How an unrecognised code behaves {id="unrecognised-code"}

The lookup is a plain linear search that **skips entry 0 and returns 0 when nothing matches**:

| Address      | Role                                                                        |
|--------------|-----------------------------------------------------------------------------|
| `0x01f7aee0` | reads `inmusic,product-code`, calls the search, caches the result           |
| `0x01f6a73c` | linear search over entries 1–25; `return 0` on no match                     |
| `0x01f6a9bc` | accessor: `if (index < 0x1a) s = table[index]; if (s == NULL) s = fallback` |

Entry 0 is `"none"` with a **NULL name pointer**, so an unrecognised or missing product code makes the
UI render the hardware name as `<Unknown>` — and the info screen's firmware heading, which is the
hardware name plus the literal `" Firmware"`, becomes `<Unknown> Firmware`.

> The result is cached behind a `__cxa_guard` one-time initialiser, so the code is read **once per
> process**. Creating or correcting the device-tree node after MPC has started will not take effect
> until it is restarted.
> {style="note"}

The numeric id at `+0x04` is not part of the match; the search compares the code string. The id is
also not unique — `ACV5`/`ACV5S` share `0x013a`, `ACVF`/`LDMD` share `0x014a`, and `ACVM`/`LDMF` share
`0x014b` — so while it looks like a USB product ID, that has not been confirmed. Everything in this
section was read from the `3.9.1` binary and has not been diffed against other versions.

## Telemetry

MPC has two independent channels.

| Channel     | Destination                                                                          | Notes                                                                            |
|-------------|--------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| **Sentry**  | `https://e53c7927c8f44619b322ff4de861380f@o230257.ingest.sentry.io/4504809045295104` | Crash reporting. Same Sentry org (`o230257`) as [](Engine.md), different project |
| **PostHog** | **not present in the image**                                                         | Product analytics — see below                                                    |

Crash reporting is Crashpad-backed: compiled into MPC, plus a standalone `/usr/bin/crashpad_handler`
that MPC launches with `--no-rate-limit` and `--shared-client-connection`. No separate minidump
upload URL appears in the image, so minidumps go to the same Sentry host.

The analytics channel is `Analytics::PostHog::Session` / `Analytics::PostHog::UserIdentity`, driven
through `Analytics::CurlHttpSession`, with settings keys `AnalyticsSettings.enabled`,
`AnalyticsSettings.PostHog.opaqueId`, `AnalyticsSettings.PostHog.userEmail` and
`AnalyticsSettings.PostHog.userId`. Note that this identifies the user by email — it is not anonymous.

> **No PostHog hostname, project key or capture endpoint exists anywhere in the image**, searched as
> both ASCII and UTF-16. It is definitively not the `o230257.ingest.sentry.io` host, and it is not
> any of the self-hosted PostHog endpoints [](Engine.md) uses. Where it posts is unresolved; it may
> be configured at runtime, possibly via `api.inmusicbrands.com` or `device.inmusicbrands.com`, both
> of which the binary contains. **Blocking the Sentry host alone does not stop this channel.**
> {style="warning"}

Whether analytics is enabled by default could not be determined: `AnalyticsSettings.enabled` exists
only inside the binary, with no shipped settings file, so the default is compiled in.

### Other outbound hosts

`api.inmusicbrands.com`, `auth.profile.inmusicbrands.com`, `profile.inmusicbrands.com`,
`device.inmusicbrands.com` (account, auth and device services), `feedback.akaipro.com/feedback/create/`
(user-initiated feedback, over plain **HTTP**), plus store and download links to `thempcstore.com`,
`akaipro.com`, `splice.com` and `2getheraudio.com`.

`/usr/share/Splice/splicesync` is a separate 5 MiB Go binary that MPC launches, bundling
`github.com/getsentry/sentry-go v0.11.0`. It has no DSN compiled in and nothing in the rootfs sets
`SENTRY_DSN`, so its Sentry is inert unless MPC injects one into its environment.

## Related Files

- [](MPC.md) covers the operating system this application ships on.
- [](Engine.md) is the equivalent application on the Denon DJ side; the two share a Sentry
  organisation and an `az0x` base distribution but no code.
- [](Engine-Ghidra-Analysis.md) documents the `.ARM.exidx` recovery technique, which this binary
  needs just as much.
- [](HARDWARE-ID-Test-App.md) covers the separate factory test application.
