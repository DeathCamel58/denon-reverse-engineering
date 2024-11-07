# Hardware Notes

## Mali-T764

The
`RK3288` ([Rockchip](https://en.wikipedia.org/wiki/Rockchip_RK3288); [Wikipedia](https://en.wikipedia.org/wiki/Rockchip_RK3288))
includes the Mali-T764 GPU. Although the [Panfrost](https://docs.mesa3d.org/drivers/panfrost.html) driver should be able
to drive this GPU, the problem is that the default applications use Rockchips [
`libmali`](https://developer.arm.com/downloads/-/mali-drivers/user-space) userspace driver, which interacts with
the [Open Source Mali Midgard GPU Kernel Driver](https://developer.arm.com/downloads/-/mali-drivers/midgard-kernel).

To use the Mali Midgard GPU kernel driver, download the driver `tgz` from [
Arm's site](https://developer.arm.com/downloads/-/mali-drivers/midgard-kernel), extract
`driver/product/kernel/*` into your kernel sources, and apply this patch:

| Mali Midgard Version | Kernel Version | Patch                                                  |
|----------------------|----------------|--------------------------------------------------------|
| `r32p0`              | `6.11`         | <resource src="Mali_Midgard_r32p0_kernel_6.11.patch"/> |

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

## Displays

In the firmwares I've analyzed, the display panel (found in the device tree as `panel@0`) is either a [
`UMO-P076MD-T`](https://simplespec.microtipsusa.com/uploads/spec/datasheetFile/1906/MT_UMO-P076MD-T_V0_R2_20160526P.PDF)
or a `UMO-P080MD-T` (can't find the datasheet). Both of these panels are made
by [Microtips Technology](https://microtipsusa.com).

To get additional datasheets, emailing `mtusainfo@microtipsusa.com` is likely a good method. They seemed willing to
provide them when I was on the phone with them.

Mainline Linux **does not** have a driver for these specific panels, but some other Microtips Technology panels are
supported. Since the mainline supported ones are in the simple panel driver, we can likely build a data field and mad
`compatible` for these panels to get them working properly.

| Panel                 | Link                                                                                                          |
|-----------------------|---------------------------------------------------------------------------------------------------------------|
| `urt,umsh-8596md-t`   | [Mainline Source](https://elixir.bootlin.com/linux/v6.11.4/source/drivers/gpu/drm/panel/panel-simple.c#L4943) |
| `urt,umsh-8596md-1t`  | [Mainline Source](https://elixir.bootlin.com/linux/v6.11.4/source/drivers/gpu/drm/panel/panel-simple.c#L4946) |
| `urt,umsh-8596md-7t`  | [Mainline Source](https://elixir.bootlin.com/linux/v6.11.4/source/drivers/gpu/drm/panel/panel-simple.c#L4949) |
| `urt,umsh-8596md-11t` | [Mainline Source](https://elixir.bootlin.com/linux/v6.11.4/source/drivers/gpu/drm/panel/panel-simple.c#L4952) |
| `urt,umsh-8596md-19t` | [Mainline Source](https://elixir.bootlin.com/linux/v6.11.4/source/drivers/gpu/drm/panel/panel-simple.c#L4955) |
| `urt,umsh-8596md-20t` | [Mainline Source](https://elixir.bootlin.com/linux/v6.11.4/source/drivers/gpu/drm/panel/panel-simple.c#L4958) |

From what I've seen in the OEM firmware, the kernel build configs list configuration options that aren't in mainline.

* `CONFIG_DRM_PANEL_URT_UMO_9399MD_1T`
* `CONFIG_DRM_PANEL_URT_UMO_P076MD_T`
* `CONFIG_DRM_PANEL_URT_UMO_P080MD_T`
* `CONFIG_DRM_PANEL_URT_UMOH_9522MD_T`

They must have some proprietary driver for these panels that they are using to expose a framebuffer that interacts with
the panels.

> The OEM firmware uses the driver `panel-urt-umoh-9522md-t` to initialize the `UMO-P076MD-T`.
> {style="note"}

### Adding Kernel Driver

These displays can be supported in `panel-simple.c` by setting up panel description data in the DSI configuration.

Define the panel modes and description:

```C
static const struct drm_display_mode urt_umo_p076md_mode = {
  .clock = 73000, // 73000 in OEM
  .hdisplay = 800,
  .hsync_start = 800 + 77,
  .hsync_end = 800 + 77 + 24,
  .htotal = 800 + 77 + 24 + 24, // 925 in OEM
  .vdisplay = 1280, // 1280 in OEM
  .vsync_start = 1280 + 12,
  .vsync_end = 1280 + 12 + 4,
  .vtotal = 1280 + 12 + 4 + 18, // 1314 in OEM
};

static const struct panel_desc_dsi urt_umo_p076md = {
  .desc = {
    .modes = &urt_umo_p076md_mode,
    .num_modes = 1,
    .bpc = 8,
    .size = {
      .width = 96,
      .height = 152,
    },
    .connector_type = DRM_MODE_CONNECTOR_DSI,
  },
  .flags = MIPI_DSI_MODE_VIDEO,
  .format = MIPI_DSI_FMT_RGB888,
  .lanes = 4,
};
```

> I can verify that `urt_umo_p076md_mode` is correct, as `dmesg` reports the data in this struct during boot with
> `debug loglevel=7 drm.debug=0x1f`
>
> Our driver: `Probed mode: "800x1280": 60 73000 800 877 901 925 1280 1292 1296 1314 0x48 0x0`
>
> Their driver: `Modeline "800x1280": 60 73000 800 877 901 925 1280 1292 1296 1314 0x48 0x0`
> {style="note"}

Add a reference to the new definitions by adding the following to `dsi_of_match` in the same file:

```C
{
  .compatible = "urt,umo-p076md-t",
  .data = &urt_umo_p076md
}, {
  .compatible = "urt,umo-p080md-t",
  .data = &urt_umo_p076md
}, {
  .compatible = "urt,umoh-9522md-t",
  .data = &urt_umo_p076md
}
```

This is enough to get `/dev/fb0` to appear, but there are still issues (like no display). `cat /dev/urandom > /dev/fb0`
doesn't produce anything on the display (should display noise).

#### Boot Log {collapsible="true"}

Here's the relevant section of the boot log, which shows the panel booting up with our added panel support.

It looks like there's a
[VOP](https://github.com/torvalds/linux/blob/c2ee9f594da826bea183ed14f2cc029c719bf4da/Documentation/devicetree/bindings/display/rockchip/rockchip-vop.yaml)
issue in this.

```
dw-mipi-dsi-rockchip ff960000.mipi: Fixed dependency cycle(s) with /mipi@ff960000/panel@0
mipi-dsi ff960000.mipi.0: Fixed dependency cycle(s) with /mipi@ff960000
panel-simple-dsi ff960000.mipi.0: Looking up power-supply from device tree
rockchip-vop ff930000.vop: [drm:vop_bind] Warning: not support global_regdone_en
rockchip-vop ff930000.vop: [drm:vop_bind] Warning: not support gate
rockchip-vop ff930000.vop: [drm:vop_bind] Warning: not support gate
rockchip-vop ff930000.vop: [drm:vop_bind] Warning: not support channel
rockchip-vop ff930000.vop: [drm:vop_bind] Warning: not support channel
rockchip-drm display-subsystem: bound ff930000.vop (ops 0xc0954268)
rk_iommu ff940300.iommu: Page fault at 0x7f4bda00 of type read
rk_iommu ff940300.iommu: iova = 0x7f4bda00: dte_index: 0x1fd pte_index: 0xbd page_offset: 0xa00
rk_iommu ff940300.iommu: mmu_dte_addr: 0x014e0000 dte@0x014e07f4: 0x000000 valid: 0 pte@0x00000000: 0x000000 valid: 0 page@0x00000000 flags: 0x0
rockchip-vop ff940000.vop: [drm:vop_bind] Warning: not support global_regdone_en
rockchip-vop ff940000.vop: [drm:vop_bind] Warning: not support gate
rockchip-vop ff940000.vop: [drm:vop_bind] Warning: not support gate
rockchip-vop ff940000.vop: [drm:vop_bind] Warning: not support channel
rockchip-vop ff940000.vop: [drm:vop_bind] Warning: not support channel
rockchip-drm display-subsystem: bound ff940000.vop (ops 0xc0954268)
rockchip-drm display-subsystem: bound ff960000.mipi (ops 0xc0958c6c)
[drm:rockchip_drm_bind] IOMMU context initialized (aperture: 0x0-0xffffffff)
[drm:drm_minor_register] 
[drm:drm_minor_register] 
[drm:drm_minor_register] new minor registered 0
[drm:drm_minor_register] 
rockchip-drm display-subsystem: [drm:drm_sysfs_connector_add] [CONNECTOR:51:DSI-1] adding connector to sysfs
rockchip-drm display-subsystem: [drm:drm_sysfs_connector_hotplug_event] [CONNECTOR:51:DSI-1] generating connector hotplug event
[drm] Initialized rockchip 1.0.0 for display-subsystem on minor 0
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] 
[drm:drm_mode_object_get] OBJ ID: 51 (2)
rockchip-drm display-subsystem: [drm:drm_helper_probe_single_connector_modes] [CONNECTOR:51:DSI-1]
rockchip-drm display-subsystem: [drm:drm_helper_probe_single_connector_modes] [CONNECTOR:51:DSI-1] status updated from unknown to connected
rockchip-drm display-subsystem: [drm:drm_helper_probe_single_connector_modes] [CONNECTOR:51:DSI-1] probed modes:
rockchip-drm display-subsystem: [drm:drm_helper_probe_single_connector_modes] Probed mode: "800x1280": 60 73000 800 877 901 925 1280 1292 1296 1314 0x48 0x0
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] [CONNECTOR:51:DSI-1] enabled? yes
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] Not using firmware configuration
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] [CONNECTOR:51:DSI-1] looking for cmdline mode
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] [CONNECTOR:51:DSI-1] looking for preferred mode, tile 0
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] [CONNECTOR:51:DSI-1] Found mode 800x1280
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] picking CRTCs for 4096x4096 config
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] [CRTC:45:crtc-1] desired mode 800x1280 set (0,0)
[drm:drm_mode_object_get] OBJ ID: 51 (2)
[drm:drm_mode_object_put] OBJ ID: 51 (3)
rockchip-drm display-subsystem: [drm:__drm_fb_helper_initial_config_and_unlock] test CRTC 0 primary plane
rockchip-drm display-subsystem: [drm:drm_fbdev_dma_helper_fb_probe] surface width(800), height(3840) and bpp(32)
rockchip-drm display-subsystem: [drm:drm_mode_addfb2] [FB:53]
[drm:drm_mode_object_put] OBJ ID: 53 (2)
rk_iommu ff940300.iommu: Enable stall request timed out, status: 0x00004b
irq 47: nobody cared (try booting with the "irqpoll" option)
CPU: 0 UID: 0 PID: 50 Comm: kworker/0:6 Not tainted 6.11.0primego-dirty #51
Hardware name: Rockchip (Device Tree)
Workqueue: pm pm_runtime_work
Call trace: 
 unwind_backtrace from show_stack+0x10/0x14
 show_stack from dump_stack_lvl+0x50/0x64
 dump_stack_lvl from __report_bad_irq+0x28/0xc0
 __report_bad_irq from note_interrupt+0x1ec/0x294
 note_interrupt from handle_irq_event_percpu+0x30/0x38
 handle_irq_event_percpu from handle_irq_event+0x44/0x68
 handle_irq_event from handle_fasteoi_irq+0xbc/0x14c
 handle_fasteoi_irq from handle_irq_desc+0x1c/0x2c
 handle_irq_desc from gic_handle_irq+0x60/0x70
 gic_handle_irq from generic_handle_arch_irq+0x28/0x3c
 generic_handle_arch_irq from call_with_stack+0x18/0x20
 call_with_stack from __irq_svc+0x98/0xb0
Exception stack(0xf09c5dd0 to 0xf09c5e18)
5dc0:                                     c0ea75d8 00000001 c14da040 c06aaab8
5de0: c0eef9c8 c0eef9c0 0000024a 00000000 c0eef9c8 00000000 c0eb4180 c14d7880
5e00: 00000000 f09c5e20 c0180460 c06aaab8 60000013 ffffffff
 __irq_svc from arch_counter_read+0x0/0x10
 arch_counter_read from timekeeping_get_ns+0xc/0x20
 timekeeping_get_ns from ktime_get+0x50/0x7c
 ktime_get from rk_iommu_disable+0x84/0x144
 rk_iommu_disable from rk_iommu_suspend+0x1c/0x30
 rk_iommu_suspend from genpd_runtime_suspend+0xc4/0x260
 genpd_runtime_suspend from __rpm_callback+0x3c/0x110
 __rpm_callback from rpm_callback+0x54/0x58
 rpm_callback from rpm_suspend+0x234/0x3f0
 rpm_suspend from pm_runtime_work+0x74/0x8c
 pm_runtime_work from process_scheduled_works+0x180/0x254
 process_scheduled_works from worker_thread+0x16c/0x1f0
 worker_thread from kthread+0xd4/0xdc
 kthread from ret_from_fork+0x14/0x28
Exception stack(0xf09c5fb0 to 0xf09c5ff8)
5fa0:                                     00000000 00000000 00000000 00000000
5fc0: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
5fe0: 00000000 00000000 00000000 00000000 00000013 00000000
handlers:
[<(ptrval)>] rk_iommu_irq
[<(ptrval)>] vop_isr
Disabling IRQ #47
rockchip-drm display-subsystem: [drm:drm_sysfs_hotplug_event] generating hotplug event
rockchip-drm display-subsystem: [drm] fb0: rockchipdrmfb frame buffer device
rockchip-drm display-subsystem: [drm:drm_fb_helper_hotplug_event] 
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] 
[drm:drm_mode_object_get] OBJ ID: 51 (3)
rockchip-drm display-subsystem: [drm:drm_helper_probe_single_connector_modes] [CONNECTOR:51:DSI-1]
rockchip-drm display-subsystem: [drm:drm_helper_probe_single_connector_modes] [CONNECTOR:51:DSI-1] probed modes:
rockchip-drm display-subsystem: [drm:drm_helper_probe_single_connector_modes] Probed mode: "800x1280": 60 73000 800 877 901 925 1280 1292 1296 1314 0x48 0x0
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] [CONNECTOR:51:DSI-1] enabled? yes
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] Not using firmware configuration
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] [CONNECTOR:51:DSI-1] looking for cmdline mode
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] [CONNECTOR:51:DSI-1] looking for preferred mode, tile 0
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] [CONNECTOR:51:DSI-1] Found mode 800x1280
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] picking CRTCs for 800x3840 config
[drm:drm_mode_object_put] OBJ ID: 51 (3)
rockchip-drm display-subsystem: [drm:drm_client_modeset_probe] [CRTC:45:crtc-1] desired mode 800x1280 set (0,0)
[drm:drm_mode_object_get] OBJ ID: 51 (2)
[drm:drm_mode_object_put] OBJ ID: 51 (3)
rockchip-drm display-subsystem: [drm:drm_atomic_state_init] Allocated atomic state (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_plane_state] Added [PLANE:32:plane-0] (ptrval) state to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_plane_state] Added [PLANE:34:plane-1] (ptrval) state to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_set_fb_for_plane] Set [NOFB] for [PLANE:34:plane-1] state (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_plane_state] Added [PLANE:37:plane-2] (ptrval) state to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_set_fb_for_plane] Set [NOFB] for [PLANE:37:plane-2] state (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_plane_state] Added [PLANE:39:plane-3] (ptrval) state to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_set_fb_for_plane] Set [NOFB] for [PLANE:39:plane-3] state (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_plane_state] Added [PLANE:41:plane-4] (ptrval) state to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_plane_state] Added [PLANE:43:plane-5] (ptrval) state to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_set_fb_for_plane] Set [NOFB] for [PLANE:43:plane-5] state (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_plane_state] Added [PLANE:46:plane-6] (ptrval) state to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_set_fb_for_plane] Set [NOFB] for [PLANE:46:plane-6] state (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_plane_state] Added [PLANE:48:plane-7] (ptrval) state to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_set_fb_for_plane] Set [NOFB] for [PLANE:48:plane-7] state (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_crtc_state] Added [CRTC:36:crtc-0] (ptrval) state to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_set_mode_for_crtc] Set [NOMODE] for [CRTC:36:crtc-0] state (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_set_fb_for_plane] Set [NOFB] for [PLANE:32:plane-0] state (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_add_affected_connectors] Adding all current connectors for [CRTC:36:crtc-0] to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_crtc_state] Added [CRTC:45:crtc-1] (ptrval) state to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_set_mode_for_crtc] Set [MODE:800x1280] for [CRTC:45:crtc-1] state (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_set_crtc_for_plane] Link [PLANE:41:plane-4] state (ptrval) to [CRTC:45:crtc-1]
rockchip-drm display-subsystem: [drm:drm_atomic_set_fb_for_plane] Set [FB:53] for [PLANE:41:plane-4] state (ptrval)
[drm:drm_mode_object_get] OBJ ID: 53 (1)
rockchip-drm display-subsystem: [drm:drm_atomic_add_affected_connectors] Adding all current connectors for [CRTC:45:crtc-1] to (ptrval)
[drm:drm_mode_object_get] OBJ ID: 51 (2)
rockchip-drm display-subsystem: [drm:drm_atomic_get_connector_state] Added [CONNECTOR:51:DSI-1] (ptrval) state to (ptrval)
[drm:drm_mode_object_get] OBJ ID: 51 (3)
rockchip-drm display-subsystem: [drm:drm_atomic_set_crtc_for_connector] Link [CONNECTOR:51:DSI-1] state (ptrval) to [CRTC:45:crtc-1]
rockchip-drm display-subsystem: [drm:drm_atomic_check_only] checking (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_helper_check_modeset] [CRTC:45:crtc-1] mode changed
rockchip-drm display-subsystem: [drm:drm_atomic_helper_check_modeset] [CRTC:45:crtc-1] enable changed
rockchip-drm display-subsystem: [drm:drm_atomic_helper_check_modeset] [CRTC:45:crtc-1] active changed
rockchip-drm display-subsystem: [drm:drm_atomic_helper_check_modeset] Updating routing for [CONNECTOR:51:DSI-1]
rockchip-drm display-subsystem: [drm:drm_atomic_helper_check_modeset] [CONNECTOR:51:DSI-1] using [ENCODER:50:DSI-50] on [CRTC:45:crtc-1]
rockchip-drm display-subsystem: [drm:drm_atomic_helper_check_modeset] [CRTC:45:crtc-1] needs all connectors, enable: y, active: y
rockchip-drm display-subsystem: [drm:drm_atomic_add_affected_connectors] Adding all current connectors for [CRTC:45:crtc-1] to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_add_affected_planes] Adding all current planes for [CRTC:45:crtc-1] to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_add_encoder_bridges] Adding all bridges for [encoder:50:DSI-50] to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_private_obj_state] Added new private object (ptrval) state (ptrval) to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_get_private_obj_state] Added new private object (ptrval) state (ptrval) to (ptrval)
rockchip-drm display-subsystem: [drm:drm_atomic_normalize_zpos] [CRTC:45:crtc-1] calculating normalized zpos values
rockchip-drm display-subsystem: [drm:drm_atomic_normalize_zpos] [PLANE:41:plane-4] processing zpos value 0
rockchip-drm display-subsystem: [drm:drm_atomic_normalize_zpos] [PLANE:41:plane-4] normalized zpos value 0
rockchip-drm display-subsystem: [drm:drm_atomic_commit] committing (ptrval)
rockchip-drm display-subsystem: [drm:drm_calc_timestamping_constants] crtc 45: hwmode: htotal 925, vtotal 1314, vdisplay 1280
rockchip-drm display-subsystem: [drm:drm_calc_timestamping_constants] crtc 45: clock 73000 kHz framedur 16650000 linedur 12671
rockchip-drm display-subsystem: [drm:drm_atomic_helper_commit_modeset_disables] modeset on [ENCODER:50:DSI-50]
rockchip-drm display-subsystem: [drm:drm_atomic_helper_commit_modeset_enables] enabling [CRTC:45:crtc-1]
rk_iommu ff940300.iommu: Disable paging request timed out, status: 0x00004b
brd: module loaded
rk_iommu ff940300.iommu: Enable stall request timed out, status: 0x00004b
rockchip-vop ff940000.vop: [drm:vop_crtc_atomic_enable] *ERROR* failed to get pm runtime: -110
rockchip-vop ff940000.vop: [drm:drm_atomic_helper_commit_modeset_enables] *ERROR* Failed to enable vop (-110)
rockchip-drm display-subsystem: [drm:drm_atomic_helper_commit_modeset_enables] enabling [ENCODER:50:DSI-50]
loop: module loaded
dwc2 ff540000.usb: Looking up vusb_d-supply from device tree
dwc2 ff540000.usb: Looking up vusb_d-supply property in node /usb@ff540000 failed
dwc2 ff540000.usb: supply vusb_d not found, using dummy regulator
dwc2 ff540000.usb: Looking up vusb_a-supply from device tree
dwc2 ff540000.usb: Looking up vusb_a-supply property in node /usb@ff540000 failed
dwc2 ff540000.usb: supply vusb_a not found, using dummy regulator
dwc2 ff540000.usb: Looking up vbus-supply from device tree
dwc2 ff540000.usb: Looking up vbus-supply property in node /usb@ff540000 failed
------------[ cut here ]------------
WARNING: CPU: 0 PID: 49 at drivers/gpu/drm/rockchip/rockchip_drm_vop.c:943 drm_atomic_helper_commit_planes+0x1b0/0x274
Modules linked in:
CPU: 0 UID: 0 PID: 49 Comm: kworker/0:5 Not tainted 6.11.0primego-dirty #51
Hardware name: Rockchip (Device Tree)
Workqueue: events output_poll_execute
Call trace: 
 unwind_backtrace from show_stack+0x10/0x14
 show_stack from dump_stack_lvl+0x50/0x64
 dump_stack_lvl from __warn+0x74/0xbc
 __warn from warn_slowpath_fmt+0xac/0x104
 warn_slowpath_fmt from drm_atomic_helper_commit_planes+0x1b0/0x274
 drm_atomic_helper_commit_planes from drm_atomic_helper_commit_tail_rpm+0x34/0x60
 drm_atomic_helper_commit_tail_rpm from commit_tail+0x6c/0x138
 commit_tail from drm_atomic_helper_commit+0xf4/0x104
 drm_atomic_helper_commit from drm_atomic_commit+0xbc/0xf0
 drm_atomic_commit from drm_client_modeset_commit_atomic+0x178/0x200
 drm_client_modeset_commit_atomic from drm_client_modeset_commit_locked+0x3c/0x148
 drm_client_modeset_commit_locked from drm_client_modeset_commit+0x28/0x3c
 drm_client_modeset_commit from __drm_fb_helper_restore_fbdev_mode_unlocked+0x8c/0x94
 __drm_fb_helper_restore_fbdev_mode_unlocked from drm_fb_helper_set_par+0x2c/0x38
 drm_fb_helper_set_par from drm_fb_helper_hotplug_event+0xcc/0xe0
 drm_fb_helper_hotplug_event from drm_client_dev_hotplug+0xb4/0x108
 drm_client_dev_hotplug from output_poll_execute+0xc8/0x228
 output_poll_execute from process_scheduled_works+0x180/0x254
 process_scheduled_works from worker_thread+0x16c/0x1f0
 worker_thread from kthread+0xd4/0xdc
 kthread from ret_from_fork+0x14/0x28
Exception stack(0xf09c1fb0 to 0xf09c1ff8)
1fa0:                                     00000000 00000000 00000000 00000000
1fc0: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
1fe0: 00000000 00000000 00000000 00000000 00000013 00000000
---[ end trace 0000000000000000 ]---
------------[ cut here ]------------
WARNING: CPU: 0 PID: 49 at drivers/gpu/drm/rockchip/rockchip_drm_vop.c:1576 drm_atomic_helper_commit_planes+0x238/0x274
Modules linked in:
CPU: 0 UID: 0 PID: 49 Comm: kworker/0:5 Tainted: G        W          6.11.0primego-dirty #51
Tainted: [W]=WARN
Hardware name: Rockchip (Device Tree)
Workqueue: events output_poll_execute
Call trace: 
 unwind_backtrace from show_stack+0x10/0x14
 show_stack from dump_stack_lvl+0x50/0x64
 dump_stack_lvl from __warn+0x74/0xbc
 __warn from warn_slowpath_fmt+0xac/0x104
 warn_slowpath_fmt from drm_atomic_helper_commit_planes+0x238/0x274
 drm_atomic_helper_commit_planes from drm_atomic_helper_commit_tail_rpm+0x34/0x60
 drm_atomic_helper_commit_tail_rpm from commit_tail+0x6c/0x138
 commit_tail from drm_atomic_helper_commit+0xf4/0x104
 drm_atomic_helper_commit from drm_atomic_commit+0xbc/0xf0
 drm_atomic_commit from drm_client_modeset_commit_atomic+0x178/0x200
 drm_client_modeset_commit_atomic from drm_client_modeset_commit_locked+0x3c/0x148
 drm_client_modeset_commit_locked from drm_client_modeset_commit+0x28/0x3c
 drm_client_modeset_commit from __drm_fb_helper_restore_fbdev_mode_unlocked+0x8c/0x94
 __drm_fb_helper_restore_fbdev_mode_unlocked from drm_fb_helper_set_par+0x2c/0x38
 drm_fb_helper_set_par from drm_fb_helper_hotplug_event+0xcc/0xe0
 drm_fb_helper_hotplug_event from drm_client_dev_hotplug+0xb4/0x108
 drm_client_dev_hotplug from output_poll_execute+0xc8/0x228
 output_poll_execute from process_scheduled_works+0x180/0x254
 process_scheduled_works from worker_thread+0x16c/0x1f0
 worker_thread from kthread+0xd4/0xdc
 kthread from ret_from_fork+0x14/0x28
Exception stack(0xf09c1fb0 to 0xf09c1ff8)
1fa0:                                     00000000 00000000 00000000 00000000
1fc0: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
1fe0: 00000000 00000000 00000000 00000000 00000013 00000000
---[ end trace 0000000000000000 ]---
------------[ cut here ]------------
WARNING: CPU: 0 PID: 49 at drivers/gpu/drm/drm_atomic_helper.c:2515 drm_atomic_helper_commit_hw_done+0xb4/0xcc
Modules linked in:
CPU: 0 UID: 0 PID: 49 Comm: kworker/0:5 Tainted: G        W          6.11.0primego-dirty #51
Tainted: [W]=WARN
Hardware name: Rockchip (Device Tree)
Workqueue: events output_poll_execute
Call trace: 
 unwind_backtrace from show_stack+0x10/0x14
 show_stack from dump_stack_lvl+0x50/0x64
 dump_stack_lvl from __warn+0x74/0xbc
 __warn from warn_slowpath_fmt+0xac/0x104
 warn_slowpath_fmt from drm_atomic_helper_commit_hw_done+0xb4/0xcc
 drm_atomic_helper_commit_hw_done from drm_atomic_helper_commit_tail_rpm+0x44/0x60
 drm_atomic_helper_commit_tail_rpm from commit_tail+0x6c/0x138
 commit_tail from drm_atomic_helper_commit+0xf4/0x104
 drm_atomic_helper_commit from drm_atomic_commit+0xbc/0xf0
 drm_atomic_commit from drm_client_modeset_commit_atomic+0x178/0x200
 drm_client_modeset_commit_atomic from drm_client_modeset_commit_locked+0x3c/0x148
 drm_client_modeset_commit_locked from drm_client_modeset_commit+0x28/0x3c
 drm_client_modeset_commit from __drm_fb_helper_restore_fbdev_mode_unlocked+0x8c/0x94
 __drm_fb_helper_restore_fbdev_mode_unlocked from drm_fb_helper_set_par+0x2c/0x38
 drm_fb_helper_set_par from drm_fb_helper_hotplug_event+0xcc/0xe0
 drm_fb_helper_hotplug_event from drm_client_dev_hotplug+0xb4/0x108
 drm_client_dev_hotplug from output_poll_execute+0xc8/0x228
 output_poll_execute from process_scheduled_works+0x180/0x254
 process_scheduled_works from worker_thread+0x16c/0x1f0
 worker_thread from kthread+0xd4/0xdc
 kthread from ret_from_fork+0x14/0x28
Exception stack(0xf09c1fb0 to 0xf09c1ff8)
1fa0:                                     00000000 00000000 00000000 00000000
1fc0: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
1fe0: 00000000 00000000 00000000 00000000 00000013 00000000
---[ end trace 0000000000000000 ]---
rockchip-drm display-subsystem: [drm:drm_atomic_state_default_clear] Clearing atomic state (ptrval)
[drm:drm_mode_object_put] OBJ ID: 51 (4)
rockchip-drm display-subsystem: [drm:__drm_atomic_state_free] Freeing atomic state (ptrval)
rockchip-drm display-subsystem: [drm:drm_client_dev_hotplug] fbdev: ret=0
```
