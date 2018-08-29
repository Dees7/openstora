# Changing User Limit

Note: this limit only applies if you add users through the web interface. After getting root access you can use command line tools like "adduser", which ignore this limit.

## Preparation

- You need [Easy Root Access](Easy_Root_Access.md) (or any method of root access)
- First make sure you [Disable the Access Patrol](Disable_the_Access_Patrol_service.md) service or this number will get reset to 3 (5 on newer firmware) when your Stora next reboots!

## Changing user limit

You do this by editing the file /etc/features.conf, like this:

- SSH login as root (see guide for enabling root access)
```
nano /etc/features.conf
```

(Note if nano isn't installed you can use vi instead, or see guide under [Installing a package manager](Installing_a_package_manager.md) for installing nano).

-Find this line:
```
maxNbUsers=
```
- Change the number (probably 3 or 5) to 0, indicating no limit; save and exit your text editor.
- Reboot your Stora, make sure the number hasn't reverted to default. If it has, you probably still need to [Disable the Access Patrol service](Disable_the_Access_Patrol_service.md)!


You can now add users through the web interface up to whatever limit you chose.
Be very careful updating the firmware on your Stora *(if limit is restored, are users deleted??)*

Discussion here: http://www.openstora.com/forum/viewtopic.php?f=1&t=358