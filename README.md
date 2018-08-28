## info

The Information in this wiki is not only for **Netgear Stora**. It's useful for all NAS which use **HipServ OS** from Axentra. e.g.:

- Seagate GoFlex Home (Marvell "Kirkwood" 88F6281 at 1.2 GHz, 128 MB soldered RAM (Nanya NT5TU64M16DG), 512 MB flash, 10/100/1000 Marvell 88E1116R Gigabit Ethernet)
- LaCie Ethernet Disk mini – Home Edition
- Verbatim MediaShare (1TB/2TB/mini)
- [Raidsonic IcyBox IB-NAS6210](pages/Raidsonic_IcyBox_IB-NAS6210.md) (Same as Stora, but double RAM, 1 drivebay)
- Medion Lifecloud 2-bay NAS

## FAQ

### Why choose the Stora?

Stora is sold as a cheap home NAS, with mixed reviews. What makes it interesting to us is that the hardware is one of the best in its price range (1Ghz, 2 drive bays etc.) and because it runs (open source) Red Hat Linux; with some time and this wiki you can fix some of these limitations and bugs, and make it do a lot of things that it can't do "out of the box".

### Do I need an internet connection to use the Stora?

1. If you want [Easy Root Access](pages/Easy_Root_Access.md) you need to register the first user account online before you can SSH in (telnet or tftp methods below can be used without registration)
2. If you ever need to do [Stora Recovery](pages/Stora_Recovery.md)
and obviously 3) if you use any of the Stora's optional online services (Flickr etc. integration)!
Basically: No, in normal use the Stora is capable of authenticating you on its own, even though by default it redirects you through mystora.com. To fix this see [Disable mystora.com redirection](pages/Disable_mystora.com_redirection.md). Then you will be able login to the web interface without internet access or if the web site is down.

### Can I have more than 3 users (5 users with newer firmware)? What's the user limit?

Yes, you need [Easy Root Access](pages/Easy_Root_Access.md), then read [Changing User Limit](pages/Changing_User_Limit.md). There is then no hard limit (128MB RAM is more likely to become an issue than the 1Ghz CPU). You can also add users via the command line with the normal RedHat linux tools (Users added this way will not appear in web interface).

### Are there any limitations which can't be solved/worked around?

You can do almost anything you would expect a NAS to do even with the stock (original) firmware, but you will need to learn some linux CLI (command line interface) tools because we don't have a GUI (graphical) interface replacement that covers all the possible new options. For a good collection of suitable ARM binaries, [see](http://ipkg.nslu2-linux.org/feeds/optware/cs08q1armel/cross/stable)

### What's the performance like on the Stora?

Real world speeds seem to be in the 20MB-30MB/s range for reads and writes over gigabit networks (copying a single large file). This depends on factors such as hard drive speed, network, system load, file sizes etc. Jumbo frames do not appear to be supported so this may be a limiting factor.

### Can I upgrade hardware on the Stora?

Hard drive(s) are very easy to change, both physically (just slide off front panel and pull lever) and because Stora doesn't use hard drive for its OS (256MB flashram). Almost any SATA 3.5" drive should work including 4k advanced sector 2TB+ drives. Usually it's just a case of swapping the drive and rebooting, then formatting the new drive (web frontend or with root commands). By default only /home and /tmp are mounted to the hard drive, but take care if you have linked any system directories.
RAM is not upgradeable. There is 128MB on board.
You can [add an internal USB Hub](Adding_an_Internal_USB_Hub.md) for more USB devices.

### RAID0/JBOD support, other disk configurations?

By default the Stora will create a RAID1 mirror (even if you only insert 1 drive; it creates a degraded mirror, waits for another drive of equal or greater capacity). JBOD support was added in firmware 2.x. It's also possible via root access to [create a RAID0 array](http://www.openstora.com/forum/viewtopic.php?f=1&t=181) or just [mount the drives separately](http://www.openstora.com/forum/viewtopic.php?f=1&t=399). There are some bugs you may need to fix though, e.g. Fixing spindown not working on JBOD setup. Be sure to backup all your data in /home before changing drive configuration.

### Is it safe to update my firmware after applying hacks from this wiki?

We have confirmed up to version 2.3.3 (hbs-5033.upd) should be safe i.e. nothing appears to be lost/uninstalled/blocked by Netgear/Axentra. Check your current firmware 'VersionName' by typing 'more /etc/oe-release'. Some configuration files will be overwritten if you update so first make a backup of your /etc folder e.g. 'cp -p -r /etc /home/backups/etc'. Log in via the web interface, click check for updates, click download, then click apply. Firmware v2.3 or later is a recommended update as it will give you an important recovery mechanism if you ever damage your Stora system files, discussed [here](http://www.openstora.com/forum/viewtopic.php?f=1&t=221&p=2795#p2795)

### It seems my Stora won't mount any USB drives attached to it...

First of all you must know that Stora mounts your USB attached hard drive inside the FamilyLibrary, it creates a new directory named with the label of the inserted disk, also, you need to have at least one SATA drive attached inside the Stora for the USB mount to work.

### Help, I've messed up and my Stora won't boot / I can't login!

You may not have bricked it, read [Stora Recovery](pages/Stora_Recovery.md) procedure. When using root access, remember this: If you think you've done something seriously wrong, do not reboot! Try to back out the change, if unsure ask on the forum. The nature of a NAS (no display, no local access) means some errors that would be easily fixable on a normal PC will require a complete system reinstall.

### Can I ask for help?

Yes we have a forum, but please first use Netgear's forum for original hardware/software issues or any linux site for general linux help (e.g. samba, user management, as these are all standard red hat linux). Only questions about hacking the Stora on this site please!

### Why do I get 'command not found' even though something is installed?

Probably because it's installed somewhere that is not on your PATH. Easy fix is do just do:

   bash-3.2# echo "PATH=/usr/local/bin:/usr/bin:/bin:/opt/bin:/usr/sbin:/opt/sbin:/usr/sbin:/sbin" > /etc/environment

And then restart your Stora. This means you can type 'ipkg' instead of '/opt/bin/ipkg'. Easy huh?

### Why do I get 'audit_log_user_command(): Connection refused' all the time?

You can ignore these messages, it's due to the way the kernel was compiled (without audit support), it's just the 'sudo' command complaining that it can't record the event, the command will still have run. You should pay attention to other messages, just not this one!

## Accessing Root

Having root access to your device is essential if you want to do anything beyond the provided WebUI:

- [Easy Root Access](Easy_Root_Access.md) (recommended for most users - no hardware tricks involved, but must register the first user account online!)

The following methods are much more difficult, so only recommended for advanced users or where no internet connection possible.

- [Root Access Via Serial Console]
- [Root Access Via TFTP]
- [Root Access via TelnetEnabled]

## Configuration

* [Getting Started - the first steps for most users]
* [Disabling updates and external access]
* [Enabling secure FTP]
* [Enabling the Root Account]
* [Create administrative Web user from shell command line]
* [Create a Share with Guest Access]
* [Disabling UPnP]
* [Modify Stora Firewall]
* [Disable mystora.com redirection]
* [Enable system logging]
* [Disabling Access Patrol]
* [Modifying spin-down settings]
* [Changing User Limit]
* [Changing hostname]
* [Fixing smartmontools]
* [Fixing spindown not working on JBOD setup]
* [Fixing FamilyLibrary share on Mac (AppleTalk)]
* [Manual firmware update and info]
* [USB Serial Adapter]
* [Installing Mediatomb]
* [RSyncBackup]
* [Power scheduling (advanced/non WebUI) (includes instructions for automatically restarting after a power failure)]
* [Services overview]
* [Make your Stora responsive even while its downloading]
* [Configuring SSMTP for Gmail]
* [Mount Stora as NFS on Linux]
* [Configuring 3TB HD]

## Recovery

* [Stora Recovery] (firmware reinstall procedure, 'unbricking')
* [Seagate GoFlex Home Recovery] (firmware reinstall procedure, 'unbricking')
* [Harddrive Recovery]
* [Harddrive Recovery / Transfer to New Drive in Stora]

##  Software addons for the original Stora firmware (stock firmware)

* [sshd substitution]
* [Transmission torrent client]
* [Unrar for Stora]
* [aMule for Stora]
* [PlowShare for Stora]
* [pyLoad]
* [Installing a package manager]
* [Installing Screen (Manager shell sessions)]
* [Kernel mode NFS]
* [Installing userland NFS (UNFSD)]
* [Installing a Proxy Server]
* [Installing iSCSI Enterprise Target]
* [Installing Squeezebox Server]
* [Installing inadyn]
* [All-In-One Installer Script]
* [miniDLNA for Stora]
  * [Updating miniDLNA]
  * [Troubleshooting media formats]
* [ Hamachi for Stora]
* [Installing Sick Beard]
* [Installing Couch Potato]
* [Extract-xiso]
* [Stora as OpenVPN server]

## Projects

* [How to install Debian Linux on NETGEAR Stora]
* [How to install your custom firmware]
* [EasyHackStoraLiveCd]
* [Emdebian Alternative firmware]
* [Using A Serial Connection]
* [Adding an Internal USB Hub]


## Software addons for other custom firmwares (aka emDebian, etc...)

* [Installing MiniDLNA]
* [Installing Webmin]
* [Prevent unclean filesystems from blocking the boot process]
* [Use the stora as a printserver]
* [Use the stora as a soundproxy]
* [Compile the kernel]

## Technical Information

* [Jtag Pinout]
* [Parts List]
* [Boot sequence information]
* [PCB Photos]
* [Test Points on Stora PCB]
* [Source Code]
* [Development HOWTOS]
* [To Do Index]


```    _    _               _    
   | |  | |             | |                       
   | |__| | __ _ _ __ __| |_      ____ _ _ __ ___ 
   |  __  |/ _` | '__/ _` \ \ /\ / / _` | '__/ _ \
   | |  | | (_| | | | (_| |\ V  V / (_| | | |  __/
   |_|  |_|\__,_|_|  \__,_| \_/\_/ \__,_|_|  \___|
                                                 
                                                 
    _    _            _                   _    _       _ _       
   | |  | |          | |                 | |  | |     (_) |      
   | |__| | __ _  ___| | _____ _ __ ___  | |  | |_ __  _| |_ ___ 
   |  __  |/ _` |/ __| |/ / _ \ '__/ __| | |  | | '_ \| | __/ _ \
   | |  | | (_| | (__|   <  __/ |  \__ \ | |__| | | | | | ||  __/
   |_|  |_|\__,_|\___|_|\_\___|_|  |___/  \____/|_| |_|_|\__\___|
                                                                
    ** MARVELL BOARD: RD-88F6281A LE
       
   U-Boot 1.1.4 (Jul 31 2009 - 10:33:00) Marvell version: 3.4.14
       
   U-Boot code: 00600000 -> 0067FFF0  BSS: -> 006CEE60
       
   Soc: 88F6281 A0 (DDR2)
   CPU running @ 1000Mhz L2 running @ 333Mhz
   SysClock = 333Mhz , TClock = 200Mhz 
       
   DRAM CAS Latency = 5 tRP = 5 tRAS = 18 tRCD=6
   DRAM CS[0] base 0x00000000   size  64MB
   DRAM CS[1] base 0x04000000   size  64MB
   DRAM Total size 128MB  16bit width
   Flash:  0 kB
   Addresses 8M - 0M are saved for the U-Boot usage.
   Mem malloc Initialization (8M - 7M): Done
   NAND:256 MB 
       
   CPU : Marvell Feroceon (Rev 1) 
       
   Streaming disabled
   Write allocate disabled 
       
   Module 0 is RGMII
   Module 1 is TDM 
       
   USB 0: host mode
   PEX 0: interface detected no Link.
   Net:   egiga0, egiga1 [PRIME]
   Hit any key to stop autoboot:  0
   Marvell>>

