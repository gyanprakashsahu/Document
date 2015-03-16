### Docker: networking

When Docker starts, it creates a virtual interface named docker0 on the host machine. It randomly chooses an address and subnet from the private range defined by that are not in use on the host machine, and assigns it to docker0.
                  When we run an image as a docker container and do ifconfig in container bash shell it will show docker 0 and veth035e (It may be different). docker0 is the linux bridge and veth035e is an interface on that bridge.
                   docker0 assigned an available IP in it's subnet to the container. Default gateway will be the docker0 IP for this container. You can see it by `traceroute` command.
                   You can make your own bridge (docker0) with your desired ip and assign it to your containers. For this, follow below steps:

```
service docker stop

# Now make an file called ifcfg-docker0

vi /etc/sysconfig/network-script/ifcfg-docker0

# Now write following lines :

EVICE="docker0"
TYPE="Bridge"
ONBOOT="yes"
NM_CONTROLLED="no"
BOOTPROTO="static"
IPADDR= YourIp
NETMASK= Your NetMask

## Save and exit and restart the network service 
## You can define a subnet at the place of IP.
service network restart

## Now, when you will make any image, it will be assigned an IP in your IP range of bridge.
```
#### Port Binding:

1. You can bind a container port with host port at the time of starting container as follows:

docker run -d -p HostPort:ContainerPort YourImage /usr/sbin/httpd

#### Docker Container Linking:
```
Suppose you made a container name "apache". For Example:
docker run -d -P --name apache srbhkmrsngh/webapp python app.py

# Now you have to delete this container and want to link this name with other container name db. Do so-

docker rm -f web
docker run -d -P --name web --link db:db training/webapp python app.py
```
#### Few Important Parameters Of Dcoker Networking:

If you run these parameters at the time of starting the image. You will get following effect:
#### --net=bridge
The default action, that connects the container to the Docker bridge.

#### --net=host
It live “outside” in the main Docker host and have full access to its network interfaces while container processes will still be confined to their own filesystem and process list and resource limits.In this case Docker does not containerize the container's networking.

#### --net=container:NAME_or_ID
Tells Docker to put this container's processes inside of the network stack that has already been created inside of another container. The new container's processes will be confined to their own filesystem and process list and resource limits, but will share the same IP address and port numbers as the first container, and processes on the two containers will be able to connect to each other over the loopback interface.

#### --net=none
 Tells Docker to put the container inside of its own network stack but not to take any steps to configure its network, leaving you free to build any of the custom configurations.
               But If you set it manually then after restarting the image, all configuration will be destroyed.

### NOTE: You can't specify private IP for docker container. For this purpose you should use "FIG" along with Docker. I will explain it later.

