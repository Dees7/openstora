# Enabling the Root Account
## Introduction

For the most part, sudo is all you will need to anything on your stora. However, there are occasions when you need to log in as root, for example when you want to make use of a GUI SFTP client to manipulate and modify files. By default, the root account on the stora is, very sensibly, locked. This articles shows you how to enable it.

## Enabling the Root Account

- If you want to be able to freely edit and move files on the root file system you will need to unlock the root user account. Obviously, if you just want to browse the root file system, you don't need to do this step.
```
 sudo passwd root
```
This will prompt you to enter a password. Once you've entered it, the root account will be active, so you can do this:
```
login as: root
Stora version 10.0.x
root@Stora's password:
-bash-3.2#
```
(i.e. no need to type "sudo -E -s" every time you login to perform root activities!)

## Disabling the Root Account

Leaving the root account active all the time is a bad idea because it gives a potential hacker a working super-user account to attack. The most sensible thing to do is to lock the root account once you've finished doing whatever you've been doing. You can do this by:
```
sudo passwd -l root
```
Enjoy!

[Forum Article for Feedback](http://www.hardwarehackersunite.com/forum/topic?id=151)

-- Ham 