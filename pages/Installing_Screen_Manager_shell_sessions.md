# Installing Screen (Manager shell sessions)

To install screen your need to edit the `ipkg.conf` Stora in `/opt/etc`

This file contains a src (source) unstable or beta programs, what we will do is replace it with a stable versions
```
src cs08q1armel http://ipkg2.nslu2-linux.org/feeds/optware/cs08q1armel/cross/stable/
```
Once we changed the line and save changes from a shell type:
```
sudo ipkg update
Password: [Type your password]
sudo ipkg install screen
```

The basic commands you can use the following help:
```
   *bash-3.2$ screen --help
   *Use: screen [-opts] [cmd [args]]
   *or: screen -r [host.tty]

   *Options:
   *-a Force all capabilities into each window's termcap.
   *-A -[r|R] Adapt all windows to the new display width & height.
   *-c file Read configuration file instead of '.screenrc'.
   *-d (-r) Detach the elsewhere running screen (and reattach here).
   *-dmS name Start as daemon: Screen session in detached mode.
   *-D (-r) Detach and logout remote (and reattach here).
   *-D -RR Do whatever is needed to get a screen session.
   *-e xy Change command characters.
   *-f Flow control on, -fn = off, -fa = auto.
   *-h lines Set the size of the scrollback history buffer.
   *-i Interrupt output sooner when flow control is on.
   *-l Login mode on (update /var/run/utmp), -ln = off.
   *-list or -ls. Do nothing, just list our SockDir.
   *-L Turn on output logging.
   *-m ignore $STY variable, do create a new screen session.
   *-O Choose optimal output rather than exact vt100 emulation.
   *-p window Preselect the named window if it exists.
   *-q Quiet startup. Exits with non-zero return code if unsuccessful.
   *-r Reattach to a detached screen process.
   *-R Reattach if possible, otherwise start a new session.
   *-s shell Shell to execute rather than $SHELL.
   *-S sockname Name this session .sockname instead of ...
   *-t title Set title. (window's name).
   *-T term Use term as $TERM for windows, rather than "screen".
   *-U Tell screen to use UTF-8 encoding.
   *-v Print "Screen version 4.00.03 (FAU) 23-Oct-06".
   *-wipe Do nothing, just clean up SockDir.
   *-x Attach to a not detached screen. (Multi display mode).
   *-X Execute as a screen command in the specified session.
   *bash-3.2$
```
## The basic use is:
```
Open session = screen -S [Name of session]
Put on Background = Ctrl + a + d
Restore the session = screen -r [Number].[Name of session]
```