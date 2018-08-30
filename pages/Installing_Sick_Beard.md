# Installing Sick Beard

This guide will assume the following:

- You have installed a package manager (IPKG)
- You have your PATH variable set correctly
- You do not yet have Python installed
- Your current working directory is where you want to install Sick Beard
- You are logged in as root, use sudo where necessary if this is not the case or sudo -E -s to become root


### Install Python:
```
ipkg install python
```
### Download and install Cherrypy:
```
wget http://download.cherrypy.org/cherrypy/3.2.2/CherryPy-3.2.2.tar.gz
tar -xvf CherryPy-3.2.2.tar.gz
cd CherryPy-3.2.2
python setup.py install
cd ..
```

### Install Cheetah
```
ipkg install py25-cheetah
```
Grab Sick Beard
```
wget --no-check-certificate https://github.com/midgetspy/Sick-Beard/tarball/master
```
The file name will differ depending upon the latest version, so replace XXXXXXX with the appropriate text:
```
tar -xvf midgetspy-Sick-Beard-XXXXXXX.tar.gz
mv midgetspy-Sick-Beard-XXXXXXX sickbeard
cd sickbeard
python SickBeard.py
```
You should now be able to connect to sickbeard, http://stora:8081 (replace stora with IP address if DNS doesn't resolve)

To make Sick Beard run on boot add the following to /etc/rc.local
```
/home/opt/bin/python2.5 /pathtosickbeard/SickBeard.py --quiet &
```
--Apostasy 09:18, 10 June 2011


## To install SickBeard to run as a different user

First off we need to change the ownership of the sickbeard folder to be STORA_USER, an the unprivileged user you want to run SickBeard as (typically one of your Stora's user)
```
sudo chown -R STORA_USER:STORA_USER sickbeard
```

Then we need to start your favourite editor (nano in my case)
```
nano -w sickbeard.sh
```

Then paste in this little lot (assuming your sickbeard directory is /home/STORA_USER/sickbeard otherwise you'll need to change that)
```
#!/bin/bash
#chkconfig: 2345 30 14
#description: sickbeard init script
### BEGIN SCRIPT INFO
# Provides: Sick Beard Start/Stop
# Copied by Ainer.org from http://forums.sabnzbd.org/index.php?topic=2013.0
# Modified by Ainer on various dates in 2010
# Updated for Netgear Stora by Norm for OpenStora user forums
### END SCRIPT INFO
#Required & Must Be Changed
USER="STORA_USER" #Set to a Stora username here.
#Required but Defaults Provided (only change if you know you need to).
HOST="127.0.0.1" #Set Sick Beard address here.
PORT="8081" #Set Sick Beard port here.
#Script -- No changes needed below.
case "$1" in
start)
#Start Sick Beard.
cd /home/$USER/sickbeard
echo "Starting Sick Beard"
/bin/su - $USER -c "/home/opt/bin/python2.5 /home/$USER/sickbeard/SickBeard.py --quiet &"
;;
stop)
#Shutdown Sick Beard.
cd /home/$USER/sickbeard
echo "Stopping Sick Beard"
wget -q --delete-after "http://$HOST:$PORT/home/shutdown/"
sleep 6s
;;
*)
echo "Usage: $0 {start|stop}"
exit 1
esac
exit 0
```

Save this. Now you need to chmod it..
```
chmod +x sickbeard.sh
```

Copy to /etc/init.d
```
cp sickbeard.sh /etc/init.d/sickbeard
```

Lets check and see if your SickBeard script runs.
```
/etc/init.d/sickbeard start
/etc/init.d/sickbeard stop
```

Then you are already to add it to your Stora's start up services.
```
/sbin/chkconfig --add sickbeard
/sbin/chkconfig --levels 2345 sickbeard on
```

Reboot your Stora and you are done.


Original forum post [here](http://www.openstora.com/phpBB3/viewtopic.php?f=1&t=1010)

--Norm 12:58, 2 July 2011 (UTC)