# RSyncBackup

## Introduction

The aim of this is to have a synced folder that will automatically back up to a remote location. The main reason that I wanted to do this was so that I could have a folder of documents backed up to my VPS and synced to my work machine and laptops.

It is similar in style to the 'dropbox' service.

It is largely based by this [HOWTO](http://fak3r.com/2009/09/14/howto-build-your-own-open-source-dropbox-clone/). It just covers a number of points for installing on the Stora!

This guide assumes you have a fully working package management system as described here - [Installing a package manager](Installing_a_package_manager.md) and also assumes that you have SSH fully working.

## SSH Key Setup

- Setup your ssh keys to your remote server by doing the following:
  - Generate your keys (don't enter a password - this enables you to have the syncing done without user intervention!) `ssh-keygen -N -f ~/.ssh/id_dsa`
  - Copy public key to server so you can login `ssh-copy-id -i ~/.ssh/id_dsa.pub user@REMOTE_SERVER`
  - Tidy up permissions `ssh user@REMOTE_SERVER 'chmod 700 .ssh'`
  - Check you can login without a password `ssh user@REMOTE_SERVER`

## Software Install

- Now prepare for the lsyncd build by installing the following packages by running the command 
```
sudo ipkg install gcc
sudo ipkg install lua
sudo ipkg install make
sudo ipkg install pkgconfig
sudo ipkg install rsync
```
- You need to add the newly installed packages into the $PATH variable : `PATH=$PATH:/home/opt/bin`
- Download and untar lsyncd from the [lsyncd project](http://code.google.com/p/lsyncd/)
- Configure the build by running `./configure --prefix=/opt/local --exec-prefix=/opt` (this assumes your addition software is on /opt, otherwise tailor to your needs, but avoid the onboard flash!)
- Modify the Makefile to set the following: `LDFLAGS = -ldl`
- Assuming you got no errors from the configure do make all
- Now do `sudo -E make install` (the -E just utilises the same environment settings of the current user as when doing sudo PATH seems to disappear and it won't be able to find the commands!)
- Now run `sudo ln -s /opt/lib/liblua.so /usr/lib/liblua.so so` that lsyncd can locate the library
- Now run `lsyncd -help` to see if all is well (you should get a list of options)!

## Configuration

(Please refer to the [lsync manual](http://code.google.com/p/lsyncd/wiki/Lsyncd20Manual#The_Configuration_File) for comprehensive guidance)

- Copy `example/lrsyncssh.lua` to a location of your choice (I created `$HOME/sync_config`)
- Fill out the configuration file `sync_conf.lua` with the appropriate details (the file can be put anywhere):
```
sync{default.rsyncssh, source="/path/to/src", host="user@REMOTE_SERVER", targetdir="/path/to/dst", rsyncOps=""}
```
- Run lsyncd in no daemon mode to check for errors:
```
lsyncd -nodaemon sync_conf.lua
```
- Drop some files in the watched directory and make sure they get uploaded properly with no errors
- Run lsyncd in daemon mode to have it watch the folder:
```
lsyncd sync_conf.lua
```

## Reverse Sync

Adding the following crontab line is particularly useful if you want to have your laptop or other machines sync up, or at least have the Stora pull from the remote server.
```
* * * * * rsync -ltur user@REMOTE_SERVER:/path/to/src/ /path/to/dst >> /path/to/log/dir/rsync_pull.log
```
## Final Comments

This setup can be replicated on your laptop and work machines to create the full dropbox like experience.