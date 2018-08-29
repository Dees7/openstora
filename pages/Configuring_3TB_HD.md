# Configuring 3TB HD

Instruction to configure two 3TB (or 4TB) HDs in Raid0 or Raid1:

- Get root
```
sudo -E -s
```
- Navigate to sbin directory
```
cd /sbin
```
- Partition the first drive with GNU parted (to make a GPT file format so that 3TB+ drives work)
```
./parted /dev/sda
mklabel gpt
unit TB
mkpart primary 0.00TB 3.00TB #(4.00TB for 4tb but I haven't tried it).
print
quit
```

- Partition the second drive with GNU parted
```
./parted /dev/sdb
mklabel gpt
unit TB
mkpart primary 0.00TB 3.00TB
print
quit
```
- Make the raid0 array with a blocksize of 128 (the blocksize is defaulted to 64, but 128 might be more desireable)

```
./mdadm --create /dev/md0 --level=0 --chunk=128 --raid-devices=2 /dev/sda1 /dev/sdb1
y
```
or

- Make the raid1 array
```
./mdadm --create /dev/md0 --level=1 --chunk=128 --raid-devices=2 /dev/sda1 /dev/sdb1
y
```
- Wait for sync **Takes Long Time** Mine took 4 1/2 hours.

watch
```
cat /proc/mdstat (also shows in web interface)
```

- Make the USB Stick a swap partition and mount it as swap drive
```
./mkswap /dev/sdc
./swapon /dev/sdc
```
- Then we partition the drives to XFS filesystem
```
./mkfs.xfs /dev/md0
```
- Then we are going to text edit the fstab system (this system mounts the drive at startup)
```
nano /etc/fstab
```
Remove the # in front of or add this line:
```
/dev/md0 /home xfs defaults,bsdgroups,noatime,nodiratime 0 0
```
Then as a finishing very important touch we make sure that the raid0 array is remembered at startup
```
./mdadm -Q --detail --brief /dev/md0 > /etc/mdadm.conf
```

Then reboot the device
```
./reboot
```

Wait a few minutes, you should see the device boot. The HDD leds will go from green to orange for a few seconds, don't worry they will become green again and you should hear the HDD's working. This is probably the stora finalizing the partition so that it's ready for use.