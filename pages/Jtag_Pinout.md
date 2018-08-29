# Jtag Pinout
## JTAG Pin Out
From JTAG Pin | Function | To Stora | Function
--------------|----------|----------|---------
1 |  Vref   | J2-1  |  VDD
3 |  TAP reset - Active low | TP2     JT_RST_CPU
5 |  TDI  |   TP3    | JT_TDI_CPU
7 |  TMS  |   TP4    | JT_TMS_CPU
9 |  TCK  |   TP5    | JT_CLK_CPU
12 |  GND  |   J2-4  |  GND
13 |  TDO  |   TP7   |  JT_TDO_CPU

AA = SysRSTn
## Diagram
```
                    U9

     +-----------------+

     |                 |
 
     |    AA      BB   |

     |                 |

     +----+       +----+

          |  CC  |

          +------+
```
## Links
[JTAG Wikipedia](http://en.wikipedia.org/wiki/Joint_Test_Action_Group)