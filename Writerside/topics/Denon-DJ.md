# Denon DJ

Denon DJ's devices use the [](Engine-OS.md) operating system.

## Hardware IDs

| Device Name        | Model ID (Per DTS)          | Hardware ID       | DTS Files                                                                                                                                          |
|--------------------|-----------------------------|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Prime 4            | `Denon DJ Prime 4`          | `JC11`            | [](rk3288-az01-jc11-dts.md) <br /> [](rk3288-az01-jc11-c-dts.md)                                                                                   |
| Prime 4 Plus       | `Denon DJ PRIME 4 Plus`     | `JC11S`           | [](rk3288-az01-jc11s-dts.md)                                                                                                                       |
| Prime 2            | `Denon DJ Prime 2`          | `JC16`            | [](rk3288-az01-jc16-dts.md) <br /> [](rk3288-az01-jc16-c-dts.md)                                                                                   |
| SC5000 Prime       | `Denon DJ SC5000 Prime`     | `JP07`            | [](rk3288-az01-jp07-dts.md) <br /> [](rk3288-az01-jp07-c-dts.md)                                                                                   |
| SC5000M Prime      | `Denon DJ SC5000M Prime`    | `JP08`            | [](rk3288-az01-jp08-dts.md) <br /> [](rk3288-az01-jp08-c-dts.md) <br /> [](rk3288-az01-jp08-revf-dts.md) <br /> [](rk3288-az01-jp08-c-revf-dts.md) |
| Prime GO           | `Denon DJ Prime GO`         | [`JP11`](JP11.md) | [](rk3288-az01-jp11-dts.md) <br /> [](rk3288-az01-jp11-c-dts.md)                                                                                   |
| Prime GO Plus      | `Denon DJ Prime GO+`        | `JP11S`           | [](rk3288-az05-jp11s-dts.md)                                                                                                                       |
| SC6000 Prime       | `Denon DJ SC6000 Prime`     | `JP13`            | [](rk3288-az01-jp13-dts.md) <br /> [](rk3288-az01-jp13-c-dts.md)                                                                                   |
| SC6000M Prime      | `Denon DJ SC6000M Prime`    | `JP14`            | [](rk3288-az01-jp14-dts.md) <br /> [](rk3288-az01-jp14-c-dts.md)                                                                                   |
| SC Live 2          | `DENON DJ SCX-2`            | `JP20`            | [](rk3288-az05-jp20-dts.md)                                                                                                                        |
| SC Live 4          | `DENON DJ SCX-4`            | `JP21`            | [](rk3288-az05-jp21-dts.md)                                                                                                                        |
| LC6000 Prime       | —                           | `JC20`            | —                                                                                                                                                  |

> The DTS [](rk3288-az01b-dts.md) exists, but I'm not sure what device uses it, or what it's for.
> {style="note"}

## Unannounced Hardware IDs

These hardware IDs appear in shipping Engine OS firmware, but the products have not been announced or released. Only the
ID and the name (or names) the firmware uses for them are listed here — anything further is deliberately withheld.

| Hardware ID | Name(s) in firmware                                  | DTS Files                                                                        |
|-------------|------------------------------------------------------|----------------------------------------------------------------------------------|
| `JP12`      | `SC4000 PRIME` — cancelled, no hardware ever shipped | —                                                                                |
| `JP13X`     | `SC7000 PRIME`, and an `SC6500 PRIME` alongside it   | [](rk3588-az04-jp13x-dts.md)                                                     |
| `JP21X`     | `QUANTUM`, earlier `SC LIVE MAX`                     | [](rk3288-az05-jp21x-dts.md)                                                     |
| `JP22`      | `PRIME COMMAND`, earlier `PRIME ULTRA`               | [](rk3588-az04-jp22-dts.md)                                                      |
| `JP23`      | `PRIME AIR`                                          | [](rk3288-az05-jp23-dts.md)                                                      |
| `JP24`      | `PRIME 4 G2`, earlier `PRIME 4 MKII`                 | [](rk3588-az04-jp24-dts.md) <br /> [](rk3588s-az04b-jp24-dts.md)                 |

Several of these carry two names because the product was renamed during development, and the firmware was not updated
everywhere at once.

`JP12` has no device tree — it was cancelled before one ever shipped in a firmware image, and the only thing left of it
is its control mapping. `JP24` has two, for two different board revisions.

> `JS03` is a seventh code in the same space. It has been present in every Engine build for years, but no firmware has
> ever carried a name for it, so there is nothing to list beyond the ID itself.
> {style="note"}
