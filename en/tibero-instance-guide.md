## Create a Tibero Instance


To use Tibero, you must first create an instance.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image1.png)

Click the **Create** button in **Create Tibero Instance** to go to **Compute > Instance > Create Instance**.


### Image

The image provided by default includes CentOS 7.8 with Tibero 6 version (Tibero 6 FS07 CS2005 build194603 r144754).

![image.png](http://static.toastoven.net/prod_tibero/tibero_image2.png)


### Instance Information

* Availability Zone: Select 'Any Availability Zone'
* Instance Name: The instance name of the server to be created
* Flavor
    * Select 8vCPU 8G Memory, which is the minimum recommended specification to use Tibero, or higher.
* Key pair: Create a new PEM key or use an existing key. If you create a new one, download and keep the key.
* Block Storage Type
    * The root volume. SSD is recommended for faster speed.
    * Set 50 GB or higher to avoid root full.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image3.png)


### Network

![image.png](http://static.toastoven.net/prod_tibero/tibero_image4.png)

### Floating IP

For SSH connection, enable the floating IP.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image5.png)

### Security Group

Because SSH access to the instance is required, you must create and use a security group that allows access to the SSH port (22).

![image.png](http://static.toastoven.net/prod_tibero/tibero_image6.png)

![image.png](http://static.toastoven.net/prod_tibero/tibero_image7.png)


### Additional Block Storage

Create an additional volume in addition to the root volume.
Tibero Machine Image (TMI) requires an additional volume of 150GB, so an **additional block storage of 150G or more** must be set.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image8.png)


### Complete Instance Creation

After entering all the information above, click the **Create Instance** button, and an instance is created as shown below.
The created instance includes the installation file for database creation, and for actual installation and database creation, you need to connect to the instance with an SSH client and perform the TMI installation process.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image9.png)


## Install TMI


### Connect to Instance

After the instance creation is complete, use SSH to access the instance.
The instance must have a floating IP associated and TCP port 22 (SSH) must be allowed in the security group.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image10.png)

Connect to the instance using an SSH client and the set key pair.
For a detailed guide on SSH connection, refer to [SSH Connection Guide](https://docs.toast.com/en/Compute/Instance/en/overview/#linux).

### Install TMI

Run the dbca command in the /root path with the root account.
```
$ ./dbca OS_ACCOUNT DB_NAME DB_CHARACTERSET DB_PORT
```


```
[centos@tiberoinstance ~]$ sudo su root
[root@tiberoinstance centos]# cd
[root@tiberoinstance ~]# pwd
/root
[root@tiberoinstance ~]# ./dbca nhncloud tiberotestdb utf8 8639
```

| No | Item | Argument value |
| :---: | --- | --- |
| 1 | OS\_ACCOUNT | OS account under which Tibero runs |
| 2 | DB\_NAME | DB\_NAME used in Tibero (SID) |
| 3 | DB\_CHARACTERSET | DB character set used by Tibero |
| 4 | DB\_PORT | Service IP port used by Tibero |

### Complete Installation

When the dbca command is run, the progress is output and the database is created in the nomount mode. It takes less than 10 minutes. When finished, the output is as below.

```
SQL>
System altered.

SQL>
System altered.

SQL> Disconnected.
[root@tiberoinstance ~]#
```


### Check the Operation and the Installation Log

Check if Tibero is running.

```
[root@tiberoinstance ~]# ps -ef | grep tbsvr
nhncloud 13933     1  0 09:10 ?        00:00:04 tbsvr          -t NORMAL -SVR_SID tiberotestdb
nhncloud 13944 13933  0 09:10 ?        00:00:00 tbsvr_FGWP006  -t NORMAL -SVR_SID tiberotestdb
nhncloud 13945 13933  0 09:10 ?        00:00:00 tbsvr_FGWP007  -t NORMAL -SVR_SID tiberotestdb
nhncloud 13946 13933  0 09:10 ?        00:00:00 tbsvr_FGWP008  -t NORMAL -SVR_SID tiberotestdb
nhncloud 13947 13933  0 09:10 ?        00:00:08 tbsvr_FGWP009  -t NORMAL -SVR_SID tiberotestdb
nhncloud 13948 13933  0 09:10 ?        00:00:00 tbsvr_PEWP000  -t NORMAL -SVR_SID tiberotestdb
nhncloud 13949 13933  0 09:10 ?        00:00:00 tbsvr_PEWP001  -t NORMAL -SVR_SID tiberotestdb
nhncloud 13950 13933  0 09:10 ?        00:00:00 tbsvr_PEWP002  -t NORMAL -SVR_SID tiberotestdb
nhncloud 13951 13933  0 09:10 ?        00:00:00 tbsvr_PEWP003  -t NORMAL -SVR_SID tiberotestdb
nhncloud 13952 13933  0 09:10 ?        00:00:09 tbsvr_AGNT     -t NORMAL -SVR_SID tiberotestdb
nhncloud 13953 13933  0 09:10 ?        00:00:07 tbsvr_DBWR     -t NORMAL -SVR_SID tiberotestdb
nhncloud 13954 13933  0 09:10 ?        00:00:00 tbsvr_RCWP     -t NORMAL -SVR_SID tiberotestdb
root     21066 12596  0 11:06 pts/0    00:00:00 grep --color=auto tbsvr
[root@tiberoinstance ~]#
```

The installation log can be found in /root/.dbset.log.

```
[root@tiberoinstance ~]# ls -al
합계 36
dr-xr-x---.  4 root root  154  1월 13 09:12 .
dr-xr-xr-x. 23 root root 4096  1월 13 09:05 ..
-rw-------   1 root root  264  1월 12 19:08 .bash_history
-rw-r--r--.  1 root root   18 12월 29  2013 .bash_logout
-rw-r--r--.  1 root root  176 12월 29  2013 .bash_profile
-rw-r--r--.  1 root root  176 12월 29  2013 .bashrc
-rw-r--r--.  1 root root  100 12월 29  2013 .cshrc
-rw-r--r--   1 root root 7732  1월 13 09:12 .dbset.log
drwxr-----   3 root root   19  1월 13 09:04 .pki
drwx------   2 root root   29  1월  4 16:58 .ssh
-rw-r--r--.  1 root root  129 12월 29  2013 .tcshrc
```

## Connect to Tibero


### Change the Account

Log in with the OS\_ACCOUNT created with the dbca command.


```
[root@tiberoinstance ~]# su - nhncloud
마지막 로그인: 목  1월 13 11:34:43 KST 2022 일시 pts/0

 #####   ###  ######    ###         #######  ###  ######   #######  ######   #######  #######  #######   #####   #######  ######   ######
#     #   #   #     #   ###            #      #   #     #  #        #     #  #     #     #     #        #     #     #     #     #  #     #
#         #   #     #   ###            #      #   #     #  #        #     #  #     #     #     #        #           #     #     #  #     #
 #####    #   #     #                  #      #   ######   #####    ######   #     #     #     #####     #####      #     #     #  ######
      #   #   #     #   ###            #      #   #     #  #        #   #    #     #     #     #              #     #     #     #  #     #
#     #   #   #     #   ###            #      #   #     #  #        #    #   #     #     #     #        #     #     #     #     #  #     #
 #####   ###  ######    ###            #     ###  ######   #######  #     #  #######     #     #######   #####      #     ######   ######

[nhncloud@tiberoinstance ~]$

```

### Check Connection

```
[nhncloud@tiberoinstance ~]$ tbsql sys/tibero

tbSQL 6

TmaxData Corporation Copyright (c) 2008-. All rights reserved.

Connected to Tibero.

SQL> select * from v$instance;

INSTANCE_NUMBER INSTANCE_NAME
--------------- ----------------------------------------
DB_NAME
----------------------------------------
HOST_NAME                                                       PARALLEL
--------------------------------------------------------------- --------
   THREAD# VERSION
---------- --------
STARTUP_TIME
----------------------------------------------------------------
STATUS           SHUTDOWN_PENDING
---------------- ----------------
TIP_FILE
--------------------------------------------------------------------------------
              0 tiberotestdb
tiberotestdb
tiberoinstance.novalocal                                        NO
         0 6
2022/01/13
NORMAL           NO
/db/tibero6/config/tiberotestdb.tip


1 row selected.

SQL>
```


### Tibero Default Accounts

The default accounts provided by Tibero are as follows.

| Schema | Password | Description |
| --- | --- | --- |
| sys | tibero | SYSTEM schema |
| syscat | syscat | SYSTEM schema |
| sysgis | sysgis | SYSTEM schema |
| outln | outln | SYSTEM schema |
| tibero | tmax | SAMPLE schema with the DBA privilege |
| tibero1 | tmax | SAMPLE schema with the DBA privilege |

* SYS: Performs administrator tasks for the database.
* SYSCAT: Creates DATA DICTIONARY & CATALOGVIEW.
* SYSGIS: Creates GIS-related tables and performs GIS-related tasks.
* OUTLN: Performs tasks such as storing related hints so that the same SQL can always be executed with the same plan.
* TIBERO/TIBERO1: An example user with the DBA privilege.