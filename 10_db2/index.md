## 10.Db2 Warehouseのインストールと構成
Cloud Pak For DataへDb2 Warehouse のインストール手順とDB設定の手順を記載
作業前提は、前項のCloud Pak for Dataと同様です。

参考.Installing the Db2 Warehouse service

https://www.ibm.com/docs/en/cloud-paks/cp-data/4.0?topic=warehouse-installing-db2-service


### 手順前提
IBM CloudPak for Data (CP4D)と同様

## IBM Db2U オペレーターの導入
1. IBM Db2 のオペレーターの導入
OpenshiftのOperatorHubで、「IBM Db2」を選択し、「インストール」をクリック
![](2022-04-18-10-42-22.png)

2. namespaceには「ibm-common-services」を指定し、「インストール」をクリック
![](2022-04-18-10-46-24.png)

3. 数分後、Operatorがインストールされます
![](2022-04-18-10-45-56.png)

## IBM Db2 Warehouseのオペレーターの導入

1. IBM Db2 Warehouseのオペレーターの導入
OpenshiftのOperatorHubで、「IBM® Db2 Warehouse Operator Extension for IBM® Cloud Pak for Data」を選択肢、「インストール」をクリック
![](2022-04-02-21-28-23.png)

2. namespaceには「ibm-common-services」を指定し、「インストール」をクリック
![](2022-04-02-21-29-36.png)

数分後、Operatorがインストールされます
![](2022-04-02-21-33-18.png)

## IBM Db2 Warehouseのカスタムリソースの作成

参考.Installing the Db2 Warehouse service
https://www.ibm.com/docs/en/cloud-paks/cp-data/4.0?topic=warehouse-installing-db2-service

1. oc loginを実施する

```bash
oc login OpenShift_URL:port
```

2. カスタムリソースの作成
プロジェクト名は「ibm-common-services」、ライセンスは適切なライセンスを指定して以下のyamlファイルの適用する(以下のyamlはライセンスが「Standard」の例)。

```bash
cat <<EOF |oc apply -f -
apiVersion: databases.cpd.ibm.com/v1
kind: Db2whService
metadata:
  name: db2wh-cr
  namespace: ibm-common-services
spec:
  license:
    accept: true
    license: Standard 
EOF
```
## データベースの作成

1. CP4Dのダッシュボードにログインし、「サービスのディスカバー」をクリック

![](2022-04-02-21-34-20.png)

2. Db2 Warehouseを選択

※Db2 Warehouseが利用可能な場合、「使用可能」というアイコンが表示されます( 「カスタムリソースの作成」から数分程度経過した後、表示されるようになります)。

![](2022-04-03-07-52-53.png)

3. 「プロビジョンインスタンス」を選択

![](2022-04-03-07-56-09.png)

4. データベースの作成
当手順ではデータベース名は「MANAGEDB」

![](2022-04-03-07-59-13.png)

5. 4Kセクターサイズにチェックを入れる

![](2022-04-03-08-01-15.png)

6. デフォルトのまま次へ

![](2022-04-03-08-01-58.png)

7. 当手順では、IBM Cloudのため、ストレージクラスは「ibmc-file-gold-gid」を選択

ストレージクラスについては以下参照
参考.https://www.ibm.com/docs/en/cloud-paks/cp-data/4.0?topic=warehouse-installing-db2-service


![](2022-04-03-08-04-29.png)

8. 当手順では、ストレージクラスは「ibmc-file-gold-gid」を選択

![](2022-04-03-08-05-49.png)

9. 当手順では、ストレージクラスは「ibmc-file-gold-gid」を選択

![](![](2022-04-03-08-05-49.png).png)

10. 当手順では、ストレージクラスは「ibmc-file-gold-gid」を選択

![](2022-04-03-08-07-26.png)

11. 当手順では、ストレージクラスは「ibmc-file-gold-gid」を選択

![](2022-04-03-08-08-12.png)

12. 設定を確認し、「作成」をクリック

![](2022-04-03-08-08-57.png)

![](2022-04-03-08-09-18.png)

13. データベースの作成が開始する。20分程度で作成が完了する
![](2022-04-03-08-12-26.png)

14. 完了後、データベースの詳細を開く
![](2022-04-03-14-17-34.png)

15. 「デプロイメントID」を控えておき、「Download SSL Certificate」から証明書をダウンロードしておく

![](2022-04-03-14-18-05.png)

![](2022-04-03-14-18-24.png)



```bash

```
## データベースの管理者ユーザーの作成

1. cp4dのダッシュボードにログインし、ハンバーガーメニューから「アクセス制御」を選択

![](2022-04-03-08-16-55.png)

2. 「ユーザーの追加」をクリック

![](2022-04-03-08-17-38.png)

3. ユーザープロファイルの指定
当手順ではユーザー名は「maximo」とする

![](2022-04-03-08-18-38.png)

4. 「ロールの直接割り当て」を指定

![](2022-04-03-08-19-45.png)

5. ロールに「ユーザー」「管理者」を指定

![](2022-04-03-08-56-39.png)

6. 「追加」をクリック

![](2022-04-03-08-54-42.png)

ユーザーが追加されます

![](2022-04-03-09-00-44.png)

7. cp4dのメニューから「データベース」を選択

![](2022-04-03-09-05-05.png)

8. データベースの「アクセス権限の管理」を選択

![](2022-04-03-09-06-04.png)

9. 「ユーザーの追加」をクリック

![](2022-04-03-09-06-32.png)

10. 追加したユーザーにチェックをつけ、「Admin」を設定して追加

![](2022-04-03-09-06-58.png)

インスタンスにユーザーが追加される。

![](2022-04-03-09-08-31.png)

## Db2 Warehouseの設定
DBの行列の設定を実施する。詳細は以下の「Before you begin」参照
参考. https://www.ibm.com/docs/en/maximo-manage/8.3.0?topic=deployment-configuring-db2-warehouse

1. Openshift Webコンソール > WorkLoads > Pods > project:(CP4Dのproject) > DB2WHインスタンスのPod「c-(デプロイメントID)-db2u-0」


![](2022-04-03-10-33-34.png)

![](2022-04-03-10-34-28.png)


2. Terminal タブから行列の変更を行うコマンドを実行

「展開」からTerminalタブは拡大可能。

※Terminalタブからコマンド実行時に表示がおかしい場合、ローカルPCのターミナルからこの手順を行うことも可能。							

その場合の方法はこちらのページを参照：							
		https://www.ibm.com/docs/en/maximo-manage/8.3.0?topic=deployment-configuring-db2-warehouse


コマンド:					
```bash
su - db2inst1
export DB_NAME=MANAGEDB
echo $DB_NAME

db2 connect to $DB_NAME
db2 update db cfg for $DB_NAME using dft_table_org row
db2 terminate
db2 deactivate db $DB_NAME
db2stop
db2start
db2 activate db $DB_NAME
```

実行結果:					
```bash
sh-4.4$ su - db2inst1
Last login: Sun Apr  3 01:34:39 UTC 2022
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ GEDB   <
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1b2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ echo $DB_NAME
MANAGEDB
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 connect to $DB_NAME

   Database Connection Information

 Database server        = DB2/LINUXX8664 11.5.7.0
 SQL authorization ID   = DB2INST1
 Local database alias   = MANAGEDB

[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ dft_table_org row               <
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 terminate
DB20000I  The TERMINATE command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 deactivate db $DB_NAME
DB20000I  The DEACTIVATE DATABASE command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2stop
04/03/2022 01:36:54     0   0   SQL1064N  DB2STOP processing was successful.
SQL1064N  DB2STOP processing was successful.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2start
04/03/2022 01:37:58     0   0   SQL1063N  DB2START processing was successful.
SQL1063N  DB2START processing was successful.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 activate db $DB_NAME
DB20000I  The ACTIVATE DATABASE command completed successfully.
```

 3. 続けてDb2 WarehouseのDB設定の準備。必要に応じて以下のように環境変数を書き換えます。

```bash
export DB_NAME=MANAGEDB
export DB_TABLESPACE=MAXDATA
export DB_TEMP=MAXTEMP
export DB_INDEX=MAXINDEX
export DB_SCHEMA=maximo
export DB_USERNAME=maximo
```

4. Db2 WarehouseのDB設定 (ブロック単位等で実行)
```bash
db2 connect to $DB_NAME
db2 update db cfg for $DB_NAME using SELF_TUNING_MEM ON
db2 update db cfg for $DB_NAME using APPGROUP_MEM_SZ 16384 DEFERRED
db2 update db cfg for $DB_NAME using "APPLHEAPSZ 2048 AUTOMATIC DEFERRED"
db2 update db cfg for $DB_NAME using "AUTO_MAINT ON DEFERRED"
db2 update db cfg for $DB_NAME using "AUTO_TBL_MAINT ON DEFERRED"
db2 update db cfg for $DB_NAME using "AUTO_RUNSTATS ON DEFERRED"
db2 update db cfg for $DB_NAME using "AUTO_REORG ON DEFERRED"
db2 update db cfg for $DB_NAME using "AUTO_DB_BACKUP ON DEFERRED"
db2 update db cfg for $DB_NAME using "CATALOGCACHE_SZ 800 DEFERRED"
db2 update db cfg for $DB_NAME using "CHNGPGS_THRESH 40 DEFERRED"
db2 update db cfg for $DB_NAME using "DBHEAP AUTOMATIC"
db2 update db cfg for $DB_NAME using "LOCKLIST AUTOMATIC DEFERRED"
db2 update db cfg for $DB_NAME using "LOGBUFSZ 1024 DEFERRED"
db2 update db cfg for $DB_NAME using "LOCKTIMEOUT 300 DEFERRED"
db2 update db cfg for $DB_NAME using "LOGPRIMARY 20 DEFERRED"
db2 update db cfg for $DB_NAME using "LOGSECOND 100 DEFERRED"
db2 update db cfg for $DB_NAME using "LOGFILSIZ 8192 DEFERRED"
db2 update db cfg for $DB_NAME using "SOFTMAX 1000 DEFERRED"
db2 update db cfg for $DB_NAME using "MAXFILOP 61440 DEFERRED"
db2 update db cfg for $DB_NAME using "PCKCACHESZ AUTOMATIC DEFERRED"
db2 update db cfg for $DB_NAME using "STAT_HEAP_SZ AUTOMATIC DEFERRED"
db2 update db cfg for $DB_NAME using "STMTHEAP AUTOMATIC DEFERRED"
db2 update db cfg for $DB_NAME using "UTIL_HEAP_SZ 10000 DEFERRED"
db2 update db cfg for $DB_NAME using "DATABASE_MEMORY AUTOMATIC DEFERRED"
db2 update db cfg for $DB_NAME using "AUTO_STMT_STATS OFF DEFERRED"
db2 update db cfg for $DB_NAME using "STMT_CONC LITERALS DEFERRED"

db2 update alert cfg for database on $DB_NAME using "db.db_backup_req SET THRESHOLDSCHECKED YES"
db2 update alert cfg for database on $DB_NAME using "db.tb_reorg_req SET THRESHOLDSCHECKED YES"
db2 update alert cfg for database on $DB_NAME using "db.tb_runstats_req SET THRESHOLDSCHECKED YES"

db2 update dbm cfg using "PRIV_MEM_THRESH 32767 DEFERRED"
db2 update dbm cfg using "KEEPFENCED NO DEFERRED"
db2 update dbm cfg using "NUMDB 2 DEFERRED"
db2 update dbm cfg using "RQRIOBLK 65535 DEFERRED"
db2 update dbm cfg using "HEALTH_MON OFF DEFERRED"
db2 update dbm cfg using "AGENT_STACK_SZ 1000 DEFERRED"
db2 update dbm cfg using "MON_HEAP_SZ AUTOMATIC DEFERRED"
db2 update db cfg using "DDL_CONSTRAINT_DEF YES"

db2set DB2_SKIPINSERTED=ON
db2set DB2_INLIST_TO_NLJN=YES
db2set DB2_MINIMIZE_LISTPREFETCH=Y
db2set DB2_EVALUNCOMMITTED=YES
db2set DB2_FMP_COMM_HEAPSZ=65536
db2set DB2_SKIPDELETED=ON
db2set DB2_USE_ALTERNATE_PAGE_CLEANING=ON

db2stop force
db2start
db2 connect to $DB_NAME

db2 CREATE BUFFERPOOL MAXBUFPOOL IMMEDIATE SIZE 4096 AUTOMATIC PAGESIZE 32 K

db2 CREATE REGULAR TABLESPACE $DB_TABLESPACE PAGESIZE 32 K MANAGED BY AUTOMATIC STORAGE INITIALSIZE 5000 M BUFFERPOOL MAXBUFPOOL
db2 CREATE TEMPORARY TABLESPACE $DB_TEMP PAGESIZE 32 K MANAGED BY AUTOMATIC STORAGE BUFFERPOOL MAXBUFPOOL
db2 CREATE REGULAR TABLESPACE $DB_INDEX PAGESIZE 32 K MANAGED BY AUTOMATIC STORAGE INITIALSIZE 5000 M BUFFERPOOL MAXBUFPOOL

db2 GRANT USE OF TABLESPACE $DB_TABLESPACE TO USER $DB_USERNAME
db2 CREATE SCHEMA $DB_SCHEMA AUTHORIZATION $DB_USERNAME

db2 GRANT DBADM,CREATETAB,BINDADD,CONNECT,CREATE_NOT_FENCED_ROUTINE,IMPLICIT_SCHEMA,LOAD,CREATE_EXTERNAL_ROUTINE,QUIESCE_CONNECT,SECADM ON DATABASE TO USER $DB_USERNAME
db2 GRANT USE OF TABLESPACE $DB_TABLESPACE TO USER $DB_USERNAME
db2 GRANT CREATEIN,DROPIN,ALTERIN ON SCHEMA $DB_SCHEMA TO USER $DB_USERNAME

db2 connect reset

```




参考.実行結果:					
```bash
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ export DB_NAME=MANAGEDB
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ export DB_TABLESPACE=MAXDATA
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ export DB_TEMP=MAXTEMP
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ export DB_INDEX=MAXINDEX
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ export DB_SCHEMA=maximo
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ export DB_USERNAME=maximo
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 connect to $DB_NAME

   Database Connection Information

 Database server        = DB2/LINUXX8664 11.5.7.0
 SQL authorization ID   = DB2INST1
 Local database alias   = MANAGEDB

[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ ELF_TUNING_MEM ON               <
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using APPGROUP_MEM_SZ 16384 DEFERRED
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "APPLHEAPSZ 2048 AUTOMATIC DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "AUTO_MAINT ON DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "AUTO_TBL_MAINT ON DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "AUTO_RUNSTATS ON DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "AUTO_REORG ON DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "AUTO_DB_BACKUP ON DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "CATALOGCACHE_SZ 800 DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "CHNGPGS_THRESH 40 DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "DBHEAP AUTOMATIC"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "LOCKLIST AUTOMATIC DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "LOGBUFSZ 1024 DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "LOCKTIMEOUT 300 DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "LOGPRIMARY 20 DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "LOGSECOND 100 DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "LOGFILSIZ 8192 DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "SOFTMAX 1000 DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "MAXFILOP 61440 DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "PCKCACHESZ AUTOMATIC DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "STAT_HEAP_SZ AUTOMATIC DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "STMTHEAP AUTOMATIC DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "UTIL_HEAP_SZ 10000 DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "DATABASE_MEMORY AUTOMATIC DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "AUTO_STMT_STATS OFF DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg for $DB_NAME using "STMT_CONC LITERALS DEFERRED"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update alert cfg for database on $DB_NAME using "db.db_backup_req SET THRESHOLDSCHECKED YES"
DB20000I  The UPDATE ALERT CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update alert cfg for database on $DB_NAME using "db.tb_reorg_req SET THRESHOLDSCHECKED YES"
DB20000I  The UPDATE ALERT CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ ing "db.tb_runstats_req SET THRESHOLDSCHECKED YES"                                                <
DB20000I  The UPDATE ALERT CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update dbm cfg using "PRIV_MEM_THRESH 32767 DEFERRED"
DB20000I  The UPDATE DATABASE MANAGER CONFIGURATION command completed 
successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update dbm cfg using "KEEPFENCED NO DEFERRED"
DB20000I  The UPDATE DATABASE MANAGER CONFIGURATION command completed 
successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update dbm cfg using "NUMDB 2 DEFERRED"
DB20000I  The UPDATE DATABASE MANAGER CONFIGURATION command completed 
successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update dbm cfg using "RQRIOBLK 65535 DEFERRED"
DB20000I  The UPDATE DATABASE MANAGER CONFIGURATION command completed 
successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update dbm cfg using "HEALTH_MON OFF DEFERRED"
DB20000I  The UPDATE DATABASE MANAGER CONFIGURATION command completed 
successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update dbm cfg using "AGENT_STACK_SZ 1000 DEFERRED"
DB20000I  The UPDATE DATABASE MANAGER CONFIGURATION command completed 
successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update dbm cfg using "MON_HEAP_SZ AUTOMATIC DEFERRED"
DB20000I  The UPDATE DATABASE MANAGER CONFIGURATION command completed 
successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 update db cfg using "DDL_CONSTRAINT_DEF YES"
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2set DB2_SKIPINSERTED=ON
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2set DB2_INLIST_TO_NLJN=YES
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2set DB2_MINIMIZE_LISTPREFETCH=Y
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2set DB2_EVALUNCOMMITTED=YES
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2set DB2_FMP_COMM_HEAPSZ=65536
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2set DB2_SKIPDELETED=ON
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2set DB2_USE_ALTERNATE_PAGE_CLEANING=ON
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2stop force
04/03/2022 01:55:45     0   0   SQL1064N  DB2STOP processing was successful.
SQL1064N  DB2STOP processing was successful.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2start
04/03/2022 01:55:54     0   0   SQL1063N  DB2START processing was successful.
SQL1063N  DB2START processing was successful.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 connect to $DB_NAME
SQL1224N  The database manager is not able to accept new requests, has 
terminated all requests in progress, or has terminated the specified request 
because of an error or a forced interrupt.  SQLSTATE=55032
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 connect to $DB_NAME

   Database Connection Information

 Database server        = DB2/LINUXX8664 11.5.7.0
 SQL authorization ID   = DB2INST1
 Local database alias   = MANAGEDB

[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 CREATE BUFFERPOOL MAXBUFPOOL IMMEDIATE SIZE 4096 AUTOMATIC PAGESIZE 32 K
DB20000I  The SQL command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ C STORAGE INITIALSIZE 5000 M BUFFERPOOL MAXBUFPOOL                                                <
DB20000I  The SQL command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ MANAGED BY AUTOMATIC STORAGE BUFFERPOOL MAXBUFPOOL                                                <
DB20000I  The SQL command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ C STORAGE INITIALSIZE 5000 M BUFFERPOOL MAXBUFPOOL                                                <
DB20000I  The SQL command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 GRANT USE OF TABLESPACE $DB_TABLESPACE TO USER $DB_USERNAME
DB20000I  The SQL command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 CREATE SCHEMA $DB_SCHEMA AUTHORIZATION $DB_USERNAME
DB20000I  The SQL command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ NE,QUIESCE_CONNECT,SECADM ON DATABASE TO USER $DB_USERNAME                                        <
DB20000I  The SQL command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 GRANT USE OF TABLESPACE $DB_TABLESPACE TO USER $DB_USERNAME
DB20000I  The SQL command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 GRANT CREATEIN,DROPIN,ALTERIN ON SCHEMA $DB_SCHEMA TO USER $DB_USERNAME
DB20000I  The SQL command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ db2 connect reset
DB20000I  The SQL command completed successfully.
[db2inst1@c-db2wh-1648941110207627-db2u-0 - Db2U db2inst1]$ 
```

### 次項
- [11_Manage Operatorのデプロイ](../11_manageop/index.md)
