####Install httpd with php on your server:

```
$yum install httpd mod_ssl

## Now configure it as per requirement.

$chkconfig httpd on

## Now install few common modules of php for httpd

$yum install php-common php-gd php-mcrypt php-pear php-pecl-memcache / 
           php-mhash php-mysql php-xml php-devel php-snmp php-xmlrpc /
          php-snmp php-imap php-memcache php-mbstring php-xcache /
         php-gd 

$yum start httpd

```
Now, You have to configure httpd.conf and php.ini files. I have shown a demo of configuration in this documentation for httpd and php.
