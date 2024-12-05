# Engine

This is the main DJ application that the user interacts with. This does all the mixing, cloud file loading, and almost
everything else the user sees.

> Change diffs are available on [my private GitLab instance](https://gitlab.randomcpu.com/engine-os/engine-app). Contact
> me to get access.

## Application Data

Path: `/usr/Engine/Engine`

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

## Runtime Console Output {collapsible="true"}

`LD_LIBRARY_PATH=/usr/qt/lib:${LD_LIBRARY_PATH} /usr/Engine/Engine -d0`

- `-d0` flag is the debug logging flag

```
```
{ src="applications/engine/runtime" }

## Related Files

- [](Controller-Assignments.md) covers how the hardware controls different functions of this application.
