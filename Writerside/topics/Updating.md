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
