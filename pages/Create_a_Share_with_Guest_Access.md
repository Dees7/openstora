# Create a Share with Guest Access

Sometimes it is useful to have a share available with Guest access.

To best use this feature with the Stora, the following is suggested:

- create a new folder \\STORA\FamilyLibrary\Guest (you can do this in Windows)

- use vi to edit /etc/samba/smb.conf to add the following
```ini
[Guest]
comment = Stora (Guest Share)
path=/home/0common/Guest
browseable = yes
readonly = yes
guest ok = yes
```
Now restart samba to make your share visible, type:
```
/etc/init.d/smb restart
```
All Stora users can see and access \\STORA\FamilyLibrary\Guest with full privileges. Thus they can place content here.

Any network user can access \\STORA\FamilyLibrary\Guest readonly.

All users includes simple network devices such as media players (useful for UPNP unsupported content).

In the GoFlex Home, it is also needed to change, in /etc/samba/smb.conf, the line
```
map to guest = never
```
to
```
map to guest = Bad User
```
since otherwise it will keep asking for a valid user even for the guest shares. 