### Adding LVM Disk

Add a disk to the machine and -

Create new partition :

```
fdisk -cu /dev/disk_name
```

Add the disk to your machine as a primary partition. you can add lvm as extendedpartition but it doesn't make any sense because lvm as primary partition is more
manageable than any other extended partition. And basically lvm partition remove
our dependency on extended partition and provide more flexibility in terms of
manageability (increasing/decreasing the size of entire disk, addition/deletion
of other disks etc.).

Now Follow below steps:

```
Command (m for help): n

Command action P

Partition number (1-4): 1  ## as available

First cylinder (2611-3916, default 2611): "enter"

Last cylinder, +cylinders or +size{K,M,G} (2611-3916, default 3916): +100G  ##de  
                                                           fine size as desired.

Command (m for help): t

Partition number (1-5): 1

Hex code (type L to list codes): 8e

Command (m for help): w

```



Now, Create physical LVM volume on your disk:

```
pvcreate /dev/disk_name1
```

Create Volume group for this disk:

```
vgcreate volume-group-name /dev/disk_name1
```

Now Create logical volume within this volume group:

```
lvcreate -L 100G -n logical-volume-name volume-group-name 

### put size as u wish but obviously it can not be greater than the size of the disk.
```

You can see the physical volume, volume group and logical volume by running 

following commands:

```
pvdisplay
vgdisplay
lvdisplay
```

Create filesystem and mount it:

```
mkfs.ext4 /dev/mapper/volume_group_name-logical_volume_name
mount /dev/mapper/volume_group_name-logical_volume_name /mountpoint

### Mount Point should be created.

## Now, make an entry in /etc/fstab file so that it will be mounted after reboot.

/dev/mapper/volume_group_name-logical_volume_name /mountpoint ext4 deafults 0 0
```

### Increase The Size Of Linux LVM In Virtul Machine

After shutdown the system increase the disk size in VM then start it.

Now perform foolowing steps:

```
fdisk -l  ## It will list the increased disk (most probably as /dev/sda)
```

```

fdisk /dev/sda 

Command (m for help): n

Command action P

Partition number (1-4): 3  ## as available

First cylinder (2611-3916, default 2611): "enter"

Last cylinder, +cylinders or +size{K,M,G} (2611-3916, default 3916): "enter"

Command (m for help): t

Partition number (1-5): 3

Hex code (type L to list codes): 8e

Command (m for help): w 
```

Now reboot the system or run "partx -a /dev/sda3" coomand. And then run the following commands:

```
 pvcreate /dev/sda3

vgextend VG-Name-Of-Increasing-LV /dev/sda3  ## You can see VG name by 

                                                "vgdisplay" command.

lvextend /dev/VG-Name-Of-Increasing-LV/LV-Name-To-Be-Incrementing /dev/sda3

resize2fs /dev/VG-Name-Of-Increasing-LV/LV-Name-To-Be-Incrementing

```

Now, all done. You can see your incremented Volume size by "lvdisplay" command.






