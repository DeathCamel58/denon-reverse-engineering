# Extracting

> I'm currently working on my own firmware modification utility that will allow easy plugin support for automated
> modifications across many of the firmware images
> {style="note"}

## Prerequisites

* [`mpcimg`](https://github.com/TheKikGen/MPC-LiveXplore/blob/master/imgmaker/mpcimg)
* [Binwalk](https://github.com/ReFirmLabs/binwalk)

## Extract

1. Run `mpcimg` with `mpcimg extract <firmware_file> <output_file>`
2. Run `binwalk` on the resulting `<output_file>.img` with `binwalk -e <output_file>.img`

Now the rootfs is in a folder for you to browse/modify.

## Additional References

* [`mpcimg`](https://github.com/TheKikGen/MPC-LiveXplore/blob/master/imgmaker/mpcimg) - Tool for extracting and
  repackaging MPC firmwares
