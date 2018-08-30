# Enabling secure FTP

From the Stora prompt, type:
```
sudo vi /etc/vsftpd/vsftpd.conf
```
From the vi editor hit i and go to the last line and change `ssl_enable=NO` to `ssl_enable=YES`, now hit the `Esc` key, and type `:wq` to exit vi.

If you want to force the use of SSL connections (you won't be able to connect to the ftp unless you're using FTPS) also change `force_local_data_ssl=NO` and `force_local_logins_ssl=NO` to `YES`

Restart vsftpd by typing:
```
sudo /etc/init.d/vsftpd restart
```
Original forum post: [here](http://www.hardwarehackersunite.com/forum/topic?id=86)

--Pippone 08:59, 17 May 2010 (UTC)