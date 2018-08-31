# Installing userland NFS (UNFSD)

**Important: we now have [Kernel mode NFS](Kernel_mode_NFS.md) which should be superior to this, recommend you try kernel NFS first!**

My first attempt to contribute to this wiki, i hope it's beginners proof ;)

## How to Install NFS using UNFSD on the Stora

Requirements:

- A Stora NAS of course.
- You should have an up-and-running IPKG package manager, if you don't follow these instructions: [Installing a package manager](Installing_a_package_manager.md).
- Know how to use a text-editor like "vi" or "nano" (you need to install it through IPKG if you haven't).
- A degree in Computer Science (just kidding).

## Step 1: Login

Login to your Stora using SSH (see this article if you don't know how to do it: Easy_Root_Access)

become root: // -E ensures that you set the Super user enviroment (just less typing of /opt/bin/....)
```
-bash-3.2$ sudo -E su 
```
just for the linux novices out there:

- bash-3.2`$` means normal user, chances are slim that you could wreck your system.
- bash-3.2`#` means ROOT/SUPERUSER/one mistake could kill your linux install

so: always work as normal user, for minor actions use sudo -E "the action you would like to do" eg: sudo -E nano /etc/exports

ensure your IPKG is setup correctly:
```
bash-3.2# ipkg list |grep nfs

you should see something like:
dstat - 0.7.0-1 - dstat is a versatile replacement for vmstat, iostat, netstat, nfsstat, and ifstat
nfs-server - 2.2beta47-4 - Minimal NFS server without TCP Wrappers (not recommended, use nfs-utils instead)
unfs3 - 0.9.18-1 - Version 3 NFS server (not recommended, use nfs-utils instead)
```

## Step 2: Install UNFSD and Portmap

now we are going to instal unfs and portmap
```
bash-3.2# ipkg install unfs3 portmap

it should install unfsd and portmap without issues.
```

## step 3: Configure UNFSD

Now it's time to edit/create the exports file. This file tells unfsd what to share, with what rules
```
touch /etc/exports
nano /etc/exports
```
You should be presented with an empty file:

you should insert the following line, but edit it for your situation:

```
/home/vincent 172.19.3.0/24(rw,anonuid=501,anongid=503,all_squash)
```

The first bit `/home/vincent` is the starting point which is shared. 172.19.3.0/24 means share to all pc's. 172.19.3.1 till 172.19.3.254 (NOT so safe on a business lan, but for home it's ok) (rw,anonuid=501,anongid=503,all_squash) rw means READ_AND_WRITE you could replace it with "ro" READONLY, the 501 and 503 are id's wish correspond to the user UNFSD emulates

if you like to find yours:
```
cat /etc/passwd

the last lines should look like:
ldap:!!:55:55:LDAP User:/var/lib/ldap:/bin/false
mailnull:!!:47:47::/var/spool/mqueue:/sbin/nologin
smmsp:!!:51:51::/var/spool/mqueue:/sbin/nologin
ComputersBackup:x:500:502::/home/0backups:/sbin/nologin
vincent:x:501:503::/home/vincent:/bin/bash
alexandra:x:502:504::/home/alexandra:/bin/bash
```

As I (`vincent`) like to connect to the Stora as user vincent I used my userid 501 and group id 503, Should I like to connect as 'Alexandra' I'd use this line (rw,anonuid=*502*,anongid=*504*,all_squash)

## Step 4: Fireing up UNFSD and PORTMAP for the first time

firup PORTMAP:
```
bash-3.2# portmap
```
Second we need to fireup UNFSD:
```
bash-3.2# unfsd

if you receive no errors it's fine you can proceed to a client system/device to connect to your nfs

if you like to see the options for unfsd:
bash-3.2# unfsd -h
UNFS3 unfsd 0.9.18 (C) 2006, Pascal Schmidt <unfs3-server@ewetel.net>
Usage: unfsd [options]
        -h          display this short option summary
        -u          use unprivileged port for services
        -d          do not detach from terminal
        -e <file>   file to use instead of /etc/exports
        -i <file>   write daemon pid to given file
        -n <port>   port to use for NFS service
        -m <port>   port to use for MOUNT service
        -t          TCP only, do not listen on UDP ports
        -p          do not register with the portmapper
        -s          single user mode
        -b          enable brute force file searching
        -l <addr>   bind to interface with specified address
        -r          report unreadable executables as readable
        -T          test exports file and exit
```

if you get this message:
```
bash-3.2# unfsd
bind: Address already in use
Couldn't bind to tcp port 2049
```
it's probably already running. Or another nfs service is active. you can (try) to find it using this command:
```
bash-3.2# ps aux|grep unfs
vincent    962  0.1  4.0   6700  5052 ?        Ss   Aug25   4:32 unfsd
root      6904  0.0  0.4   1864   580 pts/0    R+   14:00   0:00 grep unfs
```
as you can see it's running with PID 962. To kill the process you can use 
```
bash-3.2# kill -9 962
```
then try again

after your nfsd is running (to check: ps aux|grep unfsd). You should EXIT Superuser mode:
```
bash-3.2# exit
exit
-bash-3.2$
```
Please remind it's a GOOD habit to work as regular user on unix/linux systems.

## Step 5: Connecting with a client

on a PC/Media player connect to your Stora ip eg 172.19.3.99
```
bash-3.2# mount 172.19.3.99:/home/vincent /mnt/MY_FIRST_UNFSD_MOUNT

the 172.19.4.99 is ip of the Stora
the : is mandatory
the /home/vincent is EXACTLY the same as the first part in your /etc/exports
the /mnt/MY_FIRST_UNFSD_MOUNT is a (empty) folder in which you like to mount the share
```
to release the connection to your Stora you can use:
```
bash-3.2# unmount /mnt/MY_FIRST_UNFSD_MOUNT
```

## Step 6: AutoStart unfsd and portmap

To make unfs and pormap work at boot you could use a method described below:

edit /etc/rc.local and add the following lines at the end of it:
```
/opt/etc/init.d/S55portmap

/opt/etc/init.d/S56unfsd
```
## Comments

Just a quick write up. If you encouter errors, feel free to correct them

Good luck and kind regards

Vincent Gijsen The Netherlands.

Thanks pippone for the Portmap additions, forgot all about it ;)

ORIGINAL FORUM POST (to discuss problems/errors): [here](http://www.hardwarehackersunite.com/forum/topic?id=212)

I use this NFS as my video storage for my MythTV frontends. I had to disable the drive spin down as instructed [here](http://hardwarehackersunite.com/wiki/index.php?title=Modifying_spin-down_settings) to prevent the frontends from locking due to the Strora spinning the drives down.