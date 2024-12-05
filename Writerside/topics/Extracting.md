# Extracting

> I'm currently working on my own firmware modification utility that will allow easy plugin support for automated
> modifications across many of the firmware images.
>
> This extraction utility can be found
> on [my private GitLab instance](https://gitlab.randomcpu.com/engine-os/inmusic-firmware-modification-framework).
> {style="note"}

## Prerequisites

* [`mpcimg`](https://github.com/TheKikGen/MPC-LiveXplore/blob/master/imgmaker/mpcimg)
* [Binwalk](https://github.com/ReFirmLabs/binwalk)

## Extract

1. Run `mpcimg` with `mpcimg extract <firmware_file> <output_file>`
2. Run `binwalk` on the resulting `<output_file>.img` with `binwalk -e <output_file>.img`

Now the rootfs is in a folder for you to browse/modify.

## Signed Firmware Images

1. Make sure you have the latest version of binwalk (version `2.x` couldn't extract it, `3.1.1` could)
2. Extract the image with `binwalk -eM <image>`. The rootfs will be in a subfolder in `extractions` somewhere.

> I'm not currently sure how to extract just the rootfs image. Once I find out, I'll update here.
> {style="warning"}

## Additional References

* [`mpcimg`](https://github.com/TheKikGen/MPC-LiveXplore/blob/master/imgmaker/mpcimg) - Tool for extracting and
  repackaging MPC firmwares
