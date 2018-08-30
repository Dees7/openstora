# Installing a package manager

## Introduction

A package manager, for the uninitiated is a way of installing and maintaining software on a computer system. In the context of the stora, it allows you to easily download, install and update programs. It will also remove programs that you no longer want.

The package manager we're going to use is called Optware which was originally created for NSLU2 and it allows you to install additional software on your stora without interfering with original firmware. The Optware project supports several architectures including that of the stora and the repository (where the package manager downloads everything from) we're going to use seems to work quite nicely with the stora.

It's worth noting that certain programs have caused a few problems, although that was from a different repository that's less compatible. See the forum thread [here](http://www.hardwarehackersunite.com/forum/topic?id=98) for more details.

## How to install
### Preparatory Stuff

In order to install optware we're going to need to do a few preparatory things first.
### Logging In

If you haven't looked at this already, you'll first of all need to log into the Stora via [ssh](Easy_Root_Access.md). What is also worth doing is installing the official OpenSSH server which works better and doesn't require such a [long username](http://www.hardwarehackersunite.com/forum/topic?id=98).

### Moving /opt to the hard disk

NAND flash memory has a limited number of reads and writes, and this is what the firmware of the stora is saved on. Seeing as the usage of a package manager will increase the amount of wear and tear on the card, we're going to move /opt which is where all the programs used by the package manager will be stored to the hard disk.

Firstly we need to create persistent root access:
```
sudo -E -s
```
Next, we need to copy the contents of the old opt directory to the new location. In this example I've put the opt folder in home (/home/opt).
```
cd /
cp -r /opt /home/opt
```

All going well, the contents of /opt should now be copied to /home/opt. The next thing to do is to create a symbolic link in the root directory to the new folder so that everything that is written to opt is stored on the hard disk.
```
mv opt opt-old
ln -s /home/opt /opt
```
With that done we're now ready to actually install Optware and most importantly, it's lovely package manager, ipkg.
### Installing Optware

First, we're going to create a folder in your home directory to download ipkg to, and then download it.
```
cd ~
mkdir ipkg
cd ipkg
wget http://ipkg.nslu2-linux.org/feeds/optware/cs08q1armel/cross/stable/ipkg-opt_0.99.163-10_arm.ipk
```
You should see some stuff downloading. Now we're going to unpack the files we need:
```
tar -xzf ipkg-opt_0.99.163-10_arm.ipk
cp ./data.tar.gz /data.tar.gz
cd /
tar -xzf data.tar.gz
rm  data.tar.gz
```
Now we're going to set up and install ipkg:
```
echo src cs08q1armel http://ipkg.nslu2-linux.org/feeds/optware/cs08q1armel/cross/stable >> /opt/etc/ipkg.conf
/opt/bin/ipkg update
```
All going well, you should see this:
```
Downloading http://ipkg.nslu2-linux.org/feeds/optware/cs08q1armel/cross/stable/Packages
Updated list of available packages in /opt/lib/ipkg/lists/cs08q1armel
Successfully terminated.
```

**NB** If you want to live more dangerously and/or need newer packages, you could use the unstable branch of ipkg instead. To do so simply replace "stable" with "unstable" in steps 5 and 11 above:
```
wget http://ipkg.nslu2-linux.org/feeds/optware/cs08q1armel/cross/unstable/ipkg-opt_0.99.163-10_arm.ipk
```
and
```
echo src cs08q1armel http://ipkg.nslu2-linux.org/feeds/optware/cs08q1armel/cross/unstable >> /opt/etc/ipkg.conf
```
## Optional Extra Steps

These extra steps aren't necessary but for me at least, are quite handy.

### Installing Nano

This step is strictly optional but if you're not a fan of vi then nano is a much more straightforward text editor to use and will come in handy for editing lots of configuration files. **Note:** If you have logged out since installing ipkg, then you will need to use sudo to execute the commands as root.
```
/opt/bin/ipkg install nano
```
All going well, nano should now be installed.
### Editing the PATH variable

When I did this, the 'environment' file which is where you store the environment variable was empty. You can check this for yourself by doing the following:
```
sudo /opt/bin/nano /etc/environment
```
### If the file is empty

If you don't edit the PATH variable then you will have to enter the full path of the executable you want to run every time you run it. The PATH variable is held in `/etc/environment` and when I looked that file was empty. First, you need to do copy the text below:
```
PATH=/usr/local/bin:/usr/bin:/bin:/opt/bin:/usr/sbin:/opt/sbin:/usr/sbin:/sbin
```
Copy the above to your clipboard (sometimes its helpful to paste into a text file on your PC first to make sure the line is clean, then select and copy from there) With /etc/environment open in Nano, Ctrl+0 to paste the above text into the file, Ctrl+X to exit, Y to save and confirm filename

Now (or when you open a new SSH session), you will be able to type program names e.g. "nano", without the path, and your Stora will be able to find them for you.
### If the file is not empty

If the file is not empty all you need to do is to add this directory to the end of the PATH variable.
```
/opt/bin:/opt/sbin
```
Whether the PATH variable is there or not, you'll need to log out and log back in again once you've finished editing the PATH settings. If that doesn't work, you'll need to reboot which you can do by entering the following command. You can do this via ssh by entering the command:
```
sudo reboot
```
After all that you should have a working Optware install and should be easily able to execute any programs you install.
### Root user path

If you want to be able to switch to a root user with sudo -i, for example, then you'll find the root user gets their PATH variable munged!

Seems like it promotes the 'sbin' paths ahead of the 'bin' paths. For me it had the effect of removing the /opt/sbin and /opt/bin entries.

I hacked the munging script directly with:
```
sudo -i
/opt/bin/nano /etc/profile
```
Then edited this section to read:
```
# Path manipulation
if [ "$EUID" = "0" ]; then
        pathmunge /opt/bin
        pathmunge /sbin
        pathmunge /opt/sbin
        pathmunge /usr/sbin
        pathmunge /usr/local/sbin
fi
```
Now when I switch to root user I still have a sane PATH to nano.

Enjoy!

Original article by guestus.
ORIGINAL FORUM POST: [here](http://www.hardwarehackersunite.com/forum/topic?id=98)

## How to uninstall

### Uninstall a package

As space and memory on the Stora is quite limited then you might want to uninstall a package, like so:
```
sudo ipkg remove [package name]
```
### Uninstall the package manager

First you have to unmount the /opt folder that was created on the hard drives
```
umount /media/[path]/opt
rm -rf /media/[path]/opt
```
Then delete the package manager
```
rm -rf /usr/lib/ipkg
```