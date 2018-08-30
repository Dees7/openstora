# Harddrive Recovery

## Recovering Data from your Stora Harddrive

Assuming you've managed to brick your Stora, or say you want to transfer/backup a load of files from your Stora and want to maximise the transfer rate (rather than saturate your ethernet connection), the best thing to do is pull your harddrive out of the Stora and stick it in a desktop pc (or SATA enclosure).

Windows will probably balk at the idea of accessing a quasi-RAID1 XFS formatted drive... so we'll use Ubuntu instead.
Grab Ubuntu

Hopefully you have a CD lying around already, if not pop here and grab one (get 32bit unless you're confident your PC is x64). Either burn to a cd or find a spare 1gb USB pendrive and use the [Universal USB Installer](http://www.pendrivelinux.com/downloads/Universal-USB-Installer/Universal-USB-Installer.exe).

## Boot Ubuntu

Hit a key when you see the little keyboard+man on the screen, choose 'Try Ubuntu without installing' and make a cup of tea whilst it boots to the desktop.

## Find out what drive it is

Use GParted (System->Administration->GParted) and note down which drive (dropdown on the right) is yours.

For example, mine was /dev/sda.

If you have multiple drives of the same size in your PC then it'll be the one with the XFS partition.

## Bring up the Terminal

**Note:** In order to correctly access all files without changing permission, we'll need to run the Ubuntu file manager (nautilus) as root

Hit Applications->Accessories->Terminal.

Become root with the following command:
```
ubuntu@ubuntu:/$ sudo -i
```
your terminal should now show a pound (#) sign meaning you're now root. Type the following, replacing '/dev/sda' with the one you noted down earlier
```
ubuntu@ubuntu:/# mkdir /media/stora
ubuntu@ubuntu:/# mount -t xfs -r /dev/sda1 /media/stora/
```
Hopefully a little drive icon will pop up on your Desktop. Instead of clicking on it, type one additional command on the terminal:
```
ubuntu@ubuntu:/# nautilus
```
This will open the Ubuntu file manager as root user, allowing you to access every file without restriction, now you can just use drag/drop to copy the various files you want to keep (probably stuff in the home directory).

**Note:** The `-r` flag mounts the drive as read-only to prevent you damaging the drive (ie if you wanted to put it back in the stora).

## Done

Once you're done you can just switch off, take the CD out, remove the Stora HDD and boot back to Windows (or whatever OS you are running :]).

[Thread in forum](http://www.openstora.com/phpBB3/viewtopic.php?f=1&t=550) -- streetster