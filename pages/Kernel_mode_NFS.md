# Kernel mode NFS

This is a (hopefully better) alternative to [Installing userland NFS UNFSD](Installing_userland_NFS_UNFSD.md) on the Stora!

Aug 2011 update: thanks to missing script by Michael pleased to confirm file locking appears to be working! (mounts work without the nolock option). The packages have been updated. credit to Michael for discovering the issue

## Preparation

Get [Easy Root Access](Easy_Root_Access.md), install Nano and fix your PATH (see under [Installing a package manager](Installing_a_package_manager.md))
Note that all the commands on this page are run as root so begin your SSH sessions with `sudo -E -s` and make sure you have the `bash-3.2#` prompt.

- For the **Stora** Download the NFS package from [here](http://www.openstora.com/files/albums/userpics/10221/nfs-utils_STORA_tar.gz), rename the file to "nfs-utils_STORA.tar.gz".
- Check that your Stora is running the correct kernel for these NFS modules:
```
-bash-3.2# uname -a
Linux axentraserver.xxxx.mystora.com 2.6.22.18-Netgear #16 Sun Jun 13 19:54:46 EDT 2010 armv5tejl armv5tejl armv5tejl GNU/Linux
```
At the time of writing this NFS package by Pipponne is built for kernel v2.6.22.18 (tested under Netgear firmware v1.2 and v2.3)

- For the **GoFlex Home** Download the NFS package from [here](http://www.openstora.com/files/albums/userpics/12676/nfs-utils_GFH_tar.gz), rename the file to "nfs-utils_GFH.tar.gz".
- Check that your GoFlex Home is running the correct kernel for these NFS modules:
```
-bash-3.2# uname -a
Linux axentraserver.XXXX.seagateshare.com 2.6.22.18 #16 Thu Jun 17 01:37:53 EDT 2010 armv5tejl armv5tejl armv5tejl GNU/Linux
```

At the time of writing this, NFS kernel modules are built for kernel v2.6.22.18 (tested under Seagate firmware v2.5.1). The rest of the package was built by Pipponne for the Stora.

- For **Stora** and **GoFlex Home** drop the NFS package onto a share on your Stora or GoFlex Home and copy it to the root of the filesystem (this is to make sure everything extracts into the correct paths).

e.g. if you drag and drop the package to the FamilyShare on Stora:
```
cd /home/0common
cp nfs-utils_STORA.tar.gz /
```
e.g. if you drag and drop the package to the GoFlex Home Public on GoFlex Home:
```
cd /home/0common
cp nfs-utils_GFH.tar.gz /
```

## Setting up the NFS kernel modules

With the nfs-utils_STORA.tar.gz package in / the root of your Stora as explained above and still running as root user:
```
cd /
tar xvfz nfs-utils_STORA.tar.gz
```
For the GoFlex Home the file is named nfs-utils_GFH.tar.gz so you have to run
```
cd /
tar xvfz nfs-utils_GFH.tar.gz
```
You will see a whole list of files extracted.

Refresh and load the new modules:
```
depmod -a
modprobe nfsd
```
now check the loaded modules with "lsmod" - you should see nfsd, exportfs, and nfs_acl amoungst the others you already had

create a simple nfs export config file (this is just a test, you will go back and create your real NFS config later!)
```
nano /etc/exports
```
if you copy paste the following line you will export (share) your FamilyLibrary folder (GoFlex Home Public in the GoFlex Home) so it's available for mounting to all machines on your LAN (assuming your LAN uses the range 192.168.1.x, change as needed):
```
/home/0common 192.168.1.0/255.255.255.0(rw,sync,no_root_squash,no_subtree_check)
```
save and exit Nano. **Note:** NFS cannot be used with Paragon NTFS driver. This means that you cannot export GoFlex Home NTFS formatted drive. Then start the server like this:
```
-bash-3.2# /etc/init.d/portmap start
Starting portmap:                                          [  OK  ]
-bash-3.2# /etc/init.d/nfs start
Starting NFS services:                                     [  OK  ]
Starting NFS daemon:                                       [  OK  ]
Starting NFS mountd:                                       [  OK  ]
```
Check that your exports are working by typing `exportfs -v` - the line(s) in your /etc/exports should be shown
At this point you can try mounting the share on your NFS client - any Linux workstation should do, remember to install the nfs-common package if your distro doesn't include it (Ubuntu for example has it in its repos but isn't installed by default).

## Making the NFS server start automatically when your Stora or GoFlex Home boots

As root, type the following lines
```
chkconfig --add portmap
chkconfig --level 235 portmap on
chkconfig --add nfs
chkconfig --level 235 nfs on
```
Then you need to specify something for RPCNFSDARGS inside /etc/sysconfig/nfs
```
nano /etc/sysconfig/nfs
```
uncomment (remove the # in front of) the line `RPCNFSDARGS="-N 4"` (v4 NFS isn't supported anyway we don't think)

Now the service startup should load the module automatically if it isn't already loaded (e.g. at boot). Reboot and run
```
exportfs -v
```
again to test.
## Q&A

- Which NFS version is this?

NFSv3 is the highest supported, you can force v2 if you prefer through the config file (see above).

- What's NFS performance like on the Stora? What settings can i change to speed NFS up?

From my (Smiff's) testing NFS is faster (or more efficient) than SMB by a significant margin, from 10% to 30% depending on client. CPU usage on the Stora is similar however (around 50% to 75% for large file transfers). I have tried numerous settings whilst setting up home shares on the Stora and the setting I found to have the most significant effect by far was **async**, which roughly halved the time needed to open a large application e.g. a web browser. Application close times are reduced even more, probably because these apps are writing large numbers of small files when they exit and this is slow over NFS (compared to a local disk).
Configure async in your /etc/exports e.g.
```
/home/user       192.168.1.0/24(rw,async,no_subtree_check,no_root_squash)
```
Note there is a slight risk of data loss e.g. if power is lost to the Stora during writes, however the performance benefit is great so this is probably a good tradeoff.

Note that only a small subset of possible NFS server settings seem to be supported on the Stora (for example nosuid isn't supported?). To check which settings the server is actually understanding and using (as opposed to what you've told it to use in /etc/exports), run
```
cat /proc/fs/nfs/exports 
```
Other relevant settings:
**wdelay** you will find is enabled by default; this option has no affect with async (?)
**rsize** and **wsize** values, good values to try are 8192, 16384, 32768 (8k,16k,32k) you will need to experiment to see what works best on your network. For my (gigabit) network and my Ubuntu client, 16 and 32 give similar write speeds, both are about 40% faster than 8192.
**noatime** don't forget your client mount options (probably set in auto.master if using autofs). anything you can do to reduce file system writes is good.

Good little article on NFS performance optimisations http://www.techrepublic.com/blog/opensource/tuning-nfs-for-better-performance/64 If anyone has other tips to improve performance please let us know on the forum!

- Why are there 8 NFSD processes?

If you use top during a file transfer you may see 8 threads - this is normal, 8 is the default for an NFS server. you can change this by editing /etc/sysconfig/nfs but note that you must have at least one thread per client session. You may need to raise this number if you need more simultaneous sessions.

- I've edited my NFS configuration or exports, do I have to reboot my Stora?

You don't need to restart the whole machine, to just restart the NFS service type
```
service nfs restart
```
or to quickly update the exports:
```
exportfs -r
```

- I need help configuring/setting up NFS!

Further reading: troubleshooting NFS http://www.troubleshooters.com/linux/nfs.htm


-- Page written by Smiff, nearly all information provided by Pippone
Have you followed this page? Did Kernel mode NFS work well/badly/not at all for you? Please tell us [here](http://www.openstora.com/forum/viewtopic.php?f=1&t=282)

--J.M. 17:17, 10 March 2011 (UTC) Added GoFlex Home kernel modules and utils (from Pippone). Information [here](http://www.openstora.com/forum/viewtopic.php?f=16&t=798)