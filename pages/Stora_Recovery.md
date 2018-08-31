# Stora Recovery
## When to use the recovery procedure

### Symptoms:

- Solid orange, flashing blue LED. This is normal to see during boot, but if it stays like this for several minutes your Stora is not booting properly.
- You cannot SSH to Stora
- Stora is not connecting to your network
- Stora's name appears on your router as "StoraRamFS"

So your Stora appears 'bricked', and you would need to return it or even throw it away.

### Causes:

- Careless or malicious use of root access
- Bad firmware updates (v1.x to v2.x has been known to do this for some users but it also installs the recovery mechanism ok!).
- Filling the 256MB flashram (type 'df' to see free space, see notes about linking directories to hard drive)
- Unknown system software bugs
- NAND blocks damage
- Hardware failure (unlikely, and of course this procedure won't help in this case).

## What you need

- Stora with above symptoms and firmware at least v2.3 (no known recovery mechanism on previous firmware -> RMA?)
- Internet connection (your Stora will need to download new firmware from www.axentra.com)
- Stopwatch or a clock that displays seconds
- Pointy stick to press reset button


Note: The procedure should work with no drive(s) inserted but leaving them on won't cause any damage to your existing data.
Make sure you can see Stora front panel whilst accessing the back aswell.
## How to perform recovery

- To put the device into recovery mode, power on the device, wait 15 seconds (exact), then hold the reset button while bootup continues.
  - The bootup process will take approximately 30 seconds and the blue LED will be blinking during that period.
- As soon as the recovery mechanism kicks in, the blue light turns off and HDDLED1 will turn orange. The reset button can be released at this point.
- Then the device will request a DHCP lease and try to get an IP
  - If an IP is obtained, HDDLED2 will also turn orange for 1 second.
  - If no IP is assigned the following pattern will be shown on the leds and the recovery mechanism will stop.
- Power LED Off -> HDDLED1 and HDDLED2 will blink 3 times together -> All LEDs off
- Once network is up, the download and burn process will begin. During the download procedure the LED behaviour is:
- Power LED Off -> HDDLED1 and HDDLED2 will turn on and off in turns
- The download and burn will take around 4-5 mins with a fast connection speed.
- Once the download and burn is done:
  - If the burn is successful, all LEDs will turn on for 5 seconds and then the device will reboot normally
  - If the burn fails the LED behaviour will stay as follows and the recovery mechanism will stop
- Power LED On -> HDDLED1 and HDDLED2 will blink together

Now you have to start from the beginning following the other guides in this wiki (unless you have any backups of key flashram folders e.g. `/etc`)

Credit to Mikael for this http://www.openstora.com/forum/viewtopic.php?p=1555#p1555 (without correct timing - not explained by Netgear - it doesn't work!)

-- Smiff