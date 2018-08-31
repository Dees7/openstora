# Prevent unclean filesystems from blocking the boot process

On rare ocasions it may happen that a filesystem gets corrupted. If this happens, debian will notice this on stratup while doing the fsck. It will then ask for user interaction and offer a shell. Well, you can only access this shell from the serial console and that means opening the stora. If you don't want to open your stora just to perform an fsck do the following:

Use your favorite text editor and set `FSCKFIX=yes` inside `/etc/default/rcS`. This will cause fsck to automatically correct all errors it finds. Thats exactly what you would do anyways wouldn't you?