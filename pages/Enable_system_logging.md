# Enable system logging
## Enable system logging

Stora by default doesn't log anything, this has been made in order to avoid filling the flash memory with info you won't be even able to read, unless of course you hack your Stora. Enabling system logging will give you the opportunity to better troubleshoot what's going on with your Stora, this isn't something you should do, it's Netgear duty to fix and troubleshoot their stuff, but sometimes it can be useful, especially if you install custom software and you need to troubleshoot why it's not working. Having said so, let's see how to enable logging!

- ssh to your stora and gain root access
- choose where to store your logs, you really want to place them under /home so they will end up on your hard drives (see #Note on GoFlex Home), I made a /home/log directory for example
```
mkdir /home/log
chmod 777 /home/log
```
- edit /etc/syslog.conf and uncomment the lines for the log you want to enable, I'm posting here my syslog.conf so you can see it better
```bash
# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.*                                                 /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none               /home/log/messages

# The authpriv file has restricted access.
authpriv.*                                             /home/log/secure

# Log all the mail messages in one place.
mail.*                                                 -/home/log/maillog


# Log cron stuff
cron.*                                                 /home/log/cron

# Everybody gets emergency messages
*.emerg                                                        *

# Save news errors of level crit and higher in a special file.
uucp,news.crit                                         /home/log/spooler

# Save boot messages also to boot.log
local7.*                                               /home/log/boot.log
```
Note that all the paths are edited from /var to /home so we log everything to the hard drive and don't fill the Stora's 256MB flashram!

- Restart syslogd

```
/etc/init.d/syslog restart
```

From now on you'll be able to see all kind of loggings by examining files under /home/log!
## Update Logrotate

Edit the logrotate configuration for syslogd, /etc/logrotate.d/syslog, and change /var/log to /home/log
```
/home/log/messages /home/log/secure /home/log/maillog /home/log/spooler /home/log/boot.log /home/log/cron {
    sharedscripts
    postrotate
        /bin/kill -HUP `cat /var/run/syslogd.pid 2> /dev/null` 2> /dev/null || true
        /bin/kill -HUP `cat /var/run/rsyslogd.pid 2> /dev/null` 2> /dev/null || true
    endscript
}
```
## Note on GoFlex Home

In the GoFlex Home, the **/home** directory is actually in the internal memory and, then, writing your logs there is not a good idea since it may fill your internal memory and it will wear out. You may want to change **/home** in all the previous discussion by **/mnt/eSata/.users/** that is where the users directories are actually stored in the hard disk or to any other folder under **/mnt/eSata**.

If you do not mind that your logs are in the public samba share, you can use */home/0common* as it is an alias for the public samba share.

--Pippone 12:55, 22 May 2010 (UTC)

--J.M. 19:37, 7 March 2011 (UTC) - Note on GoFlex Home