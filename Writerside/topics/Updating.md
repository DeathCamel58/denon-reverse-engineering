# Updating

## PC Application

1. Download a updater application from [Engine DJ](https://enginedj.com/downloads)
2. Use [`7zip`](https://www.7-zip.org/) to extract the `exe`
3. Replace `update.img` in the extracted files with your firmware to flash
4. Run application while the device is in [Firmware Update Mode](#accessing-firmware-update-mode)

## With a USB

> Only tested on the Prime Go (likely the same for all Engine OS)
> {style="note"}

1. Boot the device
2. Hold `View` for 2 seconds
3. Tap `UTILITY`
4. Tap `UPDATE FIRMWARE`
5. Select the option you'd like the update with

## With SSH

1. Login with SSH
2. Run `reboot loader`

## With Fastboot {id="with-fastboot"}

> Confirmed on a **Prime Go** (`JP11`) reporting fastboot `0.4`. Other Engine OS devices run the same
> U-Boot family, but neither the unlock command nor the partition names have been checked on them.
> {style="note"}

Unlike every other method on this page, fastboot does **not** take an update image. The `.img` you
download is a container — see [](Unsigned-Firmware-Layout.md) and [](Signed-Firmware-Layout.md) — so
flashing it directly writes the container header onto the partition instead of its contents. The
payloads have to be unpacked first with [](Extracting.md).

For `JP11-4.3.4.img` (a `d00dfeed` FDT container) the three payloads and the partitions they declare
as targets are:

| Payload          | Target partition | Uncompressed size | Contents          |
|------------------|------------------|-------------------|-------------------|
| `rootfs`         | `rootfs`         | 500.0 MB          | ext2/ext4         |
| `splash`         | `splash`         | 4,096,000 B       | raw framebuffer   |
| `recoverysplash` | `recoverysplash` | 4,096,000 B       | raw framebuffer   |

> The `hash` node each payload carries is a SHA-1 over the **compressed** bytes, not the decompressed
> image. Verify before decompressing, or the comparison will fail for every partition.
> {style="note"}

### Entering fastboot

Run `fastboot 0` at the U-Boot console ([](UART.md)), or use the button combination under
[](#accessing-firmware-update-mode).

### What this bootloader implements

`getvar all` is not implemented, so variables have to be queried one at a time:

| Variable                                                             | Result                 |
|----------------------------------------------------------------------|------------------------|
| `version`, `version-bootloader`                                      | `0.4`                  |
| `serialno`                                                           | unit serial            |
| `max-download-size`                                                  | `0x70000000` (1.75 GB) |
| `partition-type:rootfs`, `partition-type:data`                       | `ext4`                 |
| `all`, `product`, `secure`, `unlocked`, `slot-count`, `current-slot` | not implemented        |
| `partition-size:` *(any partition)*                                  | not implemented        |

No `oem` introspection exists either — `oem help`, `oem partitions` and `oem device-info` all return
`unknown oem command`. The only OEM commands are the ones dumped from the bootloader in [](UART.md).

> `partition-type` answers only for filesystem partitions. `splash` and `recoverysplash` report
> `Variable not implemented` yet flash perfectly well, so **getvar is not a reliable test of what can
> be written**.
> {style="warning"}

### Unlocking writes

Out of the box the bootloader refuses every write:

```
$ fastboot flash rootfs rootfs.img
Sending 'rootfs' (512000 KB)    FAILED (remote: 'permission denied')
```

One of the OEM commands lifts that:

```
$ fastboot oem inmusic-unlock-magic-7de5fbc22b8c524e
OKAY [  0.000s]
Finished. Total time: 0.000s
```

It is a **global** gate rather than a per-partition one. The clearest evidence is that an unrelated
flashing command changes its failure mode:

| Command                         | Before unlock       | After unlock             |
|---------------------------------|---------------------|--------------------------|
| `flashing get_unlock_ability`   | `permission denied` | `missing partition name` |
| `flash rootfs`                  | `permission denied` | `OKAY`                   |

The second error means the command now gets *past* the permission check and fails only while parsing
its arguments. The unlock took effect immediately, needed no reboot, and did not wipe user data.

### Flashing

```bash
fastboot flash rootfs         rootfs.img
fastboot flash splash         splash.img
fastboot flash recoverysplash recoverysplash.img
```

Measured while writing `4.3.4` to a Prime Go:

| Partition        | Size     | Send    | Write   |
|------------------|----------|---------|---------|
| `rootfs`         | 500.0 MB | 41.7 s  | 19.4 s  |
| `splash`         | 4.0 MB   | 0.33 s  | 0.17 s  |
| `recoverysplash` | 4.0 MB   | 0.33 s  | 0.17 s  |

The host prints `Warning: skip copying <part> image avb footer (<part> partition size: 0, ...)` for
every partition. That is `fastboot` reacting to `partition-size` being unimplemented, not a real
zero-sized partition — it is safe to ignore.

`max-download-size` is 1.75 GB, comfortably above the 500 MB rootfs, so no sparse splitting is needed.

> This path skips the on-device updater entirely, so none of the container's hashes are checked and
> whatever bookkeeping the updater normally performs does not happen. The device only reports `OKAY`
> for the transfer; it does not verify what was written. Treat a successful flash as unconfirmed until
> the unit boots.
> {style="warning"}

> The remaining OEM commands in [](UART.md) are unlocked by the same magic, and four of them —
> `oem format`, `oem resetenv`, `oem update-bootloader` and `oem flash-bootloader` — are destructive.
> `JP11` update images ship **no bootloader payload at all** (see [](Firmware.md)), so there is
> nothing vendor-supplied to feed the two bootloader commands; firing them with the wrong input is the
> most plausible way to brick a unit here.
> {style="warning"}

### Recovery

Because `JP11` images contain no bootloader, this procedure cannot damage the boot chain — the worst
realistic outcome is a rootfs that will not boot, which is recoverable through the normal update mode
above, or through RK3288 maskrom mode with
[`rkdeveloptool`](https://github.com/rockchip-linux/rkdeveloptool). Note `rkdeveloptool ld` will not
see a device that is sitting in fastboot; it has to be in maskrom/loader mode.

## Accessing firmware update mode

### Denon Prime Go

1. Power device off
2. Hold `< Load` and `Load >`, then press power button. Continue holding until firmware upload image appears.
3. Use the desktop updater to flash the device.

### Denon Prime 4

1. Power device off
2. On the right deck hold `Shift` and `Eject`, then press power button. Continue holding until firmware upload image
   appears.

### SC5000

1. Power device off
2. On the right deck hold `Source`, `Eject`, and `Layer`, then press power button. Continue holding until firmware
   upload image appears.

### SC5000M

1. Power device off
2. On the right deck hold `Source`, `Eject`, and `Layer`, then press power button. Continue holding until firmware
   upload image appears.

### SC6000

1. Power device off
2. On the right deck hold `Source`, `Eject`, and `Layer`, then press power button. Continue holding until firmware
   upload image appears.

### SC6000M

1. Power device off
2. On the right deck hold `Source`, `Eject`, and `Layer`, then press power button. Continue holding until firmware
   upload image appears.

### MCX8000

1. Power device off
2. Place a USB drive that contains the latest firmware update package into USB 1.
3. Power on the MCX8000.
4. The left deck Cue button will flash. Press the Cue button.
5. The left deck Sync button will flash. This indicates the unit is updating.
6. When the left deck Play button is lit solid green the update is complete.
7. Turn the MCX8000 off, count to 4, then turn it back on again.

### Other Devices

> I'd love some help with additional data here! If you have a device that I don't have a firmware update mode data for,
> please file a PR with your device's firmware update mode
> instructions [here](https://github.com/DeathCamel58/denon-reverse-engineering/issues) if you know it!
> {style="note"}

## Additional References

* [Unbricking inMusic Products](https://github.com/RedHate/Unbricking-inMusic-Products) - Useful information to unbrick
  devices using fastboot. May want to do some more research to find the other unit's button combinations.
* Additional fastboot button combinations can be found on
  the [Akai MPC Forums](https://www.mpc-forums.com/viewtopic.php?t=213466)
