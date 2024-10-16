# Buildroot

> Buildroot is a tool that simplifies and automates the process of building a complete Linux system for an embedded
> system, using cross-compilation.

## Denon Prime Go

| Name | Details                                                          |
|------|------------------------------------------------------------------|
| CPU  | [RK3288](https://opensource.rock-chips.com/wiki_RK3288)          |
| DTS  | [](rk3288-az01-jp11-dts.md) <br /> [](rk3288-az01-jp11-c-dts.md) |

### Buildroot Basic Config {collapsible="true"}

This bare-minimum `defconfig` for buildroot will generate working applications for the Prime Go. This **will not**
generate a working boot image or firmware update.

```
BR2_arm=y
BR2_cortex_a17=y
BR2_ARM_FPU_NEON_VFPV4=y
BR2_TOOLCHAIN_BUILDROOT_CXX=y
BR2_ROOTFS_POST_BUILD_SCRIPT="board/asus/tinker/post-build.sh"
BR2_ROOTFS_POST_IMAGE_SCRIPT="support/scripts/genimage.sh"
BR2_ROOTFS_POST_SCRIPT_ARGS="-c board/asus/tinker/genimage.cfg"
BR2_TARGET_ROOTFS_EXT2=y
BR2_TARGET_ROOTFS_EXT2_4=y
BR2_TARGET_ROOTFS_EXT2_SIZE="512M"
BR2_PACKAGE_HOST_DOSFSTOOLS=y
BR2_PACKAGE_HOST_DTC=y
BR2_PACKAGE_HOST_GENIMAGE=y
BR2_PACKAGE_HOST_KMOD=y
BR2_PACKAGE_HOST_MTOOLS=y
BR2_PACKAGE_HOST_UBOOT_TOOLS=y
```
