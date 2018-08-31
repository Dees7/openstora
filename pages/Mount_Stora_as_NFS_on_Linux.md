# Mount Stora as NFS on Linux

On PC where you want to mount stora: edit fstab
```
sudo gedit /etc/fstab
```
add this line:
```
//stora_ip/path /mounting_point cifs username=USERNAME,password=PASSWORD,nocase,noperm,file_mode=0777,dir_mode=0777 0 0
```
the user must have root grants

es:
```
//10.0.0.5/FamilyLibrary /home/jalbit/stora_FamilyLibrary cifs username=jalbit,password=******,nocase,noperm,file_mode=0777,dir_mode=0777 0 0
```
save and exit

then create the folder (es. mkdir `/home/jalbit/stora_FamilyLibrary`)
reboot the system

Remember to grant access to the pointing folder
```
sudo chmod 777 /home/jalbit/stora_FamilyLibrary 
```