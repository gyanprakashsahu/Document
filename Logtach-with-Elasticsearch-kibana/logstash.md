# Logstash Installation
- - -

## - Prerequisites

- - -

### First of all install following packages on your server where you want to collect your logs.

- - - 
- java
- rsyslog
- logstash
- nginx
- elasticsearch
- kibana 

- - -

```
yum -y install java-1.7.0-openjdk  (java version should be latest).
```

```
yum -y install rsyslog
```

```
yum -y install nginx- 
```

- - -

```
cd /etc/nginx/conf.d/ 
```

```
mv default.conf default.txt
```

```
cd /tmp; wget https://github.com/elasticsearch/kibana/raw/master/sample/nginx.conf
```

```
cp nginx.conf /etc/nginx/conf.d/default.conf
```

```
vi /etc/nginx/conf.d/default.conf
```

> Replace server name with your logstash server ip and document root /usr/share/nginx/kibana3

> Now Save and exit


```
cd /tmp; wget https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-1.3.2.noarch.rpm-  
```

```
rpm -ivh elasticsearch-1.3.2.noarch.rpm- 
```

```
vi /etc/elasticsearch/elasticsearch.yml
```


>> Find "#cluster.name: elasticsearch" (Uncomment this line and change the name as you wish).

>> Replace #path.conf: /path/of/conf WITH path.conf: /etc/elasticsearch

>> Replace #path.logs: /path/of/logs WITH path.logs: /var/log/elasticsearch

>> Replace #network.bind_host: 192.168.1.0 WITH network.bind_host: logstashServerIP

>> Replace network.publish_host: 192.168.1.0 WITH network.publish_host: logstashServerIP

>> Replace network.host: 192.168.1.0 WITH network.host: logstashServerIP

>> Uncomment #discovery.zen.ping.multicast.enabled: false

>> Below #discovery.zen.ping.unicast.hosts: ["host1", "host2:port"] add following line

```
discovery.zen.ping.unicast: ["LogstashServerIP"]
```

> At the bottom of the page add: 

```
http.jsonp.enable: true
http.cors.allow-origin: "/.*/"
http.cors.enabled: true
```

> exit the file

```
wget https://download.elasticsearch.org/logstash/logstash/packages/centos/logstash-1.4.2-1_2c0f5a1.noarch.rpm 
```

```
rpm -ivh logstash-1.4.2-1_2c0f5a1.noarch.rpm
```

```
wget https://download.elasticsearch.org/logstash/logstash/packages/centos/logstash-contrib-1.4.2-1_efd53ef.noarch.rpm
``` 

```
rpm -ivh logstash-contrib-1.4.2-1_efd53ef.noarch.rpm
```

```
wget https://download.elasticsearch.org/kibana/kibana/kibana-3.1.0.tar.gz- 
```

```
tar -xvzf kibana-3.1.0.tar.gz
```

```
mkdir -p /usr/share/nginx/kibana3
```

```
cp -R /tmp/kibana-3.1.0/* /usr/share/nginx/kibana3/ 
```

```
cd /usr/share/nginx/kibana3- 
```

```
vi config.js
```

> find  default_route     : '/dashboard/file/default.json', #and change it to:

```
default_route     : '/dashboard/file/logstash.json', 
```

> save and exit.

- - -
## Logstash Server Side Configuration**- 

- - -

* **Rsyslog Configuration**- 

```
vi /etc/rsyslog.conf
```

> Uncomment following lines-  

```
#$ModLoad imudp 
#$UDPServerRun 514
```
> add following lines in Global Directive Section

```
$FileOwner root
$FileCreateMode 0640
$DirCreateMode 0755

```
> Add Following lines at the bottom of the file- 

```
$template FILENAME, "/logstash/%HOSTNAME%.log"
authpriv.* ?FILENAME 
```

```
vi /etc/logstash/conf.d/input.conf (open this file and add following lines)-  
```

```
      1. input {
      2. file {
      3. path => "/logstash/*.log"
      4. }
      5. }
```

> Now save and exit.

```
vi /etc/logstash/conf.d/output.conf (Open this file and add following lines)-  
```

```
output {
elasticsearch {
host => "logstash-server-ip"
protocol => "http"
codec => "json"
flush_size => 10
idle_flush_time => 1
}
}
```

> Now save and exit.


* **Elasticsearch Configuration**- 

```
vi /etc/elasticsearch/elasticsearch.yml 
```

> find discovery.zen.ping.unicast.hosts: ["LogstashServerIP"] an modify it as follows:

```
discovery.zen.ping.unicast.hosts: ["LogstashServerIP"]
```

> Now save and exit. 


```
service logstash start
```

```
service elasticsearch start
```

```
service nginx start
```

- - -
                                      

## Add A Linux Server  

- - -


* **Client Side Configuration**- 

- - -
> editing in rsyslog.conf 

```
yum install rsyslog
```

```
vi /etc/rsyslog.conf
```

> find following lines and comment them: 

```
kern.* .
*.info;mail.none;authpriv.none;cron.none
mail.* 
cron.
*.emerg 
uucp,news.crit 
local7.
```

> find # *.* @@remote-host:514 and add follwoing line below this:

```
*.* @logstash-server-name-or-ip:514 
````

- - -


## Add A Windows Server** -

- - - 

*  **Client Side Configuration**- 

- -- 
> Download nxlog from http://sourceforge.net/projects/nxlog-ce/files/nxlog-ce-2.8.1248.msi/download
> Install it.
> Run Notepad with administrative priviliges and open C:\Program Files (x86)\nxlog\conf

> Find [input in] and Add following lines below [input in]- 

**NOTE:** For above line use greater than mark and less than mark at the place of square brackets.


```
Query   <QueryList>\
<Query Id="0">\
<Select Path="Security">*</Select>\
</Query>\
</QueryList>
```

Now find line "host 192.168.1.1" replace this ip with logstashServerIP



> Just after next line of this line add:- 

```
Exec    $EventReceivedTime = integer($EventReceivedTime) / 
1000000; \
to_json();

```

> Now save and exit. 

* Run services.msc and start the service nxlog .


- - -

###Logs Searching in Kibana

Lucene provides the ability to create your own queries through its API, it also provides a rich query language through the Query Parser.

```
Search a specific keyword		"keyword"

Wildcard Searches			test*   or   tes?

Fuzzy Searches				roam~

BOOlean Operators			use 'AND' 'OR' 'NOT'

Grouping Ex. 				"WEB-5" AND ("Dec 27" OR "Dec 30")

Field Grouping Ex.			(+"WEB-50" +"WEB-60")

### Use custom setting in kibana dashboard for date and time selection.

```
