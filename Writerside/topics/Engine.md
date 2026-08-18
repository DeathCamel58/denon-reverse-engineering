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

## Command-line arguments

`Engine` parses its arguments with `QCommandLineParser`, and calls
`setSingleDashWordOptionMode(ParseAsLongOptions)`, so **long names take a single dash** —
`-skipFirmwareUpdate`, not just `--skipFirmwareUpdate`. Both forms work. This is why the launch
script in `/usr/Engine/Scripts/engine` can write `-d0 -loggerOptions "..."`.

The table below is the complete set for **5.0.4**, recovered from the registration function at
`00bb746c`, which constructs `QCommandLineOption` and calls `addOption` exactly **44** times. All 44
names were recovered, and there is no second registration site, so nothing is missing. `--version`
also works, via `addVersionOption()`.

**Being registered does not mean being implemented.** The **Read** column records whether the name is
referenced anywhere *other than* its own `addOption` call — i.e. whether setting it could do anything
at all. Eleven of the 44 are never read, so they are inert; see [](#options-that-are-never-read).
`no` in that column is not a claim about what an option would do, only that nothing looks at it.

Options with an empty **Value** column are flags. Value names and descriptions are the binary's own.

| Option                         | Value                | Type        | Read      | Description                                                                                                              |
|--------------------------------|----------------------|-------------|-----------|--------------------------------------------------------------------------------------------------------------------------|
| `aaa`                          |                      | flag        | C++, QML  | Enables the Access All Areas mode.                                                                                       |
| `activeFocusItem`              |                      | flag        | **no**    | Shows the item with active focus.                                                                                        |
| `d0`                           |                      | flag        | argv      | Sends debug output to the console, instead of a log file.                                                                |
| `dcolor`                       |                      | flag        | argv      | Output console only debug with ANSI color.                                                                               |
| `dcolour`                      |                      | flag        | argv      | Output console only debug with ANSI colour.                                                                              |
| `demoDriveDir`                 | `path`               | path        | C++       | Adds a directory as a demo drive                                                                                         |
| `detectUILags`                 |                      | flag        | C++       | Prints a message if frame to frame time exceeds a 30 milliseconds threshold                                              |
| `detectUILagsThreshold`        | `threshold`          | int (ms)    | C++       | Overrides the default threshold in milliseconds for detectUILags                                                         |
| `dfilterGdpr`                  |                      | flag        | argv      | Enables gdpr filtering for log files, and sentry.                                                                        |
| `dimensionInfo`                |                      | flag        | **no**    | Shows the width and height of the application.                                                                           |
| `disableElastiqueOnTrackDeck`  |                      | flag        | C++       | Disables elastique for track decks.                                                                                      |
| `discoPort`                    | `port`               | int         | C++, argv | Sets the network discovery port.                                                                                         |
| `dshowNoise`                   |                      | flag        | argv      | Shows noise normally filtered out from debug output.                                                                     |
| `dumpBigLog`                   |                      | flag        | C++       | Writes a one off 16 MB of log file data.                                                                                 |
| `ecoMode`                      |                      | flag        | C++       | Locks the UI update rate to 30 fps.                                                                                      |
| `enableAutoSelectSingleSource` |                      | flag        | **no**    | If only one device is connected it will be selected.                                                                     |
| `extraDriveDir`                | `path,ejectable`     | path[,bool] | C++       | Adds a directory as a source drive. The optional parameter ejectable can be set to true but else would default to false. |
| `forceEnableShortcuts`         |                      | flag        | C++       | Enables shortcuts via QWERTY keyboard on embedded.                                                                       |
| `frameTimer`                   |                      | flag        | QML       | Shows a frame timer.                                                                                                     |
| `freezeCheckThreadPools`       |                      | flag        | C++       | Checks whether the asynchronous librarian runner or main thread pool are frozen.                                         |
| `gigTimer`                     |                      | flag        | **no**    | Shows the gig timer                                                                                                      |
| `ignoreDatabaseCheck`          |                      | flag        | C++       | Disables the Database schema hash check.                                                                                 |
| `laser`                        |                      | flag        | **no**    | Just try that yourself.                                                                                                  |
| `loggerOptions`                | `options`            | CSV list    | C++, argv | Comma separated list of options to modify the content and the layout of the logging output. Possible values are: %1.     |
| `noAnalyzerOutput`             |                      | flag        | C++       | *(no description: the binary literally stores `....`)*                                                                   |
| `noFeatureUpdatePopup`         |                      | flag        | C++       | Do not show the new feature update pop-up message.                                                                       |
| `noOnBoardingScreen`           |                      | flag        | C++       | Hides the on-boarding screen.                                                                                            |
| `noPromptForDb`                |                      | flag        | C++       | Dismiss user prompts during database migration                                                                           |
| `noSplashScreen`               |                      | flag        | C++, QML  | Hides the splash screen.                                                                                                 |
| `nosmpopup`                    |                      | flag        | **no**    | Disable Sync Manager popup message boxes.                                                                                |
| `onlyOneAnalyzer`              |                      | flag        | C++       | Will spawn only a single offline analyzer.                                                                               |
| `packMissingFiles`             |                      | flag        | C++       | Packs files even if the audio file is missing.                                                                           |
| `pageCacheSize`                | `bytes`              | int         | C++       | Sets the page cache size.                                                                                                |
| `reportTags`                   | `tags`               | path list   | C++       | Paths to JSON files with custom analytics report tags                                                                    |
| `samplerDemoDriveDir`          | `path`               | path        | C++       | Adds a directory as a sampler demo drive                                                                                 |
| `showSystemMonitor`            |                      | flag        | **no**    | Shows the System Monitor                                                                                                 |
| `sideDisplays`                 |                      | flag        | **no**    | Show left and right side screens.                                                                                        |
| `simulateMixer`                | `number of channels` | int         | C++       | Simulates a network mixer.                                                                                               |
| `simulatePowerFail`            |                      | flag        | C++, QML  | Enables option for simulating a power failure.                                                                           |
| `skipFirmwareUpdate`           |                      | flag        | C++       | Skip firmware updates. See [](Engine-MIDI-Devices.md).                                                                   |
| `spoofSensors`                 |                      | flag        | C++       | Uses fake sensors instead.                                                                                               |
| `updateServerUrl`              | `URL`                | URL         | **no**    | Overwrites the URL for the default update server (`https://autoupdate.airmusictech.com/PrimeUpdates.xml`).               |
| `virtualDrivesDir`             | `path`               | path        | **no**    | A directory to put dynamically created virtual drives.                                                                   |
| `watchdog`                     |                      | flag        | **no**    | *(no description: the binary literally stores `...`)*                                                                    |

> `watchdog` and `noAnalyzerOutput` ship with placeholder descriptions (`...` and `....`), so what
> they do cannot be read off the help text. `noAnalyzerOutput` is at least read; `watchdog` is not.
> {style="note"}

### How options are consumed {id="how-options-are-consumed"}

Three separate mechanisms read arguments, which matters because checking only the first produces
false negatives — `d0` looks unused until the second is taken into account:

| Mechanism                                                        | What it looks like in the binary                          | Used by                                                              |
|------------------------------------------------------------------|-----------------------------------------------------------|----------------------------------------------------------------------|
| `QCommandLineParser::isSet(QString)` through the wrapper at `00bb1908` | a standalone name literal                            | most options                                                          |
| a raw `argv` scan against **dash-prefixed** literals at `01d7ac08` | `-d0`, `-dcolour`, `-dcolor`, `-loggerOptions`, `-dshowNoise`, `-dfilterGdpr` | the logger, which initialises before Qt's parser exists |
| the QML bridge `Planck.hasCommandLineArgument(name)` / `Planck.commandLineArgumentValue(name)` | the name inside a QML blob, not a standalone literal | `aaa`, `frameTimer`, `noSplashScreen`, `simulatePowerFail`, `renderInfo`, `wifiPassword` |

The QML channel is easy to miss: Engine's QML is stored as plaintext, so those names appear only as
substrings of a much larger string and never as their own literal.

### Options that are never read {id="options-that-are-never-read"}

`activeFocusItem`, `dimensionInfo`, `enableAutoSelectSingleSource`, `gigTimer`, `laser`, `nosmpopup`,
`showSystemMonitor`, `sideDisplays`, `updateServerUrl`, `virtualDrivesDir`, `watchdog`.

For each of these the name occurs exactly **once** in the binary and the only reference to it is the
`addOption` call, so no code path can observe it. They parse without error and then do nothing.
`laser` is the clearest case — its description, "Just try that yourself.", is the whole joke.

Additional checks behind that claim:

- Engine's embedded QML contains none of them, in either ASCII or UTF-16.
- No other `/usr/Engine` binary or data file mentions them.
- Only `isSet(const QString&)` is linked; `isSet(const QCommandLineOption&)` is **not**, so an option
  can only ever be queried through its name string.
- The same holds in `5.1.0-beta8`: all eleven still occur once and none appear in a
  `hasCommandLineArgument` call.
- `laser` specifically has been registered-but-unread in every release checked — `1.6.2`, `2.4.0`,
  `3.4.0`, `4.3.4`, `5.0.4` and `5.1.0-beta8`.

> Two options are read yet still reported as having no visible effect. `aaa` is read in four C++
> functions and gates two QML `AIRDropZone` loaders — drag-and-drop targets for the decks — which
> have nothing to show on hardware with no drag source. `frameTimer` sets `showFrameTimer` on a
> `Row`, OR'd with the state-map property `/GUI/Scripted/ShowFrameTimer`, so it is wired but its
> component may not be instantiated on these products. "Read" is therefore a lower bound on
> usefulness, not a promise.
> {style="warning"}

### Names that are queried but never registered

Six names are read by code yet never passed to `addOption`, so `QCommandLineParser` does not know
them: `runTests`, `repeatTests`, `repeatTestsFor`, `validateAMCResultsPath`, `renderInfo` and
`wifiPassword`. Qt's own `QCommandLineParser: option not defined:` warning string is present in the
binary. An unregistered name makes `parse()` record an unknown-option error and makes `isSet()` warn
and return `false`, so these read as leftover scaffolding rather than usable switches.

`runTests` is the interesting one, because `MidiDeviceControl::quitAndStartFirmwareUpdate`
(`00595758`) tests it first and returns immediately when set — which would make the whole
firmware-update exit a no-op. See [](Engine-MIDI-Devices.md).

### Version coverage

`skipFirmwareUpdate` was **added in `2.0.0`**; it is absent from every `1.x` build. The **Read**
column was determined against `5.0.4` and spot-checked against `5.1.0-beta8`; the rest of the table
has not been diffed across versions.

## Runtime Console Output {collapsible="true"}

`LD_LIBRARY_PATH=/usr/qt/lib:${LD_LIBRARY_PATH} /usr/Engine/Engine -d0`

- `-d0` flag is the debug logging flag; see [](#command-line-arguments) for the rest

```
```
{ src="applications/engine/runtime" }

## Related Files

- [](Controller-Assignments.md) covers how the hardware controls different functions of this application.
- [](Engine-MIDI-Devices.md) covers how this application finds its control surface, wheel displays and mixer over MIDI,
  and where their MCU firmware and versions live.
- [](Engine-Ghidra-Analysis.md) covers getting this binary into a navigable state in Ghidra.
- [](Engine-Networking.md) covers the gRPC services, discovery, and cloud endpoints.
