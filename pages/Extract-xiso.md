# Extract-xiso

extract-xiso is an open-source application which lets you extract the XBOX360 iso filesystem using a simple command.

It's often used to install games on XBOX360s hacked with JTAG or Reset-Glitch-Hack kernels.

I assume that you already have installed a package manager, we will need it!

Login on your Stora as root:
```
ipkg install make gcc
```
This will install the basics needing for compiling the source code of extract-xiso.

Please use a temporary dir to compile and then install it, it's easy to erase all the compiling crap :D

```bash
mkdir exiso 
cd exiso 
wget http://dl.dropbox.com/u/26622025/Stora/extract-xiso_v2.7_src_110930_by_somski.tar.gz 
tar xzvf extract-xiso_v2.7_src_110930_by_somski.tar.gz cd extract-xiso 
make
```

[Wait for a while... it's compiling]

If everything went fine, you should not get any error and have a "extract-xiso" in the current dir (use `ls` to check) Now we are going to grant it the execution access: `chmod +x extract-xiso`

Test if it works: 
```
./extract-xiso -v
```

If it echoes back the version it's all good :D

To use it anywhere just move it to an ENVIRONMENT FOLDER: 
```
mv extract-xiso /opt/bin
```

So if you simply use: extract-xiso It should works like a charm!

PS: You can use extract-xiso to extract your files directly into a connected USB HDD... To do this just connect your FAT32 USB HDD and wait for it to mount in the FamilyArchive, then make the proper command using extract-xiso. (use `extract-xiso --help` to view all the parameters) 