# Root Access via TelnetEnabled
## Root Access via TelnetEnabled

There's one more easy way to get root access to the Stora, this time by enabling the telnet daemon that is running by default on each device. How to do that:

- Download the TelnetEnabler file/script, .exe version [here](http://www.openstora.com/files/albums/uploads/Windows_telnetEnable.rar) if you have Windows or python script [here](http://www.openstora.com/files/albums/uploads/Perl_netgear-telnetenable-0_1_39.zip) for all other platform (of course you can use the python script even on Windows, but that requires Python interpreter).
- Get your Stora ip and mac address (the mac address can be obtained using `arp -a` or looking behind the Stora, there's a sticker with the MAC address printed.

**NOTE:** the STORA_MAC_ADDRESS must be written without columns or hyphens, like this: 0011ADBC2233

- Run the .exe or the python script with the following options:
```
For the Windows version: telnetenable.exe <STORA_IP> <STORA_MAC_ADDRESS> Gearguy Geardog
For the python version: python telnetenable.py <STORA_IP> <STORA_MAC_ADDRESS> Gearguy Geardog
```
```
EXAMPLE: telnetenable.exe 192.168.1.100 001122ABCDE4 Gearguy Geardog
```
- If you did all this correctly, telnet should be enabled on the stora allowing you to just "telnet STORA_IP" and get root access.

From that point on, follow the tutorial on the wiki or on the forum to get the best from your Stora!

Forum post: [here](http://www.openstora.com/forum/viewtopic.php?f=1&t=142)

--Pippone 08:08, 4 January 2011 (UTC) 