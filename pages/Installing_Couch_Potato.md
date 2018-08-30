# Installing Couch Potato

Installing Couch Potao This guide will assume the following:

- You have installed a package manager (IPKG)
- You have your PATH variable set correctly
- You do not yet have Python installed
- Your want to install Couch Potato in /opt/bin/couchpotato
- You are logged in as root, use sudo where necessary if this is not the case or sudo -E -s to become root


### Install Python2.6:
```
ipkg install python26
```
(note. this may take a while be patient ie. 10-20 minutes)

### download and install Cherrypy:
```
wget http://download.cherrypy.org/cherrypy/3.2.0/CherryPy-3.2.0.tar.gz
tar -xvf CherryPy-3.2.0.tar.gz
cd CherryPy-3.2.0
python setup.py install
cd ..
```
### Installing Cheetah:
```
ipkg install py25-cheetah
```
Installing unZip (should be installed per default, you may skip):
```
 ipkg install unzip
```
### Download Couch Potato:
```
wget --no-check-certificate https://github.com/RuudBurger/CouchPotato/zipball/master
```
### Install couch potato (your filename might differ, so replace the df88048 with your according RuudBurger-CouchPotato-XXXXXXX.zip):
```
unzip RuudBurger-CouchPotato-df88048.zip
mv RuudBurger-CouchPotato-df88048 /opt/bin/couchpotato
cd /opt/bin/couchpotato
/opt/bin/python2.6 CouchPotato.py
```
You should now be able to connect to couch potato through your browser of choice: http://stora:5000 (replace stora with IP address if DNS doesn't resolve) Now would be a good time to update your Couch Potato, do that by clicking the bottom link reading: Version: UNKNOWN Build. , Check for new version

### Optional: Run Couch Potato on boot:

Install nano (if you don't already have it, or use vi):
```
ipkg install nano
```
Add Couch Potato to start on boot:
```
nano /etc/rc.local
```
You should edit a file looking similar to
```
#!/bin/sh
#
# This script will be executed *after* all the other init scripts.
# You can put your own initialization stuff in here if you don't
# want to do the full Sys V style init stuff.

touch /var/lock/subsys/local
/sbin/ldconfig
/sbin/telnetenabled
/usr/bin/ubifs_repair.sh
/opt/bin/python2.6 /opt/bin/couchpotato/CouchPotato.py -d
```
Add the following line:
```
nice -n 15 ionice -c2 -n6 /opt/bin/python2.6 /opt/bin/couchpotato/CouchPotato.py -d
```
(note. that i have added nice and ionice levels to my CP to prevent it eating up too many resources, you don't need that)

Reboot your Stora and you are done.
```
reboot
```
--Deandre 01:57, 4 December 2011 (UTC) This was first posted here:

http://www.openstora.com/phpBB3/viewtopic.php?f=1&t=1009&p=6579&hilit=Couchpotato#p6579
