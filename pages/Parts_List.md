# Parts List
## TWSI
TWSI | Address | ChipLoc | PartNum | Description   |  Useful links
-----|---------|---------|---------|---------------|--------------
TWSI |  0x1b  | u17  |  TC654      | Microchip fan controller. | [Datasheet](http://ww1.microchip.com/downloads/en/DeviceDoc/21734a.pdf)
TWSI |  0x64  | u15  |  PCF8563    | RTC.                      | [Datasheet](http://www.nxp.com/documents/data_sheet/PCF8563.pdf)
TWSI |  0x48  | u16  |  LM75BIMM-3 | temperature sensor.       | [Datasheet](http://www.national.com/ds/LM/LM75.pdf)
TWSI |  0x51  |  ?   |  ?          | Most likely U2 (custom bootrom, perhaps). |

I don't think 0x51 is U2 (or indeed U1 or U3). These are identified below as programmable regulators, but the interface on them is not I2C. B22824@prtnx.com 17:54, 20 June 2011 (UTC)
## Other
ChipLocation |  PartNumber | Description  |  Useful Links
-------------|-------------|--------------|---------------
U10          |  88F6281    |  SoC         | [Product brief](http://www.marvell.com/products/processors/embedded/kirkwood/88F6281-004_ver1.pdf)  [Datasheet](http://www.marvell.com/products/processors/embedded/kirkwood/HW_88F6281_OpenSource.pdf)
U18          | 88E1116R-NNC1 |  Gigabit Ethernet
U6, U7       | 74hct132d  | Quad 2-input NAND Schmitt Trigger.
U8,          | 74hct74d   | Dual D-type flip-flop with set and reset.
U1, U3       | 88PG8227   | Programmable Voltage Regulators
U11, U12     | K4T51163QG | 64MB DDR2 SDRAM x 2
T11          | LAN3219    | Transformer, NIC isolation
Q4           | 78D05L     | 5v regulator
U14          | K9F2G08U0B |256MB NAND Memory
U19          | TPS2065    | 1 Amp solid state switch (controls power to the unit)

## Unknown
ChipLocation  |  PartNumber | Description
--------------|-------------|-------------
Q3, Q5        | BA9S1L?     |  By the identifier assumed to be a transistor array or a fet. Maybe controls power up sequence.
U2            | MRVL H101 9251|  ~~This is the one we need to know. Made by Marvell.~~ It's a 88PH8101 voltage regulator.
Q8            | AJ3AF FDS 6678AZ |   Another FET.
