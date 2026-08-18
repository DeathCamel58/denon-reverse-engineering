# Firmware Updater

This flashes the MCU boards attached to the unit — control surface, wheel displays, mixer and motor — from the payloads
shipped in `/usr/Engine/Firmware/[FIRMWARE_PART]/`.

It isn't launched directly. [](Engine.md) exits with a quit reason of `UpdateFirmware` and the wrapper script
`/usr/Engine/Scripts/engine` dispatches, passing the product code as the only argument:

```bash
/usr/Engine/FirmwareUpdater $PRODUCTCODE
```

See [](Engine-MIDI-Devices.md) for the payload directory layout, the `firmware.json` descriptor that carries each
board's version, and how devices are matched in the first place.

[//]: # (TODO: Trace how it picks payloads, reads the running version off each board, and reports progress.)

## Application Data

Path: `/usr/Engine/FirmwareUpdater`

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

[//]: # (TODO: Additional Research)

## Runtime Console Output

`LD_LIBRARY_PATH=/usr/qt/lib:${LD_LIBRARY_PATH} /usr/Engine/FirmwareUpdater`

```
```
{ src="applications/firmware-updater/runtime" }
