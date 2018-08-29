##  Easy Root Access

This method for root access has turned out to be by far the simplest and most efficient.

## Requirements

- Netgear Stora, Seagate GoFlex Home or Medion Lifecloud 2-bay NAS unit
- Computer with SSH Client
- Network connection

## Getting started

Located the Product Key for your unit it will be in the form of *XXXX-XXXX-XXXX-XXXX*, Located both on the CD Jacket and the bottom of the Netgear Stora Unit.

From a Linux Terminal type:

```
ssh USERNAME_hipserv2_netgear_XXXX-XXXX-XXXX-XXXX@YOUR_IP_ADDRESS
```

Where *USERNAME* is your user name (**note**: all lower case!) on your stora unit, *YOUR_IP_ADDRESS* is your Stora ip address on your network, and *XXXX-XXXX-XXXX-XXXX* is your Product Key.

**Note**: The Product Key must be all caps.

You should be presented with a password prompt at this point, simply type the password for the account name you used to log into the Netgear Stora.


## Getting started - with Windows XP/Vista/7

If you're doing all this from a windows computer:

First download [PUTTY.exe](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html (an SSH client) Instead of entering all the above in one line, run Putty and connect to the IP of your Stora on port 22, then enter the username and password separately when prompted exactly as explained above (i.e. USERNAME_hipserv2_netgear_XXXX-XXXX-XXXX-XXXX). You can save the settings in putty so you don't have to type the username every time (look under Connection > Data > Auto-login username)

After logging in everything works the same as a Linux SSH client, you can do everything from windows if you want!

## Notes

The "hipserv2_netgear" part may be different for some users, although I suppose is the same for everybody, if you already hacked your Stora using other ways I'd ask you to check the "/etc/oe-release" and post here the DistName line and we'll try to figure it out. (see also: [Original forum post](http://www.openstora.com/forum/viewtopic.php?f=1&t=80))

For **Seagate GoFlex Home** use seagateplug, e.g.:
```
ssh USERNAME_hipserv2_seagateplug_XXXX-XXXX-XXXX-XXXX@YOUR_IP_ADDRESS
```
For **Verbatim MediaShare** use verbatim, e.g.:

```
ssh USERNAME_hipserv2_verbatim_XXXX-XXXX-XXXX-XXXX@YOUR_IP_ADDRESS
```

For **ICYBOX IB-NAS6210** use Raidsonic, e.g.:
```
ssh USERNAME_hipserv2_raidsonic_XXXX-XXXX-XXXX-XXXX
```
*note: IP found to be unnecessary*

For **Medion Life P89634 (MD 86783)** and **MEDION LifeCloud P89654 MD 90224**... use medion, e.g.:
```
ssh USERNAME_hipserv2_medion_XXXX-XXXX-XXXX-XXXX
```
*note: the product key is found on login page behind **über Medion LifeCloud** *

## Become root

To gain root control type, after login:
```
sudo -E -s
```
Again type your account password, you'll probably get a: "audit_log_user_command(): Connection refused" but it doesn't matter, you'll get the root prompt!

**Note**: All administrative Stora user accounts will have Sudoer access.

There's another way to login concatenating the username with the string "_axsync_" but it allows you to issue only "mkdir" and "rsync" commands.

If your initial account name contains a '-' (and possibly other allowed punctuation) such as stora-admin then you may get:
```
stora-admin is not in the sudoers file.  This incident will be reported.
```
If this happens, try adding a new user with the web interface, making sure you click the 'Create as administrator' checkbox and use that user instead. Now that I have root access I notice that stora-admin was not included in admin group unlike my previous stora. Perhaps the default account is no longer an admin, or punctuation matters.

## Explanation

Basically Netgear, or more probably Axentra, ships his software with a modified version of SSHD that disallows regular user access, except for "root" and "apache" users, what it does is basically changing the logging username by substituting the first character with a "0" so it doesn't match with the list of allowed users inside the /etc/passwd.

The SSH Deamon leaves a "backdoor" open, which basically is: if your username is appended with the above string, this substitution doesn't take place, and you can login normally.

## Where to go from here

- [Enabling the Root Account](Enabling_the_Root_Account.md) - Allow ssh access as root user.
- [sshd substitution](Sshd_substitution.md) - Replaced sshd with a fixed one allowing normal ssh access for user accounts.
