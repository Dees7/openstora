# Control LED onto FrontPanel


- TurnOff Power LED
```
/usr/bin/set-led-status power off
```

- Blinking Power LED
```
usr/bin/oe-visual-indicator bootup-start
shutdown-start
reset-start
```
- Blinking off Power LED
```
usr/bin/oe-visual-indicator bootup-finish
shutdown-finish
reset-finish
```
- HDD LED orange
```
/usr/bin/oe-visual-indicator hdd0 error
/usr/bin/oe-visual-indicator hdd1 error
```
- HDD LED green
```
/usr/bin/oe-visual-indicator hdd0 on
/usr/bin/oe-visual-indicator hdd1 on
```

