# MIDI Devices

Engine talks to its control surface, wheel displays, mixer and motor boards as **MIDI devices** — over USB for most
hardware, or over a UART for some (see [](#transports)). This page collects where all of that lives in the firmware:
payload paths, version metadata, detection rules and the flashing mechanism.

Control *mappings* are a separate topic — see [](Controller-Assignments.md).

[//]: # (TODO: This is a stub. Fill in per-device specifics, the FirmwareUpdater flow, and the SysEx command set.)

## Where things live

| What                                    | Where                                                               |
|-----------------------------------------|---------------------------------------------------------------------|
| **MCU firmware payloads**               | `/usr/Engine/Firmware/<Device Name>/`                               |
| **Firmware version + payload filename** | `/usr/Engine/Firmware/<Device Name>/firmware.json`                  |
| **Device detection / MIDI identity**    | `/usr/Engine/Content/KnownDevices.vfsb`                             |
| **Control mappings**                    | `/usr/Engine/AssignmentFiles/PresetAssignmentFiles/<HWID>/`         |
| **Flashing tool**                       | `/usr/Engine/FirmwareUpdater` (takes the product code as `argv[1]`) |
| **Device enumeration helper**           | `/usr/Engine/MidiDeviceScanner` (shells out to `amidi`)             |
| **UART control-surface ALSA node**      | `/dev/snd/by-path/platform-ff190000.serial`                         |

> Every Engine OS image ships **all** devices' firmware payloads and assignment files, not just the ones for the
> hardware the image targets — a player's image contains the console payloads and vice versa. So presence in these
> directories says nothing about which device you are looking at.
> {style="note"}

## Firmware payloads

One directory per flashable board, named `<HWID> <Role>`. Roles seen so far: `Controller` (the control surface),
`Display` (platter/wheel display), `Mixer`, `Motor`, and on some consoles `Left Display MCU` / `Right Display MCU`.

> In releases up to `1.6.2` these are named after the *product* rather than the hardware ID — see
> [](#schema-drift).
> {style="note"}

Each directory holds exactly two things — the payload and its descriptor:

```
/usr/Engine/Firmware/JP13 Controller/
├── firmware.json
└── jp13_tp_update_00_16_118596.dfu
```

### `firmware.json`

**The version is stored here**, as a string, alongside the payload filename.

This layout is remarkably stable. Across all 48 non-beta releases from `1.3.1` to `5.0.4` — 822 descriptors in total —
the path never moves, every single subdirectory of `Firmware/` has a `firmware.json`, all of them parse as JSON, and
`version`, `filename` and `type` are present in every one and are always strings. Reading a board's version needs no
version-specific handling.

The *rest* of the schema does drift, though — see [](#schema-drift).

Three payload types exist and the descriptor shape differs between them.

`rbin` — the in-house format, used for control surfaces:

```json
{
	"version" : "1.0.1.38",
	"filename" : "UpdateImage.rbin",
	"type" : "rbin",
	"deviceToFlash" : "Control Surface:Control Surface 16:0"
}
```

`dfu` — standard USB DFU, used for STM32-based boards:

```json
{
	"version" : "00.16",
	"filename" : "jp13_tp_update_00_16_118596.dfu",
	"type" : "dfu",
	"alt-names" : [ "@Internal Flash  /0x08000000/04*016Kg,01*064Kg,01*128Kg"],
	"start-sysex" : ["F0 00 02 0B 01 0D 72 00 00 F7"],
	"stop-command" : "dfu-util -a %alt% -S %serial% -s 0x08000000:1:leave -U %temp%"
}
```

`bin` — a raw blob, used for the mixer DSP and the motor boards on the motorised players:

```json
{
	"version" : "01.69",
	"filename" : "JC11DSP0169_0873B9BD.bin",
	"type" : "bin",
	"deviceToFlash" : "Controller"
}
```

| Key                  | Type   | Meaning                                                                                       |
|----------------------|--------|-----------------------------------------------------------------------------------------------|
| `version`            | string | Firmware version. `rbin` uses dotted quads, `dfu` and `bin` use `MM.mm`                        |
| `filename`           | string | Payload file, relative to the same directory                                                    |
| `type`               | string | `dfu`, `bin`, or `rbin`                                                                         |
| `deviceToFlash`      | string | Which attached device this targets — see below                                                   |
| `realName`           | string | Matches the `realName` of the `KnownDevices` entry this payload belongs to                       |
| `min-version`        | string | Lowest running firmware version that can be updated directly, for staged upgrades                |
| `alt-names`          | list   | *(`dfu`)* DFU alt-setting strings, i.e. the target's flash layout. May list several variants     |
| `start-sysex`        | list   | SysEx that puts the board into update mode before flashing                                      |
| `stop-command`       | string | *(`dfu`)* `dfu-util` template. `%alt%`, `%serial%` and `%temp%` are substituted at runtime       |
| `serial-sysex-start` | string | *(retired)* SysEx to begin a serial-port transfer                                                |
| `write-serial`       | bool   | *(retired)* the only non-string, non-list value in the whole schema                              |

`deviceToFlash` is a **string in every release** that has it — `1.5.3` is the sole release where the key is absent
entirely. Some values are a plain role name like `"Controller"`, others are colon-delimited, e.g.
`"Control Surface:Control Surface 16:0"` or `"PRIME 4 Left Wheel Display:PRIME 4 Left Wheel Display MIDI 24:0"`, where
the last two fields look like a MIDI port name and an index. Some name the marketing product rather than the role.

[//]: # (TODO: Work out the colon-delimited deviceToFlash grammar — the trailing "<port name> <n>:0" part especially.)

#### Schema drift {id="schema-drift"}

Eight distinct key sets appear across the history, so a parser should treat everything except `version`, `filename` and
`type` as optional:

| Key                  | Present in                                                             |
|----------------------|------------------------------------------------------------------------|
| `version`, `filename`, `type` | every release, `1.3.1` – `5.0.4`                              |
| `alt-names`, `start-sysex`, `stop-command` | every release                                     |
| `deviceToFlash`      | every release except `1.5.3`                                            |
| `min-version`        | every release except `1.3.3` and `1.5.1` – `1.5.3`                      |
| `realName`           | `2.0.0` onwards — introduced with the switch to HWID-named directories   |
| `serial-sysex-start` | `1.3.2` – `2.3.3` only, then retired                                     |
| `write-serial`       | `1.3.1` – `2.3.3` only, then retired                                     |

The `type` values arrived in stages too: `dfu` and `bin` from the beginning, **`rbin` only from `1.6.0`**.

> The directory names changed at the same time as `realName` appeared. Up to `1.6.2` they are product-named —
> `SC5000 Controller`, `PRIME4 Mixer`, `MIXSTREAM PRO Controller` — and from `2.0.0` they are HWID-named, so
> `SC5000 Controller` becomes `JP07 Controller` and so on. Anything walking the full history has to map the two naming
> schemes onto each other or it will read one MCU as two unrelated boards.
> {style="warning"}

### `rbin` payload format

Starts with a `!Rbn` magic and an inline copyright string, then a device identifier:

```
00000000  21 52 62 6e 43 6f 70 79  72 69 67 68 74 20 32 30   !RbnCopyright 20
00000010  32 32 20 69 6e 4d 75 73  69 63 20 42 72 61 6e 64   22 inMusic Brand
00000020  73 00 00 00 11 a0 e4 15  00 00 00 00 08 00 00 01   s.......ØÄ......
```

The `u32` at `0x24` is little-endian and its **low byte is the device's MIDI SysEx identity byte** — `0x11` for `JP20`,
`0x12` for `JP21`, `0x3f` for `NH08`. The upper bytes are close to but not identical to the device ID in the update
container's device table (see [](Signed-Firmware-Layout.md)), so don't assume they match.

[//]: # (TODO: Decode the rest of the rbin header — the field at 0x2c looks like a version, and the body is Thumb code.)

### `dfu` payload format

Standard DFU files with the usual `UFD` suffix at the end, so `dfu-util` and `dfu-suffix` read them directly. The
`alt-names` strings are the target's DFU interface descriptors — `@Internal Flash /0x08000000/04*016Kg,01*064Kg,01*128Kg`
describes an STM32 internal flash map, and the base address `0x08000000` matches the one in `stop-command`. Where several
`alt-names` are listed, the same payload covers boards fitted with different flash sizes.

## Device detection

`/usr/Engine/Content/KnownDevices.vfsb` is a **VFS bundle** — a small container holding one `KnownDevices.xml` fragment
per product code, keyed by that code. Engine loads the fragment matching the product code it was launched with (see
[](Engine.md)) and uses it to recognise what is plugged in.

Each fragment opens with the MIDI identity request Engine sends to probe a device:

```xml
<IdRequest message="7E 00 06 01"/> <!-- MIDI Specs, identity request -->
```

and then declares the devices it expects to find:

```xml
<Device type="USB" realName="Display">
    <class name="MIDI">
        <property name="DeviceInquiryResponse" value="7E ?? 06 02 00 02 0B 0D ?? ?? ?? ?? ?? ?? 01"/>
        <property name="AssignmentFileName" value="JP13 Display"/>
        <property name="UpdateOrder" value="2" />
    </class>
</Device>
```

| Property                | Meaning                                                                                                                                                                     |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `DeviceInquiryResponse` | Expected reply to the identity request, with `??` wildcarding variable bytes. The 8th byte is the device identity byte; the trailing byte distinguishes roles on one device |
| `AssignmentFileName`    | Which directory under `PresetAssignmentFiles/` to load the mapping from                                                                                                     |
| `FirmwareFileName`      | Which directory under `Firmware/` holds this device's payload                                                                                                               |
| `UpdateOrder`           | Flashing order when a product has several updatable boards                                                                                                                  |
| `RbinVersionFormat`     | Parse the reported version as an `rbin` dotted quad rather than the `dfu` `MM.mm` form                                                                                      |
| `Product`               | Present on only a few entries — purpose not yet established                                                                                                                 |
| `required`              | `true` means Engine will not run without this device present                                                                                                                |

Note `realName` is the role (`Controller`, `Display`, `Mixer`, …), which is what ties an entry to both its assignment
directory and its firmware directory.

[//]: # (TODO: Document the vfsb container layout itself, and what `Product` gates.)

## Transports {id="transports"}

Two ways a control surface attaches, and it differs by hardware generation:

* **USB MIDI** — the surface enumerates as a USB MIDI device and is matched via `KnownDevices.vfsb`. This is the common
  case.
* **UART / `serial-midi`** — some devices wire the surface to a SoC UART instead, declared in the device tree as a
  `serial-midi` child of the serial node with `label = "Control Surface"`. ALSA exposes it at
  `/dev/snd/by-path/platform-ff190000.serial`, and `/usr/Engine/Scripts/engine` talks to it directly with `amidi` on
  shutdown, e.g.

  ```bash
  amidi -p hw:/dev/snd/by-path/platform-ff190000.serial -S 'F0 00 01 3F 00 3F 7F 00 00 F7'
  ```

A device also has a **USB MIDI gadget** mode — `f_midi-0` — used to present itself as a controller to a host computer.
Its mapping lives beside the normal one, as `f_midi-0_Assignments.qml` / `f_midi-0_Device.qml`.

[//]: # (TODO: Establish which released devices use UART vs USB for the control surface, from the device trees.)

## Flashing

`/usr/Engine/FirmwareUpdater` does the work. It is not run directly by the user — Engine exits with a quit reason and the
wrapper script dispatches, from `/usr/Engine/Scripts/engine`:

```bash
"UpdateFirmware")
        /usr/Engine/FirmwareUpdater $PRODUCTCODE
        cleanup
        if [ "$PRODUCTCODE" = "JC11" ] ; then
                systemctl poweroff
        fi
        break
;;
```

`$PRODUCTCODE` is the device-tree product code with a trailing `S` stripped, so `S`-suffixed variants share their
parent's firmware set. See [](Firmware-Updater.md).

[//]: # (TODO: Trace how FirmwareUpdater picks payloads, reads the current version off the device, and reports progress.)

## SysEx

inMusic's manufacturer prefix is `00 02 0B` for Denon DJ and `00 01 3F` for Numark. The byte after it is the device
identity byte — the same value as the low byte of the device ID.

Commands observed so far, from the `_Device.qml` mappings and the firmware descriptors:

| Command byte | Meaning                                         |
|--------------|-------------------------------------------------|
| `04`         | Query absolute control positions                |
| `42`         | Request power-on button state / test-mode entry |
| `72`         | Enter DFU mode (`start-sysex`)                  |
| `7F`         | Keep-alive / shutdown                           |

[//]: # (TODO: Build out the command table — 03/0A/0B/10/60/7c appear in the display mappings.)

## See also

* [](Controller-Assignments.md) — the QML control mappings themselves
* [](HARDWARE-ID-Test-App.md) — SysEx identity bytes per device, and entering test mode
* [](Firmware-Updater.md) — the updater application
* [](MIDI-Device-Scanner.md) — the enumeration helper
