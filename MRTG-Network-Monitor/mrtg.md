### MRTG Installation on Centos For Traffic Monitoring.


You Need SNMP installed and configured for tarffic monitoring of network devices with mrtg. So you have to install snmp and it's utilities also.

```
yum install net-snmp net-snmp-utils mrtg httpd

```

Now Configure the snmp file and start it. Set your community string which is on network device to be monitor.

```

com2sec AllUser default YourCommunityString

com2sec readonly        127.0.0.1 public

Now save and exit and restart the snmp service.
```
Verify the snmp connection by :

```
snmpwalk -v 1 -c YourCommunityString@IpOfDevice IP-MIB::ipAdEntIfIndex

It gives the IP of device.
```
Now configure MRTG 

```
vi /etc/mrtg/mrtg.cfg

HtmlDir: /var/www/mrtg

ImageDir: /var/www/mrtg

LogDir: /var/lib/mrtg

ThreshDir: /var/lib/mrtg

RunAsDaemon: Yes

Target[IpOfDevice]: port or vlan:CommunityString@IpOfDevice:

MaxBytes[IpOfDevice]: 12500000

Title[IpOfDevice]: Traffic Analysis

PageTop[IpOfDevice]: <H1>Stats of Vlan or port traffic for our Ethernet on Network Device</H1>
```
Now create index by below command:

```
indexmaker /etc/mrtg.cfg > /var/www/mrtg/index.html
```

Edit mrtg.conf in etc/httpd/conf.d/

```
Alias /mrtg /var/www/mrtg

<Location /mrtg>
    Order allow,deny
    Allow from all
    Allow from 127.0.0.1
    Allow from ::1
    # Allow from .example.com
</Location>
```

Edit Cron File for run the mrtg job periodically in 1 minute.

```
crontab -e
*/1 * * * * env LANG=C /usr/bin/mrtg /etc/mrtg/mrtg.cfg
```
#### You can use following init script if you dont want to run the cron job. Copy this script in init.d directory and chmod shoul be executable.

```
PATH=/sbin:/usr/sbin:/bin:/usr/bin
DESC="Multi Router Traffic Grapher (MRTG)"
NAME="mrtg"
DAEMON=/usr/bin/$NAME
DAEMON_ARGS="/etc/mrtg.cfg –user root --logging /var/log/mrtg.log"

case "$1" in
  start)
        echo "Starting $DESC..."
        env LANG=C $DAEMON $DAEMON_ARGS
        echo "$NAME started."
        ;;
  stop)
        echo "Stopping $DESC..."
        pkill $NAME &> /dev/null
        echo "$NAME stopped."
        ;;
  *)
        FULL_NAME=/etc/init.d/$NAME
        echo "Usage: $FULL_NAME {start|stop}." >&2
        ;;
esac

exit

```

Now You can use 'service mrtg start/stop' command. Now no need to that cron job. 
Now start the httpd , mrtg and snmpd service.

Invoke the http://IpOfDevice/mrtg from url.

#### Note: You can use cfgmaker command for creating configuration file for mrtg. example of this command is given below:

cfgmaker --global 'WorkDir: /var/www/html/mrtg' --output /etc/mrtg/mrtg.cfg CommunityString@@IpOfDevice.


