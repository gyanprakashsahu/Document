#### When Your MAC won't Boot - Disk Full

Follow below steps:

```
1. Reboot your mac and press command key with 's' key. Now you you will enter in single user mode.

2. Now, if you will try to delete few un-necessary file it will fire 'Read Only ' Error. So, you will have to mount it as writable disk and then delete un-necessary files-

/sbin/mount -uw /     ### for mounting as writable disk

3. Now, reboot your system. It will work properly.

```
