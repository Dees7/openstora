# Root Access Via TFTP

### YOU ALMOST CERTAINLY DO NOT WANT TO DO THIS

**See [Easy Root Access](Easy_Root_Access.md) for a much saner way to gain root access.** You only really need to use this TFTP method to gain root access if you have previously gained root control and then locked yourself out of the system by, for example, corrupting the /etc/sudoers file. (Stop laughing at the back.)
## How to gain root access on your Netgear Stora

Author: Bryan McGuire
Edited by: Nathan Willard, Dustin Essington

The Netgear Stora, out of the box, runs an ssh server. The password, however, is disabled. The only way to log into the Stora is as root, and there is absolutely no password you can enter for root that will work.

What you will do in this hack is add a public key for ssh onto your stora, and get a root shell without needing a password.
## Required Items

- A pc running linux (redhat, debian, gentoo, ubuntu, doesn't matter) with an unused ethernet interface. (OS X will have issues with the NFS export/tar archive. It can't be used.) Edit: aetaric (not needed: with an unused ethernet interface) Edit: yopnono
- A Netgear Stora
- A Ethernet switch preferably:a gigabit Ethernet switch -- NFS root filesystems suck hard, and are barely usable with anything less. (but it works) Edit: yopnono
- A couple of ethernet cables to hook the above three items together.

Alternatively you could use:

- Crossover cables (you don't even need a crossover cable, Stora is autosensing so a regular cable can be used to hook-up the Stora directly to the laptop with Gigabit connection!) Edit: pippone

## Installing Support Software

You now have to install the required software on your linux machine, if you haven't already. Using apt-get, emerge, yum, or whatever, install the following:

- nfs server (for me, it's called "nfs-kernel-server" )
- tftpd
- wireshark (formerly called ethereal -- if you know how to use snort you can use that instead.

Set up your ethernet interface with the ip of 10.0.0.1 and a netmask of 255.0.0.0, its default route (gateway) needs to be the interface address itself, 10.0.0.1

turn on ip masquerading: (not needed) Edit: yopnono
```
echo "1" > /proc/sys/net/ipv4/ip_forward
echo "1" > /proc/sys/net/ipv4/ip_dynaddr
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```
**Note:** eth0 is interface used for your linux box's internet connection, and not eth1, which is used by the 10.x.x.x network for the Stora)

## Obtain Alternate File system and Kernel

If you haven't yet downloaded the files you'll need, do so from [here](http://www.openstora.com/files/albums/uploads/alternates_tar.bz2).

Make sure you don't have anything under `/mnt` cause extracting the archive will eventually overwrite that directory

Make sure you are **root**

Move the alternates.tar.bz2 from wherever you downloaded it to the root directory of your linux machine:
```
mv alternates.tar.bz2 /alternates.tar.bz2
```
Extract these files on to your Linux machine:
```
cd /
tar xvjf alternates.tar.bz2
```

configure your nfs server by adding the following in the /etc/exports file:
```
/mnt/Axentra/marvell.nfs.rootfs/ 10.0.0.0/8(rw,no_root_squash,sync,no_subtree_check)
```
rehash the nfs-server:
```
service nfs-kernel-server restart
```
in `/etc/inetd.conf`, enable your tftpd server by adding the following line:
```
tftp        dgram    udp    wait    nobody    /usr/sbin/tcpd    /usr/sbin/in.tftpd /tftpboot
```
rehash the intetd daemon:
```
killall -HUP inetd
```
**Note:** some linux distros (ubuntu 9.10) don't use inetd anymore, they're using xinetd, you have three options: configure tftpd with xinetd, replace xinetd with inetd or install a daemon version of tftpd. Here's how to install the daemon version "tftpd-hpa" (recommended for Ubuntu 9.10):
```
apt-get install tftpd-hpa
```
Change this file and set the following options:
```
vi /etc/default/tftpd-hpa
#Defaults for tftpd-hpa
RUN_DAEMON="yes"
OPTIONS="-l -s /tftpboot"
```
Restart the daemon
```
sudo /etc/init.d/tftpd-hpa restart
```
**Note:** tftpd-hpa seems to be a bit on the buggy side and if you get an 'Error1: File not found error' then it's highly likely that the daemon has ignored your preference for the root directory and is looking its default directory which is /var/lib/tftpboot. Putting the uImage file in there will likely solve your problem.

## Starting the Magic

Start up wireshark (you may have to be root to do this) and start listening to eth1

While holding down the small reset button on the stora with a paper clip (or equivalent instrument), turn on the stora. You'll need to hold in the reset button for quite some time.

The lights on the front panel will go through three cycles. Eventually, you will see the tftp transfer in wireshark, and at this point you can release the reset button.

After the tftp transfer, there will be a couple of arp requests, and then you should see the nfs activity. Your linux drive will start working like crazy as your Stora boots with the nfs mounted alternate filesystem.

Once the Stora is booted, you will be able to log into it.

The ip address you need to use will be in wireshark, It's the `10.x.x.x` address that's not 10.0.0.1.
```
ssh root@10.x.x.x 
```
**Note:** x denotes the ip of the Stora found in wireshark.

The password will be **root**

Mount the Stora's regular root filesystem, by running the following commands while logged into the Stora:
```
mkdir -p /mnt/ubifs
cd /lib/modules/2.6.22.18-Netgear
modprobe ubi mtd=2,2048
modprobe ubifs
mount -t ubifs ubi0:rootfs /mnt/ubifs
```
The Stora's regular file system is now mounted on `/mnt/ubifs`

Add your public ssh key into `/mnt/ubifs/root/.ssh/authorized_keys` and type
```
shutdown -h now
```
the stora will then shutdown and turn itself off.

Now you can move the Stora back to your regular network and ssh into it as root using your private ssh key.

## Disclaimers

I did all this using ubuntu 9.10 running on a Mac Pro, and the config lines for nfs, tftpd, and such are what work on my machine with my Stora. You might have to use something else for your particular distro and/or particular unit. I'm detailing what finally worked for me. This might not work for you. You may end up bricking your Stora, and if you do, don't blame me. I don't work for Netgear, HipServ, or anyone related to the sale, development, or distribution of this device. I'm just someone who bought one at Best Buy, loved the hardware, but was thoroughly unhappy with its firmware. 
