### Docker
```
Docker is “an open source project to pack, ship and run any application as a lightweight container.” 
It builds on LinuX Containers, which uses the cgroups functionality to enable creation and running of 
multiple isolated Linux virtual environments (VE) on a single control host. So unlike a VM, a VE like 
Docker doesn’t create its own virtual computer with a distinct OS and processors and hardware emulation. 
A VE is VM-lite; it rides on the already existing kernel’s image of the underlying hardware, and only 
creates a ‘container’ in which to run your apps, and even recreate the OS if you want since the OS is 
also just another app running on the kernel. It places only a little extra load on the system, so unlike 
the traditional VM there is very little overhead when using Docker. 
```
```
Note: Few people compares Docker with Vagrant while both are different things. Docker is really an extension 
of LXC’s capabilities. It is an Virtual Environmentwhile Vagrant is a VM manager.It allows you to script and 
package the VM config and the provisioning setup. It is designed to run on top of almost any VM tool 
– VirtualBox, VMWare, AWS, etc. You can integrate Vagrant with CM tools such as Puppet and Chef to 
provision your own VM setups and configs.
```
### Reasons To Use Docker :

#### 1. Faster Development Life Cycle :
```
Docker is perfect for helping you with the development lifecycle. Docker allows your developers to 
develop on local containers that contain your applications and services. It can then integrate into 
a continuous integration and deployment workflow.

For example, your developers write code locally and share their development stack via Docker with their 
colleagues. When they are ready, they push their code and the stack they are developing onto a test 
environment and execute any required tests. From the testing environment, you can then push the Docker 
images into production and deploy your code.
```
#### 2. Easy Scaling And Deployment: 
```
Docker containers can run on a developer's local host, on physical or virtual machines in a data center, 
or in the Cloud. Docker's portability and lightweight nature also make dynamically managing workloads easy. 
You can use Docker to quickly scale up or tear down applications and services.
```

### Few Basic Definitions:

#### Docker Images:
```
A Docker image is a read-only template. For example, an image could contain an Ubuntu operating system 
with Apache and your web application installed.Docker images are the build component of Docker.
```
#### Docker Registries :
```
Docker registries hold images. These are public or private stores from which you upload or download images.
```
#### Docker containers :
```
Docker containers are similar to a directory. A Docker container holds everything that is needed for 
an application to run. Each container is created from a Docker image. Docker containers can be run, 
started, stopped, moved, and deleted. Each container is an isolated and secure application platform. 
Docker containers are the run component of Docker.
```
####Dockerfiles:
```
Each Dockerfile is a script, composed of various commands (instructions) and arguments listed successively to automatically perform actions on a base image in order to create (or form) a new one.
```
### Demonstration Of Docker: Production Environment
Note: Docker works on the basis of Daemon-client structure. If you install docker onto a machine/server, both daemon and client works on same machine/server. But for better practice in production environment you should run docker daemon on your Server and client on your/Developer's local machine because you woud'nt want to give access at server to all the developers in company. You can make your private docker-registry server for saving various images so that you can pull/push images without using more bandwidth.
                     I am going to use Centos as server and your/Developers MAC/Windows machine for client. Suppose You have a newly fresh installed Centos server.

* Install Docker
```
yum install docker docker-io
```
* Run Docker daemon in background, listening to any ip address on your host on both the network and a unix socket (It should be in your Company's private network otherwise it is vulnerable).
```
docker -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock -d & 
```
####Install Boot2docker on your local machine from it's official website.

* Open terminal and run below commands to get docker shell on local machine:
```
boot2docker init
boot2docker start
boot2docker ssh
```
* Run following command to connect with Docker Server-
```
export DOCKER_HOST=tcp://docker-server-IP:2375
```
Now whatever you want to do on docker you can do from your local machine to docker server.

####You can make your own base centos image:
NOTE: You have to just execute following script. This Script creates optimal and minimul base image for centos. It ask for an argument which would be your image name (Use only small letters in naming it.). If you execute this script then you don't need to follow step 1 and 2, directly jump to step 3.
```
#!/usr/bin/env bash
#
# Create a base CentOS Docker image.
#
# This script is useful on systems with yum installed (e.g., building
# a CentOS image on CentOS).  See contrib/mkimage-rinse.sh for a way
# to build CentOS images on other systems.

usage() {
    cat <<EOOPTS
$(basename $0) [OPTIONS] <name>
OPTIONS:
  -y <yumconf>  The path to the yum config to install packages from. The
                default is /etc/yum.conf.
EOOPTS
    exit 1
}

# option defaults
yum_config=/etc/yum.conf
while getopts ":y:h" opt; do
    case $opt in
        y)
            yum_config=$OPTARG
            ;;
        h)
            usage
            ;;
        \?)
            echo "Invalid option: -$OPTARG"
            usage
            ;;
    esac
done
shift $((OPTIND - 1))
name=$1

if [[ -z $name ]]; then
    usage
fi

#--------------------

target=$(mktemp -d --tmpdir $(basename $0).XXXXXX)

set -x

mkdir -m 755 "$target"/dev
mknod -m 600 "$target"/dev/console c 5 1
mknod -m 600 "$target"/dev/initctl p
mknod -m 666 "$target"/dev/full c 1 7
mknod -m 666 "$target"/dev/null c 1 3
mknod -m 666 "$target"/dev/ptmx c 5 2
mknod -m 666 "$target"/dev/random c 1 8
mknod -m 666 "$target"/dev/tty c 5 0
mknod -m 666 "$target"/dev/tty0 c 4 0
mknod -m 666 "$target"/dev/urandom c 1 9
mknod -m 666 "$target"/dev/zero c 1 5

yum -c "$yum_config" --installroot="$target" --releasever=/ --setopt=tsflags=nodocs \
    --setopt=group_package_types=mandatory -y groupinstall Core
yum -c "$yum_config" --installroot="$target" -y clean all

cat > "$target"/etc/sysconfig/network <<EOF
NETWORKING=yes
HOSTNAME=localhost.localdomain
EOF
# effectively: febootstrap-minimize --keep-zoneinfo --keep-rpmdb
# --keep-services "$target".  Stolen from mkimage-rinse.sh
#  locales
rm -rf "$target"/usr/{{lib,share}/locale,{lib,lib64}/gconv,bin/localedef,sbin/build-locale-archive}
#  docs
rm -rf "$target"/usr/share/{man,doc,info,gnome/help}
#  cracklib
rm -rf "$target"/usr/share/cracklib
#  i18n
rm -rf "$target"/usr/share/i18n
#  sln
rm -rf "$target"/sbin/sln
#  ldconfig
rm -rf "$target"/etc/ld.so.cache
rm -rf "$target"/var/cache/ldconfig/*

version=
if [ -r "$target"/etc/redhat-release ]; then
    version="$(sed 's/^[^0-9\]*\([0-9.]\+\).*$/\1/' "$target"/etc/redhat-release)"
fi

if [ -z "$version" ]; then
    echo >&2 "warning: cannot autodetect OS version, using '$name' as tag"
    version=$name
fi

tar --numeric-owner -c -C "$target" . | docker import - $name:$version
docker run -i -t $name:$version echo success

rm -rf "$target"
```
Now, jump to step 3. Suppose your base image name centosbaseimage

* make your account at docker hub website to pull a base centos image and then from terminal search for desired images:
```
docker serach centos
```
It will ask for your docker hub credentials (Only Once), give them. Then it will show all the images of centos available on dockerHub. 

* Choose one image and pull it. Suppose you choose centosbaseimage.
```
docker pull centosbaseimage
```
* You can see your image by following command-
```
docker images
```
* Now run this image and login in it.
```
docker -t -i centosbaseimage /bin/bash
```
Now, you will be at centos shel. Made some changes on it.

*Suppose you installed apache on it.
```
yum install httpd
```
* Give our container access to traffic:
```
docker run -d -p 80:80 centosbaseimage /usr/sbin/httpd -D FOREGROUND
```
Now open another terminal on your local machine, do ssh to boot2docker and run follwing command to see the container id:
```
docker images
```
Note down the container id of your base image. Suppose it is 6f107ecfa30d 

* Now commit the changes.
```
docker commit 6f107ecfa30d centosliveimage.
```
* Exit from the terminal of your local machine. You service httpd is running and accessible from other machines in same network.

#### Make Dockerfile:
Suppose load is increasing on your server and you want to scale (increse) the number of servers. You can make a script to run dockerfile which runs after a certain load. But here, i m going to run Dockerfile manually to create another server like centosbaseimage. Follow below steps to make new dcoker image from base image  with dockerfile:

* Make an empty directory i.e. /make-dockerfile and create a file in it named Dockerfile. Make it executable and copy below lines:
```
FROM centosbaseimage
RUN yum update
RUN yum install -y httpd
EXPOSE 80
ENTRYPOINT /usr/sbin/httpd
```
* Run below command to make other image:
```
docker build -t centosliveimage2
```
*Run this image:
```
docker run -d -p 80:80 centosliveimage2 /usr/sbin/httpd -D FOREGROUND
```
Now your second server is up.

#### Make Your own docker registry server to save various images:
Make other server for docker-registry otherwise things will be mess up. Follow below steps to make private docker-registry server:

* Install docker and docker-registry on new server:
```
yum install docker-io docker-registry
```
* Start these services:
```
service docker-io start
service docker-registry start
```
* At your local machine , do ssh for boot2docker.
```
boot2docker ssh
```
* If any image you have made before on your local machine see it's image id by 
`docker images` command and note it down. Now delete docker.pid file in /var/run
```
rm docker.pid
```
* run following command to run docker as daemon in background at your local machine:
```
docker -d --insecure-registry docker-registry-server-ip:5000 &
```
*Tag you image:
```
docker tag your-image-id docker-registry-server-ip:5000/your-image-name
```
* Now, push your image on registry server:
```
docker push your-image-id docker-registry-server-ip:5000/your-image-name
```
Now, you can see your image on registry server and pull your image anywhere.

####Few Docker commands:
```
PURPOSE				COMMAND
Build an image	        	docker build –rm=true .
Install an image		docker pull ${IMAGE}
List of installed images	docker images
List of installed images (detailed listing)	docker images –no-trunc
Remove an image			docker rmi ${IMAGE_ID}
Remove all untagged images	docker rmi $(docker images | grep “^” | awk “{print $3}”)
Remove all images		docker rm $(docker ps -aq)
Run a container	docker run
List containers	docker ps
Stop a container		docker stop ${CID}
Find IP address of the container docker inspect –format ‘{{ .NetworkSettings.IPAddress }}’ ${CID}
Attach to a container		docker attach ${CID}
Remove a container		docker rm ${CID}
Remove all containers		docker rm $(docker ps -aq) 
```
#### Dockerfile Commands:
```
FROM <image>	The FROM instruction sets the Base Image for subsequent instructions

MAINTAINER <name>  The MAINTAINER instruction allows you to set the Author field of the generated images.

RUN <command>    The RUN instruction will execute any commands in a new layer on top of the current image and commit the results.

CMD ["executable","param1","param2"]  The main purpose of a CMD is to provide defaults for an executing container

EXPOSE <port> [<port>...]  The EXPOSE instructions informs Docker that the container will listen on the specified network ports at runtime.

ENV <key> <value>   The ENV instruction sets the environment variable <key> to the value <value>. This value will be passed to all future RUN instructions.

ADD <src>... <dest>   The ADD instruction copies new files, directories or remote file URLs from <src> and adds them to the filesystem of the container at the path <dest>.

COPY <src>... <dest>  The COPY instruction copies new files or directories from <src> and adds them to the filesystem of the container at the path <dest>.

ENTRYPOINT command param1 param2   An ENTRYPOINT allows you to configure a container that will run as an executable.

ETC.
```

