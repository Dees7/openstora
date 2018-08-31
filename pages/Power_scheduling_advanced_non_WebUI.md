# Power scheduling (advanced/non WebUI)

Setting up your own power schedule.

## Intro

Sometimes the default scheduler isn't enough for your specific needs or you've set up your box in such a way that disables the web interface.

Either way through the command line you can give yourself infinitely more control.
## Create cron file

Using cron commands (aka crontab) you can set your box to execute any command you like at any time.

First navigate to the correct folder
```
cd /etc/cron.d
```
In this directory, the system will check every minute for any updates and implement them as soon as they are relevent.

Now we need to create a new cron file. I called mine power (for ease of remembering later).
```
vi power
```

## Create the cron script

Cron uses a simple but powerful way of programming events
```
*     *     *   *    *        *     command to be executed
-     -     -   -    -        -
|     |     |   |    |        |
|     |     |   |    |        +---- user to execute as (usually root)
|     |     |   |    +----- day of week (0 - 6) (Sunday=0)
|     |     |   +------- month (1 - 12)
|     |     +--------- day of month (1 - 31)
|     +----------- hour (0 - 23)
+------------- min (0 - 59)
```
Using a combination of these you can do pretty much anything

As a few examples
```
05 00 01 * * ... executes at 5 past midnight on the first of every month
00 12 * * * 3 ... executes at midday every Wednesday
00 01 01 01 * ... executes at 1 AM on the 1st January every year
```
### Shutting down

To set a shutdown program all we need is to add the shutdown command to our cron.

So to shut down every night at 2 AM
```
00 02 * * 0-6 root /sbin/shutdown -h now
```
**SIDENOTE:** To use vi is a little awkward unless you know what you're doing You need to press "a" to put it into edit mode. To paste into the terminal (in linux or Putty at least) the keyboard shortcut is Ctrl+Shift+V. To exit edit mode you need to press "Esc". To exit vi you need to type ":q", to save ":w", save and quit ":wq"
### Starting up

Starting up requires a bit of a work around. When the box is off, you obviously can't execute commands. So, to get around this we need, while the box is on, to tell the box when to wake up.

The wake up command is done via a countdown timer and alarm (the alarm being to wake up)

This can be done through some scripting and calculating the time between the power off and the next power on. However, I prefer using a set time before the turn off command, and a fixed command.

For example, if I want my Stora to turn on the next day at 5 PM then to the power cron I add:
```
00 01 * * * root /sbin/hwclock --alarm 960
```
This says at 1 AM every night set an alarm for 960 minutes time. This has to be done in minutes (960 minutes = 16 hours)

These commands can be further expanded to make more complex programs. For example: On weekdays the Stora doesnt come on until 5 PM, but on weekends the Stora comes on at 9 AM for not-getting-out-of-bed viewing
```
# Turn off every night at 2 AM
00 02 * * 0-6 root /sbin/shutdown -h now
# Sun-Thurs night calculate next day (Mon - Fri on at 5PM)
00 01 * * 0-4 root /sbin/hwclock --alarm 960
# Fri-Sat night calculate next day (Sat - Sun on at 9 AM)
00 01 * * 5-6 root /sbin/hwclock --alarm 510
```
Lines beginning with # are comment lines and not read by the system.

There you go! Make sure you save and quit (":wq" in vi)

--Morgan
## Automatically rebooting after power failure

I keep my Stora powered on all the time. When there is a power failure I would like the unit to automatically power up again when power is restored. The Stora comes with an internal hardware clock. When the clock is triggered the unit powers up. If the trigger happens while there is a power failure, then the trigger happens as soon as power is restored (MS2110 Ver 2.5, not sure about other versions).

To set the hardware clock I use the cron as follows: Create a file in /etc/cron.d, I called mine hwclock

Inside the file I add the line
```
*/2 * * * * root /sbin/hwclock --alarm 4 2>&1 >/dev/null
```
`*/2 * * * *` means run the command every 2 `minutes /sbin/hwclock --alarm 4` means set the timer to trigger in 4 minutes. Since the command is run again in 2 minutes, the clock is reset before it gets a chance to trigger. When there is a power failure, then the trigger will happen in the next 4 minutes or as soon as power is restored if longer. `2>&1 >/dev/null` prevents hwclock messages from filling up cron's log file.

Note: Using this method if you power down Stora it will automatically reboot again in the next 4 minutes. If you would like to have your Stora power off at night, then you would have to modify the cron to only run during certain hours or days. See Wiki Configuration on Power Scheduling for more info on configuring cron. Read on for instructions to prevent auto-power-on after orderly shutdown (such as via shutdown command or normal use of power switch in the back of the unit).

--Russ D
## Preventing unwanted power-up

The above instructions will cause the unit to start up shortly after powerdown, regardless of the reason the unit was shut down. Perhaps you ordered an orderly shutdown via command line. If you have followed the instructions above, the hardware clock will restart the device after a few minutes since it doesn't know you want it down; it only has orders to start up after the timer has elapsed. The result is probably not what you want. But there is hope!

The hwclock command has an option (--clearalarm) that will reset pending alarms. If we take that action as one of the final steps of an orderly shutdown, the unit will not try to start up on its own. Here's how to do that (as root).

First, we need to get to the startup/shutdown scripts directory, then we'll edit a new power script
```
cd /etc/init.d
vi nopowerup
```
Use your favorite editor if not vi. You know the drill by now.

Now copy and paste the following into your editing window:

```bash
# chkconfig: 12345 90 80
# description: To reset the power alarm during the shutdown process
#              This will prevent the device from raiding the 
#              refrigerator after we've sent it to bed.
# processname: none

. /etc/init.d/functions

#  init will call this script with either a 'start' or 'stop' parameter

case "$1" in 
  start)
    touch /var/lock/subsys/nopowerup
    exit 0
    ;;
  stop) 
    /usr/sbin/hwclock --clearalarm
    exit 0
    ;;
  *) 
    echo "Usage: $0 {start|stop}"
    exit 1
    ;;
esac
```
Now let's make that executable:
```
chmod +x nopowerup
```
The `/etc/init.d` directory contains the startup and shutdown scripts for the system. In this directory, these scripts should have meaningful names so their purpose may be easily understood by only looking at a directory listing.

The above will create a script named nopowerup that, when executed with the start parameter (as the system will do during startup), will create a file called /var/lock/subsys/nopowerup. We'll explain why this is important shortly.

If called with the "stop" parameter (as the system will do during shutdown), the script will clear any outstanding alerts in the hardware clock.

Next, we will cause that script to be executed at the appropriate times. This is the easy part.
```
/sbin/chkconfig --add nopowerup
```
Finally, we'll initialize so that the system is ready for the next normal shutdown:
```
/etc/init.d/nopowerup start
```
That's it. Now, if the device shuts down due to operator command or other normal operation, it will not try to restart. However, if power fails and you have followed the instructions on this page, then the device will start up shortly after power is restored.

## Optional reading
The reason we must create the `/var/lock/subsys/nopowerup` file is that the shutdown process tries to be
efficient and not stop "subsystems" that are not currently running.  (What would be the point?)  And by
convention, it is the presence of a file in /var/lock/subsys that tells the process that a subsystem is
running.  The name of that file is the name of the subystem, which also happens to be (and must be) the 
name of the script that controls it.  Our "subsystem" is named nopowerup, and so the script and the lock
file share that name.

Well, we don't really have a subsystem to control in this case, but in order to fit with the norm and 
have the system run our script as we want it to, we indicate at startup that our process is active.  
Then, at shutdown the system notices that fact, and nicely runs our script as desired.

When we run chkconfig with the --add option as above, it does a lot of dirty work for us, based upon 
the presence of this line in the script:

# chkconfig: 12345 90 80

By this, chkconfig knows that we want the "start" parameter passed at startup time to our script for run
levels 1,2,3,4, and 5. It assumes then that for all unmentioned run levels (so, 0 and 6 in this case), a 
"stop" parameter should be passed at shutdown time.  Based on this, it creates links to our script in 
these directories:
```
/etc/rc1.d
/etc/rc2.d
/etc/rc3.d
/etc/rc4.d
/etc/rc5.d
```
and these links will all be named S90nopowerup  (S = startup; 90 = value from our "# chkconfig" line 
above, and nopowerup is our subsystem/script name.  These will be called by the system when each of the 
runlevels 1 through 6 is entered.

It will also place links to our script into these directories
```
/etc/rc0.d
/etc/rc6.d
```
which are the shutdown and standalone run levels.  These links will be named K80nopowerup (K = Kill, 
80 is the last entry on our "# chkconfig" line, and nopowerup is our subsystem/script name).

Now we have a cohesive process that fits into the normal startup and shutdown processes of the system, 
and nicely controls the auto-power-up ability of the device.  Seems like a lot of work maybe, but it 
hopefully all makes sense once it's explained.

--Dennis