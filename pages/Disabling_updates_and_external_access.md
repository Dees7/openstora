# Disabling updates and external access
- First of all start with [sshd substitution](Sshd_substitution.md), you'll enable regular ssh access and disable some backdoors.
- Now is a good time to install Nano if you don't like or know how to use vi to edit text files - see [Installing a package manager](Installing_a_package_manager.md)


## Disabling updates and external access

- Remove execution flag from "/usr/bin/oe-update-checker":
```
chmod -x /usr/bin/oe-update-checker
```
- Disable the update check from crontabs
```
chmod -x /etc/cron.daily/update-checker
chmod -x /etc/cron.hourly/update-checker
```
- Comment out these lines inside "/etc/init.d/oe-bootfinish"
```bash
if check_for_update; then
    success
else
    failure
fi
```
to do that:
```
vi /etc/init.d/oe-bootfinish
type: ":121"
type: "i"
place a pound # in front of the 5 lines above
hit the "Esc" key
type: ":wq"
```
Try the following if the above doesn't match your file
```
Comment out the following lines
    161 #    echo "    $AXENTRA_HEADER Checking for updates..."
    162 #    if check_for_update; then
    163 #        success
    164 #        echo "        success"
    165 #    else
    166 #        failure
    167 #        echo "        failed"
    168 #    fi
```
- Now lets disable the OpenVPN daemon: remove execution flag from openvpn binary and starting script "/etc/init.d/openvpn":
```
chmod -x /etc/init.d/openvpn
chmod -x /usr/sbin/openvpn
```
- Disable execution flag from a script that would allow Axentra to open a ssh tunnel through the web interface for "support" purposes:
```
chmod -x /var/www/admin/sshtunnel.pl
```
- Disable telnetd:

Kill the daemon and comment out "/sbin/telnetenabled" from /etc/rc.local to avoid autostart
```
killall -9 telnetenabled
killall -9 telnetd
vi /etc/rc.local
type: ":10"
type: "i"
place a pound # in front of the "/sbin/telnetenabled" line
hit the "Esc" key
type ":wq"
```
- Disable the daemon watchdog that will eventually try to re-launch openvpn etc..

You need to comment out this line from /etc/crontab: 0-59/5 * * * * root /usr/sbin/oe-check-daemons
```
vi /etc/crontab
type: ":11"
comment out placing a pound # or delete that line
type ":wq"
```
There are probably other things and I'll keep updating this page when I find them, but the only way to make sure you have a clean Stora is to completely dump their firmware and install a clean and trusted one, like the Debian project we're working on.

Have fun! --Pippone 08:46, 26 April 2010 (UTC)
## Troubleshooting

- If you are getting **Operation not permitted*** errors remember to get root access using sudo -E -s
- If you see the message **audit_log_user_command(): Connection refused** it doesn't mean the command failed! It's caused by a bug
