# Harddrive Recovery / Transfer to New Drive in Stora

## Introduction

So you want to upgrade your 1TB drive to a 2Tb drive and move your files across to the new drive. It can be done without backup however be warned:

- this worked for me on my Stora with Firmware revision 2.5.1
- you will lose customisations where these are saved to the hardrive
- this method is best for a few large directories; not complex multiaccount many directory systems (which can be tedious to copy and reconfigure)
- you may loose everything if something goes wrong

### Instructions

Remove your existing drive Insert your new drive Login to the web interface Goto Disk Management Select Configure New Drive

you now have a 2TB Stora

Login using SSH and use the All-in-one setup script to reconfigure as you desire (get root access if you havebnt already) As a minimum

- set PATH
- Swap the SSH Daemon
- IPKG
- Move /opt
- Install midnightcommander file manager(ipkg install mc)

Now familiarise yourself with mc lest you do damage. RTFM!!!

Insert you original drive into slot 2. In the web interface you will be prompted to configure the new drive DO NOT DO THIS !!!!!! Stora is trying to RAID the drive. Cancel or you will loose your data. It may reprompt you DO NOT CONFIGURE THE DRIVE!!!!!

Now using SSH, mount the original drive to a directory called recover in your "FamilyLibrary".

```
cd /home/0common
mkdir recover
mount /dev/sdb1 recover
```

You can now access the contents of your original drive at /home/0common/recover.

Using mc, I suggest you copy the files you want to retain to a new directory on the larger drive (eg to another directory in the FamilyLibrary, then move them using renmov in mc to the desired location on the larger drive. Two reasons for this:

1. Copy instead of move, since move deletes from the original drive which it may be better not to do just yet.

2. It will still take about 20-24 hours to copy 1TB from drive to drive (about 13.5 MB/s - much faster than using a USB drive). Renmov using mc is almost instantaneous when moving on the same drive. (Do not do this using a PC across the network as it deeats the purpose).

Be careful entering the paths in mc. Review the Stora directory structure, particularly the "FamilyLibrary" which is actually /home/0common.

It may be possible to copy the changes you had previously made to your Stora since the full filesystem is visible on the original drive. I didnt do this and started again with clean Stora.

### Update from 5.2.2012 by czernitko

Note 1: if you leave your stora without any harddrive, then plugging in the first drive (in any slot) will result in Stora rebuilding your drive WITHOUT ASKING!... Only when inserting the second drive you have the option not to add the drive to a raid array.

Note 2: if you have lost your Stora (mine had gone for repairs due to NAND damage and I was left just with my two 2TB drives), you might need to access your data. First thought : just plug the drive into a running linux machine, mount the partition and you are good to go. Too simple, you say? Well, you cannot directly mount the drive, because it is a raid member. I have written a little howto for mounting harddrives from [Stora on another running linux distribution](https://sites.google.com/site/czernitko/linux/netgear-stora/recovering-data-from-disks). Hope it helps.

## Alternative method

One of my 1TB drives got broken, but I have a raid 1 system, so why not use this raid functionality? Now larger drives are cheap, so I wanted to expand the array to 2TB as well. This is the steps I took:

- Turn off the system and eject the bad drive or keep one as a backup.
- Insert a new disc in the right bay, put the working drive in the left bay.
- Start the system, it should start up with the light for the right drive red.
- Log on to the WEB interface, now you should be asked to add the new drive. It is OK to do this, but you will get a warning that the full capacity of the new drive can't be used.
- Alternatively (not tried), create a partition on the new drive /dev/sdb covering the complete drive using Id fd (Linux raid autodetect). This should allow you to repartition this drive later. Use sudo fdisk /dev/sdb. After this add the new drive as described in the step above.
- Wait for raid stack to recreate, this will take several hours. You can check the status under disk management in the Web interface or use `cat /proc/mdstat`
- Turn off the system. Move the first new drive from right bay to left bay and insert the second drive in the right bay.
- Turn on the system and add the new drive to the array as described above.
- After resyncing, you should have a new system with the same size as before. Now it's time to increase the size.
- Check the partition sizes with `sudo fdisk -l /dev/sda and sudo fdisk -l /dev/sdb`. On my system /dev/sda had a partition sda1 of half the size as the disk, so this need to be repartitioned. If both use the full disk you can skip next steps.
  - Eject /dev/sda1 fom the array with `sudo mdadm -f /dev/md0 /dev/sda1` followed by `sudo mdadm -r /dev/md0 /dev/sda1`
  - Repartition sda1 by using `sudo fdisk /dev/sda`, remove the partition and create a new one using the complete drive. set the Id to fd (Linux Raid autodetect). Don't forget to save the partition table.
  - Reinsert /dev/sda1 into the array with `sudo mdadm --add /dev/md0 /dev/sda1`
  - Wait again for the array to resync (check with `cat /proc/mdstat`).
- Now you should have full partition sizes on both drives. Increase the raid system with `sudo mdadm --grow /dev/md0 --size max`
- Again wait for the array to resync.
- Finally grow the filesystem using `sudo xfs_growfs -d /dev/md0`
- You are done having a new raid system with 2TB of space!

--Bimme (talk) 16:17, 4 December 2014 (MST) 