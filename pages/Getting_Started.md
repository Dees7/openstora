# Getting Started

#This guide explains how to begin hacking a Stora, and also what order to do things in.

Note that whenever I refer to the "WebUI" I am talking about the flash based interface that you log into by typing your Stora's IP into your web browser. Everything else is done over SSH.


## Initial Setup

- Unpack and connect your Stora, log in to WebUI with your web browser, and register your first user account online. You can decline the premium subscription option ;)
- Apply any firmware updates (check [Manual firmware update](Manual_firmware_update_and_info.md) and info first to make sure current firmware version is known safe for hacking - we recommend v2.x which appears as hbs-5032 or hbs-5033, because that version (and probably later ones too) gives you the [Stora Recovery](Stora_Recovery.md) option if you mess up!). Reboot the Stora and log back in.
- Go through the WebUI and set everything up as you want it - more options have been added in newer firmware. Don't worry about anything you can't set in there, we'll get to that later.
- Now it's time for [Easy Root Access](Easy_Root_Access.md)

## Preparing to install software and checking free space

You're going to need to edit lots of config (plain text) files when setting up your Stora. If you're used to graphical text editors like Notepad then you will find Nano (not installed by default) much easier to use than than vi (included on the Stora). Read [Installing a package manager](Installing_a_package_manager.md), but **skip the part about moving /opt to your hard disk** for now.

- Install optware - IPKG is a package manager (a bit like Synaptic on Ubuntu) which makes installation of lots of other software on the Stora easy
- install Nano using IPKG - Nano is an alternative command line text editor
- fix your PATH - this will save lots of typing later; you can then type most commands without having to enter the full path every time.

The above points are all explained under [Installing a package manager](Installing_a_package_manager.md), but I repeat DO NOT link /opt to your hard disk yet because you'll be in trouble if/when you change hard disk setup. and it's not needed for most users.

```
df -t rootfs -h
```

in English the above line means: "display free space, in the root filesytem, and make it human readable"
It's a good idea to keep an eye on this - completely filling it could brick your Stora! (there is normally about 60MB flashram free when the Stora is new).
## Netgear bugs/workarounds

- Your hard drive(s) might last longer and you'll get fewer long waits if you raise the timeout, see [Modifying spin-down_settings](Modifying_spin-down_settings.md). What's best depends on your network usage , I like 120 (minutes = 2 hours).
- If you get "unable to load resource module" errors opening the Web UI, try changing the language and then back again (if English, change to French or something then back to English).
- The Stora doesn't have WOL (Wake-on-Lan) or automatic power on, so what happens after a power failure? It stays off. It does least have a clock and battery so one thing you can do is to enable Power On events in the web interface (there is no need to set an Off event, unless you want to). It isn't ideal but least your Stora won't stay off for more than 12/24 hours... be sure to select and save your region (even if your region is the default one, press Submit on it) or they won't work! (another annoying Netgear bug).
- [Disable mystora.com redirection](Disable_mystora.com_redirection.md) - a good idea to make sure you can access your Stora's WebUI even if your net connection is down (not really needed on firmware 2.3)

You can see Netgear's firmware changelogs and Known Issues under [Manual firmware update and info](Manual_firmware_update_and_info.md)

## Improving security - Making your Stora your own

- [sshd substitution](Sshd_substitution.md) to make login easier (and remove backdoors?). Follow the "easy way"!
- [Disabling updates and external access](Disabling_updates_and_external_access.md) to improve security and reliability
- [Disable the Access Patrol service](Disable_the_Access_Patrol.md) allows you to remove various limitations, this also covers fixing the DNS servers.

## Choose hard drive configuration

Since changing the hard drive array type requires a backup and restore of everything in `/home`, it's a good idea to decide on an array type before you start filling the Stora, **even if you only have one drive now**! All major array types are supported (officially RAID1 and JBOD, unofficially RAID0 or separate drive mounts) see the wiki main page for details.

## Set up user accounts

You can do this through the WebUI (read [Changing User Limit](Changing_User_Limit.md)), or with the console (all 3 of these command are needed for the account to work):
```
adduser <username>
passwd <username>
smbpasswd -a <username>
```
Note that users added this way won't show in the Web UI, but otherwise can work perfectly.
It is important that you understand linux file permissions otherwise you will likely run into problems, sometimes obvious Access Denied errors e.g. when trying to access a share over SAMBA, sometimes permission errors are more difficult to track down, so you need a working knowledge of at least ls -l, chmod, and chown

- [Ubuntu's introduction to file permissions](https://help.ubuntu.com/community/FilePermissions), covers the basics!

## Install software

Where you go from here depends what you want the Stora to do, some popular things are:

- [Kernel mode NFS](Kernel_mode_NFS.md) (better file serving protocol than SMB for Linux systems and some HTPCs)
- [Privoxy web proxy server](Privoxy_web_proxy_server.md) (powerful tool for filtering websites)
- [Transmission torrent client](Transmission_torrent_client.md) (use your Stora to handle torrents 24/7)

See the wiki main page for many other common solutions. Use "df" to check free space on your flashram, don't fill this!
If you need to install lots of packages you should move some dirs e.g. /opt to your hard drive, this is explained on [Installing a package manager](Installing_a_package_manager.md)


If anyone has any other important suggestions for new users please add here!
--Smiff