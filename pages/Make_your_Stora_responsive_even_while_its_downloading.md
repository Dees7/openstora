# Make your Stora responsive even while its downloading

Having your Stora download media is great. However having this downloading, parring and unrarring affect your Stora's responsiveness is a bummer. Luckily there is a solution: if you give your download processes a lower priority(to both the CPU and hard drive) they will no longer drag your Stora down. This can be achieved by using nice and ionice.

All examples are assuming you're running as root. If not, prefix all the commands with "sudo ".

## Give processes a CPU priority using nice

In Linux process CPU priorities are called nice-levels. A process with a high nice level is very nice - meaning it happily yields its right to CPU cycles in the favor of other processes. A high nice level represents a low priority. The range of nice levels goes from -20 (highest priority) to 19 (lowest).

To apply a nice level when you start a process, of for example 19, use this command:
```
nice -n 19 /path/to/executable
```
## Give processes a IO priority using ionice

While downloading, most activities are more IO(hard drive) intensive than CPU intensive. So in order to make sure the Stora remains responsive its very important to give you download applications a low IO priority. Luckily, this is possible using ionice.

Unfortunately by default the Stora does not support ionice. Ionice requires you to have CFQ scheduling enabled. CFQ scheduling is one of the ways Linux can assign hard drive access to the processes. In the case of CFQ it is possible to give different processes different priorities. For more information refer to http://en.wikipedia.org/wiki/I/O_scheduling or http://en.wikipedia.org/wiki/CFQ .

Ionice works with three classes. 1 for real time, 2 for best-effort, 3 for idle. Class 2, best-effort, is divided into 8 levels(0-7 with 0 being the highest priority).

To apply a ionice level when launching a process u use these command
```
ionice -c3 /path/to/executable # Gives the process the lowest IO priority possible: Idle
ionice -c1 /path/to/executable # Gives the process the highest IO priority possible: real time. Carefull! This could block your system
ionice -c2 -n2 /path/to/executable # Gives the process a medium priority-class(best-effort) with a level of 2. This process has a somewhat higher priority than default.
```

## Enabling CFQ scheduling on the Stora (enabling ionice)

Thanks to pippone we have the compiled kernel modules to allow CFQ scheduling!

- Download them from: http://www.openstora.com/forum/viewtopic.php?p=5007&sid=3f202a89cc9cb27b57a705f6e0e22bff#p5007
- Unpack schedulers.tar.gz into /lib/modules/2.6.22.18-Netgear/kernel/block (block folder needs to be created)
- Execute(This lets the kernel scan the module folders to find the new module we added):
```
depmod -a
```
To load the module execute:
```
modprobe cfq-iosched
```
Now you need to configure your drives so they will use CFQ scheduling instead of the default scheduling.
```
echo cfq > /sys/block/sda/queue/scheduler
echo cfq > /sys/block/sdb/queue/scheduler # only required if you have 2 hard drives installed
```
In order to have the above done automatically every boot, create this init script and save it as /etc/init.d/setupioschedule
```
#!/bin/bash
#chkconfig: 235 02 02
#description: Loads the CFQ IO schedule module en sets drive scheduling to CFQ
#
echo "Setting up CFQ as IO scheduler"
/sbin/modprobe cfq-iosched
echo cfq > /sys/block/sda/queue/scheduler 2> /dev/null # Sets up drive 1 (if available)
echo cfq > /sys/block/sdb/queue/scheduler 2> /dev/null # Sets up drive 2 (if available)
```

Then make it executeable and have it autorun:
```
chmod +x /etc/init.d/setupioschedule
chkconfig --add setupioschedule
```
(If your worried about the startup script firing at every runlevel change - don't worry. Modprobing the same module twice doesn't have any affect. Neither do the echo's.)

## Adjusting your daemons so they run at a lower priority

I changed the startup scripts of transmission and nzbget to make sure they get a low priority.

In /etc/init.d/nzbgetd:
```
nice -n 19 ionice -c3 /opt/bin/nzbget -c /usr/etc/nzbget.conf -D &
```
/etc/init.d/transmission-daemon:
```
nice -n 15 ionice -c3 su - $DAEMON_USER -c "$DAEMON $DAEMON_ARGS"
```
Applying this works perfectly. The Stora can be downloading/par checking/unrarring and you wont notice. Even when you stream 1080p movies. Unfortunately the web gui of nzbget and transmission will be slow because we have to ionice the entire nzbget/transmission daemon and can't(a.f.a.i.k.) ionice the download threads.

Good luck!

--Preaper 10:58, 8 February 2011 (UTC) 