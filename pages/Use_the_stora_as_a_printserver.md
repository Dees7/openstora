# Use the stora as a printserver

To use the stora as a print-server you will need cups plus some configuration.

To install cups issue as root:
```
apt-get install cups
```
To allow other clients to connect to the server you will need to edit /etc/cups/cupsd.conf.

Please change:
```
# Only listen for connections from the local machine.
Listen localhost:631 
Listen /var/run/cups/cups.sock 
```
to
```
# Only listen for connections from the local machine. 
Listen *:631 
Listen /var/run/cups/cups.sock 
```
so anyone can connect to the server.

Further set
```
# Restrict access to the server... 
<Location /> 
  Order allow,deny 
</Location>
```
to
```
# Restrict access to the server... 
<Location /> 
  Order deny,allow 
</Location>
```
and
```
# Restrict access to the admin pages... 
<Location /admin> 
  Order allow,deny 
</Location>
```
to
```
# Restrict access to the admin pages...
<Location /admin> 
  Order deny,allow 
</Location>
```
to grat all users access to the settings of the server.

Suggested reading: [cupsd.conf documentation](http://www.cups.org/documentation.php/ref-cupsd-conf.html)