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
[2024-10-22 13:32:45.285][600][dbus][info] DBus client thread starting
[2024-10-22 13:32:45.311][599][][info] Setting UI size: 1280x800
[2024-10-22 13:32:45.311][599][][info] Scanning devices for test apps...
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/mmcblk0p7 ...
[2024-10-22 13:32:45.311][599][][info]   mounted at "/data"
[2024-10-22 13:32:45.311][599][][info] Initializing catalog for /data
[2024-10-22 13:32:45.311][599][][info] Scanning for the test apps with V1 packaging
[2024-10-22 13:32:45.311][599][][info] Scanning for the test apps with V2 packaging
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/loop0 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/loop2 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/loop3 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/loop1 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/loop6 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/loop5 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/mmcblk0 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/mmcblk0p1 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/mmcblk0boot1 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/mmcblk0p2 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/mmcblk0boot0 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/mmcblk0p3 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/mmcblk0p5 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/mmcblk0p4 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/mmcblk0p6 ...
[2024-10-22 13:32:45.311][599][][info]   mounted at "/"
[2024-10-22 13:32:45.311][599][][info]   skipping drive
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/loop7 ...
[2024-10-22 13:32:45.311][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.311][599][][info] Checking device /dev/sda ...
[2024-10-22 13:32:45.311][599][][info]   mounted at "/media/1942-FA32"
[2024-10-22 13:32:45.311][599][][info] Initializing catalog for /media/1942-FA32
[2024-10-22 13:32:45.311][599][][info] Scanning for the test apps with V1 packaging
[2024-10-22 13:32:45.311][599][][info] Scanning for the test apps with V2 packaging
[2024-10-22 13:32:45.312][599][][info] Checking device /dev/ram0 ...
[2024-10-22 13:32:45.312][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.312][599][][info] Checking device /dev/loop4 ...
[2024-10-22 13:32:45.312][599][][info]   no filesystem, skipping
[2024-10-22 13:32:45.312][599][][info] Searching catalog for a test app: productCode=JP11, osVersionID=2023.02.11
[2024-10-22 13:32:45.312][599][][info] No matching test app found
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
JUCE v3.1.1
PathID 0: /usr/share/test-app/jp11
PathID 1: /usr/share/test-app/jp11/Resources
ERROR: resource file '' does not exist
ERROR: failed to read application descriptor file 'Application.dsc'
INFO: Failed to load JSON resources, falling back to XML
aqt2hardwaresetupconfiguration1.png -> /usr/share/test-app/jp11/Resources/aqt2hardwaresetupconfiguration1.png
aqt2hardwaresetupconfiguration10.png -> /usr/share/test-app/jp11/Resources/aqt2hardwaresetupconfiguration10.png
aqt2hardwaresetupconfiguration2.png -> /usr/share/test-app/jp11/Resources/aqt2hardwaresetupconfiguration2.png
aqt2hardwaresetupconfiguration3.png -> /usr/share/test-app/jp11/Resources/aqt2hardwaresetupconfiguration3.png
aqt2hardwaresetupconfiguration4.png -> /usr/share/test-app/jp11/Resources/aqt2hardwaresetupconfiguration4.png
aqt2hardwaresetupconfiguration5.png -> /usr/share/test-app/jp11/Resources/aqt2hardwaresetupconfiguration5.png
aqt2hardwaresetupconfiguration6.png -> /usr/share/test-app/jp11/Resources/aqt2hardwaresetupconfiguration6.png
aqt2hardwaresetupconfiguration7.png -> /usr/share/test-app/jp11/Resources/aqt2hardwaresetupconfiguration7.png
aqt2hardwaresetupconfiguration8.png -> /usr/share/test-app/jp11/Resources/aqt2hardwaresetupconfiguration8.png
aqt2hardwaresetupconfiguration9.png -> /usr/share/test-app/jp11/Resources/aqt2hardwaresetupconfiguration9.png
arrows.png -> /usr/share/test-app/jp11/Resources/arrows.png
ce.png -> /usr/share/test-app/jp11/Resources/ce.png
circles.png -> /usr/share/test-app/jp11/Resources/circles.png
circles150.png -> /usr/share/test-app/jp11/Resources/circles150.png
displayimage100brightness.png -> /usr/share/test-app/jp11/Resources/displayimage100brightness.png
displayimage25brightness.png -> /usr/share/test-app/jp11/Resources/displayimage25brightness.png
fill-00000040.png -> /usr/share/test-app/jp11/Resources/fill-00000040.png
firehosespritesheet.png -> /usr/share/test-app/jp11/Resources/firehosespritesheet.png
jp11.front.700x86.png -> /usr/share/test-app/jp11/Resources/jp11.front.700x86.png
jp11.led.meter.png -> /usr/share/test-app/jp11/Resources/jp11.led.meter.png
jp11.top.700x465.png -> /usr/share/test-app/jp11/Resources/jp11.top.700x465.png
led.round.png -> /usr/share/test-app/jp11/Resources/led.round.png
mboxmini1.png -> /usr/share/test-app/jp11/Resources/mboxmini1.png
roundcontrols.png -> /usr/share/test-app/jp11/Resources/roundcontrols.png
session skin 2b.png -> /usr/share/test-app/jp11/Resources/session skin 2b.png
shutdown panel.png -> /usr/share/test-app/jp11/Resources/shutdown panel.png
PathID 3: /root//
MIDI input devices found:  1
  MIDI input device #0:  PRIME GO Control Surface
MIDI output devices found: 1
  MIDI output device #0: PRIME GO Control Surface
Skipping inquiry for input port "PRIME GO Control Surface": port explicitly assigned
Checking required MIDI port: functionID=0, functionIndex=0, manufacturerID=00020B, productID=0x0C, deviceID=0x00, inputPortName="PRIME GO Control Surface", outputPortName="PRIME GO Control Surface"
  Port name explicitly specified, skipping automatic port detection
Audio device found: 5
Audio device #0: "Default Audio Device (1)"
Audio device #1: "Default Audio Device (2)"
Audio device #2: "JP11, JP11 PCM inmusic,jp11-audio-codec-0; Direct hardware device without any conversions"
Audio device #3: "JP11, JP11 PCM inmusic,jp11-audio-codec-0; Direct sample snooping device"
Audio device #4: "JP11, JP11 PCM inmusic,jp11-audio-codec-0; Direct sample mixing device"
Setting realtime priority for DWC2 OTG controller interrupt threads
>>> script start <<<

		#!/bin/sh
		pids=""
		pids="$pids `ps -C irq/42-ff580000 -o pid=`"
ps: invalid option -- 'C'
BusyBox v1.36.1 (2024-09-10 21:31:18 UTC) multi-call binary.

Usage: ps [-o COL1,COL2=HEADER]

Show list of processes

	-o COL1,COL2=HEADER	Select columns for display
		pids="$pids `ps -C irq/42-dwc2_hso -o pid=`"
ps: invalid option -- 'C'
BusyBox v1.36.1 (2024-09-10 21:31:18 UTC) multi-call binary.

Usage: ps [-o COL1,COL2=HEADER]

Show list of processes

	-o COL1,COL2=HEADER	Select columns for display
		pids="$pids `ps -C irq/28-ffb20000 -o pid=`"
ps: invalid option -- 'C'
BusyBox v1.36.1 (2024-09-10 21:31:18 UTC) multi-call binary.

Usage: ps [-o COL1,COL2=HEADER]

Show list of processes

	-o COL1,COL2=HEADER	Select columns for display
		for pid in $pids
		do
			chrt -f -p 99 $pid
		done
		>>> script end <<<
Disabling soft rfkill states:
    /sys/class/rfkill/rfkill0
    /sys/class/rfkill/rfkill1
Starting command: hciconfig hci0 up
sh: hciconfig: not found
Exit status: 32512
Starting command: systemctl restart bluetooth
Exit status: 0
DBUS: Run: starting
BluetoothObject:351 found object /org/bluez/hci0
Starting command: connmanctl enable wifi
wifi is already enabled
Exit status: 0
Starting command: connmanctl scan wifi
Signal:400 properties changed on /org/bluez/hci0
Signal:400 properties changed on /org/bluez/hci0
Signal:400 properties changed on /org/bluez/hci0
Signal:400 properties changed on /org/bluez/hci0
Scan completed for wifi
Exit status: 0
Sending INIT command to control surface: F000020B7F0C60000404010100F7
Sending MIC ATT PAD to 0dB:
  8F3800
  8F3700
  9F367F
LMidiCommunicatingTestModuleMultiLookLabel button01FXAssign1 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button02FXAssign2 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button03FXOnOff SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button04Mic1OnOff SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button05EjectMedia SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button06PlayerAHotCueMode SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button07PlayerALoopMode SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button08PlayerARollMode SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button09PlayerABankSelect SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button10PlayerAPad1 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button11PlayerAPad2 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button12PlayerAPad3 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button13PlayerAPad4 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button14PlayerASync SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button15PlayerAVinylCDMode SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button16PlayerACue SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button17PlayerAPlayPause SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button18PlayerAPitchDown SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button19PlayerAPitchUp SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button20Channel1SweepFX1 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button21Channel1SweepFX2 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button22Channel1Cue SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button23Back SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button24Forward SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button25LoadA SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button26LoadB SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button27View SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button28Shift SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button29Channel2SweepFX1 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button30Channel2SweepFX2 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button31ChannelCue SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button32PlayerBHotCueMode SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button33PlayerBLoopMode SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button34PlayerBRollMode SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button35PlayerBBankSelect SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button36PlayerBPad1 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button37PlayerBPad2 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button38PlayerBPad3 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button39PlayerBPad4 SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button40PlayerBSync SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button41PlayerBVinylCDMode SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button42PlayerBCue SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button43PlayerBPlayPause SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button44PlayerBPitchDown SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button45PlayerBPitchUp SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel button46Mic2OnOff SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder01Main SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder02FXSelect SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder03FXTime SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder04PlayerAAutoLoop SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder05PlayerBAutoLoop SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder01Main SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder02FXSelect SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder03FXTime SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder04PlayerAAutoLoop SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder05PlayerBAutoLoop SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot01FXWetDry SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot02Channel1Level SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot03Channel1EQLow SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot04Channel1EQMid SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot05Channel1EQHigh SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot06PlayerAPitch SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot07Channel1SweepFX SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot08Channel1Fader SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot09Channel2SweepFX SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot10Channel2Fader SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot15Channel2Level SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot16Channel2EQLow SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot17Channel2EQMid SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot18Channel2EQHigh SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot19PlayerBPitch SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot20CrossFader SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot23CueMix SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot24CueVolume SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder02FXSelect SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel encoder03FXTime SetEnableMidiInput(false)
MIDI Capture Port destroyed (JUCE): PRIME GO Control Surface
INFO: Display backlight PWM range: 255 (0-255) RAW
INFO: Display backlight current PWM: 117 RAW (46%)
LMidiCommunicatingTestModuleMultiLookLabel pot SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel touch SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel pot SetEnableMidiInput(false)
LMidiCommunicatingTestModuleMultiLookLabel touch SetEnableMidiInput(false)
MIDI Capture Port destroyed (JUCE): PRIME GO Control Surface
Entering: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Completed: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Entering: Unnamed ThreadPool
Entering: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Completed: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Entering: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Completed: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Entering: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Completed: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Entering: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Completed: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Entering: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Completed: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Entering: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Completed: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Entering: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Completed: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Entering: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Completed: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Entering: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Completed: LRefCountedObject::ToString() not overridden. Returning TypeName: N2MA13LJuceMidiCommE
Parsed address string 00:05:95:04:d8:20: matched=6
Starting command: fw_printenv ethaddr
ethaddr=00:05:95:04:D8:20
Exit status: 0
Parsed address string 00:05:95:04:D8:20: matched=6
Starting command: fw_printenv ethaddr
ethaddr=00:05:95:04:D8:20
Exit status: 0
Parsed address string 00:05:95:04:D8:20: matched=6
Parsed address string 00:05:95:04:d8:20: matched=6
Starting command: fw_printenv serial#
serial#=A12403245317751
Exit status: 0
ISerialTest::TestStatus: 0 -> 2
PathID 2: /var//
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_1__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_2__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_3__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_4__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_5__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_6__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_7__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_8__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_9__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_10__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_11__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_12__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_13__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_14__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_15__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_16__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_17__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_18__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_19__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_20__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_21__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_22__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_23__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_24__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_25__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_26__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_27__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_28__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_29__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_30__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_31__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_32__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_33__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_34__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_35__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_36__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_37__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_38__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_39__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_40__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_41__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_42__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_43__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_44__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_45__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_46__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_47__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_48__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_49__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_50__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__SWITCHESTEST__MIDICOMMUNICATINGTESTMODULE_51__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__ENCODERSTEST__MIDICOMMUNICATINGTESTMODULE_1__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__ENCODERSTEST__MIDICOMMUNICATINGTESTMODULE_2__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__ENCODERSTEST__MIDICOMMUNICATINGTESTMODULE_3__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__ENCODERSTEST__MIDICOMMUNICATINGTESTMODULE_4__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__ENCODERSTEST__MIDICOMMUNICATINGTESTMODULE_5__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_1__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_2__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_3__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_4__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_5__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_6__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_7__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_8__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_9__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_10__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_11__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_12__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_13__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_14__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_15__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_16__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_17__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__POTSTEST__MIDICOMMUNICATINGTESTMODULE_18__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__LEDTEST__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__LEDTEST__MANUALVERIFICATIONTEST__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__DISPLAYTEST__MANUALVERIFICATIONTEST__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__CTPTEST__FOLLOWFINGERTEST__MANUALVERIFICATIONTEST__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__CTPTEST__MATCHFINGERTEST__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__PLATTERSTEST__MIDICOMMUNICATINGTESTMODULE_1__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__PLATTERSTEST__MIDICOMMUNICATINGTESTMODULE_2__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__PLATTERSTEST__MIDICOMMUNICATINGTESTMODULE_3__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__PLATTERSTEST__MIDICOMMUNICATINGTESTMODULE_4__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__TOUCHTEST__MIDICOMMUNICATINGTESTMODULE_1__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__UITESTS__TOUCHTEST__MIDICOMMUNICATINGTESTMODULE_2__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__IOTESTS__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__IOTESTS__EthernetTestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__IOTESTS__EthernetTestLog
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__IOTESTS__USBBTestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__IOTESTS__USBBTestLog
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__IOTESTS__BluetoothTestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__IOTESTS__BluetoothTestLog
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__IOTESTS__WiFiTestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__IOTESTS__WiFiTestLog
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__MEDIATESTS__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__MEDIATESTS__ExternalSDTestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__MEDIATESTS__BackUSB1TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__MEDIATESTS__ExternalSDTestLog
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__MEDIATESTS__BackUSB1TestLog
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_1__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_1__SubTestList
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_2__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_2__SubTestList
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_3__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_3__SubTestList
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_4__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_4__SubTestList
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_5__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_5__SubTestList
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_6__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_6__SubTestList
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_7__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_7__SubTestList
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_8__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_8__SubTestList
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_9__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__DEVICETESTS__AUDIOQUALITYTEST2__AUDIOQUALITYTESTCONFIGURATION2_9__SubTestList
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__MEMORYTEST__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__MEMORYTEST__RAMTestStatus
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__MEMORYTEST__EMMCTestStatus
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__MEMORYTEST__RAMTestLog
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__MEMORYTEST__EMMCTestLog
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__RTCTEST__MANUALVERIFICATIONTEST__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__VERSIONTEST__MANUALVERIFICATIONTEST__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__MACTEST__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__SERIALTEST__TestStatus
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__SERIALTEST__MidiDeviceSerialNumberTestStatus
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__SERIALTEST__BootloaderSerialNumberTestStatus
MainControl Received value T_NONE for ParamID = DEVICE__SYSTEMTESTS__BATTERYTEST__MANUALVERIFICATIONTEST__TestStatus
ISerialTest::TestStatus: 2 -> 2
ISerialTest::TestStatus: 2 -> 2
LMidiCommunicatingTestModuleMultiLookLabel button01FXAssign1 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button02FXAssign2 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button03FXOnOff SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button04Mic1OnOff SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button05EjectMedia SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button06PlayerAHotCueMode SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button07PlayerALoopMode SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button08PlayerARollMode SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button09PlayerABankSelect SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button10PlayerAPad1 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button11PlayerAPad2 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button12PlayerAPad3 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button13PlayerAPad4 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button14PlayerASync SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button15PlayerAVinylCDMode SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button16PlayerACue SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button17PlayerAPlayPause SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button18PlayerAPitchDown SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button19PlayerAPitchUp SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button20Channel1SweepFX1 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button21Channel1SweepFX2 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button22Channel1Cue SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button23Back SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button24Forward SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button25LoadA SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button26LoadB SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button27View SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button28Shift SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button29Channel2SweepFX1 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button30Channel2SweepFX2 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button31ChannelCue SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button32PlayerBHotCueMode SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button33PlayerBLoopMode SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button34PlayerBRollMode SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button35PlayerBBankSelect SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button36PlayerBPad1 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button37PlayerBPad2 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button38PlayerBPad3 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button39PlayerBPad4 SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button40PlayerBSync SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button41PlayerBVinylCDMode SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button42PlayerBCue SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button43PlayerBPlayPause SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button44PlayerBPitchDown SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button45PlayerBPitchUp SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel button46Mic2OnOff SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel encoder01Main SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel encoder02FXSelect SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel encoder03FXTime SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel encoder04PlayerAAutoLoop SetEnableMidiInput(true)
LMidiCommunicatingTestModuleMultiLookLabel encoder05PlayerBAutoLoop SetEnableMidiInput(true)
LDeviceView::ProcessIdle Sending Key: VKEY_RETURN
LMidiTestSequenceView<ISwitchesTest>::ProcessKey handled Enter
LDeviceView::ProcessIdle Sending Key: VKEY_RETURN
LMidiTestSequenceView<ISwitchesTest>::ProcessKey handled Enter
```
