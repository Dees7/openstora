# Transmission torrent client
## Transmission Daemon v1.92 for ARM (Netgear Stora)

Transmission is a bittorrent client for peer-to-peer filesharing. [more information](http://trac.transmissionbt.com/wiki/ConfigurationParameters)


## How to install

- Download the [transmission.tar.gz](http://www.openstora.com/files/displayimage.php?album=6&pid=21#top_display_media) and move it to the Stora, either using scp or dropping it into a network share from your computer.
- Move to the directory where you put it and uncompress it with:
```
tar xvfz transmission.tar.gz
```
- Now you need to move the extracted files, it is suggested that you:

place the binary `transmission-daemon` under `/usr/local/bin`

place the entire `web` directory under `/usr/share/transmission/` (so that /usr/share/transmission/web/index.html can be called by transmission while using the web interface)

place the init script `transmission-daemon` under `/etc/init.d/` The following instructions assume that you are using these locations. If you decide to use different locations, alter the commands accordingly.

- *IMPORTANT!* If you use an SFTP client/interface to move the files, make sure that you have changed the permissions on the files/directories. Many clients do not make them executable by default:
```
sudo chmod 0755 /usr/local/bin/transmission-daemon
```
- Edit the `/etc/init.d/transmission-daemon` to suit your needs, you need to edit at least:

`TRANSMISSION_HOME`, to the place where you want to keep your config files `DAEMON_USER`, to the unprivileged user you want to run transmission as (typically one of your Stora's user)

By default, `TRANSMISSION_HOME=/home/.config/transmission-daemon`

- **IMPORTANT!** Create the `TRANSMISSION_HOME` and change the ownership to `DAEMON_USER` (of course, change `$TRANSMISSION_HOME` and `$DAEMON_USER` to your actual values):
```
sudo mkdir -p $TRANSMISSION_HOME
sudo chown -R $DAEMON_USER:$DAEMON_USER $TRANSMISSION_HOME
```
- Start the daemon for the first time and verify that it is actually running, then kill the daemon because you need to edit the just created config file before using it:
```
sudo /etc/init.d/transmission-daemon start
ps -ef |grep transmission-daemon
sudo /etc/init.d/transmission-daemon stop
```
- Go under your `$TRANSMISSION_HOME` and edit the `settings.json` file.

If the file confuses you, reference [this](http://trac.transmissionbt.com/wiki/ConfigurationParameters) web page if you need help with options.

**IMPORTANT:** if transmission ran but the file `settings.json` was not created in the `$TRANSMISSION_HOME` directory, then the directory likely has a permissions problem or it is not owned by `$DAEMON_USER`

**VERY IMPORTANT:** set the `download-dir` parameter somewhere under `/home`, you don't want to download large files on your **Stora NAND**!

- Finally start the daemon again:
```
sudo /etc/init.d/transmission-daemon start
```
- You should be able to connect to http://<your_stora_ip>:9091/ and see the nice web interface

**IMPORTANT:** if you get an error on your web browser interface,

- If everything seems to be alright, and of course you wish to, set transmission-daemon to start automatically when you power on your Stora:
```
sudo /sbin/chkconfig --add transmission-daemon
sudo /sbin/chkconfig --levels 2345 transmission-daemon on
```

That's it!


**NOTE:** you may want to [modify the firewall](Modify_Stora_Firewall.md) in order to allow bittorrent connection to the Stora (and download faster)
**STARTUP NOTE:** In case your transmission-daemon doesn't start up after reboot, check:
```
chkconfig --list | grep transmission-daemon
```
If all columns are turned off then try to [disabling Access Patrol](Disabling_Access_Patrol.md) If you succeeded in it then run again
```
sudo /sbin/chkconfig --levels 2345 transmission-daemon on
```
and reboot to check if there is a result.

Another workaround, if you don't want to disable access patrol is to rename the init script:
```
sudo /sbin/chkconfig --del transmission-daemon
sudo mv /etc/init.d/transmission-daemon /etc/init.d/transmissiond
sudo /sbin/chkconfig --add transmissiond
sudo /sbin/chkconfig --levels 345 transmissiond on
```
Original Forum post: [here](http://www.openstora.com/forum/viewtopic.php?f=1&t=83)

Download: [here](http://www.openstora.com/files/displayimage.php?album=6&pid=21#top_display_media)


--Pippone 07:13, 3 August 2010 (UTC)

## (Optional) Configure watched torrent folder

The watched torrent folder is a folder where you can put torrentfiles that will automatically be added to download queue.

To configure this, you will have to add two attributes to your settings.json, watch-dir and watch-dir-enabled. First off you need to stop the transmission-daemon, if you don't the changes will be overwritten.
```
sudo /etc/init.d/transmission-daemon stop
```
Now create a folder in your share resources, ex under MyComputers. To be honest I don't know if this will work if you don't change the ownership of the folder to the `$DAEMON_USER`, I did it by habit :) You might also have to change read and write access to the folder, the example gives full access.
```
cd ~
cd MyComputers
sudo mkdir myTorrentFolder
sudo chown -R $DAEMON_USER:$DAEMON_USER myTorrentFolder
sudo chmod 777 myTorrentFolder
```
Now go to your `$TRANSMISSION_HOME` folder and open `settings.json` in your favorite editor. You can add this anywhere in the file, within the {}, I added it in the end of the file :
```
"watch-dir":"\/home\/<user>\/MyComputers\/myTorrentFolder",
"watch-dir-enabled":true
```
Once done, just start the transmission-daemon
```
sudo /etc/init.d/transmission-daemon start
```
And there you have it. When you put a .torrent file in `myTorrentFolder` transmission will automatically add it to the download queue.

Why do this? If you want to be able to add torrents from outside of your network but for security reasons don't want to be able to access the transmission gui, this is a great way, where you can add torrents using ftp.

Original Forum post: [here](http://www.openstora.com/forum/viewtopic.php?f=1&t=83)

--Gralov 15:01, 7 June 2010 (UTC) 
