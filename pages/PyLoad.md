# PyLoad

[pyLoad](http://pyload.org/) is a fast, lightweight and full featured download manager for many One-Click-Hoster, container formats like DLC, video sites or just plain http/ftp links. It aims for low hardware requirements and platform independence to be runnable on all kind of systems (desktop pc, netbook, NAS, router).

Despite its strict restriction it is packed full of features just like webinterface, captcha recognition, unrar and much more.

**Warning:** I have tested the installation procedure on a GoFlex Home. It may not work on the Stora (although it should). If this is the case, you may try to install pyload following the instructions in the original [thread](http://www.openstora.com/forum/viewtopic.php?p=1642#p1642)

To install pyLoad on the Stora or GoFlex Home follow the tutorial in Installation of [PyLoad on Optware](http://nas-tweaks.net/220/installation-of-pyload-on-optware/). I have summarized the steps here:
```
ipkg install python py25-crypto py25-curl libcurl py25-openssl py25-django tesseract-ocr unzip \ 
sqlite wget-ssl py25-pil unrar ossp-js
cd /opt/
wget http://get.pyload.org/get/src/
unzip pyload-src*.zip
rm pyload-src-*.zip
cd /opt/pyload/
python pyLoadCore.py --changedir --configdir=/opt/pyload/.pyload
```
Press Enter twice.
```
python pyLoadCore.py -s
```
Then answer to the questions. You have to choose username and passwords for the CLI and Web interfaces and select a download folder. [ULI](http://nas-tweaks.net/220/installation-of-pyload-on-optware/) also provided with the output of his installation just in case you need it.

Finally create a starter script: copy the following script to /etc/init.d/pyload
```bash
#!/bin/sh
# Pyload daemon
#
# chkconfig: 345 99 30
# description: pyLoad is a fast, lightweight and full featured \
#              download manager for many One-Click-Hoster, \
#              container formats like DLC, video sites or just \
#              plain http/ftp links
#
# Initially developed by ULI 
# http://nas-tweaks.net/220/installation-of-pyload-on-optware/
#

export PATH=/opt/bin:/opt/sbin:/sbin:/bin:/usr/sbin:/usr/bin
COMMAND="python pyLoadCore.py --configdir=/opt/pyload/.pyload"

start() {
    echo "Starting PyLoad"
    cd /opt/pyload
    $COMMAND --daemon
}

status() {
    echo -n "Status of PyLoad: "
    cd /opt/pyload
       STATUS=`$COMMAND --status`
       if [ $STATUS == "False" ] ; then
               echo "Not running"
       else
               echo "Running as PID $STATUS"
       fi
}

stop() {
    echo "Stopping PyLoad"
    cd /opt/pyload
    $COMMAND --quit
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    restart)
        stop
        sleep 4
        start
        ;;
    status)
        status
        ;;
    *)
        echo "Usage: $0 (start|stop|restart|status)"
        exit 1
        ;;
esac
exit 0
```

Then make the script executable and start the service
```
chmod +x /etc/init.d/pyload
chkconfig pyload on
```
Also, you can use
```
service pyload start 
service pyload stop
service pyload status
```
to start, stop and see the status of the service, respectively.

After starting the service you can access the pyLoad interface at http://your.device.ip:8000

Original thread on this is [here](http://www.openstora.com/forum/viewtopic.php?f=1&t=232) -- J.M. 18:06, 17 February 2011 (UTC) 