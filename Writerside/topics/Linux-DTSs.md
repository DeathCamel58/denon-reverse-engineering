# DTS

Linux (the OS running on the device)
uses [device trees](https://www.kernel.org/doc/html/latest/devicetree/usage-model.html) to specify the hardware layout
of the device currently running.

The DTBs, which are DTSs that are pre-compiled can be found at `/boot`. These can be converted back into DTSs by running
`dtc -I dtb -O dts [file].dtb[o] -o [file].dts`.

## DTS files from firmwares

DTS's that I've found in the firmware will not be useful for end users, and probably will only be useful if you're
working on establishing a working [Buildroot](https://www.buildroot.org) environment,
or [porting (page 31)](https://bootlin.com/pub/conferences/2017/elce/schulz-how-to-support-new-board-u-boot-linux/schulz-how-to-support-new-board-u-boot-linux.pdf)
the linux kernel to your device.

The tables of which device uses which DTS can be found on the corresponding [](Brands.md) pages.

* [](Akai.md)
* [](Denon-DJ.md)
* [](HeadRush.md)
