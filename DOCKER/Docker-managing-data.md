### Managing Data In Containers:

#### Data Volumes:
A data volume is a specially-designated directory within one or more containers that bypasses the Union File System to provide several useful features for persistent or shared data:
1.Volumes are initialized when a container is created 

2.Data volumes can be shared and reused between containers

3.Changes to a data volume are made directly

4.Changes to a data volume will not be included when you update an image

5.Volumes persist until no containers use them.

#### Adding a data volume: 
Use -v flag at the time of container creation:
```
Suppose You want to create a volume inside your container at /tmp-
docker run -it -v /tmp srbhkmrsngh/apache /bin/bash
```
#### Mount a Host Directory as a Data Volume:
Note: If you are using Boot2Docker, your Docker daemon only has limited access to your OSX/Windows filesystem. 
 Boot2Docker tries to auto-share your /Users (OSX) or C:\Users (Windows) directory - and so you can mount files 
 or directories using docker run -v /Users/<path>:/<container path> ... (OSX) or 
docker run -v /c/Users/<path>:/<container path ... (Windows). All other paths come from the Boot2Docker virtual machine's filesystem.

```
Suppose you want to mount host directory /src/webapp into the container at /tmp/webapp:

docker run -it -v /src/webapp:/tmp/webapp YouImage /bin/bash
Note: If the path /opt/webapp already exists inside the container's image, it's contents will be  replaced 
by the contents of /src/webapp on the host to stay consistent with the expected behavior of mount.

This is very useful for testing, for example we can mount our source code inside the container and see
 our application at work as we change the source code. The directory on the host must be specified as an 
absolute path and if the directory doesn't exist Docker will automatically create it for you.

## Docker defaults to a read-write volume but we can also mount a directory read-only by using ro flag:

docker run -it -v /src/webapp:/tmp/webapp:ro YourImage /bin/bash

## The -v flag can also be used to mount a single file - instead of just directories - from the host machine:

docker run --rm -it -v ~/.bash_history:/.bash_history YourImage /bin/bash

This will drop you into a bash shell in a new container, you will have your bash history from the host
 and when you exit the container, the host will have the history of the commands typed while in the container.

```
#### Creating and mounting a Data Volume Container:
If you have some persistent data that you want to share between containers, or want to use from non-persistent containers, it's best to create a named Data Volume Container, and then to mount the data from it.
                Let's create a new named container with a volume to share. While this container doesn't run an application, it reuses the training/postgres image so that all containers are using layers in common, saveing disk space.
```
 sudo docker create -v /dbdata --name dbdata training/postgres
You can then use the --volumes-from flag to mount the /dbdata volume in another container.

$ sudo docker run -d --volumes-from dbdata --name db1 training/postgres
And another:

$ sudo docker run -d --volumes-from dbdata --name db2 training/postgres
You can use multiple 
--volumes-from parameters to bring together multiple data volumes from multiple containers.

You can also extend the chain by mounting the volume that came from the dbdata container in yet another  
container via the db1 or db2 containers.

$ sudo docker run -d --name db3 --volumes-from db1 training/postgres
If you remove containers that mount volumes, including the initial dbdata container, or the subsequent 
containers db1 and db2, the volumes will not be deleted. To delete the volume from disk, you must 
explicitly call docker rm -v against the last container with a reference to the volume. This allows you to 
upgrade, or effectively migrate data volumes between containers.
```

#### Backup, restore, or migrate data volumes:
Another useful function we can perform with volumes is use them for backups, restores or migrations. We do this by using the --volumes-from flag to create a new container that mounts that volume, like so:
```
$ sudo docker run --volumes-from dbdata -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /dbdata
Here we've launched a new container and mounted the volume from the dbdata container. We've then mounted 
a local host directory as /backup. Finally, we've passed a command that uses tar to backup the contents of the
 dbdata volume to a backup.tar file inside our /backup directory. When the command completes and the container
 stops we'll be left with a backup of our dbdata volume.

You could then restore it to the same container, or another that you've made elsewhere. Create a new container.

$ sudo docker run -v /dbdata --name dbdata2 ubuntu /bin/bash
Then un-tar the backup file in the new container's data volume.

$ sudo docker run --volumes-from dbdata2 -v $(pwd):/backup busybox tar xvf /backup/backup.tar
You can use the techniques above to automate backup, migration and restore testing using your preferred tools.
```
