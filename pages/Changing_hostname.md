# Changing hostname

Set hostname by issuing hostname <newhostname>

## Making the change permanent

(as root) do the following Edit the file /etc/sysconfig/network and change **HOSTNAME=\<yourhostname\>**

if you have a static IP and gateway you can add the gateway here, e.g **GATEWAY=10.0.0.1** and also add the IP / hostname to the /etc/hosts file **echo "\<ip\> \<hostname\>" >> /etc/hosts**

now restart the network services **service network restart**

verify by loggin in and out again.