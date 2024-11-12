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

```
```

{ src="applications/test-app/new-runtime" }

### Loading Test Apps

When the test app is started, it iterates over all mounts on the device, and calls the methods to check for V1 or V2
packaged apps.

After it gets a list of test apps that are available, it displays them in the launcher, and allows you to run them.

There are two packaging methods of test apps. Making an application that is packaged either way listed here will allow
you to run arbitrary software on the device.

- [`V1` packaging](#v1-packaging)
- [`V2` packaging](#v2-packaging)

#### V1 Packaging

I'm currently unsure of how V1 packaging works.

[//]: # (TODO: Reasearch this)

#### V2 Packaging

1. Checks if `TestAppsCatalog.zip` exists on the root of the drive currently being scanned.
2. Unzips the archive, and checks for [`manifest.yaml`](#v2-manifest-yaml)
3. Attempts to automatically launch the best script in `manifest.yaml`, allows the user to go to the UI listing of all
   test apps, where the user can load any test app in the manifest.

##### `manifest.yaml` Documentation {id="v2-manifest-yaml"}

This file contains an array of test applications under the testApps key, each specifying details needed to locate,
identify, and launch test applications on compatible hardware.

###### Structure Overview

```yaml
testApps:
  - version: <version>
    osVersionID: <osVersionID>
    products:
      - <productID1>
      - <productID2>
    signedImage: <True/False>
    basePath: <path>
    relativeExePath: <exePath>
    launcher-XXXXXX: <launcherName>
    name: <appName>
```

###### Fields

- `testApps`
    - Description: A required top-level key. testApps must be named exactly as shown.
    - Type: Array
    - Contents: Each item in this array defines a single test application with specific properties as outlined below.

###### Application Properties

Each application entry in the `testApps` array contains the following fields:

- `version`
    - Description: Specifies the version of the test application.
    - Type: String
    - Example: 1.0.0
- `osVersionID`
    - Description: The operating system version that the test application is compatible with.
    - Type: String
    - Example: 2023.02.11
- `products`
    - Description: An array of hardware IDs that the test app supports. This is used to specify the compatible devices
      for the application.
    - Type: Array of Strings
    - Example:

```yaml
products:
  - JP11
  - JP12
```

- `signedImage`
    - Description: Likely indicates if the test application is digitally signed.
    - Type: Boolean
    - Example: true or false
- `basePath`
    - Description: Specifies the folder within the archive (e.g., ZIP file) where the test application files are
      located.
    - Type: String
    - Example: jp11
- `relativeExePath`
    - Description: Specifies the executable file path relative to basePath that is used to launch the application.
    - Type: String
    - Example: JP11TestApp
- `launcher-XXXXXX`
    - Description: Purpose is currently unknown. This field may be used as a reference to a launcher configuration or
      specific launcher type.
    - Type: String
    - Example: SomeLauncher
- `name`
    - Description: The display name of the test application.
    - Type: String
    - Example: JP11 Test App

###### Example

Below is an example `manifest.yaml` that shows a typical structure with two test applications:

```yaml
- version: 1.0.0
  osVersionID: 2023.02.11
  products:
    - JP08
  signedImage: False
  basePath: test-apps/jp08
  relativeExePath: JP08TestApp
  launcher-XXXXXX: SomeLauncher
  name: JP08 Test App
- version: 1.0.0
  osVersionID: 2023.02.11
  products:
    - JP11
  signedImage: False
  basePath: test-apps/jp11
  relativeExePath: JP11TestApp
  launcher-XXXXXX: SomeLauncher
  name: JP11 Test App
```

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

```
```

{ src="applications/test-app/old-runtime" }
