# MiniDLNA for Stora/Updating

## Updating miniDLNA to a later version
### Instructions for LATEST version as of 15/05/2015 - 1.1.4

The following binary has been compiled from sources, natively on the Netgear Stora, so you don't need any additional library, just follow the instruction:

- Download the updated binary here: [minidlna-1.1.4_STORA.tar.gz](http://www.openstora.com/files/albums/userpics/10221/minidlna-1_1_4_STORA_tar.gz) and make sure to rename it to minidlna-1.1.4_STORA.tar.gz)
- As root user, make a backup copy of the existing /usr/sbin/minidlna in case something goes wrong: `sudo -E cp /usr/sbin/minidlna /usr/sbin/minidlna_OLD`
- then replace the extracted "minidlna" file inside /usr/sbin and restart the minidlna service: `sudo -E /etc/init.d/minidlna.init restart`

the first time you restart with the new version, the database will be upgraded so it can take a while before you can see all your files via DLNA.

### Other versions

- Check for a later version here [dockstar minidlna](http://schuett.io/2011/08/minidlna-dockstar-dlna-server/)
- [minidlna-1_1_2-1-arm](http://www.openstora.com/files/albums/userpics/15704/minidlna-1_1_2-1-arm.zip)
- [libavformat_54](http://www.openstora.com/files/albums/userpics/15704/libavformat_54.zip)
Instructions for version 1.0.25

Download the following files:

- [miniDLNA-1.0.25](https://web.archive.org/web/20160615040739/http://www.openstora.com/files/albums/userpics/14441/minidlna-1-0-25-compiled.zip)
- [libjpeg-8](https://web.archive.org/web/20160615040739/http://www.openstora.com/files/albums/uploads/libjpeg_so_8_0_2.zip)
- [libavutil-49](https://web.archive.org/web/20160615040739/http://www.openstora.com/files/albums/userpics/15704/libavutil_so_49_15_0.zip)

Unpack/unzip the file to your local machine

Copy the files to your Stora. Following assumes you copied it to the base of Stora "share" path, being `/home/0common`

Login to the Stora as “root”

**note:** This command STOPS the minidlna daemon
```bash
-bash-3.2# /etc/init.d/minidlna.init stop
-bash-3.2# chown root:root /home/0common/minidlna
-bash-3.2# chown root:root /home/0common/libjpeg.so.8.0.2
-bash-3.2# chown root:root /home/0common/libavutil.so.49.15.0
-bash-3.2# chmod 755 /home/0common/minidlna
-bash-3.2# chmod 755 /home/0common/libjpeg.so.8.0.2
-bash-3.2# chmod 755 /home/0common/libavutil.so.49.15.0
-bash-3.2# cd /usr/sbin
```
**note:** This command renames the current miniDLNA program – just in case - replace the version number with whatever is currently installed>
```bash
-bash-3.2# mv minidlna minidlna.1.0.22
-bash-3.2# mv /home/0common/minidlna minidlna
-bash-3.2# ls -al minidlna
```
**note:** This should be the output from the `ls` command
```bash
-rwxr-xr-x 1 root root 550973 Nov 21 17:05 minidlna
```
**note:** now we need to sort out the libjpeg library

**note:** we will place libjpeg onto our hard drive so we don’t waste flash
```bash
-bash-3.2# cd /home/opt/lib
-bash-3.2# mv /home/0common/libjpeg.so.8.0.2 libjpeg.so.8.0.2
-bash-3.2# mv /home/0common/libavutil.so.49.15.0 libavutil.so.49.15.0
```
**note:** now we need to create links to the program which are version independent
```bash
-bash-3.2# cd /
-bash-3.2# ln -s /home/opt/lib/libjpeg.so.8.0.2 /home/opt/lib/libjpeg.so
-bash-3.2# ln -s /home/opt/lib/libjpeg.so.8.0.2 /home/opt/lib/libjpeg.so.8
-bash-3.2# ln -s /home/opt/lib/libjpeg.so.8.0.2 /usr/lib/libjpeg.so
-bash-3.2# ln -s /home/opt/lib/libjpeg.so.8.0.2 /usr/lib/libjpeg.so.8
-bash-3.2# ln -s /home/opt/lib/libavutil.so.49.15.0 /usr/lib/libavutil.so.49
-bash-3.2# ln -s /usr/lib/libFLAC.so.7.0.0 /usr/lib/libFLAC.so.8
```
**note:** Basically, we are done, time to start miniDLNA and watch for errors
```bash
-bash-3.2# cd /
-bash-3.2# /etc/init.d/minidlna.init start
```
**note:** if we didn’t get any errors, we are done

If everything looks fine, you should stop miniDLNA and then do a “rebuild” so that the program can update your database and create thumbnails for any artwork you have.

First, make sure you are actually running the new version.
```bash
-bash-3.2# /usr/sbin/minidlna -V
```
Then stop it, delete the old database, restart, then enjoy :)
```
-bash-3.2# /etc/init.d/minidlna.init stop
-bash-3.2# rm /tmp/minidlna/files.db
```

Now restart and allow it to rebuild its database.
```
-bash-3.2# /etc/init.d/minidlna.init start
```
Enjoy.

## Issues with non-stock 1.0.25

Testing has so far revealed the following issues when used on a Stora:

- On startup it may complain about the following but seems to work correctly
```
Starting /usr/sbin/minidlna ... /usr/sbin/minidlna: /usr/lib/libavutil.so.49: no version information available (required by /usr/sbin/minidlna)
/usr/sbin/minidlna: /usr/lib/libjpeg.so.62: no version information available (required by /usr/sbin/minidlna)
```
- It seems this binary uses /var/log/minidlna.log by default instead of /tmp/minidlna/minidlna.log for logging. Therefore you might want change the logfile position with the "log_dir=" directive inside /etc/miniupnpd/minidlna.conf to
```
log_dir=/tmp/minidlna
```

- It might be worth also checking that your /etc/miniupnpd/minidlna.conf has the "strict_dlna=" directive set to no ensure more compatiblity with existing products.
```
# set this to strictly adhere to DLNA standards.
# * This will allow server-side downscaling of very large JPEG images,
#   which may hurt JPEG serving performance on (at least) Sony DLNA products.
strict_dlna=no
```
- The Netgear branding on the renderer is lost and replaced with an image of [Tux](http://en.wikipedia.org/wiki/Tux) instead.

## Version 1.0.25 release notes

Release notes for the 1.0.25 version can be found on the minidlna [sourceforge page](http://sourceforge.net/projects/minidlna/files/minidlna/1.0.25/) and include the following improvements since 1.0.22...

- 1.0.25 - Released 13-July-2012
  - Fix a couple crash bugs on malformed WAV files. - Forcibly tweak the model number for Xbox360 clients, or they might ignore us. - Enable all network interfaces by default if none were specified. - Add flag to force downscaled thumbnails rather than using embedded ones. - Add DirecTV client detection, and fix image resolution issue. - Add support for the latest ffmpeg/libav library versions. - Fix a potential crash on requests for a resize of a non-existent image. - Make DeviceID checking more permissive for Sagem Radio.

- 1.0.24 - Released 14-Feb-2012
  - Fix playlist browsing with no SortOrder specified.
  - Fix inotify detection of caption file removal.
  - Handle an empty DeviceID from Zyxel media player SOAP request.
  - Fix false positives in playlist caching optimization when we have duplicate file names in different directories.
  - Trim the camera model name extracted from EXIF tags.
  - Add support for user-configurable log level settings.
  - Add DLNA.ORG_FLAGS support.

- 1.0.23 - Released 23-Jan-2012
  - Enable the subtitle menu on some Samsung TV's.
  - Add subtitle support for Panasonic TV's.
  - Add workarounds for LifeTab tablets' bad behavior.
  - Speed up playlist parsing.
  - Make metadata-based virtual containers case insensitive.
  - Add folder art support (very few clients support this though).
  - Improve trimming of quotation marks.
  - Fix SRT caption support with the latest Samsung Series D firmware.
  - Fix subtitles on LG TV's for items whose titles don't have a dot in them.
  - Add support for the av:mediaClass tag, so some Sony devices can filter items by media type.
  - Fix inotify detection issues on first-level folders.
  - Work around LifeTab's broken DLNA support.
  - Add image rotation support during resize. (mostly useful for TiVo)

The downloads on the sourceforge page are for intel processors, i.e. not useable by the Stora. Please use the link at the top of this page for an ARM binary.
### Reverting to the backed up version if there's a problem
```
-bash-3.2# cd /usr/bin
-bash-3.2# mv mindlna minidlna.updated
-bash-3.2# mv minidlna.sav minidlna
-bash-3.2# /etc/init.d/minidlna.init start
```
### Comment regarding AVCHD (m2ts) on Sony equipment with 1.0.18 version of miniDLNA

Early versions of Stora software use the 1.0.18 or 1.0.17 version of miniDLNA. This is quite an old version, and does not display AVCHD on Sony TVs (and probably PS3s) as well as not having support for a lot of later Samsung and other vendor features. To get it to do so, you may also like to try the following...

As root (just like above)..
```
bash-3.2# sqlite3 /tmp/minidlna/files.db
sqlite> update details set dlna_pn="MPEG_TS_SD_EU;DLNA.ORG_OP=01;DLNA.ORG_CI=0" where path like "%.%TS";
sqlite> .exit
```
According to a post on the Ubuntu forums this change allows Pausing, FF and RW of M2TS on miniDLNA 1.0.18 though versions after 1.0.22 offer this without having to manually run an SQL update.