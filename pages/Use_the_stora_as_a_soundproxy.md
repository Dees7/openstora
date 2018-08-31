# Use the stora as a soundproxy

This is a work in progress and does not work yet!!!!!

**Please note: this tutorial is intended for debian users!**

Lets suppose you have already bought a linux compatible sound card. Now you have to make shure the kernel has the appropriate modules compiled in. If you plug in the sound card lsmod | grep snd shound show some modules. If not you'll have to substitute the kernel (or compile the appropriate module).
## Install the kernel

You have two option: eighter compile the kernel by yourself or use a precompiled one.

You can download a precompiled kernel here:

**TODO:** Add a link to the kernel.

Then put the kernel package somewhere on the stora.

Get the stora flash utility:
```
wget http://dl.dropbox.com/u/1397036/stora_di/stora-flash
```
And install the kernel:
``` 
sudo dpkg -i kernel-package-name.deb
cd /boot
/path/to/stora-flash -i vmlinuz-kernel-name -r initrd.img-kernel-name
```
## Install/configure Pulseaudio

**Unfortunately the RTP module in version of pulseaudio currently shipped with debian is buggy. We'll need to compile pulseaudio on our own.**

On the stora do:
```
sudo apt-get update
sudo apt-get install make build-essential pulseaudio
sudo apt-get build-dep pulseaudio
wget http://0pointer.de/lennart/projects/pulseaudio/pulseaudio-0.9.22.tar.gz
tar -xfz pulseaudio-0.9.22.tar.gz
cd pulseaudio-0.9.22
./configure CFLAGS=-march=armv5 #The processor in the Stora is a ARM V5. 
make
sudo make install
```
Unless you have pulseaudio verison 0.9.22 on your system, do the same on your host system but without the `-mach=armv5` parameter.

On the stora edit both, `/etc/pulse/system.pa` and `/etc/pulse/default.pa` and uncomment or add the following line:
```
load-module module-rtp-recv
```
On the host system edit /etc/pulse/default.pa and change the lines:
```
#load-module module-null-sink
#load-module module-rtp-send
```
To:
```
load-module module-null-sink sink_name=rtp format=s16be channels=2 rate=44100 description="Stora"
load-module module-rtp-send source=rtp.monitor
```
Tell the stora to load pulseaudio at startup
```
sudo update-rc.d pulseaudio default
```
Restart pulseaudio on both the Stora and on your host system:
```
sudo killall pulseaudio
sudo service pulseaudio start
```
On the host system you can now tell pulseaudio to redirect the sound to the sink "Stora". You should then hear the sound comming from the Stora.
## Sources/recomended reading:

- Pauk Kench on how to install the kernel: http://www.openstora.com/forum/viewtopic.php?f=6&t=217
- Pulseaudio documentation, modules: http://www.pulseaudio.org/wiki/Modules
- Pulseaudio perfect setup: http://www.pulseaudio.org/wiki/PerfectSetup
