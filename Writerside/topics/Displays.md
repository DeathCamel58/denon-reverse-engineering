# Displays

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

## Adding Kernel Driver

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

I can verify that `urt_umo_p076md_mode` is correct, as `dmesg` reports the data in this struct during boot with
`debug loglevel=7 drm.debug=0x1f`

<compare first-title="Our Driver" second-title="Their Driver">
    <code-block>
        Probed mode: "800x1280": 60 73000 800 877 901 925 1280 1292 1296 1314 0x48 0x0
    </code-block>
    <code-block>
        Modeline "800x1280": 60 73000 800 877 901 925 1280 1292 1296 1314 0x48 0x0
    </code-block>
</compare>

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

## Boot Log {collapsible="true"}

Here's the relevant section of the boot log, which shows the panel booting up with our added panel support.

It looks like there's a
[VOP](https://github.com/torvalds/linux/blob/c2ee9f594da826bea183ed14f2cc029c719bf4da/Documentation/devicetree/bindings/display/rockchip/rockchip-vop.yaml)
issue in this.

```
```
{ src="hardware-notes/displays-dmesg-log" }
