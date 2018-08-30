# Privoxy web proxy server

Privoxy official [FAQ](http://www.privoxy.org/faq/index.html)

## Preparation

You need:

- [Easy Root Access](Easy_Root_Access.md)
- [Installing a package manager](Installing_a_package_manager.md), Nano text editor
- You may need to [Modify Stora Firewall](Modify_Stora_Firewall.md) depending which ports you use
- You may need to forward ports from your router to allow WAN access

## Install and start Privoxy
```
ipkg install privoxy
```
Open Privoxy configuration file:
```
nano /opt/etc/privoxy/config
```

Edit line 738 to reflect your current Stora IP address.
Note: if you are using SSH tunnelling, where clients SSH in first to access your proxy server, leave Privoxy's listen ip at 127.0.0.1 (this means localhost, which is where your clients will effectively be)

Start the program:
```
privoxy /opt/etc/privoxy/config
```

Now you can set your web browser to use your stora as a proxy server.
You should also be able to allow external access to your proxy if you forward a port from your router etc.

## Make Privoxy run automatically at boot

As root:
```
nano /etc/rc.local
```
Add this line somewhere in rc.local (it needs to be the full path to the app!):
```
/opt/sbin/privoxy /opt/etc/privoxy/config
```
Save, quit Nano, reboot your Stora, check the proxy is working.

## Logging your privoxy sessions

Open the privoxy config:
```
nano /opt/etc/privoxy/config
```
Scroll down to debugging section (Tip: use CTRL+W to search in Nano)
```
#logdir /opt/var/log/privoxy 
```
uncomment it and make it use your hard drive instead, e.g.
```
logdir /home/log/privoxy
```
After exiting Nano don't forget to make the log dir
```
mkdir /home/log/privoxy
```
for example, this Privoxy config will log only app startup and errors, not content
```
debug 0
#debug      1 # Log the destination for each request Privoxy let through.
#debug   1024 # Log the destination for requests Privoxy didn't let through, an$
debug   4096 # Startup banner and warnings
debug   8192 # Non-fatal errors
```
Check your privoxy log like this:
```
nano /home/log/privoxy/logfile
```

-- Started by Phreak, lots of edits by Smiff, thanks. 