# How to install your custom firmware
## DISCLAIMER

DISCLAIMER: This procedure is for experienced users, you can brick your Stora, and we will have not responsability at all, make at you own risk. Please be sure you understand all the procedure, before you start. At the moment this procedure is not automatized, and have not a lot of users that have test it. With this procedure (with luck) you will obtain a Debian Linux on your Stora, without any frienly interface or web access.

## Introduction

This document explains how to install an alternative firmware on stora, you will install a new kernel, and a new rootfs.

As far as i know there have been installed succesfully three different rootfs:

- Unstable standard Debian. (MacWire)
- Testing squeeze Emdebian Debian. (Dieren)
- Fedora Core (please fill who did it)

## Prerequisites

- You must have good Linux knowledge
- You must be familiar with [Root Access Via TFTP](Root_Access_Via_TFTP.md) aqnd have all the related tools described there.
- You have to download DEBIAN-MAKEOVER: http://www.openstora.com/files/albums/uploads/debian_makeover_tar.bz2
- Optionally EMDEBIAN-rootfs if you want it. http://hardwarehackersunite.com/index.php?option=com_remository&Itemid=58&func=fileinfo&id=31
- Read all the posts on the forum relatives to this hack: http://www.openstora.com/forum/viewtopic.php?f=6&t=65

if you have any question, do it on the forum.
## Hardware peculiarities

The Stora hardware have some important peculiarities (common to ARM arch) , that are very important to understand in order to know what you are doing with this reinstallation.

### Flash memory

The Stora have its main storage system based on MTD is a "Memory Technology Device", that is very different from harddisks, or usb memory drives. Moreover you need to use the MTD tools for Linux. More info : http://www.linux-mtd.infradead.org/faq/general.html You will need to use command like flash_eraseall, nandwrite, nanddump.

### UBOOT

This is the boot loader, (similar to LILO), it boots the kernel from flash, or from network by tftp, where using the rescue mode. More info here: http://www.denx.de/wiki/U-Boot You can access a Uboot interactive session if you build a Serial connection (not necessary for this hack). You can read or modify the Uboot values with the fw_printenv and fw_setenv commands from linux console.

### Partition table

Other interesting issue related to the use of MTD devices is that there is no partition table, the layout of the disk (MTD memory I mean), is passed to kernel as parameters (PLEASE McWire explain where you pass it).

The original Stora have three partitions:

- Original-U-BOOT (1Mb)
- Original-Kernel (6Mb)
- Original-ROOTFS (249Mb)
```
# cat /proc/mtd
dev:    size   erasesize  name
mtd0: 00100000 00020000 "u-boot"
mtd1: 00600000 00020000 "uImage"
mtd2: 0f900000 00020000 "root"
```

The custom firmware will be:

- Original-U-BOOT (1Mb)
- NEW-Kernel (4Mb)
- NEW-ROOTFS (251Mb)
```
# cat /proc/mtd 
dev:    size   erasesize  name
mtd0: 00100000 00020000 "u-boot"
mtd1: 00400000 00020000 "uImage"
mtd2: 0fb00000 00020000 "root"
```
NOTE: The compiled custom kernel have this new layout compiled as option. We will keep untouched U-BOOT, this could allows us to recover or reinstall.


## Kernel options

We have to boot the new kernel with two special parameters that are different on Original kernel. (Anyone fill more info).

### Original
```
mainlineLiNux=no
arcNumber (not set)
```
### Custom kernel
```
mainlineLinux=yes
arcNumber=2743
```
## How to install

(All the info after here is copied from README file inside Debian-Makover)

set up the regular hack for gaining root access
```
$ cd /
$ sudo -s
# tar xvfj ~/Desktop/alternates.tar.bz2
# cd /mnt/Axentra/marvell.nfs.rootfs/root/
tar xvfj ~/Desktop/debian_makeover.tar.bz2
# mkdir .ssh
# cp ~/.ssh/id_dsa.pub .ssh/authorized_keys
# wireshark &
```
hold down the red reset button ... find out what the stora's ip is
```
# exit
$ ssh root@10.x.x.x
```
Now you're logged into the stora as root, here's the scary stuff.
```
-bash-3.2# ./fw_setenv mainlineLinux yes
-bash-3.2# ./fw_setenv arcNumber 2743
-bash-3.2# shutdown -h now
```
Now your stora is off and you are back at your linux prompt, wireshark should still be running
```
$ sudo cp /mnt/Axentra/marvell.nfs.rootfs/root/uImage.mainline /tftpboot/uImage
```
hold down the red reset button ... find out what the stora's ip is
```
$ ssh root@10.x.x.x
-bash-3.2# uname -a
```
You should see 2.6.34-rc2-g85354d2 #1 PREEMPT Now you are going to flash the new kernel, install the new rootfs and setup u-boot to boot with it. This flashes the new kernel:
```
-bash-3.2# flash_eraseall /dev/mtd1
-bash-3.2# nandwrite -p /dev/mtd1 uImage.mainline
```
This sets up the ubifs filesystem:
```
-bash-3.2# ./ubiformat /dev/mtd2 -O 2048
-bash-3.2# ./ubiattach /dev/ubi_ctrl -m 2 -O 2048
-bash-3.2# ubimkvol -d 0 -N rootfs -m
-bash-3.2# mount -t ubifs ubi0:rootfs /mnt/ubifs
-bash-3.2# cd /mnt/ubifs/
-bash-3.2# tar xvf /root/deb_rootfs.tar
```
You can use the alternative emdebian-rootfs if you prefer it. Now you have to manually add your mac address
```
-bash-3.2# vi etc/network/interfaces (assuming you use vi as your editor)
```
You have no password set for root, so copy your ssh key
```
-bash-3.2# mkdir root/.ssh
-bash-3.2# chmod 700 root/.ssh/
-bash-3.2# cp /root/.ssh/authorized_keys root/.ssh/
-bash-3.2# chmod 600 root/.ssh/authorized_keys
```
Almost there, now set up u-boot env vars
```
-bash-3.2# cd /root
-bash-3.2# ./fw_setenv bootcmd 'nand read.e 0x800000 0x100000 0x400000; setenv bootargs $(console) $(bootargs_root); bootm 0x800000'
```
reboot, and good luck!
```
-bash-3.2# reboot
```

## Credits

This doc was wroten by Dieren following the debian_makeover by Macwire, and some infor from forum.