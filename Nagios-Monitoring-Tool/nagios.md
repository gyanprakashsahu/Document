### BASIC NAGIOS SETUP

```
yum install nagios

yum install nagios-plugins-all

yum install httpd

```

#### Nagios Configuration: nagios.cfg

```

Add Few lines here:

cfg_file=/etc/nagios/objects/hostgroups.cfg

cfg_file=/etc/nagios/objects/servicegroups.cfg

cfg_file=/etc/nagios/objects/hostdependecy.cfg

cfg_file=/etc/nagios/objects/servicedependency.cfg

cfg_file=/etc/nagios/objects/hostescalation.cfg

cfg_dir=/etc/nagios/objects/hosts

```

#### Configuration Of cgi.cfg

```
vi /etc/nagios/cgi.cfg

#In this file just add your user with different type of privileges in many lines.You can add just readonly user also.

```

#### Configuration Of nrpe.cfg

```
vi /etc/nagios/nrpe.cfg

# Search allowed_hosts=127.0.0.1 and add nagios server ip  as below
allowed_hosts=127.0.0.1, "NagiosServerIP"

```
#### Make Some Files and Directories

```

touch /etc/nagios/objects/hostgroups.cfg

touch /etc/nagios/objects/servicegroups.cfg

touch /etc/nagios/objects/hostdependecy.cfg

touch /etc/nagios/objects/servicedependency.cfg

touch /etc/nagios/objects/hostescalation.cfg

mkdir /etc/nagios/objects/hosts

```

#### Now Configure Some hostgroups,servicegroups,hosts and services.

```
# open hostgroups.cfg file and add below lines for creating hostgroups. This is just an example. You can add multiple hostgroups in this file.

define hostgroup{
        hostgroup_name  Test
        alias           Test Servers
        members         DB-Serevr,WEB-Server
        }

# Now vi /etc/nagios/objects/servicegroups and add following lines. This is just and example. You can add multiple servicegroups in this file:

define servicegroup{
        servicegroup_name       sql-connectios
        alias                   Number of connections to sql server
        }

# vi /etc/nagios/objects/contact_groups. Make a contact of a user for getting mails of alerts and assign him a contact group. This is just an example you can make multiple contacts and contacts group. I am creating two contacts and one contact group.

define contact{
        contact_name              username1    
        use                       generic-contact # This is keyword. Dont change it.
        alias                     alias for the username1  
        email                     username1@gmail.com
        }

define contact{
        contact_name              username2
        use                       generic-contact # This is keyword. Dont change
 it.
        alias                     alias for the username2
        email                     username2@gmail.com
        }

define contactgroup{
        contactgroup_name       DB-Users
        alias                   DB-users
        members                 username1,username2
        }

# Now, cd /etc/nagios/objects/hosts. Make a host configuration file for monitoring. Make a file host1.cfg and add following lines. This is just an example. You can monitor various services for this host.

define host{
        use                     linux-server # This is keyword. Dont change it.
        hostgroup_name		Test  
        host_name               DB-Server
        alias                   data base server
        address                 IP-Of-This-Host
        contact_groups          DB-Users
        }

define service{
        use                             local-service         ; Name of service template to use
        host_name                       DB-Serevr
        service_description             PING
        check_command                   check_nrpe!check_ping
        contact_groups                  DB-Users
        }

```

#### Define this ping command in commands.cfg on the nagios server. Go to command.cfg and add following lines:

```

define command{
        command_name    check-host-alive
        command_line    $USER1$/check_ping -H $HOSTADDRESS$ -w 3000.0,80% -c 5000.0,100% -p 5
        }

```
#### Now, you have to install nagios agent on monitoring server and all the plugins. login into that server and do following:

```
yum install nrpe

yum install nagios-plugins-all

# Now, go to nrpe.cfg in this host and add the following command

command[check_internet_link]=/usr/local/libexec/check_ping -H $ARG1$ -w $ARG2$ -c $ARG3$

# In this nrpe.cfg find allowed_host=127.0.0.1 and edit it as foolowing

allowed_host=127.0.0.1, "NagiosServerIP"
```

#### service nrpe start at client.


####Now test the configuration and then restart the nagios service and start monitoring the DB-Server.

```

service nagios configtest

service nagios restart

```

#### Other escalation and dependency configuration is advanced . We will talk about that later.
