# DTS

Linux (the OS running on the device)
uses [device trees](https://www.kernel.org/doc/html/latest/devicetree/usage-model.html) to specify the hardware layout
of the device currently running.

The DTBs, which are DTSs that are pre-compiled can be found at `/boot`. These can be converted back into DTSs by running
`dtc -I dtb -O dts [file].dtb[o] -o [file].dts`.

## DTS files from firmwares

Here are the DTS's that I've found in the firmware. These will not be useful for end users, probably will only be
useful if you're working on establishing a working [Buildroot](https://www.buildroot.org) environment,
or [porting (page 31)](https://bootlin.com/pub/conferences/2017/elce/schulz-how-to-support-new-board-u-boot-linux/schulz-how-to-support-new-board-u-boot-linux.pdf)
the linux kernel to your device.

| Device                 | DTS Files                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Numark MIXSTREAM PRO   | [](rk3288-az05-nh08-dts.md)                                                                                                                        |
| Denon DJ SC6000M Prime | [](rk3288-az01-jp14-dts.md) <br /> [](rk3288-az01-jp14-c-dts.md)                                                                                   |
| Denon DJ SC6000 Prime  | [](rk3288-az01-jp13-dts.md) <br /> [](rk3288-az01-jp13-c-dts.md)                                                                                   |
| Denon DJ Prime GO      | [](rk3288-az01-jp11-dts.md) <br /> [](rk3288-az01-jp11-c-dts.md)                                                                                   |
| Denon DJ SC5000M Prime | [](rk3288-az01-jp08-dts.md) <br /> [](rk3288-az01-jp08-c-dts.md) <br /> [](rk3288-az01-jp08-revf-dts.md) <br /> [](rk3288-az01-jp08-c-revf-dts.md) |
| Denon DJ SC5000 Prime  | [](rk3288-az01-jp07-dts.md) <br /> [](rk3288-az01-jp07-c-dts.md)                                                                                   |
| Denon DJ Prime 2       | [](rk3288-az01-jc16-dts.md) <br /> [](rk3288-az01-jc16-c-dts.md)                                                                                   |
| Denon DJ Prime 4       | [](rk3288-az01-jc11-dts.md) <br /> [](rk3288-az01-jc11-c-dts.md)                                                                                   |

> [](rk3288-az01b-dts.md) exists, but I'm not sure what device uses it, or what it's for.
> {style="note"}

