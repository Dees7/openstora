#Boot sequence information

## Netgear Stora "dmesg"
```
Linux version 2.6.22.18-Netgear (tgk@fc10-64bit) (gcc version 4.2.1) #4 Thu Sep 3 13:29:25 EDT 2009
CPU: ARM926EJ-S [56251311] revision 1 (ARMv5TE), cr=00053977
Machine: Feroceon-KW
Using UBoot passing parameters structure
Memory policy: ECC disabled, Data cache writeback
On node 0 totalpages: 32768
  DMA zone: 256 pages used for memmap
  DMA zone: 0 pages reserved
  DMA zone: 32512 pages, LIFO batch:7
  Normal zone: 0 pages used for memmap
CPU0: D VIVT write-back cache
CPU0: I cache: 16384 bytes, associativity 4, 32 byte lines, 128 sets
CPU0: D cache: 16384 bytes, associativity 4, 32 byte lines, 128 sets
Built 1 zonelists.  Total pages: 32512
Kernel command line: console=ttyS0,115200 ubi.mtd=2,2048 root=ubi0:rootfs rootfstype=ubifs init=/linuxrc
PID hash table entries: 512 (order: 9, 2048 bytes)
Console: colour dummy device 80x30
Dentry cache hash table entries: 16384 (order: 4, 65536 bytes)
Inode-cache hash table entries: 8192 (order: 3, 32768 bytes)
Memory: 64MB 64MB 0MB 0MB = 128MB total
Memory: 125312KB available (4204K code, 272K data, 112K init)
Calibrating delay loop... 992.87 BogoMIPS (lpj=4964352)
Mount-cache hash table entries: 512
CPU: Testing write buffer coherency: ok
NET: Registered protocol family 16

CPU Interface
-------------
SDRAM_CS0 ....base 00000000, size  64MB 
SDRAM_CS1 ....base 04000000, size  64MB 
SDRAM_CS2 ....disable
SDRAM_CS3 ....disable
PEX0_MEM ....base e8000000, size 128MB 
PEX0_IO ....base f2000000, size   1MB 
INTER_REGS ....base f1000000, size   1MB 
NFLASH_CS ....base fa000000, size   2MB 
SPI_CS ....base f4000000, size  16MB 
BOOT_ROM_CS ....no such
DEV_BOOTCS ....no such
CRYPT_ENG ....base f0000000, size   2MB 

  Marvell Development Board (LSP Version KW_LSP_4.2.7_patch21)-- RD-88F6281A  Soc: 88F6281 A0 LE

 Detected Tclk 200000000 and SysClk 333333333 
MV Buttons Device Load
Marvell USB EHCI Host controller #0: c05d4600
PEX0 interface detected no Link.
PCI: bus0: Fast back to back transfers enabled
SCSI subsystem initialized
usbcore: registered new interface driver usbfs
usbcore: registered new interface driver hub
usbcore: registered new device driver usb
NET: Registered protocol family 2
Time: kw_clocksource clocksource has been installed.
IP route cache hash table entries: 1024 (order: 0, 4096 bytes)
TCP established hash table entries: 4096 (order: 3, 32768 bytes)
TCP bind hash table entries: 4096 (order: 2, 16384 bytes)
TCP: Hash tables configured (established 4096 bind 4096)
TCP reno registered
RTC has been updated!!!
RTC registered
Use the XOR engines (acceleration) for enhancing the following functions:
  o RAID 5 Xor calculation
  o kernel memcpy
  o kenrel memzero
Number of XOR engines to use: 4
cesadev_init(c000c8e4)
mvCesaInit: sessions=640, queue=64, pSram=f0000000
MV Buttons Driver Load
JFFS2 version 2.2. (NAND) Â© 2001-2006 Red Hat, Inc.
io scheduler noop registered
io scheduler anticipatory registered (default)
Serial: 8250/16550 driver $Revision: 1.90 $ 4 ports, IRQ sharing disabled
serial8250.0: ttyS0 at MMIO 0xf1012000 (irq = 33) is a 16550A
serial8250.0: ttyS1 at MMIO 0xf1012100 (irq = 34) is a 16550A
loop: module loaded
Loading Marvell Ethernet Driver:
  o Cached descriptors in DRAM
  o DRAM SW cache-coherency
  o Multi RX Queue support - 4 RX queues
  o Multi TX Queue support - 2 TX Queues
  o TCP segmentation offload enabled
  o Receive checksum offload enabled
  o Transmit checksum offload enabled
  o Network Fast Processing (Routing) supported
  o Network Fast Processing (NAT) supported
  o Driver ERROR statistics enabled
  o Driver INFO statistics enabled
  o Proc tool API enabled
  o Gateway support enabled
     o Using Marvell Header Mode
     o L2 IGMP support
  o Rx descripors: q0=128 q1=128 q2=128 q3=128
  o Tx descripors: q0=532 q1=532
  o Loading network interface(s):
    o eth0, ifindex = 1, GbE port = 0
    o eth1, ifindex = 2, GbE port = 1

mvFpRuleDb (c7fd0000): 1024 entries, 4096 bytes
Intel(R) PRO/1000 Network Driver - version 7.3.20-k2-NAPI
Copyright (c) 1999-2006 Intel Corporation.
e100: Intel(R) PRO/100 Network Driver, 3.5.17-k4-NAPI
e100: Copyright(c) 1999-2006 Intel Corporation
Integrated Sata device found
scsi0 : Marvell SCSI to SATA adapter
scsi1 : Marvell SCSI to SATA adapter
NFTL driver: nftlcore.c $Revision: 1.98 $, nftlmount.c $Revision: 1.41 $
NAND device: Manufacturer ID: 0xec, Chip ID: 0xda (Samsung NAND 256MiB 3,3V 8-bit)
Scanning device for bad blocks
Bad eraseblock 618 at 0x04d40000
Bad eraseblock 1642 at 0x0cd40000
Using static partition definition
Creating 3 MTD partitions on "nand_mtd":
0x00000000-0x00100000 : "u-boot"
0x00100000-0x00700000 : "uImage"
0x00700000-0x10000000 : "root"
UBI: attaching mtd2 to ubi0
UBI: physical eraseblock size:   131072 bytes (128 KiB)
UBI: logical eraseblock size:    126976 bytes
UBI: smallest flash I/O unit:    2048
UBI: sub-page size:              512
UBI: VID header offset:          2048 (aligned 2048)
UBI: data offset:                4096
UBI: attached mtd2 to ubi0
UBI: MTD device name:            "root"
UBI: MTD device size:            249 MiB
UBI: number of good PEBs:        1990
UBI: number of bad PEBs:         2
UBI: max. allowed volumes:       128
UBI: wear-leveling threshold:    4096
UBI: number of internal volumes: 1
UBI: number of user volumes:     1
UBI: available PEBs:             0
UBI: total number of reserved PEBs: 1990
UBI: number of PEBs reserved for bad PEB handling: 19
UBI: max/mean erase counter: 2/0
UBI: background thread "ubi_bgt0d" started, PID 289
ehci_marvell ehci_marvell.70059: Marvell Orion EHCI
ehci_marvell ehci_marvell.70059: new USB bus registered, assigned bus number 1
ehci_marvell ehci_marvell.70059: irq 19, io base 0xf1050100
ehci_marvell ehci_marvell.70059: USB 2.0 started, EHCI 1.00, driver 10 Dec 2004
usb usb1: configuration #1 chosen from 1 choice
hub 1-0:1.0: USB hub found
hub 1-0:1.0: 1 port detected
ohci_hcd: 2006 August 04 USB 1.1 'Open' Host Controller (OHCI) Driver
USB Universal Host Controller Interface driver v3.0
usbcore: registered new interface driver usblp
drivers/usb/class/usblp.c: v0.13: USB Printer Device Class driver
Initializing USB Mass Storage driver...
usbcore: registered new interface driver usb-storage
USB Mass Storage support registered.
mice: PS/2 mouse device common for all mice
i2c /dev entries driver
Linux telephony interface: v1.00
md: linear personality registered for level -1
md: raid0 personality registered for level 0
md: raid1 personality registered for level 1
raid6: int32x1     81 MB/s
raid6: int32x2     95 MB/s
raid6: int32x4    102 MB/s
raid6: int32x8     92 MB/s
raid6: using algorithm int32x4 (102 MB/s)
md: raid6 personality registered for level 6
md: raid5 personality registered for level 5
md: raid4 personality registered for level 4
raid5: measuring checksumming speed
   arm4regs  :   903.600 MB/sec
   8regs     :   629.200 MB/sec
   32regs    :   750.000 MB/sec
raid5: using function: arm4regs (903.600 MB/sec)
device-mapper: ioctl: 4.11.0-ioctl (2006-10-12) initialised: dm-devel@redhat.com
dm_crypt using the OCF package.
sdhci: Secure Digital Host Controller Interface driver
sdhci: Copyright(c) Pierre Ossman
usbcore: registered new interface driver usbhid
drivers/hid/usbhid/hid-core.c: v2.6:USB HID core driver
Advanced Linux Sound Architecture Driver Version 1.0.14 (Thu May 31 09:03:25 2007 UTC).
ALSA device list:
  #0: Marvell mv88fx_snd ALSA driver
Netfilter messages via NETLINK v0.30.
nf_conntrack version 0.5.0 (1024 buckets, 8192 max)
mvFpNatDb (c7e72000): 1024 entries, 4096 bytes
ip_tables: (C) 2000-2006 Netfilter Core Team
TCP cubic registered
NET: Registered protocol family 1
NET: Registered protocol family 17
NFP (fdb) init 256 entries, 1024 bytes
Bridge firewalling registered
802.1Q VLAN Support v1.8 Ben Greear <greearb@candelatech.com>
All bugs added by David S. Miller <davem@redhat.com>
md: Autodetecting RAID arrays.
md: autorun ...
md: ... autorun DONE.
UBIFS: recovery needed
UBIFS: recovery completed
UBIFS: mounted UBI device 0, volume 0, name "rootfs"
UBIFS: file system size:   239857664 bytes (234236 KiB, 228 MiB, 1889 LEBs)
UBIFS: journal size:       9023488 bytes (8812 KiB, 8 MiB, 72 LEBs)
UBIFS: media format:       4 (latest is 4)
UBIFS: default compressor: zlib
UBIFS: reserved for root:  0 bytes (0 KiB)
VFS: Mounted root (ubifs filesystem).
Freeing init memory: 112K
Failed to execute /linuxrc.  Attempting defaults...
mvsdmmc: irq =28 start f1090000
mvsdmmc: irq_detect=92
SGI XFS with ACLs, large block numbers, no debug enabled
XFS: SB read failed
XFS: SB read failed
eth0: link down
eth0: started
eth0: link up, full duplex, speed 1 Gbps
tun: Universal TUN/TAP device driver, 1.6
tun: (C) 1999-2004 Max Krasnyansky <maxk@qualcomm.com>
```

## ICYBOX IB-NAS6210 "dmesg"

```
Linux version 2.6.22.18 (ramang@es5x86.axentra.com) (gcc version 4.3.2 (sdk3.2rc1-ct-ng-1.4.1) ) #21 Wed Dec 23 13:37:12 EST 2009
CPU: ARM926EJ-S [56251311] revision 1 (ARMv5TE), cr=00053977
Machine: Feroceon-KW
Using UBoot passing parameters structure
Memory policy: ECC disabled, Data cache writeback
On node 0 totalpages: 65536
  DMA zone: 512 pages used for memmap
  DMA zone: 0 pages reserved
  DMA zone: 65024 pages, LIFO batch:15
  Normal zone: 0 pages used for memmap
CPU0: D VIVT write-back cache
CPU0: I cache: 16384 bytes, associativity 4, 32 byte lines, 128 sets
CPU0: D cache: 16384 bytes, associativity 4, 32 byte lines, 128 sets
Built 1 zonelists.  Total pages: 65024
Kernel command line: console=ttyS0,115200 ubi.mtd=2,2048 root=ubi0:rootfs rootfstype=ubifs init=/linuxrc
PID hash table entries: 1024 (order: 10, 4096 bytes)
Console: colour dummy device 80x30
Dentry cache hash table entries: 32768 (order: 5, 131072 bytes)
Inode-cache hash table entries: 16384 (order: 4, 65536 bytes)
Memory: 256MB 0MB 0MB 0MB = 256MB total
Memory: 255616KB available (3832K code, 260K data, 128K init)
Calibrating delay loop... 1186.20 BogoMIPS (lpj=5931008)
Mount-cache hash table entries: 512
CPU: Testing write buffer coherency: ok
NET: Registered protocol family 16

CPU Interface
-------------
SDRAM_CS0 ....base 00000000, size 256MB
SDRAM_CS1 ....disable
SDRAM_CS2 ....disable
SDRAM_CS3 ....disable
PEX0_MEM ....base e8000000, size 128MB
PEX0_IO ....base f2000000, size   1MB
INTER_REGS ....base f1000000, size   1MB
NFLASH_CS ....base fa000000, size   2MB
SPI_CS ....base f4000000, size  16MB
BOOT_ROM_CS ....no such
DEV_BOOTCS ....no such
CRYPT_ENG ....base f0000000, size   2MB

  Marvell Development Board (LSP Version KW_LSP_4.3.4)-- '''DB-88F6281A-BP  Soc: 88F6281 A1 LE

 Detected Tclk 200000000 and SysClk 400000000
MV Buttons Device Load
Marvell USB EHCI Host controller #0: c0690600
PEX0 interface detected no Link.
PCI: bus0: Fast back to back transfers enabled
SCSI subsystem initialized
usbcore: registered new interface driver usbfs
usbcore: registered new interface driver hub
usbcore: registered new device driver usb
NET: Registered protocol family 2
Time: kw_clocksource clocksource has been installed.
IP route cache hash table entries: 2048 (order: 1, 8192 bytes)
TCP established hash table entries: 8192 (order: 4, 65536 bytes)
TCP bind hash table entries: 8192 (order: 3, 32768 bytes)
TCP: Hash tables configured (established 8192 bind 8192)
TCP reno registered
RTC registered
cesadev_init(c0011568)
mvCesaInit: sessions=640, queue=64, pSram=f0000000
Warning: TS unit is powered off.
MV Buttons Driver Load
JFFS2 version 2.2. (NAND) ÃÂ© 2001-2006 Red Hat, Inc.
io scheduler noop registered
io scheduler anticipatory registered (default)
Serial: 8250/16550 driver $Revision: 1.90 $ 4 ports, IRQ sharing disabled
serial8250.0: ttyS0 at MMIO 0xf1012000 (irq = 33) is a 16550A
serial8250.0: ttyS1 at MMIO 0xf1012100 (irq = 34) is a 16550A
RAMDISK driver initialized: 16 RAM disks of 4096K size 1024 blocksize
loop: module loaded
Loading Marvell Ethernet Driver:
  o Cached descriptors in DRAM
  o DRAM SW cache-coherency
  o Single RX Queue support - ETH_DEF_RXQ=0
  o Single TX Queue support - ETH_DEF_TXQ=0
  o TCP segmentation offload enabled
  o Receive checksum offload enabled
  o Transmit checksum offload enabled
  o Network Fast Processing (Routing) supported
  o Driver ERROR statistics enabled
  o Driver INFO statistics enabled
  o Proc tool API enabled
  o Rx descripors: q0=128
  o Tx descripors: q0=532
  o Loading network interface(s):
    o eth0, ifindex = 1, GbE port = 0

Warning: Giga 1 is Powered Off
mvFpRuleDb (cfe18000): 2048 entries, 8192 bytes
Intel(R) PRO/1000 Network Driver - version 7.3.20-k2-NAPI
Copyright (c) 1999-2006 Intel Corporation.
e100: Intel(R) PRO/100 Network Driver, 3.5.17-k4-NAPI
e100: Copyright(c) 1999-2006 Intel Corporation
Integrated Sata device found
scsi0 : Marvell SCSI to SATA adapter
scsi1 : Marvell SCSI to SATA adapter
<hdd info>
NFTL driver: nftlcore.c $Revision: 1.98 $, nftlmount.c $Revision: 1.41 $
NAND device: Manufacturer ID: 0xec, Chip ID: 0xda (Samsung NAND 256MiB 3,3V 8-bit)
Scanning device for bad blocks
Using static partition definition
Creating 3 MTD partitions on "nand_mtd":
0x00000000-0x00100000 : "u-boot"
0x00100000-0x00700000 : "uImage"
0x00700000-0x10000000 : "root"
UBI: attaching mtd2 to ubi0
UBI: physical eraseblock size:   131072 bytes (128 KiB)
UBI: logical eraseblock size:    126976 bytes
UBI: smallest flash I/O unit:    2048
UBI: sub-page size:              512
UBI: VID header offset:          2048 (aligned 2048)
UBI: data offset:                4096
UBI: attached mtd2 to ubi0
UBI: MTD device name:            "root"
UBI: MTD device size:            249 MiB
UBI: number of good PEBs:        1977
UBI: number of bad PEBs:         15
UBI: max. allowed volumes:       128
UBI: wear-leveling threshold:    4096
UBI: number of internal volumes: 1
UBI: number of user volumes:     1
UBI: available PEBs:             0
UBI: total number of reserved PEBs: 1977
UBI: number of PEBs reserved for bad PEB handling: 19
UBI: max/mean erase counter: 7/1
UBI: background thread "ubi_bgt0d" started, PID 302
ehci_marvell ehci_marvell.70059: Marvell Orion EHCI
ehci_marvell ehci_marvell.70059: new USB bus registered, assigned bus number 1
ehci_marvell ehci_marvell.70059: irq 19, io base 0xf1050100
ehci_marvell ehci_marvell.70059: USB 2.0 started, EHCI 1.00, driver 10 Dec 2004
usb usb1: configuration #1 chosen from 1 choice
hub 1-0:1.0: USB hub found
hub 1-0:1.0: 1 port detected
ohci_hcd: 2006 August 04 USB 1.1 'Open' Host Controller (OHCI) Driver
USB Universal Host Controller Interface driver v3.0
usb 1-1: new high speed USB device using ehci_marvell and address 2
usb 1-1: configuration #1 chosen from 1 choice
hub 1-1:1.0: USB hub found
hub 1-1:1.0: 4 ports detected
usbcore: registered new interface driver usblp
drivers/usb/class/usblp.c: v0.13: USB Printer Device Class driver
Initializing USB Mass Storage driver...
usbcore: registered new interface driver usb-storage
USB Mass Storage support registered.
mice: PS/2 mouse device common for all mice
i2c /dev entries driver
Linux telephony interface: v1.00
Marvell Telephony Driver:

Warning Tdm is Powered Off

device-mapper: ioctl: 4.11.0-ioctl (2006-10-12) initialised: dm-devel@redhat.com
dm_crypt using the OCF package.
usbcore: registered new interface driver usbhid
drivers/hid/usbhid/hid-core.c: v2.6:USB HID core driver
TCP cubic registered
NET: Registered protocol family 1
NET: Registered protocol family 17
md: Autodetecting RAID arrays.
md: autorun ...
md: considering sda1 ...
md:  adding sda1 ...
md: created md0
md: bind<sda1>
md: running: <sda1>
raid1: raid set md0 active with 1 out of 2 mirrors
md: ... autorun DONE.
UBIFS: mounted UBI device 0, volume 0, name "rootfs"
UBIFS: file system size:   239857664 bytes (234236 KiB, 228 MiB, 1889 LEBs)
UBIFS: journal size:       9023488 bytes (8812 KiB, 8 MiB, 72 LEBs)
UBIFS: media format:       4 (latest is 4)
UBIFS: default compressor: zlib
UBIFS: reserved for root:  0 bytes (0 KiB)
VFS: Mounted root (ubifs filesystem).
Freeing init memory: 128K
Failed to execute /linuxrc.  Attempting defaults...
parport: Unknown symbol asm_memzero
parport: Unknown symbol xor_memzero
parport: Unknown symbol xor_memcpy
SGI XFS with ACLs, large block numbers, no debug enabled
XFS mounting filesystem md0
Ending clean XFS mount for filesystem: md0
eth0: started
eth0: link down
eth0: link up, full duplex, speed 1 Gbps
Netfilter messages via NETLINK v0.30.
nf_conntrack version 0.5.0 (2048 buckets, 16384 max)
ip_tables: (C) 2000-2006 Netfilter Core Team
NET: Registered protocol family 5
Adding 511992k swap on /home/.swapfile.  Priority:-1 extents:1 across:511992k
```