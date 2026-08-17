# Numark

Numark's devices use the [](Engine-OS.md) operating system.

## Hardware IDs

| Device Name        | Model ID (Per DTS)          | Hardware ID       | DTS Files                                                                                                                                          |
|--------------------|-----------------------------|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Mixstream Pro      | `Numark MIXSTREAM PRO`      | `NH08`            | [](rk3288-az05-nh08-dts.md)                                                                                                                        |
| Mixstream Pro Go   | `Numark MIXSTREAM PRO GO`   | `NH10`            | [](rk3288-az05-nh10-dts.md)                                                                                                                        |
| Mixstream Pro Plus | `Numark MIXSTREAM PRO Plus` | `NH08S`           | [](rk3288-az05-nh08s-dts.md)                                                                                                                       |

> The DTS [](rk3288-az01b-dts.md) exists, but I'm not sure what device uses it, or what it's for.
> {style="note"}

## Unannounced Hardware IDs

| Hardware ID | Name(s) in firmware                                   |
|-------------|-------------------------------------------------------|
| `NH09`      | none — its control mapping is only called `MIXSTREAM` |

`NH09` sits between `NH08` and `NH10` in the same Numark ID space, and `Engine` has shipped a control mapping and MCU
firmware for it for years. No firmware has ever carried a product name for it, and no update image targets it, so which
Mixstream it is — or was going to be — is unresolved.
