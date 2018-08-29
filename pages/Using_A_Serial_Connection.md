# Using A Serial Connection

Updated 2nd August 2010: This is a work in progress, I am using this to record my progress with the Stora, and hopefully others will find this useful. If you brick your device then it is not my fault.

Background/Objective The stora is the latest in a line of embedded-type devices that I have stuck debian/emdebian on:

Linksys NSLU2 Netgear WGT634U PCEngines Alix 3c O2 Joggler

The Stora is the first of the list that can have large amounts of internal storage. I want to run as close to native debian as possible. I use Debian except where absolutely have to use something else. The instructions will assume you do the same.


## Purchase Stora

A little while ago eBuyer were selling a 500GB Stora for £70 inc postage.
## Serial port

**CAUTION: If you do not use this specific cable,
you need to make sure your cable is a LVTTL or
TTL level cable. A standard RS232 cable will
fry the Stora's main board.**


I purchased a [nokia dku-5](http://www.amazon.co.uk/gp/product/B00007BHPL) for £2.98 including postage.  And a 4 pin header plug from [e-bay](http://cgi.ebay.co.uk/ws/eBayISAPI.dll?ViewItem&item=140376266229).

The DKU-5 is a serial to USB converter, which accepts the right voltages etc. I generally use a max232 converter, but as virtually nothing has a serial port these days, this is a neater solution.

Cut the proprietary Nokia connector off the end. Find that (contrary to internet sources) the cable has 3 cables. These are (from memory, will check):

1.    Orange: Ground (pin nearest battery on stora)
2.    Red: TX (next pin to Orange)
3.    Blue: RX (next pin to red)

If you use the crimps from the kit off e-bay, and stick them straight on the pins with some insulating tape to prevent shorts, then by bending the crimps slightly, you can get the back cover on. The cable can be threaded out of the case near the feet at the bottom, no external drilling etc necessary- nice!

A DKU-5 bought in Australia had white (ground), black (TX), and red (RX) wires. Mis-wiring the white ground to +ve pin (4th pin) did not harm the board (phew).

## Accessing uBoot

Connect the USB connector to a machine of your choosing. Using 'dmesg' should show you which /dev/ttyUSB<x> the device has connected to. I used minicom or screen to connect to the port at '115200,8,N,1'.

Start the stora. You should see the boot messages, when it says 'Press a key to interrupt autostart', press a key!

Various parameters need to be set, these ones can be/need to be saved. setenv serverip <ip of tftp server> setenv ipaddr <ip of stora> setenv mainlineLinux yes setenv arcNumber 2734 saveenv reset

## Preparing the emdebian/debian file system

I couldn't be bothered using NFS and all that, my plan is to just boot a kernel from tftp and use a local filesystem. Get the emdebian root file system, and unpack it onto a SATA disk, with ext3 (if you want you can have a swap-partition. You'll need to modify the following files(on the unpacked file system):

/etc/fstab comment out the root file system and replace it with the partition with the unpacked file-system, on mine it was /dev/sdb2 (I left the original disk in place, and the swap was the first partition) so the line looked like:
```
/dev/sdb2 / ext3 defaults 1 1
```
/etc/inittab The inittab file was not configured correctly to put a console/terminal on the serial port, so this needs to be modified. Find the lines that look like the below, and make them exactly like it!

```
# Example how to put a getty on a serial line (for a terminal)
#
T0:23:respawn:/sbin/getty -L ttyS0 115200,9600 vt100
#T1:23:respawn:/sbin/getty -L ttyS1 9600 vt100
```

You can also comment out the 7 getty lines above, AFAIK they will do exactly nothing on a machine without a keyboard mouse etc.

I also edited /etc/network/interfaces, so the interface uses DHCP. A static would probably be a good idea, this was just easier. You will need to change the IP. I haven't found the need to add the MAC address.

If you can chroot into the image, you can set the root passwd. Otherwise put an ssh key in place.

## Stick the kernel somewhere

On a machine on your local LAN configure your tftpd server (apt-get install atftpd) Copy the uImage.mainline out of the repository, into /var/lib/tftpboot/

Step 4: Stick the drive in the machine, start it, and access uBoot

to load the kernel off of the tftp server:

tftpboot 0x800000 uImage.mainline

setenv bootargs $(console) root=/dev/sdb2

bootm 0x800000

et voila!!

Next: Trying to boot the debian kirkwood kernel. 