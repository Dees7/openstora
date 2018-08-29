# USB Serial Adapter

If you want to use a USB Serial Adapter with your Stora you will need to load the appropriate kernel module. These instructions are for a stock Stora running the "2.6.22.18-Netgear" kernel.

- Log in to your Stora as root.
- Run command "uname -r" to verify kernel version "2.6.22.18-Netgear"
- Download usbserial (generic, pl2303 and mct_u232) [kernel modules](http://www.openstora.com/forum/download/file.php?id=6)) for the stock Stora firmware (2.6.22.18-Netgear) to root "/"
- Uncompress it with: "tar xvfz kernel_usbserial_modules.tar.gz", this should put everything inside the right directory (/lib/modules/2.6.22.18-Netgear/kernel/driver/usb/serial)

## Install Kernel Module

- Run command "depmod -a" to refresh the available kernel modules
- Connect the usb serial adapter it should get recognized automatically run command "dmesg" to confirm. Look for something similar to the following:

```
usb 1-1: new full speed USB device using ehci_marvell and address 4
usb 1-1: configuration #1 chosen from 1 choice
usbcore: registered new interface driver usbserial
drivers/usb/serial/usb-serial.c: USB Serial support registered for generic
usbcore: registered new interface driver usbserial_generic
drivers/usb/serial/usb-serial.c: USB Serial Driver core
drivers/usb/serial/usb-serial.c: USB Serial support registered for MCT U232
mct_u232 1-1:1.0: MCT U232 converter detected
usb 1-1: MCT U232 converter now attached to ttyUSB0
usbcore: registered new interface driver mct_u232
drivers/usb/serial/mct_u232.c: Magic Control Technology USB-RS232 converter driver z2.0
```

- The line to look for is this one:
```
usb 1-1: MCT U232 converter now attached to ttyUSB0
```
You can now access your serial port at /dev/ttyUSB0

## Enable Normal User Access To Port

By default only root will have access to the port. In order to allow a normal user to access port you will need to change permissons on the port.

- Run command "chmod 666 /dev/ttyUSB0" Change to match your port identified in dmesg.

Special thanks to pippone who compiled the modules and provide the instructions.
Reference: http://www.openstora.com/forum/viewtopic.php?f=1&t=465
--Hossman 04:00, 12 November 2010 (UTC) 