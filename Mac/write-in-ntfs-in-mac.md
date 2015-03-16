####By default you can’t write to Windows NTFS hard disk and USB drives as they appear as read only on the Desktop’s of OS X 10.9 users workstations.

Xcode and homebrew should be installed on your machine. Now foolow below steps to make your external Windows NTFS hard disk and USB drives writable in MAC-

```
brew install ntfs-3g

### Now, you will have to change the mount_ntfs file, the new file will allow the writes to NTFS disks, below commands will back up the original and then link to the modified mount_ntfs file as supplied by Brew/ntfs-3g

sudo mv /sbin/mount_ntfs /sbin/mount_ntfs.orig

sudo ln -s /usr/local/Cellar/ntfs-3g/2014.2.15/sbin/mount_ntfs /sbin/mount_ntfs

brew install osxfuse ## If it is already installed then foolow below steps: 

brew info osxfuse   ### It will give you osxfuse version suppose it is 2.7.2.

sudo /bin/cp -RfX /usr/local/Cellar/osxfuse/2.7.2/Library/Filesystems/osxfusefs.fs /Library/Filesystems

sudo chmod +s /Library/Filesystems/osxfusefs.fs/Support/load_osxfusefs

Now, Re-attach your external device.

## Note: If you see a message " kernel extension is not from an identified developer", ignore it and just click at "OK".

```

Now, You will be able to write at Windows NTFS hard disk and USB drives.
