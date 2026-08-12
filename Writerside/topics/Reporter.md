# Reporter

Unsure what this does.

> Possible lead, **unconfirmed**. [QEngine](https://github.com/Peyton-C/QEngine) found that `Engine`
> launches a separate crash-reporting process on every start, backed by `crashpad` and a
> `CrashReportDB/` directory under `/data/AppDataUser/AIR Music Technology/Engine/`, which submits
> crashes and anonymous analytics to `o230257.ingest.sentry.io`. `Crashpad` is already known to be
> linked into `Engine` itself ([](Engine.md)), and nothing in that work names `/usr/Engine/Reporter`,
> so whether this binary *is* that process is still open - checking whether it opens
> `CrashReportDB/` or resolves the Sentry host would settle it.
>
> Its `docs/BLOCKING_TELEMETRY.md` blocks the traffic by pointing the ingest host at `127.0.0.1` in
> `/etc/hosts`. `Engine` already tolerates telemetry failures, so this is a cheap thing to do before
> experimenting on a unit. See [](Engine-OS.md) for the rest of that project.
> {style="note"}

## Application Data

Path: `/usr/Engine/Reporter`

### Available in firmware for

| Hardware                                                             | Available                                            |
|----------------------------------------------------------------------|------------------------------------------------------|
| [Mixstream Pro](https://www.numark.com/mixstream-pro)                | :white_check_mark:                                   |
| [Mixstream Pro GO](https://www.numark.com/product/mixstream-pro-go)  | :grey_question: (Don't know how to extract firmware) |
| [Mixstream Pro +](https://www.numark.com/product/mixstream-pro-plus) | :grey_question: (Don't know how to extract firmware) |
| [Prime 2](https://www.denondj.com/prime-2-prime2xus)                 | :white_check_mark:                                   |
| [Prime 4](https://www.denondj.com/prime-4-prime4xus)                 | :white_check_mark:                                   |
| [Prime 4 Plus](https://www.denondj.com/prime-4-plus.html)            | :grey_question: (Don't know how to extract firmware) |
| [Prime Go](https://www.denondj.com/prime-go.html)                    | :white_check_mark:                                   |
| [Prime Go Plus](https://www.denondj.com/prime-go-plus.html)          | :grey_question: (Don't know how to extract firmware) |
| [SC5000](https://www.denondj.com/sc5000%20prime-sc5000primexus)      | :white_check_mark:                                   |
| SC5000M (No Product Page)                                            | :white_check_mark:                                   |
| [SC6000](https://www.denondj.com/sc6000-prime.html)                  | :white_check_mark:                                   |
| [SC6000M](https://www.denondj.com/sc6000m-prime.html)                | :white_check_mark:                                   |
| [SC Live 2](https://www.denondj.com/sclive2.html)                    | :grey_question: (Don't know how to extract firmware) |
| [SC Live 4](https://www.denondj.com/sclive4.html)                    | :grey_question: (Don't know how to extract firmware) |

[//]: # (TODO: Additional Research)

## Runtime Console Output

`LD_LIBRARY_PATH=/usr/qt/lib:${LD_LIBRARY_PATH} /usr/Engine/Reporter`

```
```
{ src="applications/reporter/runtime" }
