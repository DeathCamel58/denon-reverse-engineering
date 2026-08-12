# Rane

Rane's devices use the [](Engine-OS.md) operating system.

## Hardware IDs

| Device Name | Model ID (Per DTS) | Hardware ID | DTS Files                    |
|-------------|--------------------|-------------|------------------------------|
| System One  | `RANE SYSTEM ONE`  | `RMZ2`      | [](rk3588-az04-rmz2-dts.md)  |

## AZ04 Platform

The System One is the first device I've found that isn't built on the `RK3288`. It's an `RK3588` (4x Cortex-A76, 4x
Cortex-A55 in the DTS) on a new platform generation, `AZ04`, and it's the first `arm64` Engine OS device.

Notes below were measured against `SYSTEMONE-5.0.4-Update.img`, compared against `PRIMEGO-5.0.4-Update.img` so that
both sides are the same firmware version.

| Property     | `AZ01` (Prime GO, `5.0.4`)              | `AZ04` (System One, `5.0.4`)          |
|--------------|-----------------------------------------|---------------------------------------|
| SoC          | Rockchip RK3288                         | Rockchip RK3588                       |
| Userspace    | `ELF 32-bit`, `ARM EABI5` (`armhf`)     | `ELF 64-bit`, `ARM aarch64`           |
| Kernel       | `6.6.119-az01-2025-12-17-rt67`          | `6.18.9-imb-2026-02-06-rt3`           |
| GPU          | Mali-T764                               | Mali-G610 (`panthor` + Mesa)          |
| Audio card   | Custom machine driver                   | Mainline `simple-audio-card`          |
| Boot         | `boot.scr.uimg` + DTBs in `/boot`       | Signed kernel FIT (`sha256,rsa2048`)  |

> Both are Yocto/OpenEmbedded `scarthgap` (`5.0.14`) and both ship Qt `6.7.2`, so neither of those is specific to this
> platform. The [](Buildroot.md) notes describe the older `4.x` firmwares.
> {style="note"}

### Audio

This is the biggest change from the older platforms, and it makes the System One much easier to work with.

On `AZ01`/`AZ05`, the sound card is a pair of out-of-tree drivers. A custom machine driver plus a custom codec (see
`inmusic,jp11-audio` and `inmusic,jp11-audio-codec` in [](rk3288-az01-jp11-dts.md)).

On `AZ04`, the machine driver is gone. The card is mainline `simple-audio-card` bound to a mainline
`rockchip,rk3588-i2s-tdm` CPU DAI at `i2s@fe480000`:

```
sound {
	compatible = "simple-audio-card";
	simple-audio-card,name = "RMZ2";
	simple-audio-card,format = "dsp_b";

	simple-audio-card,cpu {
		dai-tdm-slot-num = <0x08>;
		dai-tdm-slot-width = <0x20>;
	};
};
```

The only inMusic-specific piece left is the codec, which takes all of its capabilities from the device tree:

```
az04-codec {
	compatible = "inmusic,az04-codec";
	inmusic,playback-channels = <0x06>;
	inmusic,capture-channels = <0x06>;
	inmusic,pcm-rates = <0xac44>;
	mute-gpios = <0x95 0x15 0x00>;
	reset-gpios = <0x7a 0x09 0x01>;
};
```

That driver ships in the rootfs as a 13 KB unstripped module at
`/usr/lib/modules/6.18.9-imb-2026-02-06-rt3/kernel/sound/soc/codecs/snd-soc-inmusic-az04.ko`:

```
license:        GPL v2
description:    inMusic AZ04 ASoC codec driver
author:         John Keeping <jkeeping@inmusicbrands.com>
alias:          of:N*T*Cinmusic,az04-codec
intree:         Y
vermagic:       6.18.9-imb-2026-02-06-rt3 SMP preempt_rt mod_unload aarch64
```

It has no register map and no I2C or SPI. The only kernel symbols it imports for hardware access are
`devm_gpiod_get_optional` and `gpiod_set_value_cansleep`, for the mute and reset lines, plus a regulator for `avdd`.
Everything else is DAPM widgets and routes. It's effectively a codec stub, so reimplementing it against a mainline
kernel is realistic.

> `intree: Y` means this was built inside a kernel source tree, so inMusic maintains a fork with this driver in
> `sound/soc/codecs/`. I still haven't seen any published source for it.
> {style="note"}

### Graphics

The System One has no `libmali` anywhere in the rootfs. It uses the mainline `panthor` DRM driver
(`kernel/drivers/gpu/drm/panthor/panthor.ko`) with the Mali CSF firmware blob at
`/usr/lib/firmware/arm/mali/arch10.8/mali_csffw.bin`, and a Mesa userspace (`libEGL.so.1`, `libGLESv2.so.2`,
`/usr/lib/gbm/dri_gbm.so`). Qt renders through EglFS with the KMS/GBM backend
(`libQt6EglFsKmsGbmSupport.so.6.7.2`).

This means the graphics stack on this device is entirely open source, unlike the situation described on
[](Mali-T764.md).

### Boot

The kernel FIT is signed, which the older unsigned layouts aren't:

```
description = "Kernel image for rk3588-az04-rmz2";
images {
	kernel { type = "kernel"; arch = "arm64"; os = "linux"; compression = "none"; };
	ramdisk { description = "Initrd"; type = "ramdisk"; arch = "arm64"; };
	fdt-rk3588-az04-rmz2 { type = "flat_dt"; arch = "arm64"; };
	fdt-cmdline { type = "flat_dt"; arch = "arm64"; };
};
configurations {
	conf-rk3588-az04-rmz2 { signature-0 { algo = "sha256,rsa2048"; }; };
	cmdline { signature-0 { algo = "sha256,rsa2048"; }; };
};
```

Note the separate `fdt-cmdline` image and the `Initrd`, neither of which the `AZ01` devices use. The firmware image
also carries two boot partitions, each holding a `kernel.fit`, and the DTS has a `bootcount@200` node, so this
platform looks like it does A/B updates.

`/boot` in the rootfs is empty on this device. On `AZ01` it still holds `boot.scr.uimg` alongside a loose `.dtb` for
every sibling device on that platform, which is where most of the DTS files in this repository came from.

> `binwalk` leaves `kernel.fit` as a 0-byte file when it extracts these boot partitions. You can pull the real one out
> of the `ext4` image without mounting it using
> `debugfs -R "dump /kernel.fit ./kernel.fit" <partition>.bin`.
> {style="note"}

## Additional References

* [QEngine](https://github.com/Peyton-C/QEngine) - boots this device's firmware under `QEMU`, and is furthest along on
  the System One of any target it supports. Its `docs/ENGINEOS.md` independently reaches the same conclusions as the
  sections above on `panthor`, Mesa and `Qt 6.7.2`, and adds detail this page doesn't cover: the `/data` subdirectories
  are encrypted per-directory with `ext4` `fscrypt` via `fscryptctl` at every `Engine` launch (not a whole-partition
  `LUKS` scheme), each partition is auto-formatted by an `az0x-*-mkfs` oneshot if it isn't already labeled, `/var/lib`
  is bind-mounted from `/data/system/var-lib`, and `Qt 6.7.2` has dropped the `vnc` platform plugin that the `AZ01`
  devices' `Qt 5.15.2` still ships. See [](Engine-OS.md) for a summary of the project.
