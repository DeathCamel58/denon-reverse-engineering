# UART

[UART](https://en.wikipedia.org/wiki/Universal_asynchronous_receiver-transmitter) is used to send data back and forth
between some devices. We can use this for console access.

> This page is about the **console** UART. On some devices a *second* UART carries the control surface as MIDI rather
> than a serial console — it appears in the device tree as a `serial-midi` node labelled `Control Surface`, and ALSA
> exposes it at `/dev/snd/by-path/platform-ff190000.serial`. See [](Engine-MIDI-Devices.md).
> {style="note"}

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

`oem inmusic-unlock-magic-7de5fbc22b8c524e` has since been confirmed to work: it lifts the
bootloader's blanket refusal to accept writes, after which `fastboot flash` succeeds. See
[](Updating.md#with-fastboot) for the full command reference and

> `oem format`, `oem resetenv`, `oem update-bootloader` and `oem flash-bootloader` are destructive, and
> the unlock above enables all of them.
> {style="warning"}

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

## SC6000

### Internal Image {id="internal-image_SC6000"}

![](SC6000 Internal.jpg)

The main board with the bottom panel off.

### UART Connection {id="uart-connection_SC6000"}

![](SC6000 UART.jpg)

Close-up of where those leads land, taken from the center of the internal image.

Wiring (same color scheme as the Prime Go):

| Color  | UART Pin |
|--------|----------|
| Red    | GND      |
| Orange | TX       |
| Yellow | RX       |

UART Speed: 115200 Baud

Captured on EngineOS `5.0.14` (`scarthgap`), PCB `AZ01b` rev`N`, product code `JP13`.

### U-Boot {id="u-boot_SC6000"}

The bootloader here is **`2016.05-inmusic-20200917`, built Oct 21 2020** — five years older than the
`6.6.119-az01-2025-12-17-rt67` kernel it loads. That is the expected result rather than an anomaly:
inMusic update images for these devices carry no bootloader payload, so U-Boot stays at whatever
shipped from the factory no matter how far the OS is updated. See [](Updating.md#with-fastboot). For
comparison the Prime Go log above is `2016.05-inmusic-20230621` (Nov 2023) — same U-Boot base
version, different factory build.

Two things in the log worth knowing:

- `** File not found /boot/rk3288-az01b.dtbo **` — `boot.scr` probes for an `az01b` device-tree
  overlay that this rootfs does not ship. Non-fatal; boot continues.
- The `Build: jenkins-Planck-Embedded_Release-157` tag is inMusic's internal CI job. Prime Go's is
  `-1943` from the same pipeline.

#### Boot Log {collapsible="true" id="boot-log_SC6000_u-boot"}

```
```
{ src="devices/JP13/u-boot-boot-log" }

### Linux {id="linux_SC6000"}

Kernel command line pins `isolcpus=1,2,3`, reserving three of the four A12 cores away from the
general scheduler for the real-time audio path. Note also the Paragon `ufsd` module (`UFSD_HEAD
lke_10.9.2_b331`), an out-of-tree commercial NTFS/HFS+ driver that taints the kernel — it is built
`Build_for__inMusic_Embedded_k5.4_2021-11-18`, against 5.4 headers, yet loads under 6.6.

#### Boot Log {collapsible="true" id="boot-log_SC6000_linux"}

```
```
{ src="devices/JP13/linux-boot-log" }

#### dmesg {collapsible="true" id="dmesg_SC6000"}

```
```
{ src="devices/JP13/linux-dmesg" }

#### journalctl {collapsible="true" id="journalctl_SC6000"}

```
```
{ src="devices/JP13/linux-journalctl" }

#### eMMC Partitions {id="partitions_SC6000"}

The on-device layout, which `fastboot` will not report — `partition-size` is unimplemented on these
units (see [](Updating.md#with-fastboot)):

| Partition     | Label            | Size  | Mount                  |
|---------------|------------------|-------|------------------------|
| `mmcblk0p1`   | `uboot-spl`      | 64 K  |                        |
| `mmcblk0p2`   | `env`            | 32 K  | U-Boot environment     |
| `mmcblk0p3`   | `uboot`          | 384 K |                        |
| `mmcblk0p4`   | `splash`         | 4 M   |                        |
| `mmcblk0p5`   | `recoverysplash` | 4 M   |                        |
| `mmcblk0p6`   | `rootfs`         | 500 M | `/`                    |
| `mmcblk0p7`   | `data`           | 6.8 G | `/media/az01-internal` |
| `mmcblk0boot0`|                  | 4 M   | eMMC boot area         |
| `mmcblk0boot1`|                  | 4 M   | eMMC boot area         |

Three things follow from this.

**The `rootfs` partition is exactly 500 M**, matching the 524,288,000-byte payload that update images
carry for it byte for byte. Flashing that payload raw leaves no slack and no overflow.

**The `env` partition's PARTUUID is `8f868bbe-d889-f84f-a64d-d077e73b4f09`** — the same value
`/etc/fw_env.config` names on the Prime GO, and unchanged across every `JP11` release from `1.6.2` to
`5.0.4`. It is baked into the partition template for the platform rather than generated per unit.

**The bootloader is single-slot here.** The updater binary knows how to write `spl1`/`spl2` and
`uboot1`/`uboot2` A/B pairs, but this device has one `uboot-spl` and one `uboot`, so that redundancy
is for some other product in the range.

```
```
{ src="devices/JP13/linux-partitions" }

#### Kernel Modules {collapsible="true" id="lsmod_SC6000"}

Only eleven modules are resident, and the inMusic ones are `snd_soc_inmusic_jp07` and
`snd_soc_rockchip_inmusic_jp07` — the **`JP07` (SC5000)** codec drivers, on a `JP13`. That is the
shared-module claim in [](inMusic-Kernel-Modules.md) confirmed from a running device rather than
inferred from an unpacked image. The rest are Broadcom Wi-Fi/Bluetooth and the Paragon `ufsd`/`jnl`
pair.

```
```
{ src="devices/JP13/linux-lsmod" }

#### USB Devices {collapsible="true" id="usb_SC6000"}

```
```
{ src="devices/JP13/linux-usb-devices" }

#### Audio {collapsible="true" id="audio_SC6000"}

Three cards, but only **one PCM stream** between them — `02-00` on the `JP07` codec, playback only,
one subdevice. Cards `0` and `1` (the wheel display and the controller) enumerate as USB-Audio class
yet expose no PCM at all, which makes them USB-**MIDI** endpoints rather than audio ones; see
[](Engine-MIDI-Devices.md). There is no capture device on this unit.

```
```
{ src="devices/JP13/linux-audio" }

#### Listening Sockets {id="sockets_SC6000"}

> **The unit was not in stock configuration when this was captured** — see the note below on `sshd`.
> Treat the port-22 rows as an artifact of the test setup, not as shipped behavior.
> {style="warning"}

`ss` is not present on this rootfs, so this is the raw `/proc/net` view. Decoded:

| Proto  | State  | Local                     | Note                             |
|--------|--------|---------------------------|----------------------------------|
| `tcp6` | LISTEN | `::ffff:127.0.0.1:50010`  | gRPC `enginesync.v1`             |
| `tcp6` | LISTEN | `::ffff:10.2.0.211:50010` | gRPC, bound per-interface        |
| `tcp`  | LISTEN | `0.0.0.0:41401`           | license-activation callback      |
| `udp6` | UNCONN | `:::51337` ×3             | undocumented broadcast socket    |
| `tcp6` | LISTEN | `:::` 11 ephemeral ports  | `33431`, `35559`, `35703`, `35815`, `36783`, `38969`, `39105`, `41581`, `42311`, `44787`, `45555` |
| `udp`  | UNCONN | `0.0.0.0:5353` ×2         | Avahi mDNS                       |
| `tcp`  | LISTEN | `127.0.0.1:40463`         | loopback IPC, two peer pairs     |
| `tcp`  | LISTEN | `127.0.0.1:53`, `127.0.0.53:53`, `127.0.0.54:53` | `systemd-resolved` |

Two details are worth drawing out.

**Port `50010` is bound twice, to `127.0.0.1` and to the LAN address individually, rather than once
to the wildcard.** That is the signature of an interface-enumeration loop rather than a single
wildcard bind, and it is visible here on real hardware.

**Everything on the wildcard is reachable from the LAN.** The eleven ephemeral `tcp6` listeners and
the three `51337` UDP sockets all bind `::`, and with `bindv6only=0` that covers IPv4 too.

`/proc/net` gives socket inodes but not owning processes. Correlating them needs a matching
`ls -l /proc/*/fd` capture from the same boot, which was not taken.

```
```
{ src="devices/JP13/linux-sockets" }

#### Services {collapsible="true" id="services_SC6000"}

```
```
{ src="devices/JP13/linux-services" }

#### Network {collapsible="true" id="network_SC6000"}

> MAC addresses are truncated to their OUI, and the EUI-64-derived IPv6 interface identifiers are
> masked with them. Everything else is verbatim.
> {style="note"}

```
```
{ src="devices/JP13/linux-network" }

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
