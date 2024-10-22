# Software

## Engine OS

[Engine OS](https://enginedj.com/software/enginedj-os) is InMusic's DJ operating system. This is mainly just
a [buildroot](Buildroot.md) OS, with some additional custom software.

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

## Hardware ID

The hardware ID can be found by running `cat /sys/firmware/devicetree/base/inmusic,product-code` on the target device.
