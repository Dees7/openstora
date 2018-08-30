# Hamachi for Stora

Original forum post [here](http://www.openstora.com/forum/viewtopic.php?f=1&t=504)

## Preparation

You need:

- [Easy Root Access](Easy_Root_Access.md)
- You may need to [Modify Stora Firewall](Modify_Stora_Firewall.md)
- Download and unpack the [Arm Hamachi Package](http://files.hamachi.cc/linux/nokia-770/)


Connect to your Stora through ssh and upload unpacked hamachi archive to /tmp Follow the readme and install it (`sudo ./install` in hamachi install dir)

## Installation

Move your hamachi archive into the stora, and navigate to the file using SSH

The following commands move the file to the /tmp archive and then unzips it, then installs it.
```
mv ARCHIVE_NAME /tmp
cd /tmp
tar -xvf ARCHIVE_NAME
cd /tmp/UNPACKED_NAME
sudo ./install
```

## Setup

- Run tuncfg:
```
sudo PATH=/sbin:$PATH tuncfg
```
- Create libcrypto library symlink:
```
sudo ln -s /lib/libcrypto.so.0.9.8b /lib/libcrypto.so.0.9.7
```
- Then run
```
hamachi-init
hamachi start
hamachi login
```

If you got "Login failed" message - there could be 2 reasons: disable firewall on Stora - [Modify Stora Firewall](Modify_Stora_Firewall.md) or tuncfg cannot reach ifconfg properly (/sbin PATH variables). You should enable hamachi log by running:
```
echo "LogFile ~/.hamachi/log" >> ~/.hamachi/config
echo "DebugLog yes" >> ~/.hamachi/config
hamachi stop; hamachi start
```

- Optional set nickname by
```
hamachi set-nick YOUR_NICK
```

- Connect to your existing hamachi network - if you don't have a network, see below
```
hamachi join YOUR_NETWORK
```
- and go online
```
hamachi go-online YOUR_NETWORK
```
- If you don't have a network to connect to, you can create one
```
hamachi create NETWORK_NAME PASSWORD
```
e.g.
```
hamachi create example_network password
```
thats all, now your Stora appears in all your computer worldwide with logmein hamachi2 installed like at home.

If you have "zombie" (reconencting) issues in official Logmein Hamachi2 Client, add to ~/.hamachi/config (on Stora - create if not exist) following:

KeepAlive 10

# Getting Hamachi to Start on Startup

Based on ubuntuforums wyth's post: [here](http://ubuntuforums.org/showpost.php?p=3364904&postcount=1) Create the start-up script for hamachi, as shown below.
```
vi hamachi
```
Change the user name "your name" (line 5) to your user name. Call the script "hamachi."
```bash
#!/bin/bash
#chkconfig: 235 90 90
#description: hamachi init script
###################################
### Start-up script for Hamachi ###
###################################
USER=YOUR NAME
case "$1" in
start)
/sbin/tuncfg
/bin/su - $USER -c "hamachi start"
;;
stop)
/bin/su - $USER -c "hamachi stop"
;;
restart|force-reload)
/bin/su - $USER -c "hamachi start"
/bin/su - $USER -c "hamachi stop"
;;
*)
exit 1
;;
esac
exit 0
```

- Make the script executable:
```
chmod +x hamachi
```

- Move the script to /etc/init.d/ directory:
```
sudo mv hamachi /etc/init.d
```

- finally, add hamachi service
```
sudo chkconfig --add hamachi
```
voila... you should test it by running
```
sudo reboot
```