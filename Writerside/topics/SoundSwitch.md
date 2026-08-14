# SoundSwitch

From the [SoundSwitch website](https://www.soundswitch.com):
> SoundSwitch automatically creates dynamic music-synchronized light shows that amplify your DJ performance and create
> an immersive experience for your audience. SoundSwitch is compatible with both DMX, Philips Hue Smart Lights and
> Nanoleaf allowing you to effortlessly illuminate your professional event, live stream, or house party.

> Change diffs are available on [my private GitLab instance](https://gitlab.randomcpu.com/engine-os/soundswitch-app). Contact
> me to get access.

## Application Data

Path: `/usr/SoundSwitch/SoundSwitch`

### Available in firmware for

| Hardware                                                             | Hardware ID | Available          |
|----------------------------------------------------------------------|-------------|--------------------|
| [Mixstream Pro](https://www.numark.com/mixstream-pro)                | `NH08`      | :white_check_mark: |
| [Mixstream Pro GO](https://www.numark.com/product/mixstream-pro-go)  | `NH10`      | :white_check_mark: |
| [Mixstream Pro +](https://www.numark.com/product/mixstream-pro-plus) | `NH08S`     | :white_check_mark: |
| [Prime 2](https://www.denondj.com/prime-2-prime2xus)                 | `JC16`      | :white_check_mark: |
| [Prime 4](https://www.denondj.com/prime-4-prime4xus)                 | `JC11`      | :white_check_mark: |
| [Prime 4 Plus](https://www.denondj.com/prime-4-plus.html)            | `JC11S`     | :white_check_mark: |
| [Prime Go](https://www.denondj.com/prime-go.html)                    | `JP11`      | :white_check_mark: |
| [Prime Go Plus](https://www.denondj.com/prime-go-plus.html)          | `JP11S`     | :white_check_mark: |
| [SC5000](https://www.denondj.com/sc5000%20prime-sc5000primexus)      | `JP07`      | :white_check_mark: |
| SC5000M (No Product Page)                                            | `JP08`      | :white_check_mark: |
| [SC6000](https://www.denondj.com/sc6000-prime.html)                  | `JP13`      | :white_check_mark: |
| [SC6000M](https://www.denondj.com/sc6000m-prime.html)                | `JP14`      | :white_check_mark: |
| [SC Live 2](https://www.denondj.com/sclive2.html)                    | `JP20`      | :white_check_mark: |
| [SC Live 4](https://www.denondj.com/sclive4.html)                    | `JP21`      | :white_check_mark: |

> Verified against the `5.0.4` release images — every one of these devices ships this application.
> Devices that share an update image share a single root filesystem, so presence is really a property of the
> image rather than the individual device; see [](Firmware.md) for the groupings. Note the binaries are *not*
> shared between images: the `AZ01` and `AZ0x` builds have matching sizes but different checksums.
> {style="note"}

[//]: # (TODO: Additional Research)

## Runtime Console Output

`LD_LIBRARY_PATH=/usr/qt/lib:/usr/SoundSwitch/libraries:${LD_LIBRARY_PATH} /usr/SoundSwitch/SoundSwitch`

```
```
{ src="applications/soundswitch/runtime" }
