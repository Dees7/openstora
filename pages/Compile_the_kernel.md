# Compile the kernel

*Please note: this tutorial supposes you are using Ubuntu on your PC and Debian on the Stora Don't do this without access to the serial port*

This tutorial covers cross compiling. It is based on:[Kernel Compile Howto](Kernel_Compile_Howto.md) and [1](http://www.openstora.com/forum/viewtopic.php?f=6&t=217)

First of all you will need some tools on your host:
```
sudo apt-get install build-essential make git-core gitk
```

Download and install the toochain for GNU/Linux from [here](http://www.codesourcery.com)

Get the kernel from MacWire and extract the patches:
```
git clone git://gitorious.org/openstora/orion-netgear_ms2110.git
cd orion-netgear_ms2110
gitk
```
In gitk right click on the relevant commits and choose "Write version to file" Or get the patches from here:

**TODO: Add link to patches**

# Now get the kernel from www.kernel.org
```
git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-2.6.36.y.git
```
Put the patches in your kernels folder and apply the patches:
```
git apply patchfile
```
Some patches may not apply correctly. You will have to rework those parts manually.

Now get a readymade configuration file. You can eighter use one of MacWires config files from orion-netgear_ms2110 or get this one: **TODO: Link to config file**. Save the file under .config in the kernel folder.

## Configure the kernel:
```
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- kirkwood_defconfig
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- oldconfig
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- menuconfig
```
In menuconfig choose which modules you like and which you don't.

## Now compile the kernel:
```
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- EXTRAVERSION=-5-kirkwood KDEB_PKGVERSION=1.0.kirkwood KBUILD_DEBARCH=armel deb-pkg
```
You may encounter some errors. If you get errors in the patches you'll need to adapt them. Section mismatches, etc. may come from buggy drivers. You'll need to find them and disable them in menuconfig.

If you're done and have a readymade package copy it to the stora.

Get the stora flash utility:
```
wget http://dl.dropbox.com/u/1397036/stora_di/stora-flash
```
And install the kernel:

```
sudo dpkg -i kernel-package-name.deb
cd /boot
/path/to/stora-flash -i vmlinuz-kernel-name -r initrd.img-kernel-name
```

If the kernel doesn't work, Paul Kent has some instructions you may find usefull: [2](http://www.openstora.com/forum/viewtopic.php?f=6&t=217) Just pretend you have just installed debian and go on with his instructions on how to install the kernel.
