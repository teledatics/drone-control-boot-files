# Drone control Boot Files

This repository contains files used for booting the Kimchi SBC and supported add-on boards.


## System Requirements

If you'd like to build the binaries for booting the board, you'll need a couple host tools. U-boot tools compile the boot scripts, and a device tree compiler for the device tree overlays.

Debian or Ubuntu:

```
sudo apt install u-boot-tools device-tree-compiler
```

MacOS:

```
brew install u-boot-tools dtc
```


## Building

Simply run:

```
make
```


## Deploying to Target for Testing

### Network (Ethernet or WiFi from Linux)

The boot files are (by default) located on a FAT partition on EMMC. If you have a network connection, you can do the following

```
ssh root@drone-control mount /dev/mmcblk0p1 /boot
scp -r output/* root@drone-control:/boot/
```

### USB

The files can be loaded with `uuu` and fastboot running on u-boot through the USB-C port. First you need to get u-boot into fastboot mode.

* Starting fastboot via UART

    If you have a serial connection, you can press Ctrl-C while u-boot is loading to interrupt the normal boot process and then run:

    ```
    fastboot usb 0
    ```

* Starting fastboot via USB

    You may use `uuu` to download and start u-boot from memory. This will automatically put u-boot in fastboot mode after starting. You'll need a copy of the u-boot image, which may be built from source or found in the Yocto deploy directory. Run the following:

    ```
    sudo uuu -b spl imx-boot
    ```

Now that the board is running fastboot (you'll see a blue LED lit), you can download the boot files to EMMC:

```
cd output
sudo uuu -v -b fat_write * mmc 0:1
```

To boot the board after downloading, the following can be run. Note that this command will timeout as control does not return to fastboot for a reply:

```
sudo uuu FB: ucmd boot
```
