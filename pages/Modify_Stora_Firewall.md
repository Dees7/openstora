# Modify Stora Firewall
## Stora Firewall

Stora has an active Firewall by default (iptables), all incoming connections (except the ones to ports 20,21,22,80,443) are blocked by default if coming from an ip outside your LAN, so even if you map and forward ports on your router, those ports will still be closed, unless you deactivate the firewall or open that port explicitly via iptables on the Stora.

## Disable Stora Firewall

To deactivate the firewall you need to edit /etc/init.d/oe-bootfinish, go to line 114 and comment out, or delete, this line:
```
/etc/init.d/iptables ifup > /dev/null 2>&1
```
then, to be on the safe side, do a:
```
sudo chmod -x /etc/init.d/iptables
```
now you need to clear the current iptables rules, you can simply reboot the Stora or delete them manually using:
```
sudo /sbin/iptables -F
```
## Open Additional Ports on Stora Firewall

To open additional ports you will need to edit /etc/iptables/config where you can add both TCP and UDP ports through new variables:

- TCP_OPEN_PORTS_EXT
- UDP_OPEN_PORTS_EXT

Each of these can be used to define a list of ports to be opened to incoming traffic from outside your LAN. For example if you've installed the transmission bittorrent client so the web interface (rpc-port) is on TCP port 5424, and incoming torrent connections (peer-port) are on TCP/UDP port 5425 then you would add these entries to the iptables config file:
```
TCP_OPEN_PORTS_EXT="5424 5425"
UDP_OPEN_PORTS_EXT="5425"
```
To activate these settings you need to restart the iptables firewall:
```
sudo /etc/init.d/iptables restart
```
This is just a very basic customisation of the firewall rules. If you have a look at the /etc/init.d/iptables script you will see there's a lot of scope for more advanced customisations.

--Pippone 13:03, 7 May 2010 (UTC) 