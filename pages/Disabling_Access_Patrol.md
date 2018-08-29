# Disable the Access Patrol service

## What does Access Patrol service do?

access-patrol is in charge of (as far as I've been able to analyze):

- Automatically check for updates
- Check if UPnP port forwarding is working correctly, re-start it if it doesn't
- Start the OpenVPN in case Stora is unable to open ports on your router through UPnP
- Start a ssh tunnel for support purposes (?)
- Set/Reset all the features on /etc/features.conf, probably based on your account status (premium or not) obtained from Netgear/Axentra's server
- Hijack your DNS using Axentra's one
- Most probably, updating the "mystoraname.mystora.com" dynamic dns when your public ip changes (I'm not 100% sure on this)

so, to summarize, if you disable it, your mysotraname.mystora.com will probably not work anymore, but this isn't an issue since you can use your own dynamic dns (www.dyndns.org) and update it from the stora (use the inadyn package) or from your router if it supports it. Plus, if your Stora was using the openvpn because it couldn't open the needed ports on your router through UPnP, then you'll need to manually open the ports you need on your router and point them to the Stora's internal ip so you can access the web interface and other services directly through your own dynamic dns.

reference: http://www.openstora.com/forum/viewtopic.php?f=1&t=443

## How to disable Access Patrol

"oe-access-patrol" is a daemon that starts on boot and take care of hijacking your dns, check your stora connectivity and start the openvpn if it detects ports are not correctly forwarded with UPNP for external access. If you want a clean Stora, you really want to disable it, perform these commands as root:

- Stop the service:
```
/etc/init.d/access-patrol stop
```

(for some reason this causes a glitch in the console, just press up a few times to clear it)

- Remove it from autostart
```
/sbin/chkconfig --levels 2345 access-patrol off
```
(to check which services are enabled, use "chkconfig --list |grep :on")

(to stop UDP Broadcasts to hips serv, use "chkconfig --level 2345 locator off" )

- To be safe, remove execution flag from the daemon:
```
chmod a-x /usr/sbin/access-patrol
```
- If you setup your Stora to have static ip and dns, then you also need to configure the DNS in the right place:
```
vi /etc/sysconfig/network-scripts/ifcfg-eth0
# add your dns as: DNS1="primary dns address" and optionally, if you have a backup dns server, DNS2="second dns"
```
- Manually fix the /etc/resolv.conf, you can comment every line of it with "#" and reboot the Stora, it will be automatically replaced with your DNS entries from ifcfg-eth0

--Pippone 07:03, 1 June 2010 (UTC) 