# Disable mystora.com redirection

## Disable mystora.com redirection on local access

Each time you try to login to your Stora, even if you are trying to login locally on your LAN, you'll be redirected to mystora.com for the login even if your Stora is totally capable of authenticating you, it can be a problem if you hacked the Stora or if you don't have internet connectivity at that moment but you still want to access your files through the web interface. This can be circumvented by adding "local=yes" to the web address (e.g. http://<your_stora_ip>/local=yes), newest firmwares offer this option, but in case you don't have the latest one, here's how to always force local login by automatically appending "local=yes" to every web access:

- Access your Stora's shell through SSH or whatever method you're using, make sure you are root:
```
sudo -E -s
```
- Edit the /etc/httpd/conf/httpd.conf file using "vi" and add this as the last line:
```
RewriteRule ^/homebase.php$ /homebase.php?local=yes [QSA]
```
- Optionally, you may want to do the same for https access, you also need to edit the /etc/httpd/conf.d/ssl.conf file and add the same line as above, but before the last line which is </VirtualHost>
- Reload the httpd service:
```
/sbin/service httpd reload
```
That's it!

PS: When you update the stora to firmware 2.3.2:2.0.0.262:1.1.0.180 you have to know that the stora has disabled the mystora.com redirection if you access from LAN but the redirection still remain if you access from the internet.

If you applied this hack before firmware update, you need to do it again because the file /etc/httpd/conf/httpd.conf will be rewritten during firmware updating process

--Pippone 07:30, 11 May 2010 (UTC) --Puarri 00:50, 15 Sept 2010 (UTC)

## Disable mystora.com redirection on remote access

If you want to remotely access your Stora by means of a dynamic DNS service without being redirected to mystora.com you need to disable the remote access feature. To do so:

- Log in as administrator through the web interface
- Go to Preferences > Server Preferences > Remote Access
- Remove the flag from "Enable remote access"

You might need to restart your Stora.

--Karmaprod (talk) 11:28, 4 January 2016 (MST) 