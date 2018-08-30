Fixing FamilyLibrary share on Mac (AppleTalk)

Due to a bug (feature?) on the atalk implementation on the Stora, symlink aren't officially supported, and as you probably know, the FamilyLibrary share is a symlink to `/home/0common`.

In order to fix the FamilyLibrary showing empty on your Mac (using the AppleTalk protocol, default on Mac), you just need to SSH into the Stora and gain root access (read the other wiki articles), then issue this command:
```
ln -s /home/0common /home/FamilyLibrary
```
now the FamilyLibrary share will show you all the content!

--Pippone 07:17, 28 October 2010 (UTC)

As you said Pippone [1], the procedure to get it work now is the following:

- become root
- go inside your user's home directory, eg: `/home/stora`
- remove the FamilyLibrary symlink, rm /home/stora/FamilyLibrary, it's just a symilnk so no files will be lost.
- create a new empty FamilyLibrary directory and change the ownership to the user you're fixing (in this example the user is "stora"): 
```
mkdir /home/stora/FamilyLibrary
chown stora:stora /home/stora/FamilyLibrary
```
- bind mount the real familylibrary into the new directory: `mount -o bind /home/0common /home/stora/FamilyLibrary`
- now you should be able to access your FamilyLibrary through netatalk, if so, add the `mount -o bind /home/0common /home/stora/FamilyLibrary` command to `/etc/rc.local` so the FamilyLibrary gets mounted everytime you reboot the Stora.
**UPD**:  No! use `/ets/fstab` For each user.
```
/home/0common /home/<USERNAME>/FamilyLibrary none     bind,default         0         3
```
and mount manualy without rebooting `mount /home/<USERNAME>/FamilyLibrary`. After next boot it auto-mounts.