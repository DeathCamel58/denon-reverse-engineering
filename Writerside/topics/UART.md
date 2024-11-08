# UART

[UART](https://en.wikipedia.org/wiki/Universal_asynchronous_receiver-transmitter) is used to send data back and forth
between some devices. We can use this for console access.

## Prime Go

On the Prime Go, the UART is located on an empty header that's already on the board.

### Internal Image {id="internal-image_Prime_Go"}

![](Prime Go Internal.jpg)

> There is a `RECOVERY` button located on the other side of the board in the bottom left of this image. Hold it
> down, press the power button, and it'll go into firmware update mode.
> {style="note"}

### UART Connection {id="uart-connection_Prime_Go"}

![](Prime Go UART.jpg)

This image is of the board on the bottom left of the internal image.

Wiring:

| Color  | UART Pin |
|--------|----------|
| Red    | GND      |
| Orange | TX       |
| Yellow | RX       |

UART Speed: 115200 Baud

### U-Boot

To get into recovery mode, run `fastboot 0` at the U-Boot command line. This will allow you to use the desktop updater
app to recover.

#### Fastboot OEM Commands {collapsible="true"}

Fastboot's OEM commands are specific to a device manufacturer. Here are the commands that I've found:

```
```
{ src="devices/JP11/fastboot-oem-commands" }

These were found by dumping `/dev/mmcblk0p6`, then running `strings mmcblk0p6 | grep oem`.

#### U-Boot Boot Log {collapsible="true"}

```
```
{ src="devices/JP11/u-boot-boot-log" }

#### Environment Variables {collapsible="true"}

```
```
{ src="devices/JP11/u-boot-environment-variables" }

#### Clocks {collapsible="true"}

```
```
{ src="devices/JP11/u-boot-clocks" }

#### Console Info {collapsible="true"}

```
```
{ src="devices/JP11/u-boot-coninfo" }

#### MMC Info {collapsible="true"}

```
```
{ src="devices/JP11/u-boot-mmc-info" }

#### Hush Shell Variables {collapsible="true"}

```
```
{ src="devices/JP11/u-boot-showvar" }

#### Version {collapsible="true"}

```
```
{ src="devices/JP11/u-boot-version" }

### Linux

#### dmesg {collapsible="true"}

```
```
{ src="devices/JP11/linux-dmesg" }

#### journalctl {collapsible="true"}

```
```
{ src="devices/JP11/linux-journalctl" }

## MPC Live

### Internal Image {id="internal-image_MPC_Live"}

![](MPC Live Internal.jpg)

### UART Connection {id="uart-connection_MPC_Live"}

![](MPC Live UART.jpg)

This image is of the board on the top right of the internal image.

Wiring (the 3 wires connected to the headers):

| Pin (Left to right in image) | UART Pin |
|------------------------------|----------|
| `1`                          | GND      |
| `2`                          | TX       |
| `3`                          | RX       |
| `4`                          | 3V3      |

UART Speed: 115200 Baud

> The UART speed here is a **guess**, please file a PR or
> issue [here](https://github.com/DeathCamel58/denon-reverse-engineering/issues)
> {style="note"}

For additional details, refer to Tadeáš Miňha's work on the [brmlab wiki](https://brmlab.cz/user/trimen/mpclive) or
Niklas Nisbeth's work on [their site](https://niklasnisbeth.gitlab.io/mpc-internals/).

### Linux {id="linux_MPC_Live"}

#### Boot Log {collapsible="true"}

```
```
{ src="devices/ACV8/linux-boot-log" }

## Other Devices

> I'd love some help with additional data here! If you have a device that I don't have a UART data for, please file a PR
> or issue with your device's UART pinout [here](https://github.com/DeathCamel58/denon-reverse-engineering/issues) if
> you know it!
> {style="note"}
