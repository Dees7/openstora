# Fixing spindown not working on JBOD setup

**UPDATE**: starting from firmware 2.5 this issue should be fixed, no need to follow this procedure anymore

If you have a new firmware (2.3 and newer) and you setup the Stora to use JBOD instead of Raid1, you'll probably have some issues with hard drives spindown, only one disk will actually spin down when not used, the other one will always stay on, this is a bug Netgear should fix by themselves, but since we're good people, we'll tell you how to fix it :)

- Gain root access on the Stora (check the corresponding wiki articles to choose the best way for you)
- Kill the spindownd: `killall -9 spindownd`
- Edit the file: /usr/sbin/spindownd using `vi`
- Go on line 82 and replace the line:
```
DRIVES=`/sbin/mdadm --detail /dev/$RAID |grep "/dev/sd"|tr -s " "|cut -d " " -f 8`
```
with this line:
```
DRIVES="/dev/sda /dev/sdb"
```
- Start spindownd again:
```
/usr/sbin/spindownd &
```
There you go! --Pippone 10:20, 25 October 2010 (UTC) 