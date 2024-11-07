# Test App

There are two different versions, depending on what firmware the device is running.

| Firmware Version | App Type             |
|------------------|----------------------|
| >= `4.1.0`       | [New](#new-test-app) |
| < `4.1.0`        | [Old](#old-test-app) |

> I haven't figured out how to take screenshots of this. While the app is running, `cat /dev/fb0 > /frame` then running
`convert -size 800x1280 -rotate 90 -depth 8 bgra:frame frame.png` **does not** include an image of this app, even though
> that works on other apps that render to `fb0`. I'd love to figure this out and include screenshots.
> {style="note"}

## New Test App

This is a program that lists other test apps that are located on attached storage, and
allows the user to launch them from a UI.

This will scan any flash drive that's plugged in for test apps. The binary looks for `manifest.yaml`, but I'm not sure
if this file should exist inside a zip file or not.

### Application Data {id="application-data_new"}

Path: `/bin/test-app-launcher`

### Runtime Console Output {collapsible="true" id="runtime-console-output_new"}

`JP11TestApp`

<code-block src="applications/test-app/new-runtime" />

## Old Test App

This is an application for testing the buttons and switches of the unit.

### Application Data {id="application-data_old"}

Path: `/usr/share/test-app/[HARDWARE_ID]/[HARDWARE_ID]TestApp`

- Prime Go (`JP11TestApp`): This shows an image of the Prime Go, and highlights items as they're tested.

This application uses the folder at `/usr/share/test-app/[HARDWARE_ID]` to get the device images and configuration for
the current device.

[//]: # (TODO: Additional Research)

### Runtime Console Output {collapsible="true" id="runtime-console-output_old"}

`JP11TestApp`

<code-block src="applications/test-app/old-runtime" />
