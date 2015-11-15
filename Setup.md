# Setup #
```
#step 1. download FastDFS source package and unpack it
tar xzf FastDFS_v1.x.tar.gz
#for example:
tar xzf FastDFS_v1.11.tar.gz

#step 2. enter the FastDFS dir
cd FastDFS

#step 3. make all
./make.sh

#step 4. make install
./make.sh install

#step 5. edit/modify the config file of tracker and storage

#step 6. run server programs
#start the tracker server:
/usr/local/bin/fdfs_trackerd <tracker_conf_filename>

#start the storage server:
/usr/local/bin/fdfs_storaged <storage_conf_filename>

#step 7. run test program
#run the client test program:
/usr/local/bin/fdfs_test <storage_conf_filename> <operation>
#for example, upload a file:
/usr/local/bin/fdfs_test conf/storage.conf upload /usr/include/stdlib.h 

#step 8. run monitor program
#run the monitor program:
/usr/local/bin/fdfs_monitor <storage_conf_filename>
```
# Config #
## tracker server config file sample ##
```
disabled=false
bind_addr=
port=22122
network_timeout=20
base_path=/home/yuqing/FastDFS
max_connections=256

#0: round robin
#1: specify group
#2: load balance
store_lookup=0

#when store_lookup set to 1(specify group), must set store_group to the group name
store_group=group2

#0: round robin
#1: the first server order by ip address
#recommand use the first server to upload file
store_server=1

#reserved storage space for system or other applications.
#if the free(available) space of any stoarge server in a group <= reserved_storage_space,
#no file can be uploaded to this group.
#bytes unit can be one of follows:
### G or g for gigabyte(GB)
### M or m for megabyte(MB)
### K or k for kilobyte(KB)
### no unit for byte(B)
reserved_storage_space = 4GB

#standard log level as syslog, case insensitive, value list:
### emerg for emergency
### alert
### crit for critical
### error
### warn for warning
### notice
### info
### debug
log_level=info

#unix group name to run this program,
#not set (empty) means run by the group of current user
run_by_group=

#unix username to run this program,
#not set (empty) means run by current user
run_by_user=

# allow_hosts can ocur more than once, host can be hostname or ip address,
# "*" means match all ip addresses, can use range like this: 10.0.1.[1-15,20] or
# host[01-08,20-25].domain.com, for example:
# allow_hosts=10.0.1.[1-15,20]
# allow_hosts=host[01-08,20-25].domain.com
allow_hosts=*
```

**storage server config file sample:
```
disabled=false
group_name=group1
bind_addr=
port=23000
network_timeout=20
heart_beat_interval=30
stat_report_interval=600
base_path=/home/yuqing/FastDFS
sync_wait_msec=200
max_connections=256

tracker_server=10.62.164.83:22122
tracker_server=10.62.164.84:22122

#standard log level as syslog, case insensitive, value list:
### emerg for emergency
### alert
### crit for critical
### error
### warn for warning
### notice
### info
### debug
log_level=info

#unix group name to run this program,
#not set (empty) means run by the group of current user
run_by_group=

#unix username to run this program,
#not set (empty) means run by current user
run_by_user=

# allow_hosts can ocur more than once, host can be hostname or ip address,
# "*" means match all ip addresses, can use range like this: 10.0.1.[1-15,20] or
# host[01-08,20-25].domain.com, for example:
# allow_hosts=10.0.1.[1-15,20]
# allow_hosts=host[01-08,20-25].domain.com
allow_hosts=*
```**

## Item detail ##
### 1. common items ###

|item name      |  type  | default | Must |
|:--------------|:-------|:--------|:-----|
|base\_path      | string |         |  Y   |
|disabled       | boolean| false   |  N   |
|bind\_addr      | string |         |  N   |
|network\_timeout| int    | 30(s)   |  N   |
|max\_connections| int    | 256     |  N   |
|log\_level      | string | info    |  N   |
|run\_by\_group   | string |         |  N   |
|run\_by\_user    | string |         |  N   |
|allow\_hosts    | string | `*`     |  N   |

#### memo ####
  * base\_path is the base path of sub dirs: data and logs.
  * base\_path must exist and it's sub dirs will be automatically created if not exist.
    * $base\_path/data: store data files
    * $base\_path/logs: store log files

### 2. tracker server items ###

|item name      |  type  | default | Must |
|:--------------|:-------|:--------|:-----|
|port           | int    | 22000   |  N   |
|store\_lookup   | int    |  0      |  N   |
|store\_group    | string |         |  N   |
|store\_server   | int    |  1      |  N   |
|reserved\_storage\_space| string |   1GB      |  N   |

#### memo ####
  * the value of store\_lookup is:
    * 0: round robin (default)
    * 1: specify group
    * 2: load balance (supported since V1.1)
  * store\_group is the name of group to store files. when store\_lookup set to 1(specify group), store\_group must be set to a specified group name.
  * reserved\_storage\_space is the reserved storage space for system or other applications. if the free(available) space of any stoarge server in a group <= reserved\_storage\_space, no file can be uploaded to this group (since V1.1). bytes unit can be one of follows:
    * G or g for gigabyte(GB)
    * M or m for megabyte(MB)
    * K or k for kilobyte(KB)
    * no unit for byte(B)
> > for example: 4GB or 4G

### 3. storage server items ###

|item name           |  type  | default | Must |
|:-------------------|:-------|:--------|:-----|
|group\_name          | string |         |  Y   |
|tracker\_server      | string |         |  Y   |
|port                | int    | 23000   |  N   |
|heart\_beat\_interval | int    |  30(s)  |  N   |
|stat\_report\_interval| int    | 300(s)  |  N   |
|sync\_wait\_msec      | int    | 100(ms) |  N   |

#### memo ####
  * tracker\_server can ocur more than once, and tracker\_server format is "host:port", host can be hostname or ip address.