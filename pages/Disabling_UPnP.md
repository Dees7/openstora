# Disabling UPnP
## Disabling UPnP

"[upnp-igd] asks your router to open and forward to the Stora the ports specified on the /etc/upnp-igd.conf, keep it in mind when you disable it, you'll need to manually open those ports on your router/firewall if you need them."

However, you may not want or need this service running, in some situations it can be a security risk and you may prefer to forwards from your router manually.

- Stora Firmware v2.3 or later:

There is an option under Server Preferences --> UPNP Router Configuration to disable UPnP configuration of the router.

- Stora Firmware earlier than v2.3:

as root, run
```
chkconfig --level 2345 upnp-igd off
```

## Checking your UPnP status

issue the following command as root:
```
/usr/sbin/igd-client mappings
```
If it returns "Connection refused" then uPnP is disabled, otherwise it will tell you the ports mapped.

Reference: http://www.openstora.com/forum/viewtopic.php?f=1&t=458 