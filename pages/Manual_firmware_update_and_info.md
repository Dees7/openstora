# Manual firmware update and info

You may be interested in updating your Stora even if you disabled the automatic update check (there's a wiki page for this), so here are some useful info:

**Note:** You might want to make a backup of your `/etc` folder since some
configuration files will be replaced
(e.g. `/etc/init.d/oe-bootfinish` that enables the firewall,
`/etc/samba/smb.conf` and `/etc/ssh/sshd_config`)

## Determine current firmware version

The current firmware version for your stora is located in a file in the /etc folder.

Type `cat /etc/oe-release` to reveal that information.

Example contents:
```
Version 2.0.0.368
DistName hipserv2_netgear
VersionName 2.5
CentralServer mystora.com
Serial NGKD-xxxx-xxxx-xxxx
Subdomain stora
```

## Firmware version table

**WARNING:** this table isn't official, it should just be considered a reference.
Some firmware version reported here are just test firmware, to check which firmware update
you should apply, use the website check as described below this table!
**BOLD** update names are official updates, officially released by Netgear.

Update name | Firmware version  |  Updates from  |  Version name |   Download URL |   Changelog |  Notes
------------|-------------------|----------------|---------------|----------------|-------------|-------
**hbs-5044**| 2.0.0.433 | 426,427      |  2.6.1  | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5044.upd  | No Changelog available. |
**hbs-5043**| 2.0.0.427 | 419,420,422  |  2.6    | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5043.upd  | http://support.netgear.hipserv.com/release_notes/v_2_6.html  |   adds iTunes 10.5 support
hbs-5042    | 2.0.0.420 | 377          |  2.5.3  | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5042.upd  | Forum notes http://www.openstora.com/phpBB3/viewtopic.php?f=18&t=1164 ** BETA** release - MySync support, OSX Lion Timemachine fixes replaces HBS-5040 |
hbs-5040    | 2.0.0.417 | 377          | 2.5.3   | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5040.upd  | Forum notes http://www.openstora.com/phpBB3/viewtopic.php?f=18&t=1086 **BETA** release - MySync support |
**hbs-5037**| 2.0.0.377 | 365,368,374  |   2.5.1 | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5037.upd  | http://support.netgear.hipserv.com/release_notes/v_2_5_1.html |  
**hbs-5036**| 2.0.0.368 | 365          |   2.5   | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5036.upd  | http://support.netgear.hipserv.com/release_notes/v_2_5.html   |  
**hbs-5035**| 2.0.0.368 | 145,146,149  |   2.5   | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5035.upd  | http://support.netgear.hipserv.com/release_notes/v_2_5.html   |  
**hbs-5034**| 2.0.0.368 | 261,262,271  |   2.5   | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5034.upd  | http://support.netgear.hipserv.com/release_notes/v_2_5.html   |  
**hbs-5033**| 2.0.0.271 | 145,146,149  |   2.3.3 | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5033.upd  | http://support.netgear.hipserv.com/release_notes/v_2_3_3.html |  
**hbs-5032**| 2.0.0.271 | 261,262      |   2.3.3 | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5032.upd  | http://support.netgear.hipserv.com/release_notes/v_2_3_3.html |  
hbs-5031    | 2.0.0.149 | 145,146      |   1.2.1 |                                                                |                                                               |
**hbs-5028**| 2.0.0.262 | 145,146,149  |   2.3.2 | http://support.netgear.hipserv.com/release_notes/v_2_3.html    |                                                               |
**hbs-5027**| 2.0.0.262 | 256          |   2.3.2 | http://support.netgear.hipserv.com/release_notes/v_2_3.html    |                                                               |
hbs-5026    | 2.0.0.256 | 253          |   2.3.1 |                                                                |                                                               |
hbs-5025    | 2.0.0.254 | 252,253      |   2.3   |                                                                |                                                               |
hbs-5024    | 2.0.0.253 | 61,72,74,75,77,78,84,86 | 2.3 |                                                         |                                                               |
hbs-5023    | 2.0.0.253 | 145,146,149  |   2.3   |                                                                |                                                               |
hbs-5022    | 2.0.0.149 | 145,146      |  1.3    |                                                                |                                                               |
hbs-5021    | 2.0.0.250 | 145,146      |  2.3    |                                                                |                                                               |
??          | 2.0.0.146 |              |         |                                                                |                                                               |
**hbs-5018**| 2.0.0.145 | 84,86,87     |  1.2    | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5018.upd  | http://support.netgear.hipserv.com/release_notes/v_1_2.html   | First public version (?)
??          | 2.0.0.87  |              |         |                                                                |                                                               |
??          | 2.0.0.86  |              |         |                                                                |                                                               |
hbs-5011    | 2.0.0.75  | 50,52        | 1.1     |                                                                |                                                               |
hbs-5010    | 2.0.0.84  | 72,74,75,77,78 | 1.1.1 |                                                                |                                                               |
hbs-5006    | 2.0.0.77  | 50           | 1.1     |                                                                |                                                               |
hbs-5002    | 2.0.0.53  | 52           | 1.0     | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5002.upd  |                                                               |
??          | 2.0.0.52  |              |         |                                                                |                                                               |
hbs-5001    | 2.0.0.50  | 36           |1.0      | http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5001.upd  |                                                               |
??          | 2.0.0.36  |              |         |                                                                |                                                               |

## Manually check for available firmware update

Whenever the Stora need to check if there's any available firmware update, it does it by checking a particular webpage:
```
https://update.mystora.com/downloads/updates/check.php?SN=YOUR_SERIAL&VR=CURRENT_FIRMWARE&DIST=DIST_VERSION
```
or
```
https://update.mystora.com/downloads/updates/autocheck.php?myver=CURRENT_FIRMWARE_VERSION&myserial=YOUR_SERIAL&mydist=DIST_VERSION
```
(not sure what's the difference between the two, but the first seems to show more updates)

where CURRENT_FIRMWARE_VERSION, YOUR_SERIAL (doesn't really matter, you can just use XXXX-XXXX-XXXX-XXXX) and DIST_VERSION are respectively the values for Version, Serial and DistName (most probably "hipserv2_netgear") parameters as found on the /etc/oe-release file.

If checking for that webpage returns a "no update", it means no update exists (really? :-) ), otherwise you'll get a webpage like this:
```
http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5033.upd 31223978
```
## Manually download the available firmware

With the above procedure, you'll get the url for the newest firmware (http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5033.upd in this case), ssh into the Stora, get root access and proceed to download it somewhere, for example under /tmp which resides on your hard drive(s):
```
cd /tmp
wget http://netgear.hipserv.com/updates/netgear/v_2_0/hbs-5033.upd
```
The downloaded image isn't ready yet as it contains Axentra signature, probably to check if it's genuine and to prevent people (not like us :) ) from extracting it, so issue this command to check the signature and extract the real image:
```
/usr/bin/gpg --homedir=/etc/gpg/ --output=/tmp/hbs-5033.tar /tmp/hbs-5033.upd
```
Now that you have a nice tar file, you can regularly extract it with tar:
```
mkdir /tmp/hbs-5033
tar -xvf hbs-5033.tar -C /tmp/hbs-5033
```
now after a lot of text, you'll have the update extracted inside the /tmp/hbs-5033 directory, you can then proceed with the update:
```
cd /tmp/hbs-5033
./runupdate
```
or if you're curious about what it does, you can inspect it since it's a plain text bash file.

**NOTE**: this can be useful if you're having issues with your firmware update, the whole process is logged on a file: "/var/log/2.0.0-update" so check it in case you have problems!

Have fun!

--Pippone 13:52, 29 October 2010 (UTC)