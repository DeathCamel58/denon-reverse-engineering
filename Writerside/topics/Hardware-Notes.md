# Hardware Notes

## Displays

In the firmwares I've analyzed, the display panel (found in the device tree as `panel@0`) is either a [
`UMO-P076MD-T`](https://simplespec.microtipsusa.com/uploads/spec/datasheetFile/1906/MT_UMO-P076MD-T_V0_R2_20160526P.PDF)
or a `UMO-P080MD-T`. Both of these panels are made by [Microtips Technology](https://microtipsusa.com).

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
