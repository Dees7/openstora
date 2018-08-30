# Fixing smartmontools

The configuration file /etc/smartd.conf has the statement `DEVICESCAN` in it, which makes the smartmontools daemon (smartd) scan for all disks. However this doesn't work (at least not on mine). Fire up the daemon in debug mode to see if it's running properly.
```
smartd -d
```
output on my machine
```
Device: /dev/sda, opened
Device: /dev/sda, IE (SMART) not enabled, skip device
Try 'smartctl -s on /dev/sda' to turn on SMART features
Unable to register SCSI device /dev/sda at line 23 of file /etc/smartd.conf
Unable to monitor any SMART enabled devices. Try debug (-d) option. Exiting...
```

## Activate SMART on the disk(s)

When running the above command (smartd -d) you see the devicename `Device: /dev/sda, opened`

Before activate SMART on the disk, you can make a support-test: `smartctl -i -d marvell /dev/sda`.

To activate SMART on the above disk, run the following command `smartctl -s on -d marvell /dev/sda` The Stora should respond with SMART Enabled.

## Configure SMART to run selfchecks

Edit the configuration file `/etc/smartd.conf`. Start by commenting out the DEVICESCAN (add a pound in front of it).

Now add monitoring for your disc, e.g. (-m specified where to send an email if disk problems occur)
```
/dev/sda -d marvell -a -o on -S on -s (S/../../.02|L/../../6/03) -m <user>@<yourdomain>
```
save and close the file and fire up the daemon `smartd -d` again. (exit smartd by pressing ctrl - \)

Don't forget to enable smartd to run at system boot
```
 /sbin/chkconfig --levels 2345 smartd on
```

## Configure SMART to send email notification messages using ssmtp

If you haven't installed **mail** in your system (it is not installed by default, at least in the GoFlex Home) you have to tell **smartd** to use ssmtp to send the messages. Note that first you have to configure SSMTP. You may want to [use a Gmail account](Configuring_SSMTP_for_Gmail.md) for that.

Since the syntax for **ssmtp** is different of the syntax of **mail** it is needed to create a script to send the email and tell smartd to use it to use it. I created the script `/usr/local/bin/mailto` containing

```bash
#! /bin/bash
TMP_FILE=/tmp/Smartctl.error.txt

echo To:  $SMARTD_ADDRESS > $TMP_FILE 
echo Subject:  "$SMARTD_SUBJECT" >> $TMP_FILE 
echo >> $TMP_FILE 
echo >> $TMP_FILE 

# Save the email message (STDIN) to a file:
cat >> $TMP_FILE 

# Append the output of smartctl -a to the message:
smartctl -a -d $SMARTD_DEVICETYPE $SMARTD_DEVICE >> $TMP_FILE 

# Now email the message to the user at address ADD:
/usr/sbin/sendmail  $SMARTD_ADDRESS <  $TMP_FILE 

# Delete temporary file
rm $TMP_FILE 
```

Add execute permission to the file running
```
chmod +x /usr/local/bin/mailto
```
Edit the configuration file `/etc/smartd.conf` as
```
/dev/sda -d marvell -i 190 -a -o on -S on -s (S/../../.02|L/../../6/03) -m <user>@<yourdomain> -M exec /usr/local/bin/mailto
```
and restart the service using
```
service smartd restart 
```
The `-i 190` option tells smartd to ignore the attribute 190 when checking for failure. Attribute 190 is 100-celsius temperature in Seagate disks. Ignoring it avoids receiving an email every time this attribute changes.

To test that everything is working you can edit the configuration file `/etc/smartd.conf` as
```
/dev/sda -d marvell -i 190 -a -o on -S on -s (S/../../.02|L/../../6/03) -m <user>@<yourdomain> -M exec /usr/local/bin/mailto -M test
```
and run
```
service smartd restart
```
This will restart the service and send an email to your <user>@<yourdomain> account. If you don't receive any email, check your messages log file for any error. Remember to remove the `-M test` when done. 