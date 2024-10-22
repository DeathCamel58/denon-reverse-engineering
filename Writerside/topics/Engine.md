# Engine

This is the main DJ application that the user interacts with. This does all the mixing, cloud file loading, and almost
everything else the user sees.

## Application Data

Path: `/usr/Engine/Engine`

### Available in firmware for

| Hardware                                                             | Available                                            |
|----------------------------------------------------------------------|------------------------------------------------------|
| [Mixstream Pro](https://www.numark.com/mixstream-pro)                | :white_check_mark:                                   |
| [Mixstream Pro GO](https://www.numark.com/product/mixstream-pro-go)  | :grey_question: (Don't know how to extract firmware) |
| [Mixstream Pro +](https://www.numark.com/product/mixstream-pro-plus) | :grey_question: (Don't know how to extract firmware) |
| [Prime 2](https://www.denondj.com/prime-2-prime2xus)                 | :white_check_mark:                                   |
| [Prime 4](https://www.denondj.com/prime-4-prime4xus)                 | :white_check_mark:                                   |
| [Prime 4 Plus](https://www.denondj.com/prime-4-plus.html)            | :grey_question: (Don't know how to extract firmware) |
| [Prime Go](https://www.denondj.com/prime-go.html)                    | :white_check_mark:                                   |
| [Prime Go Plus](https://www.denondj.com/prime-go-plus.html)          | :grey_question: (Don't know how to extract firmware) |
| [SC5000](https://www.denondj.com/sc5000%20prime-sc5000primexus)      | :white_check_mark:                                   |
| SC5000M (No Product Page)                                            | :white_check_mark:                                   |
| [SC6000](https://www.denondj.com/sc6000-prime.html)                  | :white_check_mark:                                   |
| [SC6000M](https://www.denondj.com/sc6000m-prime.html)                | :white_check_mark:                                   |
| [SC Live 2](https://www.denondj.com/sclive2.html)                    | :grey_question: (Don't know how to extract firmware) |
| [SC Live 4](https://www.denondj.com/sclive4.html)                    | :grey_question: (Don't know how to extract firmware) |

[//]: # (TODO: Additional Research)

## Runtime Console Output {collapsible="true"}

`LD_LIBRARY_PATH=/usr/qt/lib:${LD_LIBRARY_PATH} /usr/Engine/Engine -d0`

- `-d0` flag is the debug logging flag

```
QIconvCodec::convertToUnicode: using Latin-1 for conversion, iconv_open failed
QIconvCodec::convertFromUnicode: using Latin-1 for conversion, iconv_open failed
DropboxManager: Dropbox application has not been found.
Flush to zero not applied. This may lead to audio dropouts and/or freezes.
QStandardPaths: XDG_RUNTIME_DIR not set, defaulting to '/tmp/runtime-root'
Unable to set double buffer mode! (Invalid argument)
QVirtualKeyboardPlugin default IM has been initialized
[I] ============================================================================                                         [2024-10-11T13:38:24.111Z] [                        ]
[I] Engine  -  4.0.1                                                                                                     [2024-10-11T13:38:24.111Z] [                        ]
[I] ============================================================================                                         [2024-10-11T13:38:24.112Z] [                        ]
[I] Command Line Arguments:                                                                                              [2024-10-11T13:38:24.112Z] [                        ]
[I]    -d0                                                                                                               [2024-10-11T13:38:24.112Z] [                        ]
[I] DenonDJ  "PRIME GO 4.0.1 2c873aee05"  starting up                                                                    [2024-10-11T13:38:24.123Z] [                        ]
[I] sentry  id: 12b64ccd-13d3-4d97-829a-8d3d0a54e538                                                                     [2024-10-11T13:38:24.126Z] [                        ]
[I] session id: 0df0b322-f5cb-4844-88ee-bf6e1b1c7f7b                                                                     [2024-10-11T13:38:24.126Z] [                        ]
[I] Anonymous Statistics     : enabled                                                                                   [2024-10-11T13:38:24.177Z] [                        ]
[I] Automatic crash reporting: enabled                                                                                   [2024-10-11T13:38:24.194Z] [                        ]
[2705:2705:20241011,093824.209004:INFO retry_upload_thread.cc:39] Start
[I] Local UUID: "{34b78ffa-056f-488b-b068-bc42742d5665}"                                                                 [2024-10-11T13:38:24.254Z] [                        ]
[W] The port isn't' opened for Midi::Out::PRIME GO Control Surface                                                       [2024-10-11T13:38:24.268Z] [air.devicemanager.midi.o]
[I] Semantic version: 4.0.1                                                                                              [2024-10-11T13:38:24.492Z] [                        ]
[ ] _analyzerApplicationPath "OfflineAnalyzer"                                                                           [2024-10-11T13:38:24.515Z] [                        ]
[W] There is no databases available!                                                                                     [2024-10-11T13:38:24.700Z] [                        ]
[W] Only C and default locale supported with the posix collation implementation                                          [2024-10-11T13:38:24.702Z] [                        ]
[W] Numeric mode unsupported in the posix collation implementation                                                       [2024-10-11T13:38:24.702Z] [                        ]
[I] TimeZone detect started "JP11" "4.0.1" "2c873aee05"                                                                  [2024-10-11T13:38:24.705Z] [air.planck.controller.ti]
[W] EDisks filesystem added:  "/io/github/inmusicdev/edisksd/dev/mmcblk0p6"  label:  "1244c94bdd26dbae"                  [2024-10-11T13:38:24.717Z] [                        ]
[I] New device connected: "/io/github/inmusicdev/edisksd/dev/mmcblk0p6"                                                  [2024-10-11T13:38:24.717Z] [air.devicemanager.hotplu]
[I] "/io/github/inmusicdev/edisksd/dev/mmcblk0p6" mounted at "8a5edab282632443"                                          [2024-10-11T13:38:24.719Z] [air.devicemanager.hotplu]
[I] "/io/github/inmusicdev/edisksd/dev/mmcblk0p6" mounted at "/usr/4b2f4473e24574e8/601ad8d0cac495d5.0"                  [2024-10-11T13:38:24.722Z] [air.devicemanager.hotplu]
[W] EDisks filesystem added:  "/io/github/inmusicdev/edisksd/dev/mmcblk0p7"  label:  "b27b20f5e019cb4f"                  [2024-10-11T13:38:24.726Z] [                        ]
[I] New device connected: "/io/github/inmusicdev/edisksd/dev/mmcblk0p7"                                                  [2024-10-11T13:38:24.726Z] [air.devicemanager.hotplu]
[I] "/io/github/inmusicdev/edisksd/dev/mmcblk0p7" mounted at "bd47413b5c03dfc6"                                          [2024-10-11T13:38:24.728Z] [air.devicemanager.hotplu]
[I] "/io/github/inmusicdev/edisksd/dev/mmcblk0p7" mounted at "/97227e70daeb56c4"                                         [2024-10-11T13:38:24.731Z] [air.devicemanager.hotplu]
[W] [ 0x430c2dc ] Job Failed "Reading Playlists" ""                                                                      [2024-10-11T13:38:24.738Z] [air.planck.jobcontroller]
[ ] OfflineAnalyzer (0): QIconvCodec::convertToUnicode: using Latin-1 for conversion, iconv_open failed                  [2024-10-11T13:38:24.739Z] [                        ]
[ ] OfflineAnalyzer (0): QIconvCodec::convertFromUnicode: using Latin-1 for conversion, iconv_open failed                [2024-10-11T13:38:24.740Z] [                        ]
[ ] OfflineAnalyzer (1): QIconvCodec::convertToUnicode: using Latin-1 for conversion, iconv_open failed                  [2024-10-11T13:38:24.740Z] [                        ]
[ ] OfflineAnalyzer (1): QIconvCodec::convertFromUnicode: using Latin-1 for conversion, iconv_open failed                [2024-10-11T13:38:24.740Z] [                        ]
[I] TimeZone detect started "JP11" "4.0.1" "2c873aee05"                                                                  [2024-10-11T13:38:25.342Z] [air.planck.controller.ti]
[ ] Core Dump disabled                                                                                                   [2024-10-11T13:38:25.348Z] [                        ]
[I] TimeZone detected:  "America/New_York"                                                                               [2024-10-11T13:38:25.450Z] [air.planck.controller.ti]
[I] Board revision: 10                                                                                                   [2024-10-11T13:38:30.778Z] [qml                     ]
[ ] FORCING SWAP INTERVAL = 1                                                                                            [2024-10-11T13:38:31.031Z] [                        ]
[ ] FORCING SWAP INTERVAL = 1                                                                                            [2024-10-11T13:38:31.656Z] [                        ]
[I] TimeZone detected:  "America/New_York"                                                                               [2024-10-11T13:38:31.657Z] [air.planck.controller.ti]
[C] Planck init complete, handing over to exec. Took  7909 ms                                                            [2024-10-11T13:38:31.840Z] [air.planck              ]
[W] Failed to set discovery name Error: bluetooth::error::Code::NoUsableAdapter                                          [2024-10-11T13:38:31.844Z] [air.planck.bluetooth.con]
[W] Failed to set set discoverable Error: bluetooth::error::Code::NoUsableAdapter                                        [2024-10-11T13:38:31.844Z] [air.planck.bluetooth.con]
[W] setLatestLocalVersion version: "" path: ""                                                                           [2024-10-11T13:38:32.083Z] [                        ]
[W] setLatestLocalVersion version: "" path: ""                                                                           [2024-10-11T13:38:32.090Z] [                        ]
[W] JobController state:                                                                                                 [2024-10-11T13:38:32.091Z] [air.planck.jobcontroller]
[W]     current number of threads: 8                                                                                     [2024-10-11T13:38:32.091Z] [air.planck.jobcontroller]
[W]     maximum number of threads: 8                                                                                     [2024-10-11T13:38:32.091Z] [air.planck.jobcontroller]
[W]     queue length: 8 jobs size: 10                                                                                    [2024-10-11T13:38:32.091Z] [air.planck.jobcontroller]
[W] Jobs running:                                                                                                        [2024-10-11T13:38:32.091Z] [air.planck.jobcontroller]
[W]      "Searching on device" count 1                                                                                   [2024-10-11T13:38:32.091Z] [air.planck.jobcontroller]
[W] Jobs queued:                                                                                                         [2024-10-11T13:38:32.092Z] [air.planck.jobcontroller]
[W]      "Checking updates" count 2                                                                                      [2024-10-11T13:38:32.092Z] [air.planck.jobcontroller]
[W]      "Processing view state controller event" count 1                                                                [2024-10-11T13:38:32.092Z] [air.planck.jobcontroller]
[W]      "Searching on device" count 4                                                                                   [2024-10-11T13:38:32.092Z] [air.planck.jobcontroller]
[W]      "Updating network drives" count 1                                                                               [2024-10-11T13:38:32.092Z] [air.planck.jobcontroller]
[W] JobController state:                                                                                                 [2024-10-11T13:38:32.095Z] [air.planck.jobcontroller]
[W]     current number of threads: 8                                                                                     [2024-10-11T13:38:32.096Z] [air.planck.jobcontroller]
[W]     maximum number of threads: 8                                                                                     [2024-10-11T13:38:32.096Z] [air.planck.jobcontroller]
[W]     queue length: 13 jobs size: 15                                                                                   [2024-10-11T13:38:32.096Z] [air.planck.jobcontroller]
[W] Jobs running:                                                                                                        [2024-10-11T13:38:32.096Z] [air.planck.jobcontroller]
[W]      "Searching on device" count 1                                                                                   [2024-10-11T13:38:32.096Z] [air.planck.jobcontroller]
[W] Jobs queued:                                                                                                         [2024-10-11T13:38:32.096Z] [air.planck.jobcontroller]
[W]      "Checking updates" count 2                                                                                      [2024-10-11T13:38:32.097Z] [air.planck.jobcontroller]
[W]      "Loading persisted samples" count 1                                                                             [2024-10-11T13:38:32.097Z] [air.planck.jobcontroller]
[W]      "Processing view state controller event" count 2                                                                [2024-10-11T13:38:32.097Z] [air.planck.jobcontroller]
[W]      "Reading History" count 1                                                                                       [2024-10-11T13:38:32.097Z] [air.planck.jobcontroller]
[W]      "Reading Playlists" count 1                                                                                     [2024-10-11T13:38:32.097Z] [air.planck.jobcontroller]
[W]      "Searching on device" count 4                                                                                   [2024-10-11T13:38:32.097Z] [air.planck.jobcontroller]
[W]      "Updating network drives" count 2                                                                               [2024-10-11T13:38:32.097Z] [air.planck.jobcontroller]
[W] qrc:/ControlSurfaceModules/ControlSurfaceModules/private/STMBank.qml:14:2: QML Connections: Implicitly defined onFoo [2024-10-11T13:38:32.178Z] [                        ]
[W]  properties in Connections are deprecated. Use this syntax instead: function onFoo(<arguments>) { ... }              [2024-10-11T13:38:32.178Z] [                        ]
[I] TimeZone detect triggered during cooldown                                                                            [2024-10-11T13:38:32.186Z] [air.planck.controller.ti]
[W] qrc:/ControlSurfaceModules/ControlSurfaceModules/private/STMBank.qml:14:2: QML Connections: Implicitly defined onFoo [2024-10-11T13:38:32.255Z] [                        ]
[W]  properties in Connections are deprecated. Use this syntax instead: function onFoo(<arguments>) { ... }              [2024-10-11T13:38:32.255Z] [                        ]
[W] qrc:/ControlSurfaceModules/ControlSurfaceModules/HardwareMixerMic.qml:35:2: QML Connections: Implicitly defined      [2024-10-11T13:38:32.256Z] [                        ]
[W]  onFoo properties in Connections are deprecated. Use this syntax instead: function onFoo(<arguments>) { ... }        [2024-10-11T13:38:32.256Z] [                        ]
[W] qrc:/ControlSurfaceModules/ControlSurfaceModules/HardwareMixerMic.qml:35:2: QML Connections: Implicitly defined      [2024-10-11T13:38:32.290Z] [                        ]
[W]  onFoo properties in Connections are deprecated. Use this syntax instead: function onFoo(<arguments>) { ... }        [2024-10-11T13:38:32.290Z] [                        ]
[W] qrc:/ControlSurfaceModules/ControlSurfaceModules/VUMeter.qml:109: TypeError: Cannot read property 'c5fafca31e7f33c7' [2024-10-11T13:38:32.291Z] [                        ]
[W]  of null                                                                                                             [2024-10-11T13:38:32.291Z] [                        ]
[W] qrc:/ControlSurfaceModules/ControlSurfaceModules/VUMeter.qml:108: TypeError: Cannot read property 'c5fafca31e7f33c7' [2024-10-11T13:38:32.291Z] [                        ]
[W]  of null                                                                                                             [2024-10-11T13:38:32.291Z] [                        ]
[W] qrc:/ControlSurfaceModules/ControlSurfaceModules/VUMeter.qml:109: TypeError: Cannot read property 'c5fafca31e7f33c7' [2024-10-11T13:38:32.291Z] [                        ]
[W]  of null                                                                                                             [2024-10-11T13:38:32.291Z] [                        ]
[W] qrc:/ControlSurfaceModules/ControlSurfaceModules/VUMeter.qml:108: TypeError: Cannot read property 'c5fafca31e7f33c7' [2024-10-11T13:38:32.291Z] [                        ]
[W]  of null                                                                                                             [2024-10-11T13:38:32.291Z] [                        ]
[ ] No special power on request                                                                                          [2024-10-11T13:38:32.295Z] [qml                     ]
[W] setLatestLocalVersion version: "" path: ""                                                                           [2024-10-11T13:38:39.462Z] [                        ]
[W] setLatestLocalVersion version: "" path: ""                                                                           [2024-10-11T13:38:39.488Z] [                        ]
[W] MigrationFinished uuid for path  "851c9b64ee7abb42"   "9b7a80aa-4f11-4118-a42f-d631c2dddd34"                         [2024-10-11T13:38:39.488Z] [air.planck.database     ]
[W] MigrationFinished uuid for path  "a394739a3f3a75b5"   "0992b059-9b42-4a42-b840-b63e49fdf11c"                         [2024-10-11T13:38:39.497Z] [air.planck.database     ]
```

## Controller Assignments

Engine uses `/usr/Engine/AssignmentFiles/PresetAssignmentFiles/[HARDWARE_ID]/[HARDWARE_ID]_*.qml` to describe all of the
buttons, LEDs, and such on the unit. These buttons are mapped to various features.

For example, the `JP11_Controller_Assignments.qml` includes this by default:

```
PerformanceModes {
    ledType: LedType.RGB
    modesModel: ListModel {
        ListElement {
            note: 11
            view: 'CUES'
        }
        ListElement {
            note: 12
            view: 'LOOPS'
            altView: 'AUTO'
        }
        ListElement {
            note: 13
            view: 'ROLL'
            altView: 'SAMPLER'
            shiftView: 'SAMPLER'
        }
    }
}
```

This shows the mods that a button is mapped to with a standard press (`view`), the mode that it switches to when pressed
again (`altView`), and the mode that it switches to when `SHIFT` is held while the button is pressed (`shiftView`).

In this example, slicer mode (which isn't a feature of the `JP11`) can be added to the `LOOP` button by changing the `ListElement` for the button to:
```
ListElement {
    note: 12
    view: 'LOOPS'
    shiftView: 'SLICER'
}
```
