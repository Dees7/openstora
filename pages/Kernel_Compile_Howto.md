# Kernel Compile Howto

You need to have the codesourcery toolchain to do this. (PLEASE SOMEONE EXPLAIN HOWOTO INSTALL THE TOOLCHAIN)

!!!!!!!!!! Do not bubt this without confirmed JTAG access !!!!!!!!!!
## Compiling Uboot

```
$ git clone git://gitorious.org/openstora/u-boot-marvell-ms2110.git
$ cd u-boot-marvell-ms2110
$ make mrproper
$ make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- netgear_ms2110_config
$ make -j8 ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi-  u-boot.kwb
```

## Compiling Kernel

First make sure you have the mkimage utility, from the u-boot you compiled above, somewhere in your path along with the codesourcery toolchain.
```
$ git clone git://gitorious.org/openstora/orion-netgear_ms2110.git
$ cd orion-netgear_ms2110
$ make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- kirkwood_defconfig
$ make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- menuconfig
```
Pick out the drivers you want compiled into your kernel. You'll certainly want to make sure that the only RTC driver you have is Philips PCF8563/Epson RTC8564. You should disable the hwmon drivers except for LM75. You'll also want to enable RAID, XFS, and stuff like that. Customize the kernel however you like. Then:
```
$ make -j8 ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- uImage
$ cp arch/arm/boot/uImage /tftpboot/
$ make -j8 ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- modules
$ make -j8 ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- modules_install INSTALL_MOD_PATH=/mnt/Axentra/marvell.nfs.rootfs
```
That's pretty much it! It doesn't have kernel fan support or rtc alarms, but that stuff can come later. It's not as clean as it can be, but it's definitely something you can play around with.

I added a file, stora.config which is a working .config to get you started in case menuconfig is a little too overwhelming.

Following my own instructions from scratch:
```
-bash-3.2# uname -a
Linux fedora-arm 2.6.34-rc2-g03210d8 #1 PREEMPT Wed Mar 24 00:35:03 PDT 2010 armv5tel armv5tel armv5tel GNU/Linux
-bash-3.2#
```
ORIGINAL POST: http://www.hardwarehackersunite.com/index.php?option=com_ccboard&view=postlist&forum=1&topic=62&Itemid=53