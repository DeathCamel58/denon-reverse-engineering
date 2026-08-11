# Test App

There are two different versions, depending on what firmware the device is running.

| Firmware Version | App Type             |
|------------------|----------------------|
| >= `4.1.0`       | [New](#new-test-app) |
| < `4.1.0`        | [Old](#old-test-app) |

> I haven't figured out how to take screenshots of this. While the app is running, `cat /dev/fb0 > /frame` then running
`convert -size 800x1280 -rotate 90 -depth 8 bgra:frame frame.png` **does not** include an image of this app, even though
> that works on other apps that render to `fb0`. I'd love to figure this out and include screenshots.
> {style="note"}

There are two ways in. The [hidden Advanced tab](#advanced-tab) works from the UI on every device I've checked and
needs no special hardware access, so start there. The [power-on button combinations](#power-on-entry) are a
lower-level fallback that differ per device.

## Hidden Advanced Tab {id="advanced-tab"}

[](Engine.md) contains a developer menu that isn't shown by default. Unlock it by tapping either the **Engine OS
version string** or the **product name heading** in the settings screens **5 times**.

An **Advanced** tab appears alongside Control Center / Layout / User Profile / Settings, and
`Start Production Test Application` is its first entry. Selecting it does exactly what a power-on combination does
(see [](#how-entry-works) below).

The tab also exposes `Reboot To Computer Mode`, the device and STM serial numbers, the product code, Planck startup
time, and a statistics section with a system monitor, page cache statistics and a frame timer.

The gate is a simple counter in the embedded QML, with no per-product condition on it:

```Javascript
readonly property int secretTapsNeeded: 5
property int secretTaps: secretTapsNeeded
signal tapSecret()
onTapSecret: {
	if(secretTaps > 0) {
		secretTaps -= 1;
		if(secretTaps === 0) {
			showAdvancedTab = true;
		}
	}
}
```

Two elements emit `tapSecret()`, both `MouseArea`s covering a headline:

| `objectName`  | What it renders                    |
|---------------|------------------------------------|
| `Version`     | `Engine OS 5.0.4 (git hash)`       |
| `ProductName` | `Config.product.name`              |

### Device Support

Verified present in all three `5.0.4` `Engine` builds. Every device below has `secretTapsNeeded: 5`, both tap
targets, the Advanced tab, and `/usr/bin/test-app-launcher` on disk.

| `Engine` build | Devices                                                                  |
|----------------|--------------------------------------------------------------------------|
| `5ccb4f82`     | Prime 2, Prime 4, Prime GO, SC5000, SC5000M, SC6000, SC6000M             |
| `dedfbbb0`     | Prime 4 Plus, Prime GO Plus, SC Live 2, SC Live 4                        |
| `aarch64`      | Rane System One                                                          |

> Confirmed on real hardware on the Prime GO (`JP11`) and SC6000 (`JP13`).
>
> This is only verified on `5.0.4`. I haven't checked whether older firmwares have it, and the Mixstream Pro,
> Mixstream Pro GO and Mixstream Pro + firmwares don't extract, so those are unknown.
> {style="note"}

## Power-On Entry {id="power-on-entry"}

| Device             | How to access                                     |
|--------------------|---------------------------------------------------|
| SC Live 4 (`JP21`) | <shortcut>Left Pad 1+Shift</shortcut> during boot |

> If you find any additional key combinations to open this app, please file a PR or
> issue [here](https://github.com/DeathCamel58/denon-reverse-engineering/issues)
> {style="note"}

### How Entry Works {id="how-entry-works"}

[](Engine.md) never reads the button combination itself. The control surface MCU decides, and `Engine` only asks it
a yes/no question at startup over MIDI SysEx, using command `0x42`:

```Javascript
function requestPowerOnButtonState() {
	Midi.sendSysEx("F0 00 02 0B 7F 12 42 00 00 F7")   // JP21
}

function sysEx(sysExString) {
	var valueList = sysExToIntList(sysExString)
	if(valueList[1] === 0x00 && valueList[2] === 0x02 && valueList[3] === 0x0B && valueList[6] === 0x42)
	{
		if(valueList[9] === 0x0) { console.log("No special power on request") }
		else if(valueList[9] === 0x1) { console.log("Request test-mode entry"); quitToTestApp() }
	}
}
```

`quitToTestApp()` writes `TestApp` to `/tmp/engine-quit-reason` and exits. `/usr/Engine/Scripts/engine` reads that
file and runs `/usr/bin/test-app-launcher`. The Advanced tab entry calls the same `quitToTestApp()`.

All 16 products implement this handshake. Only the manufacturer and device ID bytes differ:

| Device         | SysEx request                   | Device         | SysEx request                   |
|----------------|---------------------------------|----------------|---------------------------------|
| `JP07`         | `F0 00 02 0B 01 06 42 00 00 F7` | `JP20`         | `F0 00 02 0B 7F 11 42 00 00 F7` |
| `JC11`/`JC11S` | `F0 00 02 0B 7F 08 42 00 00 F7` | `JP21`         | `F0 00 02 0B 7F 12 42 00 00 F7` |
| `JP08`         | `F0 00 02 0B 01 0A 42 00 00 F7` | `NH08`/`NH08S` | `F0 00 01 3F 7F 3F 42 00 00 F7` |
| `JC16`         | `F0 00 02 0B 7F 0B 42 00 00 F7` | `NH10`         | `F0 00 01 3F 7F 59 42 00 00 F7` |
| `JP11`/`JP11S` | `F0 00 02 0B 7F 0C 42 00 00 F7` | `RMZ2`         | `F0 00 00 17 7F 27 42 00 00 F7` |
| `JP13`         | `F0 00 02 0B 01 0D 42 00 00 F7` | `JC20`         | `F0 00 02 0B 01 10 42 00 00 F7` |
| `JP14`         | `F0 00 02 0B 01 0E 42 00 00 F7` |                |                                 |

These come from each product's `[HARDWARE_ID]_Controller_Device.qml` in
`/usr/Engine/AssignmentFiles/PresetAssignmentFiles/`.

### Combinations In The MCU Firmware

The MCU firmware ships in the rootfs under `/usr/Engine/Firmware/[HARDWARE_ID] Controller/`, so the checks can be
read directly. See [](inMusic-Kernel-Modules.md) for how the rest of the firmware is laid out.

On the Prime GO (`JP11`), test mode is a three-button `AND`:

```C
uint CheckTestModeCombo(void) {
  uVar1 = GetButtonState(0x28);
  uVar2 = GetButtonState(0x29);
  uVar3 = GetButtonState(0x2f);
  return uVar1 & uVar2 & uVar3;
}
```

On the SC6000 (`JP13`) the same three reads exist, but each one **overwrites** the result instead of `AND`-ing it,
so only the last read has any effect:

```C
bVar1 = GetButtonState(0x32);  *DAT_08012e58 = bVar1 ^ 1;
bVar1 = GetButtonState(0x33);  *DAT_08012e58 = bVar1 ^ 1;   // overwrites
bVar1 = GetButtonState(0x34);  *DAT_08012e58 = bVar1 ^ 1;   // overwrites
if (*DAT_08012e58 != 0) SetTestModeFlag(1);
```

This is confirmed in the disassembly, not a decompiler artifact. The two other combinations checked in the same
function do it correctly, loading and `AND`-ing the previous value first. It looks like a genuine firmware bug.

> The values passed to `GetButtonState` are **bit positions in the MCU's scan matrix, not MIDI note numbers**. On
> the Prime GO the combination uses indices `0x28`, `0x29` and `0x2F`, but the highest MIDI note in its assignment
> file is `39`.
>
> I don't have a scan-index to physical-button mapping for any device yet. On the SC6000, holding single front panel
> buttons does **not** trigger test mode, and indices `0x32`-`0x34` are read only in this one boot check and nowhere
> else in the firmware, so they may not be front panel buttons at all.
> {style="warning"}

Two indices worth knowing about while testing on an SC6000, since they change what you'll see:

| Indices         | Effect                                                                         |
|-----------------|--------------------------------------------------------------------------------|
| `0x26` + `0x27` | Enters a spin loop **before** the test mode check, so the unit appears to hang |
| `0x15` + `0x16` | Sweeps all 80 LEDs to full brightness, then continues booting                  |

Some scan indices are not buttons at all. Each firmware masks a fixed set of bits out of the button buffer and reads
those same indices separately into a configuration word, which looks like board revision or variant strapping. On the
Prime GO that's indices `5`, `6`, `8`, `9`, `0x0B`, `0x0C`, `0x2D`, `0x2E`, `0x33` and `0x34`; on the SC6000 it's `8`,
`9`, `0x30` and `0x31`.

## New Test App

This is a program that lists other test apps that are located on attached storage, and
allows the user to launch them from a UI.

This will scan any flash drive that's plugged in for test apps. The binary looks for `manifest.yaml`, but I'm not sure
if this file should exist inside a zip file or not.

### Application Data {id="application-data_new"}

Path: `/bin/test-app-launcher`

### Runtime Console Output {collapsible="true" id="runtime-console-output_new"}

`JP11TestApp`

```
```

{ src="applications/test-app/new-runtime" }

### Loading Test Apps

When the test app is started, it iterates over all mounts on the device, and calls the methods to check for V1 or V2
packaged apps.

After it gets a list of test apps that are available, it displays them in the launcher, and allows you to run them.

There are two packaging methods of test apps. Making an application that is packaged either way listed here will allow
you to run arbitrary software on the device.

- [`V1` packaging](#v1-packaging)
- [`V2` packaging](#v2-packaging)

#### V1 Packaging

I'm currently unsure of how V1 packaging works.

[//]: # (TODO: Reasearch this)

#### V2 Packaging

1. Checks if `TestAppsCatalog.zip` exists on the root of the drive currently being scanned.
2. Unzips the archive, and checks for [`manifest.yaml`](#v2-manifest-yaml)
3. Attempts to automatically launch the best script in `manifest.yaml`, allows the user to go to the UI listing of all
   test apps, where the user can load any test app in the manifest.

##### `manifest.yaml` Documentation {id="v2-manifest-yaml"}

This file contains an array of test applications under the testApps key, each specifying details needed to locate,
identify, and launch test applications on compatible hardware.

###### Structure Overview

```yaml
testApps:
  - version: <version>
    osVersionID: <osVersionID>
    products:
      - <productID1>
      - <productID2>
    signedImage: <True/False>
    basePath: <path>
    relativeExePath: <exePath>
    launcher-XXXXXX: <launcherName>
    name: <appName>
```

###### Fields

- `testApps`
    - Description: A required top-level key. testApps must be named exactly as shown.
    - Type: Array
    - Contents: Each item in this array defines a single test application with specific properties as outlined below.

###### Application Properties

Each application entry in the `testApps` array contains the following fields:

- `version`
    - Description: Specifies the version of the test application.
    - Type: String
    - Example: 1.0.0
- `osVersionID`
    - Description: The operating system version that the test application is compatible with.
    - Type: String
    - Example: 2023.02.11
- `products`
    - Description: An array of hardware IDs that the test app supports. This is used to specify the compatible devices
      for the application.
    - Type: Array of Strings
    - Example:

```yaml
products:
  - JP11
  - JP12
```

- `signedImage`
    - Description: Likely indicates if the test application is digitally signed.
    - Type: Boolean
    - Example: true or false
- `basePath`
    - Description: Specifies the folder within the archive (e.g., ZIP file) where the test application files are
      located.
    - Type: String
    - Example: jp11
- `relativeExePath`
    - Description: Specifies the executable file path relative to basePath that is used to launch the application.
    - Type: String
    - Example: JP11TestApp
- `launcher-XXXXXX`
    - Description: Purpose is currently unknown. This field may be used as a reference to a launcher configuration or
      specific launcher type.
    - Type: String
    - Example: SomeLauncher
- `name`
    - Description: The display name of the test application.
    - Type: String
    - Example: JP11 Test App

###### Example

Below is an example `manifest.yaml` that shows a typical structure with two test applications:

```yaml
testApps:
  - version: 1.0.0
    osVersionID: 2023.02.11
    products:
      - JP08
    signedImage: False
    basePath: test-apps/jp08
    relativeExePath: JP08TestApp
    launcher-XXXXXX: SomeLauncher
    name: JP08 Test App
  - version: 1.0.0
    osVersionID: 2023.02.11
    products:
      - JP11
    signedImage: False
    basePath: test-apps/jp11
    relativeExePath: JP11TestApp
    launcher-XXXXXX: SomeLauncher
    name: JP11 Test App
```

## Old Test App

This is an application for testing the buttons and switches of the unit.

### Application Data {id="application-data_old"}

Path: `/usr/share/test-app/[HARDWARE_ID]/[HARDWARE_ID]TestApp`

- Prime Go (`JP11TestApp`): This shows an image of the Prime Go, and highlights items as they're tested.

This application uses the folder at `/usr/share/test-app/[HARDWARE_ID]` to get the device images and configuration for
the current device.

[//]: # (TODO: Additional Research)

### Runtime Console Output {collapsible="true" id="runtime-console-output_old"}

`JP11TestApp`

```
```

{ src="applications/test-app/old-runtime" }
