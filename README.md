# Cloudera_Upgrade

## This docs summarize the steps of upgrading from cloudera 5.4.0 to 5.7.0

### Step 1 ： backup the databases
The database information can be found at /etc/cloudera-scm-server/db.properties

The database username and password can be found by click __instance__**>**__server_name__**>**__process__ in cloudera manager and explore those conf or xml files.
1. backup cloudera manager databases
activity monior:
```ruby  
mysqldump -h hostname -uamon -ppassword activity_monitor > /tmp/backup/amon-backup.$(date +%Y%m%d).sql
```
reports manager:
```ruby
mysqldump -h hostname -urman -ppassword reports_manager > /tmp/backup/rman-backup.$(date +%Y%m%d).sql
```
2. backup server databases
hive:
```ruby  
mysqldump -h hostname -uhive -ppassword hive > /tmp/backup/hive-backup.$(date +%Y%m%d).sql
```  
hue:
```ruby  
mysqldump -h hostname -uhue -ppassword hue > /tmp/backup/hue-backup.$(date +%Y%m%d).sql
```  
oozie:
```ruby  
mysqldump -h hostname -uoozie -ppassword oozie > /tmp/backup/oozie-backup.$(date +%Y%m%d).sql
```  
### Step 2 ：cloudera manager server, database, agent
Stop cloudera mananger
``` 
sudo service cloudera-scm-server stop
```  
Stop embedded database( if it is used)
``` 
sudo service cloudera-scm-server-db stop
``` 
Stop cloudera agent( if it is used)
``` 
sudo service cloudera-scm-agent stop
``` 
### Step 3 ：Set up repos (with online resources)
Download repo file
``` 
wget https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/cloudera-manager.repo
``` 
Copy repos file to yum
``` 
copy cloudera-manager.repo /etc/yum.repos.d/
``` 
Edit (vim) baseurl in repos file to the verison you want to upgrade
``` 
name=Cloudera Manager
baseurl=https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/5.7.0/
gpgkey = https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/RPM-GPG-KEY-cloudera
gpgcheck = 1
``` 
### Step 4 ：Upgrade Cloudera manager
You may need the __python-psycopg2__ before upgrade, which can be foun here ([link](http://yum.oracle.com/repo/OracleLinux/OL6/8/base/x86_64/index.html)) and `rpm -i python-psycopg2_filename` 
Start upgrade:
``` 
sudo yum clean all
sudo yum upgrade cloudera-manager-server cloudera-manager-daemons cloudera-manager-server-db-2 cloudera-manager-agent
``` 
Restart cloudera manager:
```
sudo service cloudera-scm-server-db start
sudo service cloudera-scm-server start
```

Login to cloudera mangager and go through steps of upgrade wizard

### Step 5 ：Upgrade CDH with parcels

1. Click __Home__>__Status__>__upgrade cluster__
2. You may not find the CDH in newer version and  need to __Modify the Remote Parcel Repository URLs__
3. Add 'http://archive.cloudera.com/cdh5/parcels/5.7.0/'
4. And then following the wizard steps





