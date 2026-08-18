# MPC

MPC is inMusic's MPC operating system, with some additional custom software on top of a stock embedded Linux base.

Like [](Engine-OS.md), that base changed generation: MPC `2.x` was built with [](Buildroot.md), and MPC `3.x` is built
with Yocto/OpenEmbedded (`scarthgap`). See [](#base-distribution) for the evidence and the version boundary.

## Custom Applications

This is additional custom software that I've noticed in the firmware, along with documentation pages for them.

> My research is not focused on this, so documentation will likely be severely lacking here.
> {style="note"}

| Software name          | Software Path               | Description                                     | Page                        |
|------------------------|-----------------------------|-------------------------------------------------|-----------------------------|
| MPC                    | `/usr/bin/MPC`              | The main software that the user interacts with. | [](MPC-Application.md)      |
| [HARDWARE_ID] Test App | `/bin/[HARDWARE_ID]TestApp` | Debug software for testing switches, LEDs, etc. | [](HARDWARE-ID-Test-App.md) |

> [HARDWARE_ID] Test App does exist in these firmwares. However, I don't have a device to test this on.
> {style="note"}

## Hardware and product codes

`MPC` is one image for the whole family. Which unit it is comes from the device tree, not the
application — see [](MPC-Application.md#device-identity). The `3.9.1` image ships 14 `.dtb`s in
`/boot`, covering two board generations:

| Product code | Model                           | Board          | DTS                                                      |
|--------------|---------------------------------|----------------|----------------------------------------------------------|
| `ACV5`       | (generic `InMusic MPC ACV5`)    | `az01`         | [](rk3288-az01-mpc-acv5-dts.md)                          |
| `ACV5S`      | Akai Professional MPC X SE      | `az01`         | [](rk3288-az01-acv5s-dts.md)                             |
| `ACV8`       | (generic `InMusic MPC ACV8`)    | `az01`         | [](rk3288-az01-mpc-acv8-dts.md)                          |
| `ACVA`       | Akai Professional MPC One       | `az01`         | [](rk3288-az01-acva-dts.md)                              |
| `ACVA2`      | Akai Professional MPC One+      | `az05`         | [](rk3288-az05-acva2-dts.md)                             |
| `ACVB`       | Akai Professional MPC Live Mk 2 | `az01`         | [](rk3288-az01-acvb-dts.md)                              |
| `ACVM`       | Akai Professional MPC Key 61    | `az01`, `az05` | [](rk3288-az01-acvm-dts.md), [](rk3288-az05-acvm-dts.md) |
| `ACVR`       | Akai Professional MPC Key 37    | `az05`         | [](rk3288-az05-acvr-dts.md)                              |

All are Rockchip RK3288, the same SoC as the Denon DJ units. Each code also has a `-c` `.dtb`
variant, which is the `rockchip,rk3288-c` SoC revision rather than a separate product.

The table above is what **this firmware ships device trees for**. The application itself recognises a
larger set of 25 product codes, including several with no `.dtb` in any image examined — see
[](MPC-Application.md#recognised-product-codes).

## Update image format

The container format changed at the same boundary as the base distribution:

| MPC version | Container                | Magic        |
|-------------|--------------------------|--------------|
| `2.x`       | flattened device tree    | `0xd00dfeed` |
| `3.x`       | **`AZ01`**               | `AZ01`       |

Both are described in [](Signed-Firmware-Layout.md). Unlike the Denon `AZ01` images, an MPC `AZ01`
image holds a **single `rootfs` partition**: no `splash`, no `recoverysplash`, no `kernel`. The `3.9.1` image
(`MPC-3.9.1-Gen1-update.img`) declares eight device IDs in one container:

| HWID    | Device ID    | HWID    | Device ID    |
|---------|--------------|---------|--------------|
| `ACV5`  | `0x09e8403a` | `ACVA2` | `0x09e84047` |
| `ACV8`  | `0x09e8403b` | `ACVB`  | `0x09e8404b` |
| `ACVA`  | `0x09e84046` | `ACVM`  | `0x09e84052` |
| `ACVR`  | `0x09e84056` | `ACV5S` | `0x09e84057` |

Note the `0x09e8` vendor prefix (Akai) against Denon's `0x15e4` and Rane's `0x1cc5`.

## Base distribution {id="base-distribution"}

MPC `2.x` is Buildroot; MPC `3.x` is Yocto/OpenEmbedded, the same `az0x` base distribution the Denon
units moved to. Read from `/usr/lib/os-release` in each image:

| MPC version      | `ID`                             | `VERSION`                   |
|------------------|----------------------------------|-----------------------------|
| `2.2.3`          | *(no `os-release` in the image)* |                             |
| `2.10.1`         | `buildroot`                      | `2020.02.11-12-g430905f5bd` |
| `2.12.0`         | `buildroot`                      | `-g1da559a2`                |
| `2.15.1.1`       | `buildroot`                      | `-g1da559a2`                |
| `3.4.1.81`       | `az01`                           | `5.0.5 (scarthgap)`         |
| `3.5.0.54`       | `az01`                           | `5.0.7 (scarthgap)`         |
| `3.6.0`          | `az01`                           | `5.0.8 (scarthgap)`         |
| `3.7.0`, `3.8.0` | `az01`                           | `5.0.13 (scarthgap)`        |
| `3.9.1`          | `az01`                           | `5.0.17 (scarthgap)`        |

On `3.x` the full identity is:

```
ID=az01
NAME="az0x (inMusic Brands AZ0x base distribution)"
VERSION="5.0.17 (scarthgap)"
```

with `/etc/az01-release` carrying `BUILD_TAG=jenkins-ACVS-MPC3-SME0-MPC3-Yocto-563`. So current MPC
and current Engine OS share a Yocto base distribution and differ only in the application layer above
it.

> The exact switch-over release is **not pinned down**: I don't have a `3.0`–`3.3` image, so
> all that can be said is that the last `2.x` (`2.15.1.1`) is Buildroot and the earliest `3.x`
> I have (`3.4.0`) is already Yocto.
> {style="note"}

## Device information tooling

`/usr/bin/az0x-info` and `/usr/bin/az01-info` dump hardware identity as `key="value"` pairs over
`libaz0x-info.so`, covering serials, eMMC/SD identity, eFuse CPU ID, touch panel, wireless chip and
USB-C state. `/usr/bin/az0x-hwctl` is the matching control utility. See
[](MPC-Application.md#device-identity) for the key list.
