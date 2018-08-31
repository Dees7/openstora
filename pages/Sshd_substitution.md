# Sshd substitution

Original Stora firmware has a special ssh daemon, which disables normal user logins (and has possible backdoors). There is a [trick](Easy_Root_Access.md) to login as a standard user, but it is better to install a standard trusted ssh daemon, follow this instructions if you want to do it.

**WARNING:** follow this instructions very carefully, missing a step or doing something wrong could disable SSH access, requiring a Recovery Boot to fix it! Absolutely DO NOT REBOOT or LOGOFF from the active SSH session until you confirm you can open and login to a new session after the substitution!

**WARNING - MAKE SURE YOU'Re SECURE!:** Enabling a "normal" ssh daemon opens up the possibility for remote login attacks. By default the Stora requests the port forwarding of port 22 (ssh port) from your router to itself, hence the Stora opens internet facing SSH access. With the default SSH deamon only users including the product key can actually login (very hard to guess or brute force the product key) but by replacing the ssh deamon to allow yourself to login, you in turn allow potential attackers to attempt to login by guessing / brute forcing your admin username / password which may not be very secure.

It is highly recommended that remote SSH access is disabled before you replace the SSH deamon. This can be achieved by either disabling uPNP on your router, by disabling the Stora's upnp-igd service [see](Disabling_UPnP.md) or finally just by disabling port 22 forwarding to the Stora by changing the line in `/etc/upnp-igd.conf`
```
from: "PORTS=22,80,443"
to: "PORTS=80,443"
```
and then restarting the upnp-igd service "service upnp-igd restart"

## Substitute using HHWU Binary (easy way)

- Copy the sshd_tar.gz to your Stora, either using scp or dropping it through a share from another computer. **DO NOT EXTRACT THE .tar.gz USING WINDOWS!**
- Make sure you are **root**
- Move to the directory where you downloaded the .tar.gz and extract it:
```
tar xvzf sshd_tar.gz
```
- Backup and disable the existing sshd (just in case)
```
cp /usr/sbin/sshd /usr/sbin/sshd.netgear
chmod -x /usr/sbin/sshd.netgear
```
- FROM THIS POINT ON, DO NOT DISCONNECT THE SSH SESSION OR REBOOT THE STORA UNTIL YOU CAN LOG BACK IN!
- Move the new sshd in place and make sure it is executable
```
mv sshd /usr/sbin/sshd
chmod +x /usr/sbin/sshd
```
 Restart the sshd service
```
/etc/init.d/sshd restart
```
- Carefully watch for errors, if you get all "[OK]" try to open a new ssh session to your Stora, using a regular user if you wish, if you can login you're done!

**Note that since you are now using a clean SSH server, from now on you SSH login with your actual Stora <username>, not the <username>_hipserv2_netgear_<productkey> format!**

- If you get any error, then double-check all the steps, most common mistake is moving the new sshd to a wrong place or not making sure sshd has +x flag, check it with `ls -la /usr/sbin/sshd`, it should output something like this:
```
-bash-3.2# ls -la /usr/sbin/sshd
-rwxr-xr-x 1 root users 354872 Apr 21 15:23 /usr/sbin/sshd
```

Enjoy your Stora!

Download link: [here](http://www.openstora.com/files/displayimage.php?album=6&pid=20#top_display_media)

--Pippone 09:39, 3 May 2010 (UTC)
## Substitute using Optware binary (harder way)

**Please note that this method is HIGHLY discouraged, having the sshd residing on your hard drives and symlinked to the flash memory will prevent you from logging into your Stora if your hard drives fail for some reason!**

This method requires a little more work but will keep the binary up to date as new releases are merged into the repository. To subsitute the daemon this way, you will first need to [install the package manager](Installing_a_package_manager.md). Bear in mind that all the paths given here assume that you've followed all the instructions given in the aforementioned wiki article.

- Next, we need to download and install the new ssh daemon:
```
sudo ipkg install openssh
```
- Now we need to rename the old sshd ready for replacing it with the new one. This way you can easily restore it if something goes wrong.

```
cd /usr/sbin
sudo mv sshd sshd.old
```
- With that done, we're going to create a symbolic link to the new sshd.
```
sudo ln -s /home/opt/sbin/sshd /usr/sbin/sshd
```
- Finally, we're going to make the symbolic link executable.
```
cd /usr/sbin
sudo chmod a+x sshd
```
### Optional Steps

In order to reduce wear and tear on the NAND, you can copy the ssh configuration files to the hard disk. This way, if you want to play with the configuration you don't have to worry about wearing out the NAND or losing the defaults.

- First, we're going to need to copy the configuration files to the hard disk:
```
sudo cp -r /etc/ssh /home/opt/etc/
```
- Now we're going to rename the ssh configuration directory in etc. This way you'll be able to restore everything if you need to.
```
cd /etc
sudo mv ssh ssh.old
```

- With that done we can create the symbolic link to the directory on the hard disk.

```
sudo ln -s /home/opt/etc/ssh /etc/ssh
```

That's it! Enjoy!

--Ham

Forum thread to post feedback: [here](http://www.openstora.com/phpBB3/viewtopic.php?f=1&t=84)