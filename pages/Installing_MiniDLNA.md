# Installing MiniDLNA

## MiniDLNA for Stora Version 1.0.17

Unfortunately the current Debian build for our OpenStora does not include a copy of MiniDLNA http://minidlna.sourceforge.net/, this guide will help you install MiniDLNA version 1.0.17 as originally supplied with the stock Netgear Stora firmware. Once installed, ~23Mb, it will function as a standard package.

## HOWTO INSTALL

Firstly download the following file (thanks Yopnono!)

http://www.hardwarehackersunite.com/remository/Stora/Uploads/minidlna-from-the-org-stora-OS/

Once you have the file you need to extract the files, I created a separate directory and extracted it there with:
```
tar xvjf minidlna-20100223-oe1.armv5tel.tar.bz2
```
Now you should have several files in various directories, I used SCP to copy the files across to my Stora using the following command - I was in the directory with my files and obviously the IP address of your Stora will vary..

```
scp -r * 192.168.0.125:/
```

Once you have copied the files across, you can test if it's physically installed by typing: `minidlna`


You will get an error message as follows due to some dependency errors which need solving:
```
minidlna: error while loading shared libraries: libexif.so.12: cannot open shared object file: No such file or directory
```

The following will install the necessary packages and codecs that miniDLNA depends on:
```
apt-get install libexif12 libjpeg62 libsqlite3-0 libavformat52 libid3tag0 libflac8 libflac++6 flac
```

For me miniDLNA would still not run, the Stora's looking for the library **libFLAC.so.7** which is not available in Debian but there is a simple work around, simply create a **symbolic link** from what miniDLNA needs to what we have available:
```
ln /usr/lib/libFLAC.so.8.2.0 /usr/lib/libFLAC.so.7
```

Once it's linked miniDLNA should work in all it's glory! 