# Software

Different inMusic devices run different OS's. This is due to the devices being for a specific use case

| Brand    | OS Name                | OS Page          |
|----------|------------------------|------------------|
| Akai     | :grey_question: MPC    | [](MPC.md)       |
| Denon    | Engine OS              | [](Engine-OS.md) |
| HeadRush | :grey_question: Evil   | [](Evil.md)      |
| HeadRush | :grey_question: Looper | [](Looper.md)    |

> HeadRush seems to have two different builds of the OS.
> 
> `Evil` is my name for the OS on the Gigboard, Pedalboard, and MX5. This is because the main application seems to be `Evil`
> 
> `Looper` is my name for the OS on the Looperboard. This is because the main application seems to be `Looper`
> 
> {style="note"}

## Hardware ID

The hardware ID can be found by running `cat /sys/firmware/devicetree/base/inmusic,product-code` on the target device.

Hardware ID tables can be found on the corresponding [](Brands.md) pages.

* [](Akai.md)
* [](Denon-DJ.md)
* [](HeadRush.md)
