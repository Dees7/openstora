## Root Access through the serial console

Author: Bryan McGuire

There's a second way to get root access on the Stora, You can do it by using the Stora's built-in serial console.

On the front of the Stora's board *(the side with the power and reset button, not the side with the sata slots)* is a 4-pin header.
Required Items

- Netgear Stora unit
- RS232 to serial converter (must work with cmos level serial devices)
- Terminal Emulator

Pin out
Stora serial Pin|Out Pin| Function| Notes
----------------|-------|---------|------
1| 3.3Vdc | Pin is the farthest from the battery, and should contain a white stripe.
2| RXD    | Recieve Data line from Stora.
3| TXD    | Transmit Data line from Stora.
4| GND    | Ground.

You will need to [acquire](http://www.usconverters.com/?gclid=CLiU5NjCn6ECFdFW2god0iyUxA) or [build](http://www.lammertbies.nl/comm/info/RS-232_null_modem.html) a [RS232](http://en.wikipedia.org/wiki/RS-232) converter.

## Getting Connected

The page for getting into the serial port for the nslu2 gives many options for making such a converter. Beware that pins 2 and 3 are reversed from that of the slug.

After you've connected the Stora to your computer's serial port, run your favorite terminal program.

Set your baud to 115200 Data bits to 8, Parity to None, and stop bits to 1.

Once you get the Linux login prompt, you can login using your regular account credentials as you do on mystora.com.

After you have a command line as your regular user, you can get superuser privileges by typing **sudo -E -s**. As it turns out, regular users are in the Stora's sudoers file.

## Notes

I personally used the Signalyzer Lite for my serial converter because it does double duty as a JTAG to usb interface. At under $40, you'll not find a cheaper JTAG to usb adapter anywhere.

## Arduino as TTL serial port

Guide by CableCat.

It is possible to use a Arduino board as a TTL serial port.

- Remove the chip.
- Connect ground to ground.
- Connect Auduino pin 0 to Stora pin 3.
- Connect Auduino pin 1 to Stora pin 2.
