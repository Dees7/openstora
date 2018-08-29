# Adding an Internal USB Hub
Using this hub from dealextreme:

http://www.dealextreme.com/details.dx/sku.13526

Get some crimp pins from a site like this:

http://www.tmbelectronics.com/index.php?main_page=product_info&cPath=26_28_30&products_id=194

snip the plugs off the non-power input plug, and 1 of the output sockets, and crimp on the pins, then connect like so to the 14 pin connectors on the main board and the USB board.

Looking down the stora (with it the right way up!) And looking at the top of the USB board with the USB port away from you.

```
o       | o        | o           | o           | o            | o           | o
USB 5V  | USB Gnd  | USB Data +  | LED D4 Red  | LED D3 Blue  | LED D5 Red  | LED Common
red     | black    | green       |             |              |             |
--------|----------|-------------|-------------|--------------|-------------|-----------
o       | o        | o           | o           | o            | o           | o
USB GND | USB 5V   | USB Data -  | GND         | LED D4 Green | GND         | LED D5 Green
        | white    |             |
```

I am trying to identify the other pins as well, feel free to label them....

if you have a proper crimp tool for the pins, it is possible to coax the existing pins out of the connector and replace them.

Obviously if you break something doing this, then don't come crying to me. Or Netgear. Or anyone. 