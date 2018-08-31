# Services overview
## Managing services

To get all running services on your Stora type:
```
/sbin/service --status-all | grep running
```
To see which services starts on boot:
```
/sbin/chkconfig --list | grep :on
```
If you want to stop a service you can type:
```
/etc/init.d/servicename stop
```
where **servicename** is the name of the service you want to stop, if you don't want a service to run at startup you can use:
```
/sbin/chkconfig --levels 2345 servicename off
```
and to be sure it can't be called in some other way you can just remove the executable flag from the launcher script under /etc/init.d like:
```
chmod -x /etc/init.d/servicename
```
## List of services

Just a writeup of all the services I encountered on my Stora. Use this list to determine what services you'd like to have disabled.

- access-patrol
Evil application. See [Disabling Access Patrol](Disabling_Access_Patrol.md).

- admin-db
Reapply settings saved in admin-db.xml after a restore.

- atalk
This package enables Linux to talk to Macintosh computers via the AppleTalk networking protocol and provides printer, file sharing, and AppleTalk routing services.

- avahi-daemon
This is a daemon which runs on client machines to perform Zeroconf service discovery on a network. avahi-daemon must be running on systems that use Avahi for service discovery. Avahi-daemon should not be running otherwise.

- axentra_reset
resets the server configuration whenever 'reset' key is detected on the kernel command line. Plain and simple!

- crond
cron is a standard UNIX program that runs user-specified programs at periodic scheduled times. vixie cron adds a number of features to the basic UNIX cron, including better security and more powerful configuration options.

- cups
Common UNIX Printing System (CUPS)

- httpd
Startup script for the Apache HTTP Server

- igd-daemon (upnp-igd)
UPnP Internet Gateway Device client

- locator
UDP broadcast of HipServ's presence

- mdmonitor
The mdmonitor service checks the status of all software RAID arrays on the system. In the event that any of the arrays transition into a degraded state, it notifies the system administrator. Other options are available, see the mdadm.conf and mdadm man pages for possible ways to configure this service.

- messagebus
This is a daemon which broadcasts notifications of system events and other messages. See http://www.freedesktop.org/software/dbus/

- minidlna
MiniDlna Media server

- mt-daapd
mt-daapd is a multi-threaded DAAP server for iTunes

- mynetworkd
takes care of restarting some services (dlna, upnp, port forwarding) when your network configuration (like Stora's ip) changes, you can probably get rid of it.

- netgear-lifecycle
netgear-lifecycle is used to update led in case one of your hard drive fails and probably some other hardware related functions.

- ntpd
ntpd is the NTPv4 daemon. The Network Time Protocol (NTP) is used to synchronize the time of a computer client or server to another server or reference time source, such as a rad

- oe-appserverd
that's the whole server behind the nice flash web interface, it allows the Stora to create thumbnails of pictures, change system settings, etc, you don't want to disable it if you want to keep using the web interface.

- oe-spd
Runs the System Proxy, which performs certain operations that Penzila applications require as root

- portmap
The portmapper manages RPC connections, which are used by protocols such as NFS and NIS. The portmap server must be running on machines which act as servers for protocols which make use of the RPC mechanism.

- smb
Samba daemon. Provides files sharing using the windows method.

- sshd
OpenSSH server daemon

- syslog
Syslog is the facility by which many daemons use to log messages to various system log files. It is a good idea to always run syslog.

- transmission-daemon
Bittorent client

- vsftpd
vsftpd is a Very Secure FTP daemon. It was written completely from scratch

- xinetd
xinetd is a powerful replacement for inetd. xinetd has access control mechanisms, extensive logging capabilities, the ability to make services available based on time, and can place limits on the number of servers that can be started, among other things.

--Preaper 22:08, 6 February 2011 (UTC)