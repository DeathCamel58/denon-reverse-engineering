# Unsigned Firmware Layout

> I've made a firmware extraction utility that can be found on [my private GitLab instance](https://gitlab.randomcpu.com/engine-os/inmusic-firmware-modification-framework).
> {style="note"}

The unsigned firmwares updates are essentially device trees that contain the binary data.

For example, `fdtdump <image>` on `PRIMEGO-4.1.0-Update.img` outputs:

```
```

{ src="firmware-layout/unsigned-fdt" }

## Metadata

- **Timestamp** (`timestamp`): The creation or release timestamp of the firmware.
- **Description** (`description`): A brief description of the firmware image.
- **Compatibility** (`compatible`): Specifies compatible devices.
- **Device ID** (`devices`): An identifier for the target device(s).
- **Firmware Version** (`version`): Indicates the firmware version.

## Images

Each firmware may contain multiple images, each with specific properties:

- **Image Name** (`images->[image_name]`): Unique identifier or name of the image.
    - **Description** (`description`): Describes the purpose of the image.
    - **Data** (`data`): Binary data for the image, represented as a series of hexadecimal values.
    - **Partition** (`partition`): Indicates the target partition for this image (e.g., `rootfs`, `splash`,
      `recoverysplash`).
    - **Compression** (`compression`): Specifies the compression type used on this image (e.g., `xz`).
    - **Hash** (`hash`): Contains hash information for data integrity:
        - **Value** (`hash->value`): The hash value used to verify the data.
        - **Algorithm** (`hash->algo`): The hashing algorithm used (e.g., `sha1`).
