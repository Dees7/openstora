# Installing Squeezebox Server

Howto install Slimserver/Squeezebox Server/Logitech Media Server (currently 7.3.3) on the Stora
(please note that v7.7.2 now possible - refer forum)

## Requirements:

Root access You should have an up-and-running IPKG package manager, if you don't have that follow these instructions: [Installing_a_package_manager](Installing_a_package_manager.md).

- Install the squeezecenter package
```
sudo ipkg install squeezecenter
```
- Start squeezecenter
```
sudo /opt/etc/init.d/S99squeezecenter start
```
- Connect to your squeezecenter through your web browser with 'http://\<STORAIP\>:9000'
- Enter all the relevant data into squeezecenter to set up your login

## Troubleshooting:
Check if your mysql database is up and running, `sudo /opt/etc/init.d/S70mysqld start`