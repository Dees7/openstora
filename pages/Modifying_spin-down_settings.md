# Modifying spin-down settings
## Modifying spin-down settings

Stora has a spin down daemon (spindownd) that spins down the hard drives if they are idle for 10 minutes, this is great to save power and save hard disk life, but it can be annoying for some people because when the disks are idle, it takes a couple of seconds to start working again (some hard drives take longer than others to spin up). This is how you can disable or modify the spin-down settings.

- Access your Stora as root through ssh.
- Edit the file: /etc/pwrmgr.conf, change the idletime=10 to the number of minutes after that you want your disks to spin-down, or set it to 0 to disable spin-down completely.
- Restart the spindown service

You should avoid adding comment lines, or any other lines, that include the word "idletime", as the script that reads the value is incredibly basic; it simply looks for the first occurrence of the word "idletime" in pwrmgr.conf and then takes the number immediately to the right of the equals sign. So if you have:
```
# idletime is a value in minutes ; note this is a bad example
idletime = 300
```
Then it will set idletime to zero (off) since the first line contains the word "idletime" but does not contain an equals sign or a number.

Note that unlike using hdparm to directly control the sleep timeout, using pwrmgr.conf/spindownd allows you to set idle times above 255 seconds. For example, you could set it to 180 (3 hours or 10800 seconds) or 300 (5 hours or 18000 seconds).

Be aware that if you have modified your Stora, in particular if you have logging enabled and pointed to your /home directory (as it should be to save writes on the flashram), it may perform disk activity automatically, more often than the factory distribution, and therefore may never spin down if you set a long idle timeout.

After setting a new value you must restart the service:
```
/etc/init.d/spindownd.init stop
/etc/init.d/spindownd.init start
```

## The "enabled" parameter

The "enabled=false/true" parameter is ignored. The script `/usr/sbin/spindownd` does not check for this value at all. To disable spindown, set: `idletime=0`

## Where does the spindown daemon start from?

The version of spindownd provided with the Stora is not the same as the init-script controlled daemon commonly available in other distributions and from Google Code. In particular, the Stora version does not use `/etc/spindownd.conf`, it only uses `/etc/pwrmgr.conf` .

On the Stora, `/usr/sbin/spindownd` is started from `/etc/init.d/oe-bootfinish`. It does not store the PID nor perform other usual init script functionality. oe-bootfinish just starts spindownd in parallel and then has nothing more to do with it - it doesn't even check whether it gave an error; it just assumes it works.

--Pippone / Smiff / AOakley
## See also

- [Fixing spindown not working on JBOD setup](Fixing_spindown_not_working_on_JBOD_setup.md)
