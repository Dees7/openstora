# Installing Mediatomb

Mediatomb is an UPnP AV Mediaserver for Linux

## Turn off minidlna
```
chkconfig --level 2345 minidlna.init off
service minidlna.init stop
chmod -x /etc/init.d/minidlna.init
chkconfig --level 2345 mt-daapd off
service mt-daapd stop
chmod -x /etc/init.d/mt-daapd
```
## Install Mediatomb
```
ipkg install mediatomb
```
## Configuration

To enable automatic startup of the MediaTomb daemon edit /opt/etc/default/mediatomb and change the value of MT_ENABLE to true after that start the daemon by issuing the following command:
```
/opt/etc/init.d/S90mediatomb start
```
In this configuration MediaTomb will listen on port 50500, this behavior can be changed by editing /opt/etc/mediatomb.conf and restarting the server which can by done by:
```
/opt/etc/init.d/S90mediatomb restart
```
Edit the line in mediatomb.conf that says `MT_OPTIONS="" to read MT_OPTIONS="-e eth0"` and restart mediatomb.

The config.xml and the database of the daemon configuration will be created in /opt/etc/mediatomb after the first launch.
## Start at boot
```
cd /etc/init.d
ln -s /opt/etc/init.d/S90mediatomb
chkconfig --add S90mediatomb
chkconfig --levels 2345 S90mediatomb on
```