### Mysql Replication:

```
I have two servers with mysql installed and running.
```
Now open my.cnf in master and make following changes:

```
bind-address            = IpOfMaster
server-id               = 1 #must be unique
log_bin                 = /var/log/mysql/mysql-bin.log
binlog_do_db            = DatabaseNameToBeReplicated

```
Start MySql service and login into mysql as root and make following changes: 

```

Flush Privileges;

GRANT REPLICATION SLAVE ON *.* TO 'slave_user'@'%' IDENTIFIED BY 'password';

use DatabaseToBeReplicated;

FLUSH TABLES WITH READ LOCK;

SHOW MASTER STATUS;

Note: Save these details For Further use.

```

Now dump your database in NewFile.

```
mysqldump -u root -p --opt DatabaseToBeReplicated > DatabaseToBeReplicated.sql

```

Now unlock the Tables of database.

```
UNLOCK TABLES;

Quit;

```
#### Configuration of slave database

Login into mysql and follow the below steps:

```
CREATE DATABASE DatabaseToBeReplicated;

exit;

```

Import the database that you dumped before sometime from master.

```
mysql -u root -p DatabaseToBeReplicated < /path/to/DatabaseToBeReplicated.sql

```
Now configure my.cnf of slave database.

```
vi /etc/mysql/my.cnf

server-id               = 2
relay-log               = /var/log/mysql/mysql-relay-bin.log
log_bin                 = /var/log/mysql/mysql-bin.log
binlog_do_db            = DatabaseToBeCreated

```
Now open the mysql prompt at slave and do the following-

```
CHANGE MASTER TO MASTER_HOST='IP Of Master',MASTER_USER='slave_user', MASTER_PASSWORD='password', MASTER_LOG_FILE='Bin File of show master status', MASTER_LOG_POS=  position as seen in show master status;
```
Activate the Slave server.

```
start slave;
```
Now you can see slave status:

```
SHOW SLAVE STATUS\G
```

###### This is basic setting of mysql replication.
