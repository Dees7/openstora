# All-In-One Installer Script

The idea behind this page is to create a script which everyone can use to help automate the customisation/installation of software on their stora.

## Screenshots

Stora v3-1.png

Stora v3-2.png

For those with any scripting/programming skills it should be fairly straightforward to add additional functions + programs to script. For those without, it should be fairly simple to use the script :)

## Instructions

You will need to ssh into your stora, as pastebin seems to use windows based EOL we have to convert them to unix ones too.

```
-bash-3.2$ wget -O ./storaAIO.sh.dos http://pastebin.com/raw/QDjSFtTT
-bash-3.2$ sed 's/\r$//' ./storaAIO.sh.dos > ./storaAIO.sh
-bash-3.2$ rm ./storaAIO.sh.dos
-bash-3.2$ chmod +x ./storaAIO.sh
-bash-3.2$ sudo ./storaAIO.sh
```

Choose an option from the menu.

## Collaboration

If you wish to add extra functionality to the script, please do so, but make sure it's been tested on your stora before updating the wiki :)

## Script

```
#!/bin/sh
######################################################
# storaSetup.sh
# 
# v0.03 (26.11.2010)
#
###################################################### 
# Changelog:
#
# 0.01 - Initial Version
# 0.02 - Uses functions, new menu system
# 0.03 - Added Transmission
#
######################################################
# variable setup
temp="/tmp"                             ; # this is where temporary files are downloaded to
nzb_home="/home/nzbget"                 ; # this is where nzbget will setup it's files
nfs_kernel_check="2.6.22.18-Netgear"    ; # minimum version needed for NFS
TEXT=""
# package locations
     SSHD_LOCATION="http://www.streetster.co.uk/dl/stora/sshd.tar.gz"
     IPKG_LOCATION="http://ipkg.nslu2-linux.org/feeds/optware/cs08q1armel/cross/stable/ipkg-opt_0.99.163-10_arm.ipk"
      NFS_LOCATION="http://www.streetster.co.uk/dl/stora/nfs-utils_STORA.tar.gz"
NZBGETWEB_LOCATION="http://www.streetster.co.uk/dl/stora/nzbgetweb-1.4.zip"
  NZBGETD_LOCATION="http://www.streetster.co.uk/dl/stora/nzbgetd"
   RPCLIB_LOCATION="http://www.streetster.co.uk/dl/stora/xmlrpc-2.2.2.tar.gz"
TRANSMISSIOND_LOCATION="http://www.streetster.co.uk/dl/stora/transmission-daemon"

# You should not need to edit below this line...
######################################################
#                   FUNCTIONS                        #
######################################################
######################################################
# drawMenu - user front-end
######################################################
drawMenu() {
# clear the screen
clear
cat <<MENU_END
  
  ######################################################
  # Welcome to the stora all-in-one script...
  ######################################################
  
  What would you like to do:
  
  1) Setup the PATH variable/environment
  2) Swap the SSH Daemon with a clean version
  3) Install the IPKG Package Manager
  4) Move /opt to the harddrive
  5) Install Kernel NFS
  6) Remove Access Patrol
  7) Install NZBget
  8) Install NZBget-server
  9) Install Transmission
  
  X. eXit
  
  Option: _
  
  $1
  
MENU_END
tput cup 19 10
read selection
case $selection in
    1) setPath;             checkIt;; 
    2) replaceSSHd;         checkIt;;
    3) installIPKG;         checkIt;;
    4) moveOpt;             checkIt;;
    5) installNFS;          checkIt;;
    6) disableAP;            checkIt;;
    7) installNZBget;       checkIt;;
    8) installNZBgetServer; checkIt;;
    9) installTransmission; checkIt;;
    X|x|Q|q) tput sgr0; echo ""; exit 0   ;;
    *) TEXT="Error: Invalid Selection";   ;;
esac
}
######################################################
# checkIt - displays message after function runs
######################################################
checkIt() {
    if [ "$?" -eq "0" ]
    then     
        echo "OK."
    elif [ "$?" -eq "0" ]
    then
        echo "Download Failed."
    else
        echo "Failed."    
    fi
    echo ""
    echo "Hit ENTER to continue..."
    read blah    
}
######################################################
# setPath - sets up the users /etc/environment
######################################################
setPath() {
    clear
    echo "Setting up PATH variable..."    
    # return value
    ret="0"
    
    # setup new path variable
    NEW_PATH="/usr/local/bin:/usr/bin:/bin:/opt/bin:/usr/sbin:/opt/sbin:/usr/sbin:/sbin"
    #export the path too
    export PATH=$NEW_PATH
    
    # set the environment
    echo "PATH=$NEW_PATH" > /etc/environment
    # return
    return $ret
}
######################################################
# replaceSSHd - replaces SSH daemon with clean version
######################################################
replaceSSHd() {
    clear
    echo "Replacing SSH Daemon..."
    
    # return value
    ret="0"
    
    # download
    wget -O $temp/sshd.tar.gz $SSHD_LOCATION  
    # check file exists
    if [ -s $temp/sshd.tar.gz ]
    then
        # extract
        tar -xzf $temp/sshd.tar.gz -C $temp/
        # backup original
        cp /usr/sbin/sshd /usr/sbin/sshd.original
        chmod -x /usr/sbin/sshd.original
   
        # replace and make executable
        mv $temp/sshd /usr/sbin/sshd
        chmod +x /usr/sbin/sshd
   
        # restart daemon
        /etc/init.d/sshd restart
        # clean-up
        rm $temp/sshd.tar.gz
    else
        ret="1"
    fi
    
    # return    
    return $ret
}
######################################################
# installIPKG - installs the IPKG package manager
######################################################
installIPKG() {
    clear
    echo "Install IPKG..."
    
    # return value
    ret="0"
    
    # download
    wget -O $temp/ipkg.ipk $IPKG_LOCATION
    # check file exists
    if [ -s $temp/ipkg.ipk ]
    then
        # extract
        tar -xzf $temp/ipkg.ipk -C $temp/
        tar -xzf $temp/data.tar.gz -C /
        # setup sources
        echo "src cs08q1armel http://ipkg.nslu2-linux.org/feeds/optware/cs08q1armel/cross/stable" >> /opt/etc/ipkg.conf 
        /opt/bin/ipkg update
        # clean-up
        rm $temp/ipkg.ipk
        rm $temp/data.tar.gz
        rm $temp/control.tar.gz
        rm $temp/debian-binary
    else
        ret="1"
    fi
    
    # return    
    return $ret
}    
######################################################
# moveOpt - moves /opt to /home/opt
######################################################
moveOpt() {
    clear
    echo "Moving /opt to harddrive..."
    
    # return value
    ret="0"
    
    # copy everything
    cp -r /opt /home/opt
    # rename old
    mv /opt /opt-old
    # symbolic link to hdd
    ln -s /home/opt /opt
    
    # return    
    return $ret    
}
######################################################
# installNFS - installs Kernel NFS
######################################################
installNFS() {
    clear
    echo "Installing Kernel NFS"
    
    # return value
    ret="0"
    
    # check kernel
    if [ `uname -r` = "$nfs_kernel_check" ]
    then
        # download
        wget -O $temp/nfs.tar.gz $NFS_LOCATION
    
        # check file exists
        if [ -s $temp/nfs.tar.gz ]
        then
            # extract
            tar -xzf $temp/nfs.tar.gz -C /
            # load/check module
            /sbin/depmod -a
            /sbin/modprobe nfsd
            /sbin/lsmod | grep nfsd
            # add default share
            echo "/home/0common 192.168.1.0/255.255.255.0(rw,sync,no_root_squash,no_subtree_check)" > /etc/exports
            # remove v4 support
            mv /etc/sysconfig/nfs /etc/sysconfig/nfs.original
            cat /etc/sysconfig/nfs.original | sed 's/^#RPCNFSDARGS="-N\ 4"/RPCNFSDARGS="-N\ 4"/' > /etc/sysconfig/nfs
            # start daemons
            /etc/init.d/portmap start
            /etc/init.d/nfs start
    
            # start at boot
            /sbin/chkconfig --add portmap
            /sbin/chkconfig --level 235 portmap on
            /sbin/chkconfig --add nfs
            /sbin/chkconfig --level 235 nfs on
            # check it looks ok
            /sbin/exportfs -v
            # clean-up
            rm $temp/nfs.tar.gz
        else
            # download failed
            ret="1"
        fi
    else
        # wrong kernel
        ret="2"
    fi
    
    # return    
    return $ret
}
######################################################
#
######################################################
installNZBget() {
    clear
    echo "Installing NZBget..."
    
    # return value
    ret="0"
# check if we have IPKG
    if [ ! -e /opt/bin/ipkg ]
        then
        # install ipkg
            echo "IPKG not found. Installing..."
        installIPKG
    fi
# check if we have IPKG (again)
    if [ -e /opt/bin/ipkg ]    
    then
    
    # install nzbget using ipkg
    /opt/bin/ipkg install nzbget
    
    # install unrar too
    /opt/bin/ipkg install unrar
    # set home directory, umask to 0000 and parcheck to yes...
    cat /opt/share/doc/nzbget/nzbget.conf.example | sed -e "s#~/download#$nzb_home#"\
                                -e "s#UMask=1000#UMask=0000#"\
                                -e "s#DirectWrite=no#DirectWrite=yes#"\
                                -e "s#PostProcess=#PostProcess=$nzb_home/postprocess.sh#"\
                                -e "s#ParCheck=no#ParCheck=yes#" > /usr/etc/nzbget.conf
                                
    # copy example post-process script + conf
    cp /opt/share/doc/nzbget/postprocess-example.sh $nzb_home/postprocess.sh
    cp /opt/share/doc/nzbget/postprocess-example.conf $nzb_home/postprocess-example.conf
    # setup nzbget daemon
    wget -O $temp/nzbgetd $NZBGETD_LOCATION
    mv $temp/nzbgetd /etc/init.d/nzbgetd
    chmod +x /etc/init.d/nzbgetd
    # start at boot
    /sbin/chkconfig --add nzbgetd
    /sbin/chkconfig --level 235 nzbgetd on
    # start daemon
    /etc/init.d/nzbgetd start
    
    else
        # we don't have IPKG so fail
        ret="1"
    fi
    
    # return    
    return $ret
}
######################################################
#
######################################################
installNZBgetServer() {
    clear
    echo "Setting up NZBGET-web..."
    # return value
    ret="0"
    
    # download 
    wget -O $temp/nzbgetweb.zip $NZBGETWEB_LOCATION
    wget -O $temp/xmlrpc.tar.gz $RPCLIB_LOCATION
    # grab unzip if we dont have it already
    if [ ! -e /opt/bin/unzip ]
    then
        /opt/bin/ipkg install unzip
    fi
       
    # we can share the http server by placing files in /var/www/
    /opt/bin/unzip -d /var/www $temp/nzbgetweb.zip  
    # we also need the PHP RPC library
    tar -xzf $temp/xmlrpc.tar.gz -C $temp/
    cp -r $temp/xmlrpc-2.2.2/lib /var/www/nzbgetweb/lib
    # set permissions
    chgrp -R www /var/www/nzbgetweb/
    chmod -R 755 /var/www/nzbgetweb/
    chown -R apache /var/www/nzbgetweb/
    # setup the alias
    cp /etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf.original
    echo 'Alias /nzbgetweb/ "/var/www/nzbgetweb/"' >> /etc/httpd/conf/httpd.conf
    # restart httpd daemon
    /sbin/service httpd reload
    # clean-up
    rm $temp/nzbgetweb.zip
    rm $temp/xmlrpc.tar.gz
    rm -r $temp/xmlrpc-2.2.2
    # return    
    return $ret
}
######################################################
# disableAP - disables the Access Patrol
######################################################
disableAP() {
    clear
    echo "Disabling Access Patrol..."
    # return value
    ret="0"
    
    # stop service
    /etc/init.d/access-patrol stop
    echo ""
    echo ""
    # remove from boot
    /sbin/chkconfig --levels 2345 access-patrol off
    # remove executable flag
    chmod a-x /usr/sbin/access-patrol
    # return    
    return $ret
}
######################################################
# installTransmission - install bittorrent server
######################################################
installTransmission() {
clear
echo "Installing Transmission..."
echo ""
# check if we have IPKG
if [ ! -e /opt/bin/ipkg ]
    then
    # install ipkg
        echo "IPKG not found. Installing..."
    installIPKG
fi
# get daemon user, use sudo-er by default
echo -n "Please enter the daemon user: [$SUDO_USER] "
read TRANSMISSION_USER
if [ -z $TRANSMISSION_USER ]
then
    TRANSMISSION_USER=$SUDO_USER
fi
# get transmission home location, use ~/transmission by default    
echo -n "Please enter the destination where you want files saved: [/home/$TRANSMISSION_USER/transmission/] "
read TRANSMISSION_HOME
if [ -z $TRANSMISSION_HOME ]
then
    TRANSMISSION_HOME=/home/$TRANSMISSION_USER/transmission
fi
# get monitoring directory, use ~/transmission/monitor by default
echo -n "Please enter the directory to monitor for new .torrent files: [/home/$TRANSMISSION_USER/transmission/monitor/] "
read TRANSMISSION_MONITOR_HOME
if [ -z $TRANSMISSION_MONITOR_HOME ]
then
    TRANSMISSION_MONITOR_HOME=/home/$TRANSMISSION_USER/transmission/monitor
fi

# install using ipkg
/opt/bin/ipkg install transmission

# get daemon and setup home dir
wget $TRANSMISSIOND_LOCATION -O $temp/transmission-daemon.default
cat $temp/transmission-daemon.default | sed -e "s#TRANSMISSION_HOME=#TRANSMISSION_HOME=$TRANSMISSION_HOME#"\
                                            -e "s#DAEMON_USER=#DAEMON_USER=\"$TRANSMISSION_USER\"#" > /etc/init.d/transmission-daemon
# make executable
chmod 755 /etc/init.d/transmission-daemon
# create dirs if they dont exist
if [ ! -d "$TRANSMISSION_HOME" ]
then
    echo "Creating $TRANSMISSION_HOME..."
    mkdir -p "$TRANSMISSION_HOME"
    chmod 777 "$TRANSMISSION_HOME"
    chown $TRANSMISSION_USER:$TRANSMISSION_USER "$TRANSMISSION_HOME"
fi
if [ ! -d "$TRANSMISSION_MONITOR_HOME" ]
then
    echo "Creating $TRANSMISSION_MONITOR_HOME..."
    mkdir -p "$TRANSMISSION_MONITOR_HOME"
    chmod 777 "$TRANSMISSION_MONITOR_HOME"
    chown $TRANSMISSION_USER:$TRANSMISSION_USER "$TRANSMISSION_MONITOR_HOME"
fi
# start/stop daemon to generate config file
echo "Starting Transmission..."
/etc/init.d/transmission-daemon start
# kill the daemon
echo "Killing Transmission"
/etc/init.d/transmission-daemon stop
# add extra excape chars
TRANSMISSION_MONITOR_HOME=`echo $TRANSMISSION_MONITOR_HOME | sed 's#\/#\\\\\\\/#g'`
# add configuration
mv $TRANSMISSION_HOME/settings.json $TRANSMISSION_HOME/settings.json.original
sleep 1
cat $TRANSMISSION_HOME/settings.json.original | sed "s#\"rpc-whitelist-enabled\": true,#\"rpc-whitelist-enabled\": false,\n    \"watch-dir\":\"$TRANSMISSION_MONITOR_HOME\",\n    \"watch-dir-enabled\":true,#" > $TRANSMISSION_HOME/settings.json
# make daemon start at boot
echo "Adding transmission-daemon to startup..."
/sbin/chkconfig --add transmission-daemon
/sbin/chkconfig --levels 2345 transmission-daemon on
# start transmission and we're done
/etc/init.d/transmission-daemon start
return 0
}

# end of functions #

# nice and white
tput bold
# check that we are root before starting
if [ $(whoami) != "root" ] 
then
    echo ""
    echo "  Error: You must have root privileges to run this script."
    echo ""
    echo "         Try again using 'sudo $0'."
    echo ""
    tput sgr0
    # die
    exit 1
fi
while [ 1 -lt 2 ]
do
 if [ ! "$TEXT" = "" ]
 then
     foo="$TEXT"
     TEXT=""
     drawMenu "$foo"
 else
     drawMenu
 fi
done
# EOF
```

## Forum

Original thread on this is [here](http://www.openstora.com/forum/viewtopic.php?f=1&t=471) -- streetster @ 23.11.2010 
