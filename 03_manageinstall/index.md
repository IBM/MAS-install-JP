## 3.MANAGEインストール

ibmmas/cliコンテナイメージ内で、playbookを実行して、MANAGEをインストールします。
「02_MASインストール」のコンテナを再活用していただいても構いませんし、新規にコンテナイメージから実行いただいても問題ありません。ただし、新規に立ち上げた場合は前手順のoc loginを実行してください。


### 1. ibmmas/cliコンテナイメージの起動

1. cli の後ろに: とTAG名を指定してコンテナを起動します。

    -vオプションを指定することで、コンテナイメージの/home/localにライセンスファイルを配置したディレクトリをマウントできます。  
    ここでは、ubuntu上の/workにentitlement.licを配置しており、そのディレクトリをコンテナ上の/home/localにマウントしています。

    コマンド:
    ```bash
    masenv@IBM-PF39YTN4:/work$ podman run -ti -v /work:/home/local cli:3.2.0
    ```

### 2. 環境変数の定義

1. コンテナイメージ内で、「01_事前準備」で準備した環境情報と、Manageの設定を環境変数として定義します。  

    環境に応じて以下を変更、実行してください。
      
| 環境変数の項目                                        | 設定する値                                                     |
| ------------------------------------------- | -------------------------------------------------------------- |
| **IBM_ENTITLEMENT_KEY**          | 「01.事前準備」で準備したIBM Entitlement Keyの文字列|
| **MAS_INSTANCE_ID**                         | inst1(任意のインスタンスID)                                    |
| **MAS_CONFIG_DIR** | /home/local/                                                              |
| **MAS_APP_ID**                             | manage                                            |
| **MAS_APPWS_COMPONENTS**                              | Manageのイメージのバージョン。当手順の場合、"base=latest"                       |
| **MAS_APP_SETTINGS_DEMODATA**                            | デモデータを導入するか否か。当手順の場合、true                                  |
| **MAS_APP_SETTINGS_BASE_LANG**                       | ベース言語設定。当手順の場合、EN                                |
| **MAS_APP_SETTINGS_SECONDARY_LANGS**                  | 追加の言語設定。当手順の場合、'JA'                                             |
| **MAS_APP_SETTINGS_AIO_FLAG**                   | false                                               |
| **MAS_APP_SETTINGS_PERSISTENT_VOLUMES_FLAG**                  | true         


    参考 : 環境変数の設定例
    ```bash
    export IBM_ENTITLEMENT_KEY=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJJQk0gTWFya2V0cGxhY2UiLCJpYXQiOjE2MTM1MjI0MzAsImp0aSI6IjJmYjI0OTJiNWMzMTQ2ZDY5MjExNzIwZTg2NTA5ZWNjIn0.0CTnlDWrCQdeoJZnV_7_XHt7hCCIX3XXXXXX
    export MAS_INSTANCE_ID=inst1
    export MAS_CONFIG_DIR=/home/local
    export MAS_APP_ID=manage
    export MAS_APPWS_COMPONENTS="base=latest"
    export MAS_APP_SETTINGS_DEMODATA=true
    export MAS_APP_SETTINGS_BASE_LANG=EN
    export MAS_APP_SETTINGS_SECONDARY_LANGS='JA'
    export MAS_APP_SETTINGS_AIO_FLAG=false
    export MAS_APP_SETTINGS_PERSISTENT_VOLUMES_FLAG=true
    ```
   
   <details>
    <summary>実行ログ</summary>

    ```bash
    [ibmmas/cli:3.3.0]home$ export IBM_ENTITLEMENT_KEY=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJJQk0gTWFya2V0cGxhY2UiLCJpYXQiOjE2MTM1MjI0MzAsImp0aSI6IjJmYjI0OTJiNWMzMTQ2ZDY5MjExNzIwZTg2NTA5ZWNjIn0.0CTnlDWrCQdeoJZnV_7_XHt7hCCIX3XXXXXX
    [ibmmas/cli:3.3.0]home$ export MAS_INSTANCE_ID=inst1
    [ibmmas/cli:3.3.0]home$ export MAS_CONFIG_DIR=/home/local
    [ibmmas/cli:3.3.0]home$ export MAS_APP_ID=manage
    [ibmmas/cli:3.3.0]home$ export MAS_APPWS_COMPONENTS="base=latest"
    [ibmmas/cli:3.3.0]home$ export MAS_APP_SETTINGS_DEMODATA=true
    [ibmmas/cli:3.3.0]home$ export MAS_APP_SETTINGS_BASE_LANG=EN
    [ibmmas/cli:3.3.0]home$ export MAS_APP_SETTINGS_SECONDARY_LANGS='JA'
    [ibmmas/cli:3.3.0]home$ export MAS_APP_SETTINGS_AIO_FLAG=false
    [ibmmas/cli:3.3.0]home$ export MAS_APP_SETTINGS_PERSISTENT_VOLUMES_FLAG=true
    ```

</details>




### 5. Manageインストールの実行


1. 「/home/local」上で下記コマンドを用い、Manageのインストールコマンドを実行します。

    コマンド:
    ```bash
    [ibmmas/cli:3.3.0]home$ ansible-playbook ibm.mas_devops.oneclick_add_manage
    ```

#### 補足.DB2タイムアウトで失敗する場合
検証により、DB2導入時にタイムアウトで失敗する場合があることを確認しています。その場合、再度 oneclick_add_manage を実行することにより、正常に完了しています。

<details>
<summary>実行ログ</summary>

```bash
[ibmmas/cli:3.3.0]home$ ansible-playbook ibm.mas_devops.oneclick_add_manage
Using /opt/app-root/src/ansible.cfg as config file
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that
the implicit localhost does not match 'all'
[WARNING]: running playbook inside collection ibm.mas_devops

PLAY [localhost] ***************************************************************

TASK [Gathering Facts] *********************************************************
Tuesday 13 December 2022  00:14:02 +0000 (0:00:00.048)       0:00:00.048 ****** 
ok: [localhost]

TASK [Check for required environment variables] ********************************
Tuesday 13 December 2022  00:14:03 +0000 (0:00:01.166)       0:00:01.215 ****** 
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.db2 : Fail if required properties have not been provided] ***
Tuesday 13 December 2022  00:14:04 +0000 (0:00:00.127)       0:00:01.342 ****** 
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

(中略)

PLAY RECAP ********************************************************************************************************************************
localhost                  : ok=97   changed=12   unreachable=0    failed=0    skipped=14   rescued=0    ignored=0   

Tuesday 13 December 2022  04:01:25 +0000 (2:01:10.264)       2:18:10.160 ****** 
=============================================================================== 
ibm.mas_devops.suite_app_config : Wait for application workspace to be ready (480s delay) --------------------------------------- 7270.27s
ibm.mas_devops.suite_app_install : Wait for application to be ready (60s delay) -------------------------------------------------- 487.71s
ibm.mas_devops.db2 : Wait for db2u instance to be ready (5m delay) --------------------------------------------------------------- 304.31s
ibm.mas_devops.suite_db2_setup_for_manage : Run setupdb to prepare Db2 for use with Maximo TPAE ----------------------------------- 90.25s
ibm.mas_devops.suite_app_install : Wait until the Application's CRD is available -------------------------------------------------- 73.28s
ibm.mas_devops.db2 : Generate 'ibm-registry' secret -------------------------------------------------------------------------------- 6.37s
ibm.mas_devops.db2 : Lookup storage classes ---------------------------------------------------------------------------------------- 3.45s
ibm.mas_devops.suite_db2_setup_for_manage : Copy the setupdb script into the db2u pod ---------------------------------------------- 2.82s
ibm.mas_devops.suite_config : Apply configs ---------------------------------------------------------------------------------------- 2.04s
ibm.mas_devops.suite_app_config : Configure application in workspace --------------------------------------------------------------- 1.83s
ibm.mas_devops.suite_app_install : Install application ----------------------------------------------------------------------------- 1.80s
ibm.mas_devops.install_operator : Create operator group ---------------------------------------------------------------------------- 1.72s
ibm.mas_devops.db2 : Create db2 instance ------------------------------------------------------------------------------------------- 1.65s
ibm.mas_devops.db2 : Create db2u Operand Request ----------------------------------------------------------------------------------- 1.65s
ibm.mas_devops.db2 : Create and wait for CA certificate ---------------------------------------------------------------------------- 1.63s
ibm.mas_devops.db2 : Lookup db2u TLS certificates ---------------------------------------------------------------------------------- 1.56s
ibm.mas_devops.db2 : Lookup db2u Engn Service -------------------------------------------------------------------------------------- 1.56s
ibm.mas_devops.db2 : Create db2u certificate --------------------------------------------------------------------------------------- 1.55s
ibm.mas_devops.db2 : Wait until the Db2uCluster CRD is available ------------------------------------------------------------------- 1.54s
ibm.mas_devops.suite_db2_setup_for_manage : Lookup db2 Pod ------------------------------------------------------------------------- 1.49s
ibm.mas_devops.install_operator : Create subscription ------------------------------------------------------------------------------ 1.48s
ibm.mas_devops.db2 : Create dedicated route: db2u-db2w-shared-tls-route ------------------------------------------------------------ 1.46s
ibm.mas_devops.db2 : Wait for Db2 Stateful set to be ready ------------------------------------------------------------------------- 1.41s
ibm.mas_devops.install_operator : Create namespace --------------------------------------------------------------------------------- 1.38s
ibm.mas_devops.suite_app_config : determine-storage-classes : Lookup storage classes ----------------------------------------------- 1.36s
ibm.mas_devops.db2 : Wait for 'norootsquash' DaemonSet to be running on all nodes -------------------------------------------------- 1.32s
ibm.mas_devops.db2 : Create 'norootsquash' DaemonSet ------------------------------------------------------------------------------- 1.32s
ibm.mas_devops.suite_app_install : detect-airgap : Look for the MAS ImageContentSourcePolicy --------------------------------------- 1.31s
ibm.mas_devops.install_operator : Create ibm-entitlement secret -------------------------------------------------------------------- 1.26s
ibm.mas_devops.db2 : Create certificate issuer ------------------------------------------------------------------------------------- 1.26s
ibm.mas_devops.db2 : Create db2u Namespace ----------------------------------------------------------------------------------------- 1.21s
ibm.mas_devops.db2 : Get cluster subdomain ----------------------------------------------------------------------------------------- 1.20s
ibm.mas_devops.db2 : Create internal CA certificate issuer ------------------------------------------------------------------------- 1.18s
ibm.mas_devops.suite_app_config : Lookup application information ------------------------------------------------------------------- 1.18s
ibm.mas_devops.db2 : See if db2u instance already exists --------------------------------------------------------------------------- 1.18s
ibm.mas_devops.db2 : Generate 'ibm-registry' secret -------------------------------------------------------------------------------- 1.12s
Gathering Facts -------------------------------------------------------------------------------------------------------------------- 1.12s
ibm.mas_devops.db2 : Lookup db2u instance password --------------------------------------------------------------------------------- 1.10s
ibm.mas_devops.db2 : Lookup existing db2u-tls-route -------------------------------------------------------------------------------- 1.07s
ibm.mas_devops.db2 : Copy JdbcCfg to filesytem ------------------------------------------------------------------------------------- 0.93s
ibm.mas_devops.suite_db2_setup_for_manage : Create setupdb script in local /tmp ---------------------------------------------------- 0.58s
ibm.mas_devops.suite_config : Find *.yml and *.yaml files in the MAS config directory ---------------------------------------------- 0.52s
ibm.mas_devops.suite_app_config : Debug information -------------------------------------------------------------------------------- 0.25s
ibm.mas_devops.db2 : Debug available storage classes ------------------------------------------------------------------------------- 0.12s
ibm.mas_devops.db2 : include_tasks ------------------------------------------------------------------------------------------------- 0.11s
ibm.mas_devops.suite_app_config : Check that the application is ready to configure a workspace ------------------------------------- 0.11s
ibm.mas_devops.db2 : Fail if required properties have not been provided ------------------------------------------------------------ 0.10s
ibm.mas_devops.db2 : Debug information --------------------------------------------------------------------------------------------- 0.10s
ibm.mas_devops.db2 : include_tasks ------------------------------------------------------------------------------------------------- 0.10s
ibm.mas_devops.suite_app_config : determine-storage-classes : Debug Manage PVC storage class configuration ------------------------- 0.10s
ibm.mas_devops.install_operator : Debug Entitlement Secret Creation ---------------------------------------------------------------- 0.10s
ibm.mas_devops.suite_db2_setup_for_manage : Debug information - part 1 ------------------------------------------------------------- 0.10s
ibm.mas_devops.suite_app_config : Determine Storage Class for Manage application --------------------------------------------------- 0.10s
Check for required environment variables ------------------------------------------------------------------------------------------- 0.10s
ibm.mas_devops.db2 : Default Data Storage for ROKS if not set by user (ReadWriteOnce) ---------------------------------------------- 0.09s
ibm.mas_devops.db2 : Set Db2u TLS port --------------------------------------------------------------------------------------------- 0.09s
ibm.mas_devops.db2 : Set Db2u certificates as Facts -------------------------------------------------------------------------------- 0.09s
ibm.mas_devops.suite_app_config : Run application specific tasks ------------------------------------------------------------------- 0.09s
ibm.mas_devops.suite_app_config : determine-storage-classes : Default Manage PVC Storage if not set by user ------------------------ 0.09s
ibm.mas_devops.suite_config : Fail if mas_config_dir is not provided --------------------------------------------------------------- 0.09s
ibm.mas_devops.suite_app_config : Debug available storage classes ------------------------------------------------------------------ 0.09s
ibm.mas_devops.db2 : Set 'ibm-registry' secret content ----------------------------------------------------------------------------- 0.09s
ibm.mas_devops.suite_app_config : Load default application workspace spec ---------------------------------------------------------- 0.09s
ibm.mas_devops.db2 : Load variables to use in JDBCCfg ------------------------------------------------------------------------------ 0.09s
ibm.mas_devops.db2 : Default Logs Storage for ROKS if not set by user (ReadWriteOnce) ---------------------------------------------- 0.09s
ibm.mas_devops.db2 : Set 'ibm-registry' secret content ----------------------------------------------------------------------------- 0.09s
ibm.mas_devops.db2 : Default Meta Storage for ROKS if not set by user (ReadWriteMany) ---------------------------------------------- 0.09s
ibm.mas_devops.suite_config : Debug information ------------------------------------------------------------------------------------ 0.09s
ibm.mas_devops.suite_db2_setup_for_manage : Database setup debug information ------------------------------------------------------- 0.08s
ibm.mas_devops.suite_app_install : Debug information ------------------------------------------------------------------------------- 0.08s
ibm.mas_devops.ansible_version_check : Verify minimum Ansible version is 2.10.3 ---------------------------------------------------- 0.08s
ibm.mas_devops.suite_app_install : debug ------------------------------------------------------------------------------------------- 0.08s
ibm.mas_devops.suite_app_install : Load default application spec ------------------------------------------------------------------- 0.08s
ibm.mas_devops.db2 : Default Temp Storage for ROKS if not set by user (ReadWriteOnce) ---------------------------------------------- 0.08s
Install Operator ------------------------------------------------------------------------------------------------------------------- 0.08s
ibm.mas_devops.db2 : Default Backup Storage for ROKS if not set by user (ReadWriteMany) -------------------------------------------- 0.08s
ibm.mas_devops.suite_app_install : Fail if required properties are not provided ---------------------------------------------------- 0.08s
ibm.mas_devops.suite_config : Debug the list of config files located --------------------------------------------------------------- 0.08s
ibm.mas_devops.db2 : Taint and label dedicated worker node ------------------------------------------------------------------------- 0.08s
ibm.mas_devops.suite_app_config : Load application-workspace variables ------------------------------------------------------------- 0.08s
ibm.mas_devops.suite_app_config : Fail if required properties are not provided ----------------------------------------------------- 0.08s
ibm.mas_devops.suite_db2_setup_for_manage : Check required properties are set ------------------------------------------------------ 0.08s
ibm.mas_devops.db2 : Debug DB2U storage class configuration ------------------------------------------------------------------------ 0.08s
ibm.mas_devops.suite_config : Fail if mas_instance_id is not provided -------------------------------------------------------------- 0.08s
ibm.mas_devops.db2 : Assert that a meta storage class has been defined ------------------------------------------------------------- 0.07s
ibm.mas_devops.db2 : Assert that a data storage class has been defined ------------------------------------------------------------- 0.07s
ibm.mas_devops.suite_app_config : determine-storage-classes : Assert that Manage PVC storage class has been defined ---------------- 0.07s
ibm.mas_devops.suite_app_install : Load variables ---------------------------------------------------------------------------------- 0.07s
ibm.mas_devops.suite_app_install : Determine whether this is an airgap environment ------------------------------------------------- 0.07s
ibm.mas_devops.suite_db2_setup_for_manage : Debug information - part 2 ------------------------------------------------------------- 0.07s
ibm.mas_devops.db2 : Set Facts for JdbcCfg ----------------------------------------------------------------------------------------- 0.07s
ibm.mas_devops.suite_app_config : Load application variables ----------------------------------------------------------------------- 0.07s
ibm.mas_devops.suite_app_config : Configure namespace ------------------------------------------------------------------------------ 0.07s
ibm.mas_devops.suite_app_install : detect-airgap : Set airgap_install property ----------------------------------------------------- 0.07s
ibm.mas_devops.suite_app_install : Configure namespace ----------------------------------------------------------------------------- 0.07s
ibm.mas_devops.db2 : include_tasks ------------------------------------------------------------------------------------------------- 0.06s
ibm.mas_devops.install_operator : Patch default SA --------------------------------------------------------------------------------- 0.06s
ibm.mas_devops.suite_app_config : Create Manage app JMS additional server config secrets ------------------------------------------- 0.06s
ibm.mas_devops.db2 : Load default storage class information ------------------------------------------------------------------------ 0.06s
ibm.mas_devops.suite_db2_setup_for_manage : Configure facts ------------------------------------------------------------------------ 0.06s
```

</details>

#### 参考.実行されるロールと所要時間の目安

Install Manage Applicationより抜粋
https://ibm-mas.github.io/ansible-devops/playbooks/oneclick-manage/

- Install dependencies:
  - Install IBM Db2 Universal Operator (2 minutes)
  - Create Db2 Warehouse Instance (45 minutes)
  - Additional Db2 configuration for Manage (5 minutes)
- Configure Maximo Application Suite:
  - Set up Db2 instance as the system-level JDBC datasource
- Install Maximo Manage application:
  - Install application (10 minutes)
  - Configure workspace (2 hours)


### 前項
- [ 02_MASインストール ](../02_masinstall/index.md)

### 次項
- [ 04_管理者ユーザーの作成 ](../04_maxadmin/index.md)
