# SoundSwitch

From the [SoundSwitch website](https://www.soundswitch.com):
> SoundSwitch automatically creates dynamic music-synchronized light shows that amplify your DJ performance and create
> an immersive experience for your audience. SoundSwitch is compatible with both DMX, Philips Hue Smart Lights and
> Nanoleaf allowing you to effortlessly illuminate your professional event, live stream, or house party.

## Application Data

Path: `/usr/SoundSwitch/SoundSwitch`

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

## Runtime Console Output

`LD_LIBRARY_PATH=/usr/qt/lib:/usr/SoundSwitch/libraries:${LD_LIBRARY_PATH} /usr/SoundSwitch/SoundSwitch`

```
QIconvCodec::convertFromUnicode: using Latin-1 for conversion, iconv_open failed
QIconvCodec::convertToUnicode: using Latin-1 for conversion, iconv_open failed
"Running on JP11 (PrimeGo)"
SoundSwitch version: 2.8.2.5-450ef2c1ab
QStandardPaths: XDG_RUNTIME_DIR not set, defaulting to '/tmp/runtime-root'
Unable to set double buffer mode! (Invalid argument)
[2024-10-11 11:32:18.435] [info] [main.cpp:364] Set log level to info
("QSQLITE")
[11 Oct/24 11:32:18.452 -04:00] [thread 570] [MIDIManager.cpp:159] Private(): RtMidi version: 4.0.0, compiled api: ALSA, Jack
[EDTLS_CLNT] Build info: RELWITHDEBINFO@:
Use UUID for EnginePrimeController: {ffffffff-e9d9-45f0-b5ac-adab7819fa74}
[11 Oct/24 11:32:18.656 -04:00] [thread 570] [EnginePrimeNet.cpp:119] AddNetworkAddress(): EnginePrimeHost::AddNetworkAddress: 127.0.0.1/255.0.0.0
Running player mode: "All-in-one"
activePlayerList active player: 
geometry:  QRect(0,0 128x64)
[11 Oct/24 11:32:18.764 -04:00] [thread 570] [SoundSwitchDocPrivate.cpp:761] operator()(): Using four deck: false
OnUsePlayheadSmoothing: 0
qt.svg: Cannot open file ':/ui_img/hue/deviceBridgeV2.svg', because: No such file or directory
qt.svg: Cannot open file ':/ui_img/hue/deviceBridgeV2.svg', because: No such file or directory
QIODevice::read (QFile, ":/qss/soundswitch.qss"): device not open
FORCING SWAP INTERVAL = 1
FORCING SWAP INTERVAL = 1
[11 Oct/24 11:32:18.862 -04:00] [thread 570] [mainwindow.cpp:977] operator()(): Use sceneIndex for performance mode: 1
SoundSwitch.Communication: wait for connection...
fixture list size in the venue:  2
position list size in the venue:  12
[11 Oct/24 11:32:19.303 -04:00] [thread 570] [SoundSwitchDoc.cpp:1293] LoadLightingTrack(): track file not found: /media/az01-internal/SoundSwitchData/dynamic/.ssfile
[11 Oct/24 11:32:19.303 -04:00] [thread 570] [SoundSwitchDoc.cpp:1293] LoadLightingTrack(): track file not found: /media/az01-internal/SoundSwitchData/dynamic/.ssfile
stateMap onConnected: 127.0.0.1
[11 Oct/24 11:32:19.895 -04:00] [thread 570] [SoundSwitchDoc.cpp:1293] LoadLightingTrack(): track file not found: /media/az01-internal/SoundSwitchData/dynamic/.ssfile
[11 Oct/24 11:32:19.896 -04:00] [thread 570] [SoundSwitchDoc.cpp:1293] LoadLightingTrack(): track file not found: /media/az01-internal/SoundSwitchData/dynamic/.ssfile
```
