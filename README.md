# source
Enter with root
```
sudo -i
```

Rename host
```
hostnamectl set-hostname source
```

Edit network setting (Warning: the network card make internal)
```
vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
```

comment line BOOTPROTO="dhcp"
```
#BOOTPROTO="dhcp"
```

add next lines
```
BOOTPROTO="static"
IPADDR=10.0.1.11
NETMASK=255.255.255.0
GATEWAY=10.0.1.1
DNS1=10.0.1.1
DNS2=8.8.8.8
```

Restart host
```
shutdown -r now
```

Enter with root
```
sudo -i
```

Install git
```
yum -y install git
```

Connect to GitHub repo for download to host
```
git clone https://github.com/SergSha/source.git
```
```
#------- For to upload to GitHub -------------
# Make pair keys
#ssh-keygen #Enter Enter Enter
# Copy text of pub key and paste into GitHub:
#cat /root/.ssh/id_rsa.pub
#https://github.com/settings/keys
# Connect to GitHub repo (source)
#git clone git@github.com:SergSha/source.git
------------------------------------------------
```

Make the file inst_set.sh execute
```
chmod u+x /root/source/inst_set.sh
```

Start source_inst_set.sh
/root/source/inst_set.sh

Start MySQL command console
```
mysql -uroot -p
```

Add user for replication
```
CREATE USER repl@'10.0.1.12' IDENTIFIED WITH 'caching_sha2_password' BY 'Tn91Uk57@';
```

Даю пользователю repl права на репликацию
```
GRANT REPLICATION SLAVE ON *.* TO repl@'10.0.1.12';
```

Get master status
```
SHOW MASTER STATUS\G
*************************** 1. row ***************************
             File: binlog.000001
         Position: 1373
     Binlog_Do_DB:
 Binlog_Ignore_DB:
Executed_Gtid_Set:
1 row in set (0.00 sec)
{Remember binlog & Position}
```

Exit
```
exit;
```

Get backup of databases MySQL
```
mysqldump -uroot -p --all-databases --single-transaction --source-data=2 > /root/mydb_all.sql
```

Copy backup to replica
```
scp /root/mydb_all.sql root@10.0.1.12:/root/
```

Go to install and setting replica host
```
------------------------------------------------------
# After setting replica host
mysql -uroot -p
SHOW DATABASES;
CREATE DATABASE cars_db;
SHOW DATABASES;
USE cars_db;
CREATE TABLE new_cars (id INT NOT NULL AUTO_INCREMENT, name VARCHAR(20), year YEAR, price INT, PRIMARY KEY(id));
SHOW TABLES;
INSERT INTO new_cars (name, year, price) VALUES ("Toyota", 2021, 20000), ("Nissan", 2021, 18000);
SELECT * FROM new_cars;
```

Go to replica host for test replication
