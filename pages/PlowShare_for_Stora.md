# PlowShare for Stora

To install Plowshare your need to edit the ipkg.conf Stora in /opt/etc

This file contains a src (source) unstable or beta programs, what we will do is replace it with a stable versions
``
src cs08q1armel http://ipkg2.nslu2-linux.org/feeds/optware/cs08q1armel/cross/stable/
``
Once we changed the line and save changes from a shell type:
```
sudo ipkg update
Password: [Type your password]
sudo ipkg install plowshare
sudo ipkg install libtool
sudo ipkg install tesseract-ocr
sudo ipkg install tesseract-ocr-lang-eng
sudo ipkg install imagemagick
sudo ipkg install coreutils
```
At the end of the installation look at the version of the program:
```
*-Bash-3.2 $ plowdown -version
0.9.1
```
And we have the Plowshare installed to manage the direct downloads from web servers.

Download files from file sharing servers.

Available modules: 
- 2share 
- rapidshare 
- megaupload 
- depositfiles 
- badongo 
- mediafire 
- zshare 
- 4shared 
- storage_to 
- uploaded_to 
- sendspace 
- uploading 
- netload_in 
- usershare 
- x7_to 
- hotfile 
- Divshare 
- freakshare 
- dl_free_fr
- loadfile

The basic commands you can use the following link to see in the [README](http://code.google.com/p/plowshare/wiki/Readme)