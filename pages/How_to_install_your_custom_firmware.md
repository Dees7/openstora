# How to install Debian Linux on NETGEAR Stora

This is a guide to install Debian Linux on:
- NETGEAR Stora MS2000
- NETGEAR Stora MS2110
- NETGEAR Stora MS2120

The goals are:

- Run a full stable Debian linux on NETGEAR Stora
- The system will be running from a USB stick, so system- and data-drives are not mixed.
- The system will be running a stable Debian kernel, for which you can compile modules to, yourself.


## Preparations
### Things you need

- Netgear Stora :-)
- A [TTL serial port](Root_Access_Via_Serial_Console.md), and wires to connect it.
- An USB stick. Preferably with +10MB/s write speed, and +8GB capacity.
- A PC to serve as a TFTP server, and to connect to the Stora via the TTL serial port.

All the files in this guide can also be downloaded from http://openstora.cablecat.dk.

### Setup TTL serial connection

Make a TTL serial connection as described here: [Root Access Via Serial Console](Root_Access_Via_Serial_Console.md)

Install screen and connect to the USB attached serial connection:
```
#This is done on your PC
aptitude -y install screen
screen /dev/ttyUSB0 115200
```
If you need, you can use the keyboard combo "ctrl+a d" to detach from screen, and the command `screen -r` to reattach. See "man screen" for details.

**Note:** Windows users can use [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

###Setup a TFTP server

Install tfpd and download the files to the TFTP dir:
```
#This is done on your PC
aptitude -y install tftpd-hpa
service tftpd-hpa start
cd /var/lib/tftpboot/ # or cd /srv/tftp
wget http://dl.dropbox.com/u/1397036/stora_di/uImage.di
wget http://dl.dropbox.com/u/1397036/stora_di/uInitrd.di
wget http://dl.dropbox.com/u/1397036/stora_di/uImage
wget http://dl.dropbox.com/u/1397036/stora_di/uInitrd
```
**Note:** Windows users can use [PumpKIN](http://kin.klever.net/pumpkin/binaries).

## Install Debian
### Prepare the Stora

- Detach the hard-drive(s). You so don't accidentally install anything on them. And so your USB stick always becomes /dev/sda.
- Attach your USB stick.

### Interrupt U-Boot

When the Stora starts, you will have a short time where you can interrupt the boot process.
```
Hit any key to stop autoboot:
```
Type "help" for more information about what you can do.

With the command "printenv", you can see the settings of U-Boot.

See also: [Default environment of U-Boot](Default_environment_of_U-Boot.md).
### Setup environments for flashing
```
setenv mainlineLinux yes
setenv arcNumber 2743
setenv ipaddr XXX.XXX.XXX.XXX   #This is the IP of your Stora
setenv serverip XXX.XXX.XXX.XXX #This is the IP of your TFTP server
saveenv
reset
```
### Load and start the installation
```
#Interrupt the U-Boot process.
tftpboot 0x200000 uImage.di
tftpboot 0x800000 uInitrd.di
setenv bootargs console=ttyS0,115200n8 base-installer/initramfs-tools/driver-policy=most
bootm 0x200000 0x800000
```
### Running the installer

Install Debian as normal.

You can ignore errors about:

- No kernel modules were found.
- Software RAID not available
- Logical Volume Manager not available
- No installable kernel was found

### Boot the system
```
#Interrupt the U-Boot process.
tftpboot 0x200000 uImage
tftpboot 0x800000 uInitrd
setenv bootargs $(console) root=/dev/sda1
bootm 0x200000 0x800000
```
You might have to replace `root=/dev/sda1` with `root=/dev/sda2` or `root=/dev/sda5`. In case you installed "/" on another partition. Remember, do not attach any hard-drives yet.

### Install the kernel
```
#Login to your new Debian
aptitude update
aptitude -y install uboot-mkimage initramfs-tools ntpdate
ntpdate-debian
wget http://dl.dropbox.com/u/1397036/stora_di/linux-headers-2.6.32-6-kirkwood_2.6.32-6-kirkwood-10.00.Custom_armel.deb
wget http://dl.dropbox.com/u/1397036/stora_di/linux-image-2.6.32-6-kirkwood_2.6.32-6-kirkwood-10.00.Custom_armel.deb
wget http://dl.dropbox.com/u/1397036/stora_di/2.6.32.mach.tgz
dpkg -i linux-*-2.6.32-6-kirkwood_*.deb
ln -s /usr/src/linux-headers-2.6.32-6-kirkwood/ /lib/modules/2.6.32-6-kirkwood/build
tar xzf 2.6.32.mach.tgz -C /usr/src/linux-headers-2.6.32-6-kirkwood/arch/arm/
```
**Note:** The modification done to this kernel can be found [here](https://github.com/kenchy/debian_squeeze_stora_kernel)

### Configure the kernel for booting
```
wget http://dl.dropbox.com/u/1397036/stora_di/stora-flash
chmod +x stora-flash
/root/stora-flash -i /boot/vmlinuz-2.6.32-6-kirkwood -r /boot/initrd.img-2.6.32-6-kirkwood
```
### Reading the UUID of your root partition

Right now your root partition is most likely /dev/sda1. If you attach one hard-drive and reboot. Then the root partition will become /dev/sdb1, and booting will fail. Therefor you need to use the UUID of the root partition instead.

This will tell the UUID of your root partition:
```
grep " / " /etc/fstab
```
Copy it. It will be used in the next steps.
### Test boot the new kernel
```
#Reboot the system and interrupt the U-Boot process.
setenv bootargs $(console) root=UUID=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX rootdelay=8
usb reset
ext2load usb 0 0x200000 /boot/uImage
ext2load usb 0 0x800000 /boot/uInitrd
bootm 0x200000 0x800000
```
See #Installing Kernel to NAND, if this does not work.

### Setup permanent boot

Assuming that the last step worked without problems, you can now setup the stora to always boot from the USB stick.
```
#Reboot the system and interrupt the U-Boot process.
setenv bootcmd_usb 'usb reset; ext2load usb 0 0x200000 /boot/uImage; ext2load usb 0 0x800000 /boot/uInitrd'
setenv bootcmd 'setenv bootargs $(console) root=UUID=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX rootdelay=8; run bootcmd_usb; bootm 0x200000 0x800000'
saveenv
reset
```

## Installing Kernel to NAND
# WARNING!!
This step is only necessary if you cannot load/boot the kernel from your USB stick. For example some USB-sticks makes "usb reset" halt. If you can boot your system now, you are done.

Take care when using the command "nand" in U-Boot. This command will brick your Stora, if you use an offset less that 0x100000. See "help nand".

If you follow this guide, you will destroy the original file system on the Stora. Which you will no longer need, since your filesystem is on the USB stick.

Since this is dangerous to do. The output of the commands are included in this part of the guide.

The purpose is to copy the kernel to these places on the Stora flash:
```
# uImage  - start:0x700000 size:0x300000 - Starts at  7MB, ends at 10MB, max 3MB large
# uInitrd - start:0xA00000 size:0x6F0000 - Starts at 10MB, ends at 17MB, max 7MB large

# The max size of uInitrd is set to 0x6F0000, rather than 0x700000,
# so you don't confuse the max size of uInitrd, and the start of uImage.
```
### Copy the kernel to TFTP
```
copy the file /boot/uImage and /boot/uInitrd from your Stora to your TFTP server.
```
### See bad blocks
```
Marvell>> nand bad

Device 0 bad blocks:
  00a40000 # <- This block is inside where we are going to store the kernel.
  052a0000
  0bfe0000
  0c020000
  0d240000
  0d3c0000
  0ef00000
```
On this example Stora there are quite a few bad block. On yours there are probably less.

As you see, there is a bad block inside where we are going to store the kernel. This is not a problem, because the Stora will skip this block when using the commands read.e and write.e.
### Copy uImage from TFTP server to RAM
```
Marvell>> tftpboot 0x200000 uImage-2.6.32-6
Using egiga0 device
TFTP from server 10.51.11.11; our IP address is 10.51.11.123
Filename 'uImage-2.6.32-6'.
Load address: 0x200000
Loading: #################################################################
         #################################################################
         #################################################################
         #################################################################
         ######################
done
Bytes transferred = 1438920 (15f4c8 hex)
```
### Erase NAND (DANGEROUS)
```
Marvell>> nand erase 0x700000 0x300000

NAND erase: device 0 offset 0x700000, size 0x300000
Erasing at 0x9e0000 -- 100% complete.
OK
```
### Copy uImage from RAM to NAND (DANGEROUS)
```
Marvell>> nand write.e 0x200000 0x700000 0x300000

NAND write: device 0 offset 0x700000, size 0x300000

Writing data at 0x9ff800 -- 100% complete.
 3145728 bytes written: OK

### Copy uInitrd from TFTP server to RAM
```
Marvell>> tftpboot 0x800000 uInitrd-2.6.32-6
Using egiga0 device
TFTP from server 10.51.11.11; our IP address is 10.51.11.123
Filename 'uInitrd-2.6.32-6'.
Load address: 0x800000
Loading: #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #########################
done
Bytes transferred = 5116874 (4e13ca hex)
```
### Erase NAND (DANGEROUS)
```
Marvell>> nand erase 0xA00000 0x6F0000

NAND erase: device 0 offset 0xa00000, size 0x6f0000
Skipping bad block at  0x00a40000
Erasing at 0x10e0000 -- 100% complete.
OK
```
### Copy uImage from RAM to NAND (DANGEROUS)
```
Marvell>> nand write.e 0x800000 0xA00000 0x6F0000

NAND write: device 0 offset 0xa00000, size 0x6f0000

Bad block at 0xa40000 in erase block from 0xa40000 will be skipped
Writing data at 0x110f800 -- 100% complete.
 7274496 bytes written: OK
```
As you see, the bad block at 0xa40000 was automatically skipped.
### Setup booting
```
setenv bootcmd_usb 'nand read.e 0x200000 0x700000 0x300000; nand read.e 0x800000 0xA00000 0x6F0000'
setenv bootcmd 'setenv bootargs $(console) root=UUID=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX rootdelay=12; run bootcmd_usb; bootm 0x200000 0x800000'
saveenv
```
To reduce the boot time, you can experiment with lowing 0x300000, 0x6F0000, and rootdelay=12.
### Reset

Power cycle your Stora to proper test this.