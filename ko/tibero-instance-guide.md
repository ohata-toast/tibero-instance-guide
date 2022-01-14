## Tibero Instance 생성
<br>
Tibero<span style="color:#313338">를 사용하려면 먼저 인스턴스를 생성해야 합니다.</span>
![image.png](http://static.toastoven.net/prod_tibero/tibero_image1.png)

<span style="color:#313338">**Tibero Instance 생성**에 있는 **생성** 버튼을 클릭하면 **Compute > Instance > 인스턴스 생성**으로 이동합니다.</span>

<br>
### 이미지
<br>
기본 제공되는 이미지는 CentOS 7.8 with Tibero 6 버전(Tibero 6 FS07 CS2005 build194603 r144754)이 포함됩니다.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image2.png)
<br>
### 인스턴스 정보
<br>
* 가용성 영역 : 임의의 가용성 영역 선택
* 인스턴스 이름 : 생성되는 서버의 인스턴스 이름
* 인스턴스 타입
    * 티베로를 사용하기 위한 <b><span style="color:#e11d21">최소 권장사양</span><span style="color:#000000">은 2vCPU 8G Memory </span></b>이므로 권장사양 이상의 스펙을 선택. <span style="color:#e11d21">이하 스펙에서는 정상적으로 생성되지 않을 수 있음.</span>
* 키페어 : Pem key를 새로 생성하거나 기존 Key를 사용. 새로 생성하는 경우 다운로드하여 보관
* 블록 스토리지 타입
    * root 볼륨. 빠른 속도를 위해 SSD를 권장
    * root Full이 발생하지 않도록 최소 50GB 이상 설정

![image.png](http://static.toastoven.net/prod_tibero/tibero_image3.png)
<br>
### 네트워크
<br>
![image.png](http://static.toastoven.net/prod_tibero/tibero_image4.png)

<br>
### 플로팅IP
<br>
ssh 접속을 위해 플로팅 IP를 사용합니다.
![image.png](http://static.toastoven.net/prod_tibero/tibero_image5.png)

<br>
<br>
### 보안그룹
<br>
인스턴스에 ssh 로 접속을 필요하므로 ssh 포트(22) 접근을 허용한 보안그룹 생성하여 사용하여야 합니다.
![image.png](http://static.toastoven.net/prod_tibero/tibero_image6.png)
![image.png](http://static.toastoven.net/prod_tibero/tibero_image7.png)
<br>
### 추가 블록 스토리지
<br>
root 볼륨 이외의 추가 볼륨을 생성합니다.
<span style="color:#000">TMI 는 추가볼륨 150GB 를 요구하기 때문에 </span>**<span style="color:#000">추가 블록 스토리지 150G 이상</span>**<span style="color:#000">을 반드시 설정해야 합니다.</span><span style="color:#000"></span>

![image.png](http://static.toastoven.net/prod_tibero/tibero_image8.png)
<br>
### 인스턴스 생성 완료
<br>
위 정보를 모두 입력 후 인스턴스 생성 버튼을 누르면 아래와 같이 인스턴스가 생성됩니다.
생성된 인스턴스는 데이터베이스 생성을 위한 설치파일이 포함되며 실제 설치와 데이터베이스 생성은 SSH 클라이언트 접속하여 TMI 설치과정을 수행해야 합니다.

### ![image.png](/files/3184955716482441473)
<br>
## TMI(Tibero Machine Image) 설치
<br>
### 인스턴스 접속
<br>
<span style="color:#313338">인스턴스 생성 완료 후 SSH를 사용하여 인스턴스에 접근합니다.</span>
인스턴스에 Floating IP가 연결되어 있어야 하며 보안 그룹에서 TCP 포트 22(SSH)가 허용되어야 합니다.

![image.png](http://static.toastoven.net/prod_tibero/tibero_image9.png)

<span style="color:#313338">SSH 클라이언트와 설정한 키페어를 이용해 인스턴스에 접속합니다.</span>
SSH 연결에 대한 자세한 가이드는 [SSH 연결 가이드](https://docs.toast.com/ko/Compute/Instance/ko/overview/#linux)<span style="color:#313338">를 참고하시기 바랍니다.</span>
<br>
### TMI설치
<br>
root 계정으로 /root 경로에서 dbca 명령어를 실행합니다.
$ \./dbca &#91;OS\_ACCOUNT&#93; &#91;DB\_NAME&#93; &#91;DB\_CHARACTERSET&#93; &#91;DB\_PORT&#93;
<br>
```
[centos@tiberoinstance ~]$ sudo su root
[root@tiberoinstance centos]# cd
[root@tiberoinstance ~]# pwd
/root
[root@tiberoinstance ~]# ./dbca nhncloud tiberotestdb utf8 8639
```
<br>
| No | 항목 | 인자값 |
| :---: | --- | --- |
| 1 | OS\_ACCOUNT | TIBERO가 구동되는 OS 계정 |
| 2 | DB\_NAME | TIBERO에서 사용되는 DB\_NAME (= SID ) |
| 3 | DB\_CHARACTERSET | TIBERO에서 사용하는 DB 캐릭터셋 |
| 4 | DB\_PORT | TIBERO에서 사용하는 서비스 IP의 PORT |
<br>
### 설치완료
<br>
dbca 명령어 수행시 진행상황이 출력되며 nomount 모드에서 database가 생성됩니다. 소요시간은 10분 이하입니다. 완료되면 아래와 같이 출력됩니다.

```
... 생략 ...

SQL>
System altered.

SQL>
System altered.

SQL> Disconnected.
[root@tiberoinstance ~]#
```
<br>
### 기동확인 및 설치로그 확인
<br>
티베로가 기동중인지 확인합니다.

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
<br>
설치로그는 /root/.dbset.log에서 확인이 가능합니다.

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
<br>
### 계정변경
<br>
dbca 명령어로 생성한 OS\_ACCOUNT로 로그인 합니다.

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
<br>
### 접속확인
<br>
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
<br>
### Tibero 기본계정
<br>
Tibero에서 제공하는 기본계정은 다음과 같습니다

| 스키마 | 패스워드 | 설명 |
| --- | --- | --- |
| sys | tibero | SYSTEM 스키마 |
| syscat | syscat | SYSTEM 스키마 |
| sysgis | sysgis | SYSTEM 스키마 |
| outln | outln | SYSTEM 스키마 |
| tibero | tmax | SAMPLE 스키마 DBA권한 |
| tibero1 | tmax | SAMPLE 스키마 DBA권한 |

* SYS: database의 관리자 task를 수행합니다.
* SYSCAT: data dictionary & catalogview를 생성합니다.
* OUTLN: 동일한 SQL 수행 시 항상 같은 plan으로 수행될 수 있게 관련 hint를 저장하는 등의 일을 수행합니다.
* SYSGIS: GIS관련 table 생성 및 일을 수행하는 계정입니다.
* TIBERO/TIBERO1: example user이며 DBA 권한을 가지고 있습니다.