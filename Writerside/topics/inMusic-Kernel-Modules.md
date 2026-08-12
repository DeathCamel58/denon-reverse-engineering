# inMusic Kernel Modules

Almost everything in `/usr/lib/modules/[kernel]/kernel` is stock mainline Linux. Across every `5.0.4` firmware I
can unpack, only **seven** modules are inMusic's own, and all seven are audio drivers.

They ship as **unstripped** `ELF` relocatable objects with intact symbol tables, so they load into Ghidra with
every function already named. There's no `DWARF` debug info in any of them.

> Every one of these is `GPL` licensed and marked `intree: Y`, which means they were built inside a kernel source
> tree rather than as external modules. inMusic maintains a kernel fork containing them, but I haven't found any
> published source.
> {style="note"}

## Where to find them

Every `RK3288` device reports `ID=az01` in `/usr/lib/os-release` and runs the same kernel, so **the same six modules
ship across the whole platform**. There's no need to pull them per device. Confirmed on the `5.0.4` firmware for the
Prime 2, Prime 4, Prime 4 Plus, Prime GO, Prime GO Plus, SC5000, SC5000M, SC6000, SC6000M, SC Live 2 and SC Live 4.

> The rootfs is **not** byte-identical across those devices. There are two different `Engine` builds on the same
> platform: one for the Prime 2 / Prime 4 / Prime GO / SC5000 / SC5000M / SC6000 / SC6000M, and another for the
> Prime 4 Plus / Prime GO Plus / SC Live 2 / SC Live 4. The kernel and this module set are common to both.
> {style="note"}

| Platform | Path                                                              |
|----------|-------------------------------------------------------------------|
| `AZ01`   | `/usr/lib/modules/6.6.119-az01-2025-12-17-rt67/kernel/sound/soc/` |
| `AZ04`   | `/usr/lib/modules/6.18.9-imb-2026-02-06-rt3/kernel/sound/soc/`    |

## Module Overview

| Module                          | Description                      | Role      | License  | Size    | Funcs |
|---------------------------------|----------------------------------|-----------|----------|---------|-------|
| `snd-soc-inmusic-jp07`          | InMusic JP07 ASoC codec driver   | Codec     | `GPL v2` | 16.8 KB | 10    |
| `snd-soc-rockchip-inmusic-jp07` | InMusic JP07 machine ASoC driver | Machine   | `GPL v2` | 17.5 KB | 10    |
| `snd-soc-inmusic-nh08`          | InMusic NH08 ASoC codec driver   | Codec     | `GPL v2` | 12.4 KB | 7     |
| `snd-soc-rockchip-inmusic-nh08` | InMusic NH08 machine ASoC driver | Machine   | `GPL v2` | 8.8 KB  | 10    |
| `snd-soc-tas5805m-inmusic`      | TAS5805M audio amplifier driver  | Amplifier | `GPL`    | 24.0 KB | 25    |
| `snd-soc-eta5805`               | ETA5805 Audio Amplifier Driver   | Amplifier | `GPL v2` | 13.6 KB | 13    |
| `snd-soc-inmusic-az04`          | inMusic AZ04 ASoC codec driver   | Codec     | `GPL v2` | 13.5 KB | 5     |

> `snd-soc-tas5805m-inmusic` declares `GPL` where every other module declares `GPL v2`. It also matches the
> mainline compatible `ti,tas5805m`, so it's a fork of mainline `sound/soc/codecs/tas5805m.c` rather than a
> from-scratch driver, and can be diffed against upstream.
> {style="note"}

## Authorship

Every one of these is by the same person, and the address changes between platform generations:

| Modules                | Author                                      |
|------------------------|---------------------------------------------|
| All six `AZ01` modules | `John Keeping <john@metanate.com>`          |
| `snd-soc-inmusic-az04` | `John Keeping <jkeeping@inmusicbrands.com>` |

[Metanate](https://metanate.com) is a UK software consultancy, so the `AZ01` work looks like it was contracted
out, with the newer `AZ04` driver written in-house.

Two of the codecs leak their in-tree source paths in assertion strings:

```
/sound/soc/codecs/inmusic-jp07.c
/sound/soc/codecs/inmusic-nh08.c
```

## Vermagic

`vermagic` has to match the running kernel exactly or `insmod` refuses the module, so these are only loadable on
their own firmware's kernel.

| Platform | `vermagic`                                                    |
|----------|---------------------------------------------------------------|
| `AZ01`   | `6.6.119-az01-2025-12-17-rt67 SMP preempt_rt ARMv7 p2v8`      |
| `AZ04`   | `6.18.9-imb-2026-02-06-rt3 SMP preempt_rt mod_unload aarch64` |

Note `mod_unload` is only set on `AZ04` - on `AZ01` module unloading is compiled out.

## Device Tree Aliases

This is the most useful part of the metadata. Rather than one driver per product, there are two codec/machine
pairs whose `of_match` tables cover the entire fleet.

### snd-soc-inmusic-jp07 (codec)

```
inmusic,acva-audio-codec   inmusic,acvk-audio-codec   inmusic,acvm-audio-codec
inmusic,acvr-audio-codec   inmusic,ada2-audio-codec   inmusic,az05-audio-codec
inmusic,hg02-audio-codec   inmusic,hg03-audio-codec   inmusic,hg04-audio-codec
inmusic,hg08-audio-codec   inmusic,hv01-audio-codec   inmusic,jc16-audio-codec
inmusic,jp07-audio-codec   inmusic,jp11-audio-codec   inmusic,jp12-audio-codec
inmusic,ldmd-audio-codec   inmusic,mg01-audio-codec
```

### snd-soc-rockchip-inmusic-jp07 (machine)

```
inmusic,acva-audio   inmusic,acvk-audio   inmusic,acvm-audio   inmusic,acvr-audio
inmusic,ada2-audio   inmusic,az05-audio   inmusic,hg02-audio   inmusic,hg03-audio
inmusic,hg04-audio   inmusic,hg08-audio   inmusic,hv01-audio   inmusic,jc16-audio
inmusic,jp07-audio   inmusic,jp11-audio   inmusic,jp12-audio   inmusic,jp20-audio
inmusic,jp21-audio   inmusic,ldmd-audio   inmusic,ldmf-audio   inmusic,mg01-audio
inmusic,nh09-audio
```

### snd-soc-inmusic-nh08 (codec)

```
inmusic,jp20-audio-codec   inmusic,jp21-audio-codec   inmusic,ldmf-audio-codec
inmusic,nh08-audio-codec   inmusic,nh09-audio-codec   inmusic,nh10-audio-codec
```

### snd-soc-rockchip-inmusic-nh08 (machine)

```
inmusic,nh08-audio   inmusic,nh10-audio
```

### Amplifiers and AZ04

```
snd-soc-tas5805m-inmusic:  ti,tas5805m   i2c:tas5805m
snd-soc-eta5805:           eta,eta5805
snd-soc-inmusic-az04:      inmusic,az04-codec
```

## Undocumented Hardware IDs

The `of_match` tables name five hardware IDs that don't appear anywhere else in this repository, in any DTS or
on any [](Brands.md) page:

| Hardware ID | Notes                                                    |
|-------------|----------------------------------------------------------|
| `ACVK`      | `ACV` prefix, so likely an Akai MPC variant              |
| `ADA2`      | Unknown prefix, not seen elsewhere                       |
| `HG08`      | `HG` prefix, so likely a HeadRush variant                |
| `LDMD`      | Unknown prefix, not seen elsewhere                       |
| `LDMF`      | Uses the `NH08` codec, so likely an SC Live style device |

`JP12`, `NH09`, and `HG08` are also in these tables and are mentioned elsewhere in this repository, but I have no DTS for
either.

> If you have a device matching any of these, I'd love a DTS or a `/proc/config.gz`. File an issue
> [here](https://github.com/DeathCamel58/denon-reverse-engineering/issues).
> {style="note"}

## Device Tree Properties

The machine drivers read these properties from their `sound` node:

| Property                | Used by     | Meaning                            |
|-------------------------|-------------|------------------------------------|
| `inmusic,cpu`           | Both pairs  | Phandle to the I2S CPU DAI         |
| `inmusic,codec`         | `jp07` pair | Phandle to the codec               |
| `inmusic,master-codec`  | `nh08` pair | Phandle to the primary codec       |
| `inmusic,speaker-codec` | `nh08` pair | Phandle to the speaker amplifier   |
| `inmusic,master-slave`  | `jp07` pair | Clock master/slave selection       |
| `clksel0` / `clksel1`   | `jp07` pair | GPIOs for sample rate clock select |

The `jp07` machine driver also selects between `codec-clk-master` and `codec-clk-slave` pinctrl states.

The codecs take `mute`, `reset` and `enable` GPIOs. On `AZ04` the codec additionally reads its whole DAI
capability set from the device tree, via `inmusic,playback-channels`, `inmusic,capture-channels` and
`inmusic,pcm-rates`.

## Extracting Them

Unpack the firmware per [](Extracting.md), then:

```Bash
find [rootfs]/usr/lib/modules -name '*.ko' | while read -r k; do
  modinfo "$k" | grep -qi inmusic && echo "$k"
done
```

`modinfo` works fine on a foreign-architecture module, so this can run on your desktop.
