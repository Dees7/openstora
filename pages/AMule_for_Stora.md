## aMule for Stora 2.2.6

aMule version 2.2.6 compiled to work on the original Netgear Stora firmware. I included amuled, the amule daemon that runs in background and amuleweb, which is a webserver enabling web access to the daemon through External Connections. Both binaries are quite big (~2MB each) so it's up to you if you want to place them somewhere outside the NAND, keep in mind you'd have to change some variables if you want to do that.

## HOWTO INSTALL

- un-tar-gz the amuled.tar.gz

```
   tar xvfz amuled.tar.gz


- move "amuled" and "amuleweb" under /usr/bin (or wherever you like)

```
   mv amuled /usr/bin
   mv amuleweb /usr/bin


- move "init.d/amuled" to /etc/init.d, this is the starting script

```
   mv init.d/amuled /etc/init.d


- run amuled as regular user for the first time to create config files, directories and enable External Connections

```
   su - <regular_user> #change to regular user if you're root
   amuled -e
   # choose a password for external connections
   # hit ctrl+c


- run amuleweb for the first time (as regular user) to create config file

```
   amuleweb -w


- now you should have all the configuration files under /home/<your_user>/.aMule which is not bad since /home is on the hard disk, not on flash, you can edit amule.conf to suite your needs (you may want to change IncomingDir), also copy down the value for ECPassword, which is the MD5 encrypted password you entered earlier, you'll need it for the amuleweb.conf

- move the webserver directory under the newly created .aMule directory

```
   mv webserver /home/<your_user>/.aMule/


- open /home/<your_user>/.aMule/remote.conf, this is the config file for the web interface, you need to change some values:

```
  - Password= (the password amuleweb uses to connect to amuled)
              paste the ECPassword you copied from amule.conf
  - AdminPassword= (the password you'll use to connect to the web interface)
                   I used the same password above, if you want to use different one you'll need to md5 encrypt it before placing it into the config.
  - Template= (the skin used for the web interface)
              I strongly suggest you to replace the "php-default" with "litoral" which is way much better than the "php-default" skin.


Other skins you can use are: "default" and "chicane".

- I included a basic server.met downloaded from [here](http://www.server-met.de/) you can download a new one if you wish, you then need to move it under the .aMule directory

```
   mv server.met /home/<your_user>/.aMule/


- test if everything works: run amuled in foreground, open a new shell and run amuleweb, then try to connect to http://<stora.ip>:4711/

```
   amuled -i -o (make sure you're a regular user)
   # Open a new ssh session
   amuleweb (once again, make sure you're a regular user)


Then try to connect to http://<stora.ip>:4711/ you should be asked for a password, enter it and you're done! If anything goes wrong, check both the ssh windows and see if there are errors logged.

- once everything works, edit the /etc/init.d/amuled to suite your needs, you may want to change the DAEMON_USER and WEB_USER to one of your regular users and add the path where you put amuled and amuleweb if you used a custom one editing the PATH variable.

- try if everything works by issuing:

```
   /etc/init.d/amuled start
   /etc/init.d/amuled status
   /etc/init.d/amuled stop


as root user.

- if everything is ok, and you want to do so, add amuled to the startup scripts:

```
   chkconfig --add amuled
   chkconfig --levels 2345 amuled on


That's it!

Forum thread to post feedbacks: [here](http://www.openstora.com/forum/viewtopic.php?f=1&t=94)

Download link: [here](http://www.openstora.com/files/albums/uploads/amuled_tar_35.gz)

NOTE: If you don't forward ports on your router (4662 tcp and 4672 udp by default) your download will be really slow and it will take a while to get connected! If you whish to change these ports, you'll have to edit the amule.conf. Remember Stora has a firewall that closes all the incoming connections, you may want to modify [this](Modify_Stora_Firewall.md).

--Pippone 07:31, 28 April 2010 (UTC) 