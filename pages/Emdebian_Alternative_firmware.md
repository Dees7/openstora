# Emdebian Alternative firmware

This guide shows the procedure to create your rootfs from emdebian distro, There is little Stora specific.

## Building the bootstrap image

The image can be build on other architecture like a i386 or AMD64 Debian Desktop.
```
debootstrap --arch=armel --foreign squeeze grip/ http://www.emdebian.org/grip
```
Make a a package with the files you have created,
```
tar cvf emdebian-squeeze.tar grip/
```
Now copy the file emdebian-squeeze.tar to a webserver or a FAT16 USB stick.

## Deploying the bootstrap image

[Login the the Store NAS](Easy_Root_Access.md)

Become root:
```
sudo -s -E
```
Go to a dir that has more than 200MB free space:
```
cd /home/
```
Transfer the tar file to the Store NAS:
```
wget http://kom.aau.dk/~pmr/emdebian-squeeze.tar #Replace with your own file, if you want.
```
Extract the file:
```
tar xvf emdebian-squeeze.tar
```
chroot to the extracted dir:
```
/usr/sbin/chroot grip/
```
**Note:** If you do not have the command chroot, try [installing a package manager](Installing_a_package_manager.md).

Create missing dir:
```
mkdir -p /usr/share/man/man1
```
Start the bootstrap process:
```
debootstrap/debootstrap --second-stage -verbose
```
The bootstrap process will take some time. You will get this message when it is done:
```
I: Base system installed successfully.
```
## Configuring the installation

### Root password

Set the root password:
```
passwd
```
### Network configuration

Create the file: /etc/network/interfaces
```
# Loopback
auto lo
iface lo inet loopback

# Ethernet
auto eth0
iface eth0 inet static
        address 192.168.1.93
        netmask 255.255.255.0
        gateway 192.168.1.1
        dns-nameservers 8.8.8.8 8.8.4.4
        hwaddress ether 02:12:34:56:62:40 #Replace with your Stora's MAC address.
```
**Note:** You have to modify the file to your network setup. Use the MAC address that is written on a sticker on the back of your Stora.

Modify /etc/resolv.conf
```
nameserver 8.8.8.8
nameserver 8.8.4.4
```
### Set time zone

Configure the time zone:
```
dpkg-reconfigure tzdata
```
### Setup fstab

Create the /etc/fstab

```
#
# /etc/fstab: static file system information.
#
# file system   mount       type    options           dump    pass
#/dev/sda1       /           ext3    defaults          1       1
proc            /proc       proc    defaults          0       0
devpts          /dev/pts    devpts  gid=5,mode=620    0       0
sysfs           /sys        sysfs   defaults          0       0
#/dev/sda3   /home   ext3    defaults,bsdgroups,noatime,nodiratime    0   0
##/dev/md0   /home   xfs    defaults,bsdgroups,noatime,nodiratime    0   0

## other examples, uncomment to activate
#/dev/sda2      swap        swap    defaults          0       0
nodev           /var/log tmpfs
##nodev                 /mnt/tmpfs tmpfs
ubi0:rootfs / ubifs defaults 1 1
```
And mount /dev/pts:
```
mount /dev/pts
```
### Setup APT repository

Create the file: /etc/apt/sources.list
```
deb http://www.emdebian.org/grip/ squeeze main
##deb http://ftp.gva.es/mirror/debian/ squeeze main contrib non-free
deb-src http://www.emdebian.org/grip/ squeeze main
```
Install SSH

We need to install a ssh server. Here we choose dropbear, because it is lighter than openssh.
```
aptitude update
aptitude install dropbear
```
This will give an error because 22 port is in use.

Edit the file /etc/default/dropbear, and change port 22 to port 2222, and then retry:
```
aptitude install dropbear
```
Reedit /etc/default/dropbear, and change the port back to 22.

Link the dropbear executable to the standart name of ssh. This will be useful for programs like rsync:
```
ln -s /usr/bin/dbclient /usr/local/bin/ssh
```
Other useful packages

aptitude install rsync less sudo host

### Install the rootfs

With this filesystem prepared you can follow the MacWire procedure, and install this rootfs onto the stora. :-) To install this rootfs you have to follow this guide [1](How_to_install_your_custom_firmware.md) using the rootfs filesystem created.

The built rootfilesystem is uploaded to HHU repository.