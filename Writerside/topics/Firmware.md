# Firmware

The firmware for most of the inMusic devices is packaged the same, so we're able
to [extract](Extracting.md), [recompress](Recompressing.md), and [update](Updating.md) most device firmwares.

> Many of the devices can be extracted with
> the [mpcimg](https://github.com/TheKikGen/MPC-LiveXplore/tree/master/imgmaker) tool. Some of the newer devices aren't
> compatible with this tool, as inMusic added image signature checking. More of this is detailed in
> the [](Firmware-Layout.md) page.
> {style="note"}

## One Image, Many Devices

inMusic publishes a separate per-product download for every device, but those downloads aren't always separate
firmwares. From Engine OS `5.0.0` onward, several of them are **byte-identical** - same MD5, served out of the same
release directory, differing only in filename. All Engine OS devices on `5.0.4` are covered by six distinct images:

| Devices                                           | Hardware IDs                     | Vendor Filenames (all identical within a row)                                                                         |
|---------------------------------------------------|----------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| Mixstream Pro, Prime 2, Prime 4                   | `NH08`, `JC16`, `JC11`           | `MIXSTREAMPRO-5.0.4-Update.img`, `PRIME2-5.0.4-Update.img`, `PRIME4-5.0.4-Update.img`                                 |
| Prime 4 Plus, Prime GO Plus, SC Live 2, SC Live 4 | `JC11S`, `JP11S`, `JP20`, `JP21` | `PRIME4PLUS-5.0.4-Update.img`, `PRIMEGOPLUS-5.0.4-Update.img`, `SCLIVE2-5.0.4-Update.img`, `SCLIVE4-5.0.4-Update.img` |
| Prime GO, SC5000 Prime, SC5000M Prime             | `JP11`, `JP07`, `JP08`           | `PRIMEGO-5.0.4-Update.img`, `SC5000-5.0.4-Update.img`, `SC5000M-5.0.4-Update.img`                                     |
| SC6000 Prime, SC6000M Prime                       | `JP13`, `JP14`                   | `SC6000-5.0.4-Update.img`, `SC6000M-5.0.4-Update.img`                                                                 |
| Mixstream Pro Go, Mixstream Pro Plus              | `NH10`, `NH08S`                  | `MIXSTREAMPROGO-5.0.4-Update.img`, `MIXSTREAMPROPLUS-5.0.4-Update.img`                                                |
| System One                                        | `RMZ2`                           | `SYSTEMONE-5.0.4-Update.img`                                                                                          |

The same six-way grouping holds for `5.0.0` through `5.0.4`. The System One ([](Rane.md), `RMZ2`) stays on its own
image throughout, which is expected - it's the only `RK3588` / `AZ04` device.

> This grouping is **finer** than the two-`Engine`-build split described in [](inMusic-Kernel-Modules.md). The
> Mixstream Pro / Prime 2 / Prime 4 image, the Prime GO / SC5000 / SC5000M image, and the SC6000 / SC6000M image all
> carry the same `Engine` build, but the images themselves are still three different files.
> {style="note"}

### It Isn't Consistent Across Versions

Image sharing comes and goes, so it can't be assumed from the version number alone. SC Live 2 and SC Live 4 shipped a
shared image at `2.3.3` and `2.4.0`, then diverged into separate per-product images for every release from `3.0.0`
through `4.3.4`, then converged again at `5.0.0` - this time into the four-device image alongside Prime 4 Plus and
Prime GO Plus.

### Akai MPC

The MPC line does something different again. Rather than publishing duplicates, a single download covers eight
hardware IDs outright: `MPC-3.9.1-Gen1-update.img` serves `ACV5`, `ACV5S`, `ACV8`, `ACVA`, `ACVA2`, `ACVB`, `ACVM`,
and `ACVR` (see [](Akai.md)), with `MPC-3.9.1-Gen2-update.img` as the Gen 2 counterpart.

> Two practical consequences. When collecting firmware, hash the downloads - naming them per product implies far more
> distinct images than actually exist. And when analyzing, a finding from one device's `5.0.x` image applies verbatim
> to the other devices in its row, since it's the same bytes.
> {style="note"}

## Additional References

* [Denon Prime Go Console Access](http://dnttalo.cluster029.hosting.ovh.net/doku.php?id=denon_prime_console) -
  Information to unpack the firmware, add in remote access, and re-pack the firmware.
