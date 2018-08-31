# Installing iSCSI Enterprise Target

Copy the iscsitarget.tar.gz (you'll find it on the forum post linked at the end of this page) to your / and uncompress it, everything should go at its place, run "depmod -a" in order to refresh the available kernel modules, edit the /etc/iet/ietd.conf file to suite your needs and start the daemon with: /etc/init.d/iscsi-target start enjoy and let me know if there is any problem!

## EXAMPLE OF CONFIGURATION:

Create a file that will be your exported device:
```
dd if=/dev/zero of=/home/iscsidev bs=1k count=1000k   (1000k means a 1GB device)
```
edit the `/etc/iet/ietd.conf` and add these lines at the end:
```
 Target iqn.2010-10.home:stora.iscsidev
 Lun 0 Path=/home/iscsidev,Type=fileio
```

start or restart the iscsi-target:
```
/etc/init.d/iscsi-target restart
```

Want to expand your iSCSI drive? You can using the following command:
```
dd if=/dev/zero of=/home/iscsidev bs=1k count=200k oflag=append conv=notrunc
```

After doing this, you must restart the iscsi-target service (shown above), then expand your volume on your client (Windows 7's disk management works well for NTFS volumes!)

Original forum post: [here](http://www.hardwarehackersunite.com/phpBB3/viewtopic.php?f=1&t=287&start=0)

Forum post for Seagate GoFlex Home: [here](http://www.openstora.com/phpBB3/viewtopic.php?f=16&t=406)