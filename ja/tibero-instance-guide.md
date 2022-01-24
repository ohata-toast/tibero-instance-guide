## Tibero Instance作成


Tiberoを使用するには、まずインスタンスを作成する必要があります。

![image.png](http://static.toastoven.net/prod_tibero/tibero_image1.png)

**Tibero Instance作成**の**作成**ボタンをクリックすると、**Compute > Instance > インスタンス作成**に移動します。


### イメージ

基本提供されるイメージにはCentOS 7.8 with Tibero 6バージョン(Tibero 6 FS07 CS2005 build194603 r144754)が含まれています。

![image.png](http://static.toastoven.net/prod_tibero/tibero_image2.png)


### インスタンス情報

* アベイラビリティゾーン：任意のアベイラビリティゾーンを選択
* インスタンス名：作成されるサーバーのインスタンス名
* インスタンスタイプ
    * Tiberoを使用するための最低推奨仕様8vCPU 8G Memory以上を選択。
* キーペア：PEMキーを新しく作成するか、既存のキーを使用。 新しく作成する場合はダウンロードして保管
* ブロックストレージタイプ
    * rootボリューム。 高速化のためにSSDを推奨
    * root Fullが発生しないように50GB以上に設定

![image.png](http://static.toastoven.net/prod_tibero/tibero_image3.png)


### ネットワーク

![image.png](http://static.toastoven.net/prod_tibero/tibero_image4.png)

### Floating IP

SSH接続のためにFloating IPを使用します。

![image.png](http://static.toastoven.net/prod_tibero/tibero_image5.png)

### セキュリティグループ

インスタンスにSSH接続が必要なため、SSHポート(22)アクセスを許可したセキュリティグループを作成して使用する必要があります。

![image.png](http://static.toastoven.net/prod_tibero/tibero_image6.png)

![image.png](http://static.toastoven.net/prod_tibero/tibero_image7.png)


### 追加ブロックストレージ

rootボリューム以外の追加ボリュームを作成します。
<span style="color:#000">TMI(Tibero Machine Image)は追加ボリューム150GBを必要とするため、</span>**<span style="color:#000">追加ブロックストレージ150G以上</span>**<span style="color:#000">を必ず設定する必要があります。</span><span style="color:#000"></span>

![image.png](http://static.toastoven.net/prod_tibero/tibero_image8.png)


### インスタンス作成完了

上記の情報をすべて入力して**インスタンス作成**ボタンを押すと、以下のようにインスタンスが作成されます。
作成されたインスタンスにはデータベースを作成するためのインストールファイルが含まれます。実際のインストールとデータベース作成はSSHクライアントでインスタンスに接続してTMIインストールを行う必要があります。

![image.png](http://static.toastoven.net/prod_tibero/tibero_image9.png)


## TMIインストール


### インスタンス接続

インスタンスの作成が完了したら、SSHを使用してインスタンスにアクセスします。
インスタンスにFloating IPが接続されていて、セキュリティグループでTCPポート22(SSH)が許可されている必要があります。

![image.png](http://static.toastoven.net/prod_tibero/tibero_image10.png)

SSHクライアントと設定したキーペアを利用してインスタンスに接続します。
SSH接続の詳細については[SSH接続ガイド](https://docs.toast.com/ko/Compute/Instance/ko/overview/#linux)<span style="color:#313338">を参照してください。</span>

### TMIインストール

rootアカウントで /rootパスからdbcaコマンドを実行します。
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

| No | 項目 | 因子値 |
| :---: | --- | --- |
| 1 | OS\_ACCOUNT | Tiberoが動作するOSアカウント |
| 2 | DB\_NAME | Tiberoで使用されるDB\_NAME (= SID ) |
| 3 | DB\_CHARACTERSET | Tiberoで使用するDB文字コード |
| 4 | DB\_PORT | Tiberoで使用するサービスIPのポート |

### インストール完了

dbcaコマンドを実行すると進行状況が表示され、nomountモードでdatabaseが作成されます。所要時間は10分以下です。完了すると以下のように表示されます。

```
SQL>
System altered.

SQL>
System altered.

SQL> Disconnected.
[root@tiberoinstance ~]#
```


### 動作確認とインストールログの確認

Tiberoが動作していることを確認します。

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

インストールログは /root/.dbset.logで確認できます。

```
[root@tiberoinstance ~]# ls -al
合計36
dr-xr-x---.  4 root root  154  1月13 09:12 .
dr-xr-xr-x. 23 root root 4096  1月13 09:05 ..
-rw-------   1 root root  264  1月12 19:08 .bash_history
-rw-r--r--.  1 root root   18 12月29  2013 .bash_logout
-rw-r--r--.  1 root root  176 12月29  2013 .bash_profile
-rw-r--r--.  1 root root  176 12月29  2013 .bashrc
-rw-r--r--.  1 root root  100 12月29  2013 .cshrc
-rw-r--r--   1 root root 7732  1月13 09:12 .dbset.log
drwxr-----   3 root root   19  1月13 09:04 .pki
drwx------   2 root root   29  1月 4 16:58 .ssh
-rw-r--r--.  1 root root  129 12月29  2013 .tcshrc
```

## Tibero接続


### アカウント変更

dbcaコマンドで作成したOS\_ACCOUNTにログインします。


```
[root@tiberoinstance ~]# su - nhncloud
最終ログイン：1月13(木) 11:34:43 KST 2022日時pts/0

 #####   ###  ######    ###         #######  ###  ######   #######  ######   #######  #######  #######   #####   #######  ######   ######
#     #   #   #     #   ###            #      #   #     #  #        #     #  #     #     #     #        #     #     #     #     #  #     #
#         #   #     #   ###            #      #   #     #  #        #     #  #     #     #     #        #           #     #     #  #     #
 #####    #   #     #                  #      #   ######   #####    ######   #     #     #     #####     #####      #     #     #  ######
      #   #   #     #   ###            #      #   #     #  #        #   #    #     #     #     #              #     #     #     #  #     #
#     #   #   #     #   ###            #      #   #     #  #        #    #   #     #     #     #        #     #     #     #     #  #     #
 #####   ###  ######    ###            #     ###  ######   #######  #     #  #######     #     #######   #####      #     ######   ######

[nhncloud@tiberoinstance ~]$

```

### 接続確認

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
44574
NORMAL           NO
/db/tibero6/config/tiberotestdb.tip


1 row selected.

SQL>
```


### Tibero基本アカウント

Tiberoで提供する基本アカウントは次のとおりです。

| スキーマ | パスワード | 説明 |
| --- | --- | --- |
| sys | tibero | SYSTEMスキーマ |
| syscat | syscat | SYSTEMスキーマ |
| sysgis | sysgis | SYSTEMスキーマ |
| outln | outln | SYSTEMスキーマ |
| tibero | tmax | SAMPLEスキーマDBA権限 |
| tibero1 | tmax | SAMPLEスキーマDBA権限 |

* SYS：データベースの管理者タスクを実行します。
* SYSCAT：DATA DICTIONARY & CATALOGVIEWを作成します。
* SYSGIS：GIS関連テーブルの作成およびタスクを実行するアカウントです。
* OUTLN：同じSQLを実行するときに常に同じプランで実行できるように関連ヒントを保存するなどのタスクを実行します。
* TIBERO/TIBERO1：example userであり、DBA権限を持っています。
