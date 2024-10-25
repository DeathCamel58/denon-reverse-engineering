# Recompressing

> I'm currently working on my own firmware modification utility that will allow easy plugin support for automated
> modifications across many of the firmware images
> {style="note"}

## Prerequisites

* [`mpcimg`](https://github.com/TheKikGen/MPC-LiveXplore/blob/master/imgmaker/mpcimg)

## Compress

1. Run `mpcimg` on your `rootfs` folder with `mpcimg make-mpc <rootfs_folder> <output_img_file> [version_string]`

This will yield a `<output_img_file>.img` that you can flash to the device.

## Additional References

* [`mpcimg`](https://github.com/TheKikGen/MPC-LiveXplore/blob/master/imgmaker/mpcimg) - Tool for extracting and
  repackaging MPC firmwares
