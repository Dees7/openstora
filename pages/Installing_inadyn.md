# Installing inadyn

Inadyn is a service you can install to automatically update your IP addres in DNS services such as DynDNS. I use DynDNS so this example will be based on installing inadyn for DynDNS.

To install inadyn you'll need to have ipkg set up first.

- To Install inadyn execute:

```
ipkg install inadyn
```

- Configure inadyn.

Go to https://www.dyndns.com/support/tools/clientconfig.html and generate a config file for inadyn. Save this config file on your Stora as /opt/etc/inadyn.conf

- Test inadyn.

Start inadyn by executing `inadyn`. The output should be similar to this:
```
-bash-3.2# inadyn
Mon Feb  7 15:47:15 2011: Started 'Inadyn version 1.97.4' - dynamic DNS updater.
```

If not you need to make sure inadyn was installed properly and/or check the config file for errors(see the remark below about special characters).

- Having inadyn auto start on bootup

Save the code below as /etc/init.d/inadyn
```bash
#!/bin/sh
#
# chkconfig: - 60 20
# description: inadyn updates the dyndns service
#
. /etc/rc.d/init.d/functions

case "$1" in
        start)
                if [ -n "`/sbin/pidof inadyn`" ]; then
                        /usr/bin/killall inadyn 2>/dev/null
                fi
                echo "Starting inadyn"

                # update this to another path if you've got inadyn installed on another location (try: which inadyn)
                /opt/bin/inadyn &
        ;;
        stop)
                echo "Stopping inadyn"
                if [ -n "`/sbin/pidof inadyn`" ]; then
                        /usr/bin/killall inadyn 2>/dev/null
                fi
        ;;
        *)
                echo "Usage: (start|stop)"
                exit 1
esac
```

and finally execute:

```
chmod +x /etc/init.d/inadyn 
chkconfig --add inadyn
chkconfig --level 235 inadyn on
```

## Considerations when using special characters

If you have a #(hash) or \\ (backslash) sign in your DynDNS user name or password you need to escape it with a backslash within the config file. For example
```
username Strange\\user
password abc\#94
```
## Logging

Additionally you can add
```
log_file /var/log/inadyn.log
```

to the config file to have it log to /var/log/inadyn.log (or change the path since the /var/log folder is on the tmpfs and thus cleared every reboot).

--Preaper 22:07, 6 February 2011 (UTC) 