## 2.MASインストール

ibmmas/cliコンテナイメージ内で、playbookを実行してMASをインストールします。


### 1. cliコンテナイメージの確認

1. 「01_事前準備」の「3.コンテナイメージのダウンロード」でダウンロードしたコンテナを確認します。

    ```bash
    MAS-install-JP % podman images
    REPOSITORY          TAG                IMAGE ID      CREATED       SIZE
    quay.io/ibmmas/cli  3.3.0              104260da5049  2 weeks ago   2.05 GB
    ```

### 2. ibmmas/cliコンテナイメージの起動

1. cli の後ろに: とTAG名を指定してコンテナを起動します。

    -vオプションを指定することで、コンテナイメージの/home/localにライセンスファイルを配置したディレクトリをマウントできます。  
    ここでは、ubuntu上の/workに「entitlement.lic」を配置しており、そのディレクトリをコンテナの/home/local上にマウントしています。

    コマンド:
    ```bash
    masenv@IBM-PF39YTN4:/work$ podman run -ti -v /work:/home/local cli:3.3.0
    ```

    <details>
    <summary>実行ログ</summary>

    ```bash
    masenv@IBM-PF39YTN4:/work$ podman run -ti cli:3.3.0
    IBM Maximo Application Suite CLI Container v3.3.0

    https://github.com/ibm-mas/ansible-devops
    https://github.com/ibm-mas/ansible-airgap
    https://github.com/ibm-mas/cli

    Available commands:
    - mas install to launch a MAS install pipeline
    - mas provision-fyre to provision an OCP cluster on IBM DevIT Fyre (internal)
    - mas provision-roks to provision an OCP cluster on IBMCloud Red Hat OpenShift Service (ROKS)
    - mas provision-aws to provision an OCP cluster on AWS
    - mas provision-rosa to provision an OCP cluster on AWS Red Hat OpenShift Service (ROSA)
    - mas setup-registry to setup a private container registry on an OCP cluster
    - mas mirror-images to mirror container images required by mas to a private registry
    - mas configure-ocp-for-mirror to configure a cluster to use a private registry as a mirror
    ```

    </details>

### 3. MASライセンスファイルの確認

1. コンテナ上の「/home/local」に「entitlement.lic」が存在するかを確認します。

    コマンド:
    ```bash
    [ibmmas/cli:3.3.0]mascli$ cd /home/local
    [ibmmas/cli:3.3.0]local$ ls -l
    total 7848
    -rw-r--r-- 1 root root    9085 Aug 15 03:23 entitlement.lic
    ・
    ・
    ```

### 4. openshiftクラスタ環境へのログイン
1. oc ログインの実行
   「1.事前準備」で用意したROKS環境へログインします

    ```bash
    oc login --token=TOKEN --serverOpenShift_URL:port
    ```
    <details>
    <summary>実行ログ</summary>

    ```bash
    [ibmmas/cli:3.3.0]home$ oc login --token=sha256~ALk8vxoNxHaS1NRUl_nP8Xrb7IrGHRwZUIYVp_OsREM --server=https://c115-e.jp-tok.containers.cloud.ibm.com:30884
    Logged into "https://c115-e.jp-tok.containers.cloud.ibm.com:30884" as "IAM#godai@jp.ibm.com" using the token provided.

    You have access to 74 projects, the list has been suppressed. You can list all projects with 'oc projects'

    Using project "default".
    Welcome! See 'oc help' to get started.
    ```

    </details>
    参考. 「OpenShift_URL:port」は以下の手順で確認

    Openshift Webコンソールへログインし、ユーザー名のドロップダウンから「ログインコマンドのコピー」をクリック

    ![](2022-04-20-09-25-09.png)
    
    「Display Token」をクリック

    ![](2022-04-20-09-25-28.png)

    トークン付きのURLをコピー

    ![](![](2022-04-20-09-25-50.png).png)



### 5. 環境変数の定義

1. コンテナイメージ内で、「01_事前準備」で準備した環境情報と、MASの設定を環境変数として定義します。  

    環境に応じて以下を変更、実行してください。

    ※本手順ではMAS_CATALOG_VERSIONにて静的カタログを指定して導入しています。静的カタログの指定は必須では有りません。指定しない場合、動的カタログにて導入されます。詳細は以下の「補足.カタログについて」を参照ください。
      
| 環境変数の項目                                        | 設定する値                                                     |
| ------------------------------------------- | -------------------------------------------------------------- |
| **IBM_ENTITLEMENT_KEY**          | 「01.事前準備」で準備したIBM Entitlement Keyの文字列|
| **MAS_INSTANCE_ID**                         | inst1(任意のインスタンスID)                                    |
| **MAS_CONFIG_DIR** | /home/local/                                                              |
| **MAS_CHANNEL**                             | 8.9.x                                            |
| **SLS_LICENSE_ID**                              | 「01.事前準備」で準備した12桁の文字列                          |
| **SLS_LICENSE_FILE**                            | /home/local/entitlement.lic                                    |
| **UDS Contact Email**                       | 任意のe-mailアドレスを設定                                     |
| **UDS Contact First Name**                  | 任意の値を設定                                                 |
| **UDS Contact Last Name**                   | 任意の値を設定                                                 |
| **MAS_CATALOG_VERSION**                  | 任意の項目。明示的に静的カタログを指定する場合に指定         


    参考 : 環境変数の設定例
    ```bash
    export IBM_ENTITLEMENT_KEY=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJJQk0gTWFya2V0cGxhY2UiLCJpYXQiOjE2MTM1MjI0MzAsImp0aSI6IjJmYjI0OTJiNWMzMTQ2ZDY5MjExNzIwZTg2NTA5ZWNjIn0.0CTnlDWrCQdeoJZnV_7_XHt7hCCIX3FTBlS7MXXXX
    export MAS_INSTANCE_ID=inst1
    export MAS_CONFIG_DIR=/home/local
    export MAS_CHANNEL=8.9.x
    export SLS_LICENSE_FILE=/home/local/entitlement.lic
    export SLS_LICENSE_ID=7ddf95e0a76c
    export UDS_CONTACT_EMAIL=godai@jp.ibm.com
    export UDS_CONTACT_FIRSTNAME=Godai
    export UDS_CONTACT_LASTNAME=Aoki
    export MAS_CATALOG_VERSION=v8-221129-amd64
    ```


<details>
<summary>実行ログ</summary>

```bash
[ibmmas/cli:3.3.0]~$ export IBM_ENTITLEMENT_KEY=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJJQk0gTWFya2V0cGxhY2UiLCJpYXQiOjE2MTM1MjI0MzAsImp0aSI6IjJmYjI0OTJiNWMzMTQ2ZDY5MjExNzIwZTg2NTA5ZWNjIn0.0CTnlDWrCQdeoJZnV_7_XHt7hCCIX3FTBlS7MXXXX
[ibmmas/cli:3.3.0]~$ export MAS_INSTANCE_ID=inst1
[ibmmas/cli:3.3.0]~$ export MAS_CONFIG_DIR=/home/local
[ibmmas/cli:3.3.0]~$ export MAS_CHANNEL=8.9.x
[ibmmas/cli:3.3.0]~$ export SLS_LICENSE_FILE=/home/local/entitlement.lic
[ibmmas/cli:3.3.0]~$ export SLS_LICENSE_ID=7ddf95e0a76c
[ibmmas/cli:3.3.0]~$ export UDS_CONTACT_EMAIL=godai@jp.ibm.com
[ibmmas/cli:3.3.0]~$ export UDS_CONTACT_FIRSTNAME=Godai
[ibmmas/cli:3.3.0]~$ export UDS_CONTACT_LASTNAME=Aoki
[ibmmas/cli:3.3.0]~$ export MAS_CATALOG_VERSION=v8-221129-amd64
```

</details>


### 補足.カタログについて
MASのインストールでは動的カタログ(Dynamic Catalog)または静的カタログ(Static Catalogs)を利用して導入することができます。

   | 項目                    | 概要 |
   | ----------------------- | ---------------------------------------------- |
   | **動的カタログ**             |  MASが依存する各コンポーネントは、最新バージョン(Maximo Application Suiteチームにより互換性がテストされたバージョン)で導入され、自動的に更新される                    |
   | **静的カタログ** | MASが依存する各コンポーネントは、固定されたバージョンで導入される。セキュリティアップデート/バグフィックスを適用するためには静的カタログのバージョンを定期的に更新する必要がある

詳細は以下のリンク先を参照ください。静的カタログの項目にバージョンが記載されています。

Catalog Options
https://ibm-mas.github.io/cli/catalogs/

導入後、どのカタログが適用されているかは、ibm-operator-catalogの定義から確認することができます。

1. [検索]-[リソース]-[CatalogSource]の選択
   
![](2022-12-23-09-21-49.png)

2. [ibm-operator-catalog]を選択

![](2022-12-23-09-22-47.png)

3. [YAML]タブを開き、imageの項目を確認

![](2022-12-23-09-27-10.png)

### 6. インストールの実行

1. 「/home/local」上で下記コマンドを用い、MASのインストールコマンドを実行します。

    コマンド:
    ```bash
    [ibmmas/cli:3.3.0]~$ ansible-playbook ibm.mas_devops.oneclick_core
    ```

    <details>
    <summary>実行ログ</summary>

    ```bash
    [ibmmas/cli:3.3.0]~$ ansible-playbook ibm.mas_devops.oneclick_core
    Using /opt/app-root/src/ansible.cfg as config file
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match
    'all'
    [WARNING]: running playbook inside collection ibm.mas_devops

    PLAY [localhost] ******************************************************************************************************

    TASK [Gathering Facts] ************************************************************************************************
    Wednesday 07 December 2022  02:26:02 +0000 (0:00:00.071)       0:00:00.071 **** 
    ok: [localhost]

    TASK [Check for required environment variables] ***********************************************************************
    Wednesday 07 December 2022  02:26:03 +0000 (0:00:01.024)       0:00:01.095 **** 
    ok: [localhost] => {
        "changed": false,
        "msg": "All assertions passed"
    }

    TASK [ibm.mas_devops.ansible_version_check : Verify minimum Ansible version is 2.10.3] ********************************
    Wednesday 07 December 2022  02:26:03 +0000 (0:00:00.135)       0:00:01.231 **** 
    ok: [localhost] => {
        "changed": false,
        "msg": "All assertions passed"
    }

    TASK [ibm.mas_devops.ibm_catalogs : Determine whether this is an airgap environment] **********************************
    Wednesday 07 December 2022  02:26:03 +0000 (0:00:00.153)       0:00:01.385 **** 
    included: /opt/app-root/lib64/python3.9/site-packages/ansible_collections/ibm/mas_devops/common_tasks/detect_airgap.yml for localhost

    TASK [ibm.mas_devops.ibm_catalogs : detect-airgap : Look for the MAS ImageContentSourcePolicy] ************************
    Wednesday 07 December 2022  02:26:03 +0000 (0:00:00.182)       0:00:01.568 **** 
    ok: [localhost] => {"api_found": true, "changed": false, "resources": []}

    TASK [ibm.mas_devops.ibm_catalogs : debug] ****************************************************************************
    Wednesday 07 December 2022  02:26:07 +0000 (0:00:03.332)       0:00:04.901 **** 
    ok: [localhost] => {
        "content_source_policy_lookup": {
            "api_found": true,
            "changed": false,
            "failed": false,
            "resources": []
        }
    }

    TASK [ibm.mas_devops.ibm_catalogs : detect-airgap : Set airgap_install property] **************************************
    Wednesday 07 December 2022  02:26:07 +0000 (0:00:00.089)       0:00:04.990 **** 
    ok: [localhost] => {"ansible_facts": {"airgap_install": false}, "changed": false}

    TASK [ibm.mas_devops.ibm_catalogs : detect-airgap : Debug Airgap detection] *******************************************
    Wednesday 07 December 2022  02:26:07 +0000 (0:00:00.092)       0:00:05.082 **** 
    ok: [localhost] => {
        "msg": "Airgap environment .................... False"
    }

    (中略)

    TASK [ibm.mas_devops.suite_verify : Fail if mas_instance_id is not provided] ******************************************
    Wednesday 07 December 2022  03:24:06 +0000 (0:00:08.633)       0:58:03.690 **** 
    ok: [localhost] => {
        "changed": false,
        "msg": "All assertions passed"
    }

    TASK [ibm.mas_devops.suite_verify : Configure namespace] **************************************************************
    Wednesday 07 December 2022  03:24:06 +0000 (0:00:00.075)       0:58:03.765 **** 
    ok: [localhost] => {"ansible_facts": {"mas_namespace": "mas-inst1-core"}, "changed": false}

    TASK [ibm.mas_devops.suite_verify : Wait for Suite to be ready (60s delay)] *******************************************
    Wednesday 07 December 2022  03:24:06 +0000 (0:00:00.059)       0:58:03.825 **** 
    FAILED - RETRYING: [localhost]: Wait for Suite to be ready (60s delay) (50 retries 
    ```

    </details>

#### 参考.実行されるロールと所要時間の目安
実行されるロールと所要時間の目安

OneClick Install for MAS Coreより抜粋)
https://ibm-mas.github.io/ansible-devops/playbooks/oneclick-core/

1. Install IBM Operator Catalogs (1 minute)
2. Install IBM Common Services (3 minutes)
3. Install Certificate Manager Operator (3 minutes)
4. Install Service Binding Operator (2 minutes)
5. Configure Cluster Monitoring (1 minute)
6. Install Mongodb Operator and Create a Cluster (10 minutes)
7. Install and bootstrap IBM Suite License Service (10 minutes)
8. Install IBM User Data Services (30 minutes)
9. Generate a MAS Workspace Configuration (1 minute)
10. Configure Cloud Internet Services Integration for Maximo Application Suite (Optional, 1 minute)
11. Install Maximo Application Suite Core Services (1 minute)
12. Configure Maximo Application Suite (1 minute)
13. Verify the Install and Configuration of Maximo Application Suite (25 minutes)


### 前項
- [ 01 事前準備 ](../01_prereqs/index.md)


### 次項
- [ 03_Manageインストール ](../03_manageinstall/index.md)
