# Stora as OpenVPN server

Good afternoon...

I share you my experience configuring openvpn on the stora.

Go to etc/openvpn and remove client configuration
```
cd /etc/openvpn
rm client.conf
```

- Download easy rsa
```
wget http://www.linuxguide.it/downloads/config_file/networking/openvpn/easy-rsa.tar.gz
tar xvfz easy-rsa.tar.gz
```

- Edit vars and execute
```
vi /etc/openvpn/easy-rsa/vars
change KEY_PROVINCE, KEY_CITY, KEY_ORG,KEY_EMAIL
./vars
```

- Generate keys 
```
./clean-all (clean /etc/openvpn/easy-rsa/keys)
./build-ca (create root certificate, specify the Common Name ex. vpnserver)
./build-key server (create server certificate; same common name of root certificate)
./build-key client (create clients certificate; same common name of root certificate)
./build-dh (create DIFFIE-HELLMAN used by the server)
openvpn --genkey --secret ta.key (ta.key to avid DoS/Flooding)
mkdir /etc/openvpn/keys_server/
cd /etc/openvpn/easy-rsa/keys
cp * /etc/openvpn/keys_server/
```

- Keys for clients 
```
mkdir client_keys
cp ca.crt client.crt client.key ta.key dh1024.pem client_keys
tar czf client_keys.tar.gz client_keys/
```

- Server configuration
```
vi /etc/openvpn/server.conf
   daemon
   port 1194
   proto tcp
   dev tun
   ca /etc/openvpn/keys_server/ca.crt
   cert /etc/openvpn/keys_server/server.crt
   key /etc/openvpn/keys_server/server.key
   dh /etc/openvpn/keys_server/dh1024.pem
   server 10.8.0.0 255.255.255.0
   push "route 192.168.0.0 255.255.255.0"
   push "dhcp-option DNS 192.168.0.1"
   client-to-client
   keepalive 10 120
   tls-auth /etc/openvpn/keys_server/ta.key 0
   cipher BF-CBC
   comp-lzo
   max-clients 100
   persist-key
   persist-tun
   status /home/log/openvpn-status.log
   log-append /home/log/openvpn.log
   verb 5
```

change `push "route 192.168.0.0 255.255.255.0"` and `push "dhcp-option DNS 192.168.0.1"` to suit your needs.
```
chmod +x /etc/init.d/openvpn
/etc/init.d/openvpn start to start the daemon
```
vi `/etc/iptables/config` and add the line 
```
TCP_OPEN_PORTS_EXT="1194"
```
```
chkconfig openvpn on 345
reboot
```

Check if the daemon is running correctly after the reboot.

Nat the port with your router, and check if you can connect with `telnet public_ip 1194`

Add a static route on your router, something like:

>Destination 10.8.0.0 Mask 255.255.255.0 Gateway internal_ip Interface LAN

**Note:** pay attention to the file executed each time the stora boots /etc/init.d/oe-bootinit oe-bootinit: rm -rf /etc/openvpn/keys/* - this is why I saved the keys in /etc/openvpn/keys_server

## Client configuration - Linux
```
   client
   proto tcp
   dev tun
   # Server IP address/hostname port
   remote 123.123.123.123 1194
   resolv-retry infinite
   nobind
   user nobody
   group nobody
   ca /etc/openvpn/keys/ca.crt
   cert /etc/openvpn/keys/client.crt
   key /etc/openvpn/keys/client.key
   tls-auth /etc/openvpn/keys/ta.key 1
   cipher BF-CBC
   comp-lzo
   persist-key
   persist-tun
   verb 3
```
to start:
```
cd /etc/openvpn
openvpn --config client.conf
```

## Client configuration - Windows
```
   client
   proto tcp
   dev tun
   # Server IP address/hostname port
   remote 123.123.123.123 1194
   resolv-retry infinite
   nobind
   ca /etc/openvpn/keys/ca.crt
   cert /etc/openvpn/keys/client.crt
   key /etc/openvpn/keys/client.key
   tls-auth /etc/openvpn/keys/ta.key 1
   cipher BF-CBC
   comp-lzo
   persist-key
   persist-tun
   verb 3
```
to start:
- rename the file to something.ovpn
- right click on the .ovpn file


## 2 clarifications:

- UDP has better performance than TCP with a normal home-adsl (remember to apply the relative changes to iptables/router), TCP works better with fiber and low latency lines.
- For every client generate a different certificate (./build-key client1, ./build-key client2, ...) with a different common name, so the vpn server can recognize the different clients and assign a different IP address to each workstation.

I hope you appreciate this little contribution to the community. :)

Best regards, Trixter

Original forum thread: http://www.openstora.com/phpBB3/viewtopic.php?f=1&t=1620&p=9121