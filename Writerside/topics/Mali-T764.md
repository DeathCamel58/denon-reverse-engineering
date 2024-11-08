# Mali-T764

The
`RK3288` ([Rockchip](https://en.wikipedia.org/wiki/Rockchip_RK3288); [Wikipedia](https://en.wikipedia.org/wiki/Rockchip_RK3288))
includes the Mali-T764 GPU. Although the [Panfrost](https://docs.mesa3d.org/drivers/panfrost.html) driver should be able
to drive this GPU, the problem is that the default applications use Rockchips [
`libmali`](https://developer.arm.com/downloads/-/mali-drivers/user-space) userspace driver, which interacts with
the [Open Source Mali Midgard GPU Kernel Driver](https://developer.arm.com/downloads/-/mali-drivers/midgard-kernel).

To use the Mali Midgard GPU kernel driver, download the driver `tgz` from [
Arm's site](https://developer.arm.com/downloads/-/mali-drivers/midgard-kernel), extract
`driver/product/kernel/*` into your kernel sources, and apply this patch:

| Mali Midgard Version | Kernel Version | Patch                                                      |
|----------------------|----------------|------------------------------------------------------------|
| `r32p0`              | `6.11`         | <resource src="Mali_Midgard_r32p0_kernel_6.11.patch"/>     |
| `r32p0`              | `6.12-rc6`     | <resource src="Mali_Midgard_r32p0_kernel_6.12-rc6.patch"/> |

> [Paranoid Android](https://amatriz.net/tags/mali/) seems to sporadically update patches for the Mali Midgard drivers
> as well.
> {style="note"}

Then, you can enable the driver using these `.config` variables:

```Ini
CONFIG_MALI_MIDGARD=y
CONFIG_MALI_GATOR_SUPPORT=y
CONFIG_MALI_MIDGARD_ENABLE_TRACE=y
CONFIG_MALI_DEVFREQ=y
CONFIG_MALI_DMA_FENCE=y
CONFIG_MALI_PLATFORM_NAME="devicetree"
CONFIG_MALI_EXPERT=y
CONFIG_MALI_CORESTACK=y
CONFIG_MALI_SYSTEM_TRACE=y
CONFIG_MALI_2MB_ALLOC=y
CONFIG_MALI_PWRSOFT_765=y
CONFIG_MALI_JOB_DUMP=y
CONFIG_MALI_PRFCNT_SET_SECONDARY=y
```

When you compile the kernel, the Mali Midgard Driver will now be compiled into the kernel (not as a module).