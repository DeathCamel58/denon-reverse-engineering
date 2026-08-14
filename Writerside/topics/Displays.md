# Displays

In the firmwares I've analyzed, the display panel (found in the device tree as `panel@0`) is a [
`UMO-P076MD-T`](https://simplespec.microtipsusa.com/uploads/spec/datasheetFile/1906/MT_UMO-P076MD-T_V0_R2_20160526P.PDF),
a `UMO-P080MD-T` (can't find the datasheet), a `UMOH-9522MD-T` (used on the `RMZ2`), or a `wj40atyaqmnn0` (can't find any
data about this one, only used on the `hg04`). The UMO/UMOH panels are made by
[Microtips Technology](https://microtipsusa.com).

All of the panels used across Engine OS devices are **800×1280**, mounted rotated — see [](#resolutions).

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

## Resolutions

**Every Engine OS device with a main touchscreen uses an 800×1280 panel.** The panels are natively *portrait*, and the
device tree rotates them — on all but one device by 90° or 270°, which is what produces the 1280×800 landscape screen the
user sees. The 7-inch and 10.1-inch panels have the **same pixel count**; they differ in physical size and in vertical
sync timing, not in resolution.

| Hardware ID | Device               | Panel               | Native    | Rotation | As presented        |
|-------------|----------------------|---------------------|-----------|----------|---------------------|
| `JC11`      | Prime 4              | `urt,umo-p080md-t`  | 800×1280  | 90°      | 1280×800            |
| `JC11S`     | Prime 4 Plus         | `urt,umo-p080md-t`  | 800×1280  | 90°      | 1280×800            |
| `JC16`      | Prime 2              | `urt,umo-p076md-t`  | 800×1280  | 90°      | 1280×800            |
| `JP07`      | SC5000 Prime         | `urt,umo-p076md-t`  | 800×1280  | 270°     | 1280×800            |
| `JP08`      | SC5000M Prime        | `urt,umo-p076md-t`  | 800×1280  | 270°     | 1280×800            |
| `JP11`      | Prime GO             | `urt,umo-p076md-t`  | 800×1280  | 270°     | 1280×800            |
| `JP11S`     | Prime GO+            | `urt,umo-p076md-t`  | 800×1280  | 270°     | 1280×800            |
| `JP13`      | SC6000 Prime         | `urt,umo-p080md-t`  | 800×1280  | 270°     | 1280×800            |
| `JP14`      | SC6000M Prime        | `urt,umo-p080md-t`  | 800×1280  | 270°     | 1280×800            |
| `JP20`      | SC Live 2            | `urt,umo-p076md-t`  | 800×1280  | 270°     | 1280×800            |
| `JP21`      | SC Live 4            | `urt,umo-p076md-t`  | 800×1280  | 270°     | 1280×800            |
| `NH08`      | Mixstream Pro        | `urt,umo-p076md-t`  | 800×1280  | 90°      | 1280×800            |
| `NH08S`     | Mixstream Pro+       | `urt,umo-p076md-t`  | 800×1280  | 90°      | 1280×800            |
| `NH10`      | Mixstream Pro Go     | `urt,umo-p076md-t`  | 800×1280  | 90°      | 1280×800            |
| `RMZ2`      | Rane System One      | `urt,umoh-9522md-t` | 800×1280  | **180°** | **800×1280**        |
| `JC20`      | LC6000 Prime         | *none*              | —         | —        | wheel display only  |

Rotation is the `rotation` property on the `panel@0` node, mirrored by `inmusic,panel-rotation` on the root node —
`0x5a` = 90°, `0xb4` = 180°, `0x10e` = 270°.

> `RMZ2` is the only device whose rotation is 180°, which leaves the panel in its native portrait orientation rather
> than turning it landscape. This is what the device tree says and Engine reads that same property (see
> [](#screenconfiguration)), but it is worth confirming against hardware.
> {style="warning"}

### Panel timings

Read out of the `drm_display_mode` structs compiled into the kernel (see [](#finding-the-resolution-in-firmware)):

| Panel               | Resolution | Clock  | Horizontal        | Vertical                |
|---------------------|------------|--------|-------------------|-------------------------|
| `urt,umo-p076md-t`  | 800×1280   | 73000  | 800 877 901 925   | 1280 1292 1296 1314     |
| `urt,umo-p080md-t`  | 800×1280   | 73000  | 800 877 901 925   | 1280 **1298 1302** 1314 |
| `urt,umoh-9522md-t` | 800×1280   | 73000  | 800 877 901 925   | 1280 1292 1296 1314     |
| `urt,umo-9399md-1t` | 800×1280   | 74250  | 800 876 894 970   | 1280 1284 1286 1290     |

> `UMO-P080MD-T` is **not** identical to `UMO-P076MD-T`, as the driver-porting section below assumes. Same resolution and
> pixel clock, but its vertical sync pulse sits 6 lines later (`vsync_start`/`vsync_end` of 1298/1302 against
> 1292/1296). The two are interchangeable for getting a picture, but not timing-exact.
> {style="note"}

The `panel-urt-umoh-9522md-t` driver's `of_match` table also accepts several equivalent parts, all with the same
800×1280 mode: `wayton,mfc070ac-ihd3`, `wayton,mfc101gc-ihd3-1`, `goldenvision,gvh070hd98h-c-a` and
`goldenvision,gvh101hd161h-c-a`. The `070`/`101` in those part numbers is the diagonal in tenths of an inch, which is
direct confirmation that a 7.0-inch and a 10.1-inch panel share one mode.

### Centre wheel displays

Devices with a platter display drive it as a separate surface, not as a DRM panel — it hangs off the control-surface MCU
and Engine renders to it through a `Painter` (see [](Controller-Assignments.md)):

```qml
Painter {
    objectName: "Album Artwork Layer " + (index+1)
    height: 198
    width: 198
    format: Painter.AZ01_CENTER_WHEEL_DISPLAY
}
```

**198×198**, one surface per deck, and identical on every device that has one: `JC11`, `JC11S`, `JC16`, `JC20`, `JP07`
and `JP13`. `AZ01_CENTER_WHEEL_DISPLAY` is the only wheel-display format Engine defines.

> `JP08` and `JP14` — the motorised SC5000M and SC6000M — have **no** `*_Display_Assignments.qml` and no `Display`
> entry in `KnownDevices.vfsb`, unlike their non-motorised siblings. That is unlikely to mean they have no platter
> display; more probably it is addressed through the Controller MCU instead of as its own MIDI device. Unconfirmed.
> {style="warning"}

### The test app's own list

`/usr/bin/test-app-launcher` takes a `--display-type` argument, and its help text enumerates every display type
inMusic's production test app knows about:

```
display type: one of
  '4in800x480',   '7in1280x800',  '10in1280x800',
  '1.6in160x128', '7in800x1280',  '10in800x1280'
```

This is a useful independent confirmation of the 7-inch and 10-inch entries, listed in both orientations. Two entries do
not correspond to any panel found in any Engine device tree: `4in800x480`, and `1.6in160x128` — note that the latter is
*not* the 198×198 wheel surface, so either it belongs to a product outside this set or the wheel LCD is 160×128 with
Engine rendering at 198×198 and scaling. Unresolved either way.

## Finding the resolution in firmware {id="finding-the-resolution-in-firmware"}

Three independent methods, in increasing order of effort. They agree with each other.

### 1. The splash partition is a raw framebuffer

`splash` and `updatesplash` are stored **uncompressed** — `comp=none` in an `AZ01` container, flags bit 0 clear in an
`AZ0x` one (see [](Signed-Firmware-Layout.md)). They are 32-bit RGBA at the panel's native resolution, so:

```
resolution = partition size / 4 bytes per pixel
```

Every Denon and Numark Engine image has a splash of exactly **4,096,000 bytes** → 1,024,000 pixels → 800×1280.

That leaves the width ambiguous with 1280×800. Two ways to settle it:

* Compare the mean absolute difference between adjacent rows for each candidate stride. The true width wins by a wide
  margin, because real images are vertically coherent — on a `JC11S` splash, stride 800 scores 358 against 17,928 for
  stride 1280.
* Just render it. The image comes out correctly at 800×1280 with its artwork **pre-rotated**, i.e. the logo and text
  read sideways, which is exactly what a portrait framebuffer feeding a rotated panel looks like.

HeadRush images store the splash xz-compressed, so decompress first — they also come to 4,096,000 bytes.

### 2. The kernel's mode tables

Definitive, and the only way to get timings. The panel drivers are **built in** (they appear in `modules.builtin`, not
as `.ko` files), so the mode structs live in the kernel image:

1. Get the kernel. On `rk3288` boards it is a zImage — find the `1f 8b` gzip magic inside it and inflate. On `rk3588`
   the arm64 `Image` is already uncompressed.
2. Scan for `struct drm_display_mode`. On 32-bit ARM the layout is `int clock` then ten `u16`s
   (`hdisplay, hsync_start, hsync_end, htotal, hskew, vdisplay, vsync_start, vsync_end, vtotal, vscan`) then `u32 flags`.
   The monotonic relationships — `hdisplay < hsync_start < hsync_end < htotal`, same for vertical, `hskew`/`vscan` zero,
   and a refresh rate between 20 and 145 Hz — make false positives rare.
3. Attribute each mode to a panel through the `of_device_id` table rather than by guessing from nearby strings.
   `struct of_device_id` is `char name[32]; char type[32]; char compatible[128]; const void *data;`, so a compatible
   string sitting 64 bytes into a struct whose first 64 bytes are zero is an `of_match` entry, and its `data` pointer
   (at +192) leads to the driver's descriptor, whose first pointer field is `.modes`.

Watch out for two traps. A driver with its own dedicated `.c` file may keep its mode as a static struct that no
`of_device_id` points at — `panel-urt-umo-p080md-t` is like this, and its `of_data` is a **DSI init command sequence**
(recognisable as register/value byte pairs `b0 00, b1 51, b2 4b …`), not a mode. And a panel bound to `panel-edp` has a
NULL `data` pointer on purpose, because its mode comes from **EDID at runtime** and is therefore not in the firmware at
all.

### 3. The device tree

`panel@0`'s `compatible` names the panel, and its `rotation` property (mirrored as `inmusic,panel-rotation` on the root
node) gives the orientation. This is the only source for rotation, and rotation is what converts the native 800×1280
into the presented 1280×800.

### Engine's screen configuration {id="screenconfiguration"}

`/usr/Engine/ScreenConfiguration/<HWID>/ScreenConfiguration.json`, falling back to `Default/`, tells you how many
outputs Engine drives, which is primary, and where it reads rotation from — but not the resolution:

```json
{
	"outputs": [
		{
			"name": "MIPI",
			"rotation" : "/sys/firmware/devicetree/base/mipi@ff960000/panel@0/rotation",
			"primary" : true,
			"virtualIndex": 0
		}
	]
}
```

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

`UMO-P080MD-T` needs its own mode — same resolution and clock, but the vertical sync pulse is 6 lines later. Values taken
from the mode struct in the OEM kernel:

```C
static const struct drm_display_mode urt_umo_p080md_mode = {
  .clock = 73000,
  .hdisplay = 800,
  .hsync_start = 800 + 77,
  .hsync_end = 800 + 77 + 24,
  .htotal = 800 + 77 + 24 + 24,   // 925 in OEM
  .vdisplay = 1280,
  .vsync_start = 1280 + 18,       // 1298 in OEM, vs 1292 on the P076
  .vsync_end = 1280 + 18 + 4,     // 1302 in OEM, vs 1296 on the P076
  .vtotal = 1280 + 18 + 4 + 12,   // 1314 in OEM
};
```

Add references to the new definitions by adding the following to `dsi_of_match` in the same file:

```C
{
  .compatible = "urt,umo-p076md-t",
  .data = &urt_umo_p076md
}, {
  .compatible = "urt,umo-p080md-t",
  .data = &urt_umo_p080md
}, {
  .compatible = "urt,umoh-9522md-t",
  .data = &urt_umo_p076md
}
```

> `urt,umoh-9522md-t` genuinely does share the `P076` mode — that one is not an approximation.
> {style="note"}

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
