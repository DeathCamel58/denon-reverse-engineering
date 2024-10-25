# MPC

MPC is inMusic's MPC operating system. This is mainly just a [buildroot](Buildroot.md) OS, with some additional custom
software.

## Custom Applications

This is additional custom software that I've noticed in the firmware, along with documentation pages for them.

> My research is not focused on this, so documentation will likely be severely lacking here.
> {style="note"}

| Software name          | Software Path               | Description                                                     | Page                        |
|------------------------|-----------------------------|-----------------------------------------------------------------|-----------------------------|
| MPC                    | `/usr/bin/MPC`              | :grey_question: The main software that the user interacts with. | TODO: Create a page         |
| [HARDWARE_ID] Test App | `/bin/[HARDWARE_ID]TestApp` | Debug software for testing switches, LEDs, etc.                 | [](HARDWARE-ID-Test-App.md) |

> [HARDWARE_ID] Test App does exist in these firmwares. However, I don't have a device to test this on.
> {style="note"}
