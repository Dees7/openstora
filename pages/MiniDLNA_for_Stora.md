# MiniDLNA for Stora

**miniDLNA How-To … or … The Missing Manual**

This text will attempt to tell you where miniDLNA puts its files, what it does and how you can adjust it. IT will also guide you though updating miniDLNA to the latest version. It assumes a standard Stora installation.

## Intended Audience:

The hope is that this document will be written to provide some value to users of all levels. While the *nix guns will flash over some bits, other bits may save them a few minutes of playing and poking. While new users won’t find every bit of detail here, they will at least get a grasp on what is happening and where to look or go for help and guidance If they need it. If new users find that the introduction to a section refers to stuff they know nothing about, just keep reading, there will usually be a link for you towards the end of that section to steer you in the right direction or to find the files and detail you need to move on. If you’re a *nix gun and find all this mamby-pamby stuff a bit boring, all I ask is that you remember how there was a time when you were inside your mothers womb and didn’t quite know everything.

If you can’t cope with that, then just press the back button on your browser now.
## Prerequisites:

The following text assumes that you have full file system access and can login to the Stora as the root user.

Other assumptions are that you can navigate the filesystem using the command line or have a navigation tool you are comfortable with. One suggestion in this area is Midnight Commander for terminal mode access using FTP (putty).

Midnight Commander can be installed using ipkg where the package is known as “mc”. The install is as simple as typing “ipkg install mc”. There are a few directory permission issues you need to resolve at the user level, to stop mc barfing on exit, but you’ll quickly work them out.

For Windows users, who don’t already have a favourite FTP and/or filesystem Navigation tool, consider looking at WinSCP which can be found [here](http://winscp.net/eng/index.php)

The last assumption is that you can edit *nix style configuration and/or text files either using vi, nano, or another editor. This means that if you are using a Windows editor, you understand the difference between a *nix and win “text file”.

If you don’t have “root” access, then all the details are available on the Wiki which can be found [here](Easy_Root_Access.md)

If you haven’t installed ipkg yet, then all the details are available on the Wiki which can be found [here](Installing_a_package_manager.md)

If you haven’t done so yet, I would suggest you follow optional extra steps shown: install “nano” and edit the path variable. Nano is a lovely piece of work. It is small, quick, elegant, and so easy to use. While Midnight Commander and WinSCP have editors built-in you will still find nano really handy, so just do it :)

## The Basics:

miniDLNA is a DLNA server which has been written by a Netgear employee. However, the package is an open-source program and the source code along with a wiki and various tid-bits can be found [here](http://sourceforge.net/projects/minidlna/)

miniDLNA won’t transcode (convert between formats on the fly), nor will it magically fix your broken DLNA client (expensive television). The reason your TV won’t do certain things are not the fault of miniDLNA. They are issues created by the DLNA licensing/certification organisation and your TV manufacturer. While the author of this document has found miniDLNA to be quite good supporting quirks from some TV manufacturers, it can not fix basic problems that the manufacturers should address but either can’t or won’t. These issues can not be solved or supported by Netgear, this site, or the author of this document.

Extensive testing by the author of this document leads me to believe that miniDLNA may have a few quirks, although it is basically very sound. As a “server” or provider of data it can not and is not responsible for a particular file not playing or the sound not being right or even played on your TV or client. If pictures or images are not right, then chances are that your client (TV) doesn’t support them properly. miniDLNA has been tested serving files over 16GB with almost all known video and audio codecs and it has done it properly.

So, in a nutshell, miniDLNA is simply a server which sends raw data to your client which will usually be a TV. What your TV does with the data is up to the TV. How your TV asks for the data is also the responsibility of the TV.

miniDLNA runs as a background process in your Stora. It listens for requests from your TV and services them. Basically, your TV asks it what files it has available and what if any pictures it may have that go with the file list request, and miniDLNA tells it and throws the pictures (if any) at your TV.

To do this, it needs to know where you are going to dump the files, and it needs to know what sort of files it should look for. It tries to catch the filenames for these files as you drop them onto the Stora and keeps a database in a SQLite (v3) database which it manages and maintains. It also runs a “sweep” of the directory tree every once in a while and does it’s best to keep the database up to date and correct. The reason for the database is that when your TV asks miniDLNA what files or shows it has, it just looks into the database instead of scanning the entire disc. However, sometimes this database can get out of sync or it might miss something for a variety of reasons. It might also think that a file is corrupt or invalid, in which case it won’t add it to the database. If you change the name of a file (or directory) after you have put it on the Stora, then chances are that miniDLNA won’t notice or can’t track the change and that file (movie/song) won’t be available or shown to your TV/player. However, there are some simple things we can do to get miniDLNA to rebuild the database and we will get to these soon.

## The files for miniDLNA:

miniDLNA requires or creates the following files.

`/usr/sbin/minidlna` - the actual miniDLNA executable or daemon

`/etc/rc.d/init.d/minidlna.init` - A script which start and stops miniDLNA

`/etc/miniupnpd/minidlna.conf` - miniDLNA’s configuration file

`/mnt/tmpfs/var/run/minidlna.pid` - a temp system file containing the process ID of the current (or a running) instance of miniDLNA

`/tmp/minidlna` - The directory where miniDLNA stores its database and other stuff

`/tmp/minidlna/art_cache` - The directory where miniDLNA creates and stores images (pictures/thumbnails) sent to your TV

`/tmp/minidlna/files.db` - the database that miniDLNA creates/updates/uses

`/tmp/minidlna/minidlna.log` - a log file created and updated byminiDLNA


The current version of miniDLNA is 1.0.22

The current Stora version of miniDLNA is 1.0.22

The previous “public” release version of miniDLNA was 1.0.17


The latest release of miniDLNA requires the following packages:

- libexif - read/write (parse) EXIF data embedded within JPEG files
- libjpeg – (version 8) a JPEG decoder/encoder (used to create thumbnails)
- libid3tag - read/write ID3 tags
- libFLAC - Lossless Audio Codec
- libvorbis -Audio Codec
- sqlite3 – SQL Database engine- libavformat (the ffmpeg libraries)
- libavformat – libraries which provide calls to FFmpeg video libraries
- libuuid - generates unique system file and or process ID numbers


The Stora version of miniDLNA does not requires all of these packages. Therefore, some of these packages are not installed on a standard Stora. For this reason, you can not replace or update the Stora version of miniDLNA with a standard release unless the appropriate packages are installed. Updating your miniDLNA will be detailed in another section of this document.

### Commands used to Start, Stop, Get Status and Rebuild Database:

stop minidlna: 
```
/etc/init.d/minidlna.init stop
```
start minidlna: 
```
/etc/init.d/minidlna.init start
```
status minidlna: 
```
/etc/init.d/minidlna.init status
```

report version of minidlna: 
```
/usr/sbin/minidlna -V
```

run minidlna in debug mode: 
```
/usr/sbin/minidlna -d -f /etc/miniupnpd/minidlna.conf
```
rebuild minidlna database: 
```
/usr/sbin/minidlna -R -f /etc/miniupnpd/minidlna.conf
```

rebuild minidlna database in debug mode:
```
/usr/sbin/minidlna -R -d -f /etc/miniupnpd/minidlna.conf
```

**Note:** you should stop miniDLNA before you play with configuration files, or update any libraries it requires.

### Notes regarding “debug” mode and “rebuilding” the database:

It is suggested that you STOP miniDLNA before using either of these capabilities. The author of this document has never tried running two processes of miniDLNA and the results are unknown.

When running in Debug mode. The log file will not be created or updated. Debug mode can be handy when doing a rebuild so that you can tell when it is finished, at which point your console will stop. However, if miniDLNA finds any files or artwork which is corrupt or contains a problem, you will need to note it as a log entry will not be made. If you rebuild in normal mode, miniDLNA will create an entry in the log for any files it finds that are not right, thereby allowing you to just check the log to find any errors or faults. You can choose which method works for you.

Debug mode can also be handy to see the messages your TV sends and how miniDLNA responds. This may or may not help you work out why your TV does not do something you think it should. Decyphering the messages and commands received and sent are left as a reader exercise.

To exit Debug mode, press the Control-C key combination.

When running in Rebuild mode, miniDLNA will empty the database file and rebuild it from scratch. It will also rebuild any thumbnails although it won’t delete any existing thumbnails or those which may not be required due to file deletions or whatever. If you manually delete or remove the `/tmp/minidlna/artwork` directory and its content, miniDLNA will recreate the structure and files required.

If started in Rebuild mode, miniDLNA will continue to run after the rebuild is finished, but you won’t know when it has finished unless you know roughly how large the database will be. While it may take a few minutes to rebuild, it is suggested that you do rebuilds in debug mode so you can tell when it has finished and then “break” out of debug mode and restart miniDLNA in the normal fashion.

### The miniDLNA configuration file:

Filename: /etc/miniupnpd/minidlna.conf

The file itself contains comments that should allow you to see what things are and what you can do. Items that might be of interest to those who have not looked at the file yet are:

This is where you can tell miniDLNA to search for files in non-standard directories.

You can change the name of the DLNA device shown in your TV

You can add or change the names looked for when searching for cover artwork.

### Notes regarding Artwork:

As the standard Stora does not contain some of the key packages required it is assumed that the standard Stora version of miniDLNA does not support Artwork.

While miniDLNA looks for artwork using specific filenames, it will also find artwork with the exact same filename as the media file if it has a jpg extension.

If you have a directory with individual mp3 or movie files and a single artwork file which matches one of the “generic” filenames such as “cover.jpg”, then miniDLNA will create an individual thumbnail for each media file in the artwork directory. All of these thumbnails will be the same as the cover artwork.

## Updating miniDLNA to a later version

Moved to the [Updating miniDLNA](MiniDLNA_for_Stora_Updating.md) page

## AVCHD Format

Moved to the [Media Formats](MiniDLNA_for_Stora_Media_Formats.md) page

### Comment regarding AVCHD (m2ts) on Sony equipment with 1.0.18 version of miniDLNA

1.0.18 is quite an old version of miniDLNA, and does not display AVCHD on Sony TVs (and probably PS3s) as well as not having support for a lot of later Samsung and other vendor features. To get it to do so, you may also like to try the following...

As root (just like above)..
```
bash-3.2# sqlite3 /tmp/minidlna/files.db

sqlite> update details set dlna_pn="MPEG_TS_SD_EU;DLNA.ORG_OP=01;DLNA.ORG_CI=0" where path like "%.%TS";

sqlite> .exit
```
According to a post on the Ubuntu forums this change allows Pausing, FF and RW of M2TS on miniDLNA 1.0.18 though version 1.0.22 offers this without having to manually run an SQL update.