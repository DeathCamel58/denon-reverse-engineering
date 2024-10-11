# Firmware Updater

Not really sure what this does. It seems likely that it loads the firmware in `/usr/Engine/Firmware/[FIRMWARE_PART]/`,
and flashes the devices with the firmwares included in the subdirectory.

## Application Data

Path: `/usr/Engine/FirmwareUpdater`

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
| [SC5000](https://www.denondj.com/sc5000%20prime-sc5000primexus)      | :white_check_mark:                                   |
| SC5000M (No Product Page)                                            | :white_check_mark:                                   |
| [SC6000](https://www.denondj.com/sc6000-prime.html)                  | :white_check_mark:                                   |
| [SC6000M](https://www.denondj.com/sc6000m-prime.html)                | :white_check_mark:                                   |
| [SC Live 2](https://www.denondj.com/sclive2.html)                    | :grey_question: (Don't know how to extract firmware) |
| [SC Live 4](https://www.denondj.com/sclive4.html)                    | :grey_question: (Don't know how to extract firmware) |

[//]: # (TODO: Additional Research)

## Runtime Console Output

`LD_LIBRARY_PATH=/usr/qt/lib:${LD_LIBRARY_PATH} /usr/Engine/FirmwareUpdater`

```
QIconvCodec::convertToUnicode: using Latin-1 for conversion, iconv_open failed
QIconvCodec::convertFromUnicode: using Latin-1 for conversion, iconv_open failed
QStandardPaths: XDG_RUNTIME_DIR not set, defaulting to '/tmp/runtime-root'
Unable to set double buffer mode! (Invalid argument)
air.deviceidentifier: Can't find file: "KnownDevices.xml"
bool FirmwareUpdater::checkForUpdateAndPrepare()
bool FirmwareUpdater::checkForUpdateAndPrepare() Run: 0
void FirmwareUpdater::tryToUpdateUnscannedDevices(const QStringList&) ()
Finished
air.devicemanager.midi.out: The port isn't' opened for Midi::Out::PRIME GO Control Surface
```
