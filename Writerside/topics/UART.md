# UART

[UART](https://en.wikipedia.org/wiki/Universal_asynchronous_receiver-transmitter) is used to send data back and forth
between some devices. We can use this for console access.

## Prime Go

On the Prime Go, the UART is located on an empty header that's already on the board.

### Internal Image

![](../../assets/Prime Go Internal.jpg)

NOTE: There is a `RECOVERY` button located on the other side of the board in the bottom left of this image. Hold it
down, press the power button, and it'll go into firmware update mode.

### UART Connection

![](../../assets/Prime Go UART.jpg)

This image is of the board on the bottom left of the internal image.

Wiring:

| Color  | UART Pin |
|--------|----------|
| Red    | GND      |
| Orange | TX       |
| Yellow | RX       |

UART Speed: 115200 Baud

### U-Boot

#### Boot Log {collapsible="true"}

```
U-Boot SPL 2016.05-inmusic-20230621 (Nov 14 2023 - 10:30:29)
Trying to boot from MMC1


U-Boot 2016.05-inmusic-20230621 (Nov 14 2023 - 10:30:29 +0000), Build: jenkins-Planck-Embedded_Release-1943

Model: Denon DJ Prime GO
DRAM:  2 GiB
TSADC last_tshut_cru=1 (auto_con=0x03000000)
MMC:   dwmmc@ff0f0000: 0
In:    gpio-keys
Out:   serial
Err:   serial
AZ01  PCB revK (raw=9)
Hit any key to stop autoboot:  0 
switch to partitions #0, OK
mmc0(part 0) is current device
Scanning mmc 0:6...
Found U-Boot script /boot/boot.scr.uimg
489 bytes read in 7 ms (67.4 KiB/s)
## Executing script at 00000000
5583032 bytes read in 262 ms (20.3 MiB/s)
49752 bytes read in 10 ms (4.7 MiB/s)
Kernel image @ 0x2000000 [ 0x000000 - 0x5530b8 ]
## Flattened Device Tree blob at 01f00000
   Booting using the fdt blob at 0x1f00000
   Loading Device Tree to 1fff0000, end 1ffff257 ... OK
```

#### Environment Variables {collapsible="true"}

```
=> printenv
arch=arm
az01_bootcmd=if test x${dorecovery} = x1; then run az01_recovercmd; fi; run distro_bootcmd; echo 'Boot failed, resetting to bootloader...'; reset_bootloader
az01_recovercmd=echo 'Starting fastboot...'; fastboot 0
baudrate=115200
board=az01
board_name=az01
boot_a_script=load ${devtype} ${devnum}:${distro_bootpart} ${scriptaddr} ${prefix}${script}; source ${scriptaddr}
boot_extlinux=sysboot ${devtype} ${devnum}:${distro_bootpart} any ${scriptaddr} ${prefix}extlinux/extlinux.conf
boot_prefixes=/ /boot/
boot_script_dhcp=boot.scr.uimg
boot_scripts=boot.scr.uimg boot.scr
boot_targets=mmc0 mmc1 
bootargs=root=PARTUUID=24d1deac-3434-1a4e-98d1-68ee2945a5f1 rootwait ro rfkill.default_state=0 
bootcmd=run az01_bootcmd
bootcmd_mmc0=setenv devnum 0; run mmc_boot
bootcmd_mmc1=setenv devnum 1; run mmc_boot
bootdelay=0
cpu=armv7
distro_bootcmd=for target in ${boot_targets}; do run bootcmd_${target}; done
dorecovery=0
ethaddr=00:05:95:04:D8:20
fastboot.partition-type:data=ext4
fastboot.partition-type:rootfs=ext4
fdt_addr_r=0x01f00000
fdt_high=0x1fffffff
fdtcontroladdr=7b3a8368
fdtfile=rk3288-az01-jp11.dtb
initrd_high=0x1fffffff
kernel_addr_r=0x02000000
loadaddr=0x00800800
mmc_boot=if mmc dev ${devnum}; then setenv devtype mmc; run scan_dev_for_boot_part; fi
panel_compatible=urt,umoh-9522md-t
partitions=uuid_disk=02bebde5-b5a4-48cd-b262-512fe8b6ae06;name=uboot-spl,size=64KiB,start=32KiB,uuid=f6217a3c-1c7d-4969-8f05-88c0b9c0f0e6;name=env,size=32KiB,start=96KiB,uuid=be8b868f-89d8-4ff8-a64d-d077e73b4f09;name=uboot,size=384KiB,start=128KiB,uuid=461be792-3b29-4c0a-a6a2-3460c0119cbe;name=splash,size=4MiB,uuid=40bfa9cd-6d2c-4e22-a662-f0a803794462;name=recoverysplash,size=4MiB,uuid=e050b8e2-ee92-43ca-98ad-9aa4fbca8616;name=rootfs,bootable,size=500MiB,uuid=acded124-3434-4e1a-98d1-68ee2945a5f1;name=data,size=0,uuid=9dd41a93-59ad-4908-833a-9bf00af5b60e;
pcb_rev=K
pxefile_addr_r=0x00100000
ramdisk_addr_r=0x04000000
scan_dev_for_boot=echo Scanning ${devtype} ${devnum}:${distro_bootpart}...; for prefix in ${boot_prefixes}; do run scan_dev_for_extlinux; run scan_dev_for_scripts; done;
scan_dev_for_boot_part=part list ${devtype} ${devnum} -bootable devplist; env exists devplist || setenv devplist 1; for distro_bootpart in ${devplist}; do if fstype ${devtype} ${devnum}:${distro_bootpart} bootfstype; then run scan_dev_for_boot; fi; done
scan_dev_for_extlinux=if test -e ${devtype} ${devnum}:${distro_bootpart} ${prefix}extlinux/extlinux.conf; then echo Found ${prefix}extlinux/extlinux.conf; run boot_extlinux; echo SCRIPT FAILED: continuing...; fi
scan_dev_for_scripts=for script in ${boot_scripts}; do if test -e ${devtype} ${devnum}:${distro_bootpart} ${prefix}${script}; then echo Found U-Boot script ${prefix}${script}; run boot_a_script; echo SCRIPT FAILED: continuing...; fi; done
scriptaddr=0x00000000
serial#=A12403245317751
soc=rockchip
splashpos=m
stderr=serial
stdin=serial,gpio-keys
stdout=serial
vendor=inmusic

Environment size: 3037/8188 bytes
```

#### Clocks {collapsible="true"}

```
=> clock
osc: 24000000
apll: 1608000000
dpll: 533000000
cpll: 400000000
gpll: 594000000
npll: 73000000
```

#### Console Info {collapsible="true"}

```
=> coninfo
List of available devices:
serial@ff690000 00000003 IO 
gpio-keys 00000001 I. stdin 
vidconsole 00000002 .O 
serial   00000003 IO stdout stderr 
nulldev  00000003 IO
```

#### MMC Info {collapsible="true"}

```
=> mmc info
Device: dwmmc@ff0f0000
Manufacturer ID: 15
OEM: 100
Name: 8GTF4 
Tran Speed: 52000000
Rd Block Len: 512
MMC version 4.0
High Capacity: Yes
Capacity: 7.3 GiB
Bus Width: 8-bit
Erase Group Size: 512 KiB
```

#### Hush Shell Variables {collapsible="true"}

```
=> showvar
HUSH_VERSION=0.01
```

#### Version {collapsible="true"}

```
=> version

U-Boot 2016.05-inmusic-20230621 (Nov 14 2023 - 10:30:29 +0000)
arm-buildroot-linux-gnueabihf-gcc.br_real (Buildroot 2023.02.3-4-g189b0a5875) 12.3.0
GNU ld (GNU Binutils) 2.38
```

## MPC Live

Refer to Tadeáš Miňha's work on the [brmlab wiki](https://brmlab.cz/user/trimen/mpclive) or Niklas Nisbeth's work on [their site](https://niklasnisbeth.gitlab.io/mpc-internals/).
