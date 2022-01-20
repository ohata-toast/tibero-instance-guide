## Tibero Instance 생성


Tibero를 사용하려면 먼저 인스턴스를 생성해야 합니다.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image1.png)

**Tibero Instance 생성**에 있는 **생성** 버튼을 클릭하면 **Compute > Instance > 인스턴스 생성**으로 이동합니다.


### 이미지

기본 제공되는 이미지에는 CentOS 7.8 with Tibero 6 버전(Tibero 6 FS07 CS2005 build194603 r144754)이 포함됩니다.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image2.png)


### 인스턴스 정보

* 가용성 영역: 임의의 가용성 영역 선택
* 인스턴스 이름:생성되는 서버의 인스턴스 이름
* 인스턴스 타입
    * Tibero를 사용하기 위한 최소 권장 사양 8vCPU 8G Memory 이상을 선택.
* 키페어: PEM 키를 새로 생성하거나 기존 키를 사용. 새로 생성하는 경우 다운로드하여 보관
* 블록 스토리지 타입
    * root 볼륨. 빠른 속도를 위해 SSD를 권장
    * root Full이 발생하지 않도록 최소 50GB 이상 설정

![image.png](http://static.toastoven.net/prod_tibero/tibero_image3.png)


### 네트워크

![image.png](http://static.toastoven.net/prod_tibero/tibero_image4.png)

### 플로팅 IP

SSH 접속을 위해 플로팅 IP를 사용합니다.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image5.png)

### 보안 그룹

인스턴스에 SSH로 접속이 필요하므로 SSH 포트(22) 접근을 허용한 보안 그룹을 생성해 사용하여야 합니다.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image6.png)

![image.png](http://static.toastoven.net/prod_tibero/tibero_image7.png)


### 추가 블록 스토리지

root 볼륨 이외의 추가 볼륨을 생성합니다.
<span style="color:#000">TMI(Tibero Machine Image)는 추가 볼륨 150GB를 요구하기 때문에 </span>**<span style="color:#000">추가 블록 스토리지 150G 이상</span>**<span style="color:#000">을 반드시 설정해야 합니다.</span><span style="color:#000"></span>

![image.png](http://static.toastoven.net/prod_tibero/tibero_image8.png)


### 인스턴스 생성 완료

위 정보를 모두 입력 후 **인스턴스 생성** 버튼을 누르면 아래와 같이 인스턴스가 생성됩니다.
생성된 인스턴스에는 데이터베이스 생성을 위한 설치 파일이 포함되며, 실제 설치와 데이터베이스 생성은 SSH 클라이언트로 인스턴스에 접속하여 TMI 설치 과정을 수행해야 합니다.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image9.png)


## TMI 설치


### 인스턴스 접속

인스턴스 생성 완료 후 SSH를 사용하여 인스턴스에 접근합니다.
인스턴스에 플로팅 IP가 연결되어 있어야 하며 보안 그룹에서 TCP 포트 22(SSH)가 허용되어야 합니다.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image10.png)

SSH 클라이언트와 설정한 키페어를 이용해 인스턴스에 접속합니다.
SSH 연결에 대한 자세한 가이드는 [SSH 연결 가이드](https://docs.toast.com/ko/Compute/Instance/ko/overview/#linux)<span style="color:#313338">를 참고하시기 바랍니다.</span>

### TMI 설치

root 계정으로 /root 경로에서 dbca 명령어를 실행합니다.
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

| No | 항목 | 인자값 |
| :---: | --- | --- |
| 1 | OS\_ACCOUNT | Tibero가 구동되는 OS 계정 |
| 2 | DB\_NAME | Tibero에서 사용되는 DB\_NAME (= SID ) |
| 3 | DB\_CHARACTERSET | Tibero에서 사용하는 DB 문자 집합 |
| 4 | DB\_PORT | Tibero에서 사용하는 서비스 IP의 포트 |

### 설치 완료

dbca 명령어 수행 시 진행 상황이 출력되며 nomount 모드에서 database가 생성됩니다. 소요 시간은 10분 이하입니다. 완료되면 아래와 같이 출력됩니다.

```
SQL>
System altered.

SQL>
System altered.

SQL> Disconnected.
[root@tiberoinstance ~]#
```


### 구동 확인 및 설치 로그 확인

Tibero가 구동 중인지 확인합니다.

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

설치 로그는 /root/.dbset.log에서 확인이 가능합니다.

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

## Tibero 접속


### 계정 변경

dbca 명령어로 생성한 OS\_ACCOUNT로 로그인합니다.


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

### 접속 확인

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


### Tibero 기본 계정

Tibero에서 제공하는 기본 계정은 다음과 같습니다.

| 스키마 | 비밀번호 | 설명 |
| --- | --- | --- |
| sys | tibero | SYSTEM 스키마 |
| syscat | syscat | SYSTEM 스키마 |
| sysgis | sysgis | SYSTEM 스키마 |
| outln | outln | SYSTEM 스키마 |
| tibero | tmax | SAMPLE 스키마 DBA 권한 |
| tibero1 | tmax | SAMPLE 스키마 DBA 권한 |

* SYS: 데이터베이스의 관리자 태스크를 수행합니다.
* SYSCAT: DATA DICTIONARY & CATALOGVIEW를 생성합니다.
* SYSGIS: GIS 관련 테이블 생성 및 태스크를 수행하는 계정입니다.
* OUTLN: 동일한 SQL 수행 시 항상 같은 플랜으로 수행될 수 있게 관련 힌트를 저장하는 등의 태스크를 수행합니다.
* TIBERO/TIBERO1: example user이며 DBA 권한을 가지고 있습니다.