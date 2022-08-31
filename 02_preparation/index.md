## 2.MASインストール前準備

下記の手順をMASインストール前作業として実行します。


### 1. cliコンテナイメージの確認

1. 「01_事前準備」の「3.コンテナイメージのダウンロード」でダウンロードしたコンテナを確認します。

    ```bash
    masenv@IBM-PF39YTN4:/work$ podman images
    REPOSITORY                     TAG         IMAGE ID      CREATED        SIZE
    quay.io/ibmmas/cli             2.1.2       bb65336e27e4  8 days ago     1.78 GB
    ```


### 2. ibmmas/cliコンテナイメージの起動

1. cli の後ろに: とTAG名を指定してコンテナを起動します。

    -vオプションを指定することで、コンテナイメージの/home/localにライセンスファイルを配置したディレクトリをマウントできます。  
    ここでは、ubuntu上の/workに「entitlement.lic」を配置しており、そのディレクトリをコンテナの/home/local上にマウントしています。

    コマンド:
    ```bash
    masenv@IBM-PF39YTN4:/work$ podman run -ti -v /work:/home/local cli:2.1.2
    ```

<details>
<summary>実行ログ</summary>

```bash
masenv@IBM-PF39YTN4:/work$ podman run -ti -v /work:/home/local cli:2.1.2
IBM Maximo Application Suite CLI Container v2.1.2

https://github.ibm.com/ibm-mas/ansible-devops
https://github.ibm.com/ibm-mas/ansible-airgap
https://github.ibm.com/ibm-mas/cli

Available commands:
  - mas install to launch a MAS install pipeline
  - mas provision-fyre to provision an OCP cluster on IBM DevIT Fyre (internal)
  - mas provision-roks to provision an OCP cluster on IBMCloud Red Hat OpenShift Service (ROKS)
  - mas provision-rosa to provision an OCP cluster on AWS Red Hat OpenShift Service (ROSA)
  - mas setup-registry to setup a private container registry on an OCP cluster
  - mas mirror-images to mirror container images required by mas to a private registry
  - mas configure-ocp-for-mirror to configure a cluster to use a private registry as a mirror
```

</details>


### 3. 環境変数の定義

1. コンテナイメージ内で、「01_事前準備」で準備した環境情報を環境変数として定義します。  

    環境に応じて以下を変更してください。
      
    注意事項：  
      - OCP_TOKENは更新されている可能性があるため、指定前に最新の値を確認してください。  
    

   | 環境変数                |
   | ----------------------- |
   | **CPD_ENTITLEMENT_KEY** |
   | **IBMCLOUD_APIKEY**     |
   | **CLUSTER_NAME**        |
   | **OCP_VERSION**         |
   | **OCP_SERVER**          |
   | **OCP_TOKEN**           |

    参考 : 環境変数の設定例
    ```bash
    export CPD_ENTITLEMENT_KEY=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJJQk0gTWFya2V0cGxhY2UiLCJpYXQiOjE2MTYwNTU0MDksImp0aSI6IjJhMjc0ZTg1ZTlkNjRlOGY5MTcxZDVjYjUwOTc3OTBlIn0.ZmbL9RHn3O91sT5JF42WWylGgyK5XTqrXXXXXXXXXXX
    export IBMCLOUD_APIKEY=JhNR7aJ_FqAoRtywDjj4Eg9I97Xb8-TCgIDAkq1pskkH
    export CLUSTER_NAME=itzroks-2700034gbd-ug2zv49t
    export OCP_VERSION=4.8_openshift
    export OCP_SERVER=https://c115-e.jp-tok.containers.cloud.ibm.com:32726
    export OCP_TOKEN=sha256~uXQGjIu-EfltVtTiVmeYO8lDiwatVQpXkXXXXXXXXX
    export REBOOT_WORKER_NODES=true
    export UPGRADE_IMAGE_REGISTRY_STORAGE=true
    ```

### 4. 構成コマンドの実行

1. 対話形式で必要な情報を入力します。  
    
    環境変数としてTechzZoneにプロビジョニングされているAPIキーやtokenを定義しているため、新規にROKS環境がプロビジョニングされることはなく、追加構成のみが実行されます。

    注意事項：
    - 最初から値が入っている項目もあるため注意してください。
    - 空のまま「Enter」を押して先に進むことも可能ですが、その場合は大文字のアルファベットの設定が適用されます。

    コマンド:
    ```bash
    [ibmmas/cli:2.1.2]local$ mas provision-roks
    ```


    | 項目                                                   | 設定する値                        |
    | ------------------------------------------------------ | --------------------------------- |
    | **IBMCLOUD_APIKEY**                                    | 環境変数のIBMCLOUD_APIKEYより取得 |
    | **CLUSTER_NAME**                                       | 環境変数のCLUSTER_NAMEより取得    |
    | **OCP Version:(Select Version)**                       | 2(4.8を選択)                      |
    | **Resource Group**                                     | Default                           |
    | **Datacenter**                                         | tok05                             |
    | **Worker Node Type**                                   | b3c.16x64.300gb                   |
    | **Number of Worker Nodes**                             | 8                                 |
    | **Provision GPU worker nodes in cluster**              | N                                 |
    | **Upgrade Image Registry Storage from 100GB to 400GB** | y                                 |
    | **Proceed with these settings**                        | y                                 |


    構成画面ログ:
    ```bash
    [ibmmas/cli:2.1.2]local$ mas provision-roks
    IBM Maximo Application Suite ROKS Cluster Provisioner
    Powered by https://github.com/ibm-mas/ansible-devops/

    Re-use saved IBMCloud API Key Starting 'JhNR7aJ_'? [Y/n] Y

    IBM Cloud ROKS Cluster Configuration
    Select your worker node flavour and the number of worker nodes to provision.
    We recommend the 'b3c.16x64.300gb' worker node flavour to provide the best balance
    of performance, flexibility, and cost.  Three workers is sufficient to install
    all applications in the suite, but larger workloads will require more worker nodes.
    You can easily add and remove worker nodes to the cluster after provision so do not
    worry too much if you are not sure how many worker nodes you need as this can
    easily be adjusted at any time to scale your cluster up or down.

    Cluster Name > itzroks-2700034gbd-ug2zv49t

    OCP Version:
    1. 4.10 EUS (MAS 8.7-8.8)
    2. 4.8 EUS  (MAS 8.6-8.8)
    Select Version > 2
    Resource Group > Default
    Datacenter > tok05
    Worker Node Type > b3c.16x64.300gb
    Number of Worker Nodes > 8

    Bare Metal GPU Worker Support
    GPU worker nodes with flavour, mg4c.32x384.2xp100, can be added to the cluster
    for applications that require complex mathematical computations.  If you intnd
    to install the IBM Visual Inspection application you should answer 'yes' at
    the prompt below.

    Provision GPU worker nodes in cluster [y/N] N

    Upgrade Image Registry Capacity
    By default IBMCloud ROKS provisions 100GB of storage for the internal container registry.
    Answer 'yes' at the prompt below to upgrade the storage capacity to 400GB.

    Upgrade Image Registry Storage from 100GB to 400GB [y/N] y

    Review Settings

        IBMCloud Authentication
        IBMCloud API Key .......... JhNR7aJ_...
        IBMCloud Resource Group ... Default

        Cluster Configuration
        Cluster Name .............. itzroks-2700034gbd-ug2zv49t
        OCP Version ............... 4.8_openshift
        Upgrade Registry Storage .. true

        Worker Configuration
        Worker Flavour ............ b3c.16x64.300gb
        Worker Pool Zone .......... tok05
        Worker Pool Size .......... 8
        Add GPU Worker Nodes ...... false

    Proceed with these settings [y/N]

    （すべての構成が終わった後、1時間くらいかかります）
    ```


    Workerノードの再起動が含まれるため完了までは1時間ほどかかります。  
    完了すると各タスクにかかった時間とタスク実行結果が表示され、以下のようなプロンプトが表示されます。


    完了確認ログ:
    ```bash
    TASK [ibm.mas_devops.ocp_verify : Fail if one of the cluster required secrets does not exist] **************************************************************************************
    Monday 15 August 2022  05:21:29 +0000 (0:00:00.028)       0:30:29.628 *********
    ok: [localhost] => {
        "changed": false,
        "msg": "All assertions passed"
    }

    PLAY RECAP *************************************************************************************************************************************************************************
    localhost                  : ok=55   changed=14   unreachable=0    failed=0    skipped=16   rescued=0    ignored=0

    Monday 15 August 2022  05:21:30 +0000 (0:00:00.102)       0:30:29.731 *********
    ・
    ・
    ・
    IBM Cloud ROKS cluster is ready to use
    ```


<details>
<summary>実行ログ</summary>

```bash
[WARNING]: running playbook inside collection ibm.mas_devops

PLAY [localhost] *******************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************
Monday 15 August 2022  04:51:00 +0000 (0:00:00.022)       0:00:00.022 *********
ok: [localhost]

TASK [Check for required environment variables] ************************************************************************************************************************************
Monday 15 August 2022  04:51:01 +0000 (0:00:01.158)       0:00:01.181 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.ansible_version_check : Verify minimum Ansible version is 2.10.3] *********************************************************************************************
Monday 15 August 2022  04:51:01 +0000 (0:00:00.043)       0:00:01.225 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.ocp_provision : Fail if cluster name & type is not provided] **************************************************************************************************
Monday 15 August 2022  04:51:01 +0000 (0:00:00.047)       0:00:01.272 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.ocp_provision : Backwards compatability for 'quickburn' cluster type] *****************************************************************************************
Monday 15 August 2022  04:51:01 +0000 (0:00:00.041)       0:00:01.314 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_provision : Fail if cluster type is not supported] ********************************************************************************************************
Monday 15 August 2022  04:51:01 +0000 (0:00:00.028)       0:00:01.342 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.ocp_provision : Fail if no OCP version is provided] ***********************************************************************************************************
Monday 15 August 2022  04:51:01 +0000 (0:00:00.043)       0:00:01.386 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.ocp_provision : include_tasks] ********************************************************************************************************************************
Monday 15 August 2022  04:51:01 +0000 (0:00:00.069)       0:00:01.455 *********
included: /opt/app-root/lib64/python3.9/site-packages/ansible_collections/ibm/mas_devops/roles/ocp_provision/tasks/providers/roks.yml for localhost

TASK [ibm.mas_devops.ocp_provision : roks : Fail if ibmcloud_apikey is not provided] ***********************************************************************************************
Monday 15 August 2022  04:51:01 +0000 (0:00:00.094)       0:00:01.550 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.ocp_provision : roks : Debug information] *********************************************************************************************************************
Monday 15 August 2022  04:51:01 +0000 (0:00:00.033)       0:00:01.584 *********
ok: [localhost] => {
    "msg": [
        "Cluster name ................. itzroks-2700034gbd-ug2zv49t",
        "OCP version .................. 4.8_openshift",
        "IBM Cloud API key ............ ********************",
        "IBM Cloud Resource Group ..... Default",
        "ROKS zone .................... tok05",
        "ROKS flavor .................. b3c.16x64.300gb",
        "ROKS workers ................. 8",
        "ROKS flags ................... "
    ]
}

TASK [ibm.mas_devops.ocp_provision : roks : Login to IBM Cloud] ********************************************************************************************************************
Monday 15 August 2022  04:51:01 +0000 (0:00:00.049)       0:00:01.633 *********
changed: [localhost] => {"changed": true, "cmd": "ibmcloud login --apikey \"JhNR7aJ_FqAoRtywDjj4Eg9I97Xb8-TCgIDAkq1pskkH\" -q --no-region\n", "delta": "0:00:21.180055", "end": "2022-08-15 04:51:23.482985", "msg": "", "rc": 0, "start": "2022-08-15 04:51:02.302930", "stderr": "", "stderr_lines": [], "stdout": "                      \nAPI endpoint:      https://cloud.ibm.com   \nRegion:               \nUser:              AHA03926@jp.ibm.com   \nAccount:           ITZ - V2 (ead8711ba2cc4d08a16fd37427f4f01a) <-> 2112072   \nResource group:    No resource group targeted, use 'ibmcloud target -g RESOURCE_GROUP'   \nCF API endpoint:      \nOrg:                  \nSpace:                ", "stdout_lines": ["
         ", "API endpoint:      https://cloud.ibm.com   ", "Region:               ", "User:              AHA03926@jp.ibm.com   ", "Account:           ITZ - V2 (ead8711ba2cc4d08a16fd37427f4f01a) <-> 2112072   ", "Resource group:    No resource group targeted, use 'ibmcloud target -g RESOURCE_GROUP'   ", "CF API endpoint:      ", "Org:                  ", "Space:                "]}

TASK [ibm.mas_devops.ocp_provision : roks : Check resource group exists] ***********************************************************************************************************
Monday 15 August 2022  04:51:23 +0000 (0:00:21.648)       0:00:23.282 *********
changed: [localhost] => {"changed": true, "cmd": "ibmcloud resource group \"Default\"\n", "delta": "0:00:06.308274", "end": "2022-08-15 04:51:30.175863", "failed_when_result": false, "msg": "non-zero return code", "rc": 1, "start": "2022-08-15 04:51:23.867589", "stderr": "FAILED\nResource group 'Default' was not found.", "stderr_lines": ["FAILED", "Resource group 'Default' was not found."], "stdout": "Retrieving resource group Default under account ead8711ba2cc4d08a16fd37427f4f01a as AHA03926@jp.ibm.com...", "stdout_lines": ["Retrieving resource group Default under account ead8711ba2cc4d08a16fd37427f4f01a as AHA03926@jp.ibm.com..."]}

TASK [ibm.mas_devops.ocp_provision : roks : Debug current resource group status] ***************************************************************************************************
Monday 15 August 2022  04:51:30 +0000 (0:00:06.646)       0:00:29.929 *********
skipping: [localhost] => {}

TASK [ibm.mas_devops.ocp_provision : roks : Create resource group if it does not exist] ********************************************************************************************
Monday 15 August 2022  04:51:30 +0000 (0:00:00.039)       0:00:29.968 *********
changed: [localhost] => {"changed": true, "cmd": "ibmcloud resource group-create Default\n", "delta": "0:00:06.614179", "end": "2022-08-15 04:51:37.135471", "failed_when_result": false, "msg": "non-zero return code", "rc": 1, "start": "2022-08-15 04:51:30.521292", "stderr": "FAILED\nThe following error(s) occurred:\nThe token does not have permission to perform this action.\n\nTrace ID: b638f7899fa145efbca963f94d7db6e7", "stderr_lines": ["FAILED", "The following error(s) occurred:", "The token does not have permission to perform this action.", "", "Trace ID: b638f7899fa145efbca963f94d7db6e7"], "stdout": "Creating resource group Default under account ead8711ba2cc4d08a16fd37427f4f01a as AHA03926@jp.ibm.com...", "stdout_lines": ["Creating resource group Default under account ead8711ba2cc4d08a16fd37427f4f01a as AHA03926@jp.ibm.com..."]}

TASK [ibm.mas_devops.ocp_provision : roks : Switch to chosen resource group] *******************************************************************************************************
Monday 15 August 2022  04:51:37 +0000 (0:00:06.917)       0:00:36.886 *********
changed: [localhost] => {"changed": true, "cmd": "ibmcloud target -g Default\n", "delta": "0:00:06.426852", "end": "2022-08-15 04:51:43.877735", "failed_when_result": false, "msg": "non-zero return code", "rc": 1, "start": "2022-08-15 04:51:37.450883", "stderr": "FAILED\nCould not get resource group:\nThe following error(s) occurred:\nThe token does not have permission to perform this action.\n\nTrace ID: 07bc887a58ef43db9f334500ddb1c600", "stderr_lines": ["FAILED", "Could not get resource group:", "The following error(s) occurred:", "The token does not have permission to perform this action.", "", "Trace ID: 07bc887a58ef43db9f334500ddb1c600"], "stdout": "", "stdout_lines": []}

TASK [ibm.mas_devops.ocp_provision : roks : Check if cluster already exists] *******************************************************************************************************
Monday 15 August 2022  04:51:43 +0000 (0:00:06.747)       0:00:43.634 *********
changed: [localhost] => {"changed": true, "cmd": "ibmcloud oc cluster get -c itzroks-2700034gbd-ug2zv49t --output json\n", "delta": "0:00:21.808758", "end": "2022-08-15 04:52:06.060770", "failed_when_result": false, "msg": "", "rc": 0, "start": "2022-08-15 04:51:44.252012", "stderr": "", "stderr_lines": [], "stdout": "{\n    \"location\": \"Tokyo\",\n    \"dataCenter\": \"tok02\",\n    \"multiAzCapable\": true,\n    \"vlans\": [],\n    \"worker_vlans\": [],\n    \"workerZones\": [\n        \"tok02\"\n    ],\n    \"id\": \"cbsqftpt0thsm9b20hig\",\n    \"name\": \"itzroks-2700034gbd-ug2zv49t\",\n    \"region\": \"jp-tok\",\n    \"resourceGroup\": \"e1b56d2b248544e3a3549e838c1224b2\",\n    \"resourceGroupName\": \"dteroks\",\n    \"serverURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",\n    \"state\": \"normal\",\n    \"status\": \"All Workers Normal\",\n    \"createdDate\": \"2022-08-15T01:57:11+0000\",\n    \"modifiedDate\": \"2022-08-15T01:58:00+0000\",\n    \"workerCount\": 3,\n    \"isPaid\": true,\n    \"masterKubeVersion\": \"4.8.46_1566_openshift\",\n    \"targetVersion\": \"4.8.46_1566_openshift\",\n    \"ingressHostname\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud\",\n    \"ingressSecretName\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000\",\n    \"ingressStatus\": \"\",\n    \"ingressMessage\": \"\",\n    \"logOrg\": \"\",\n    \"logOrgName\": \"\",\n    \"logSpace\": \"\",\n    \"logSpaceName\": \"\",\n    \"apiUser\": \"\",\n    \"monitoringURL\": \"\",\n    \"addons\": [],\n    \"versionEOS\": \"\",\n    \"disableAutoUpdate\": false,\n    \"etcdPort\": \"\",\n    \"masterStatus\": \"Ready\",\n    \"masterStatusModifiedDate\": \"2022-08-15T02:17:06+0000\",\n    \"masterHealth\": \"normal\",\n    \"masterState\": \"deployed\",\n    \"keyProtectEnabled\": false,\n    \"pullSecretApplied\": true,\n    \"crn\": \"crn:v1:bluemix:public:containers-kubernetes:jp-tok:a/ead8711ba2cc4d08a16fd37427f4f01a:cbsqftpt0thsm9b20hig::\",\n    \"privateServiceEndpointEnabled\": false,\n    \"privateServiceEndpointURL\": \"\",\n    \"publicServiceEndpointEnabled\": true,\n    \"publicServiceEndpointURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",\n    \"podSubnet\": \"172.30.0.0/16\",\n    \"podSubnets\": \"\",\n    \"serviceSubnet\": \"172.21.0.0/16\",\n    \"type\": \"openshift\",\n    \"imageSecurityEnabled\": false,\n    \"vpcNativeRoutingEnabled\": false\n}", "stdout_lines": ["{", "    \"location\": \"Tokyo\",", "    \"dataCenter\": \"tok02\",", "    \"multiAzCapable\": true,", "    \"vlans\": [],", "    \"worker_vlans\": [],", "    \"workerZones\": [", "        \"tok02\"", "    ],", "    \"id\": \"cbsqftpt0thsm9b20hig\",", "    \"name\": \"itzroks-2700034gbd-ug2zv49t\",", "    \"region\": \"jp-tok\",", "    \"resourceGroup\": \"e1b56d2b248544e3a3549e838c1224b2\",", "    \"resourceGroupName\": \"dteroks\",", "    \"serverURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",", "    \"state\": \"normal\",", "    \"status\": \"All Workers Normal\",", "    \"createdDate\": \"2022-08-15T01:57:11+0000\",", "    \"modifiedDate\": \"2022-08-15T01:58:00+0000\",", "    \"workerCount\": 3,", "    \"isPaid\": true,", "    \"masterKubeVersion\": \"4.8.46_1566_openshift\",", "    \"targetVersion\": \"4.8.46_1566_openshift\",", "    \"ingressHostname\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud\",", "    \"ingressSecretName\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000\",", "    \"ingressStatus\": \"\",", "    \"ingressMessage\": \"\",", "    \"logOrg\": \"\",", "    \"logOrgName\": \"\",", "    \"logSpace\": \"\",", "    \"logSpaceName\": \"\",", "    \"apiUser\": \"\",", "    \"monitoringURL\": \"\",", "    \"addons\": [],", "    \"versionEOS\": \"\",", "    \"disableAutoUpdate\": false,", "    \"etcdPort\": \"\",", "    \"masterStatus\": \"Ready\",", "    \"masterStatusModifiedDate\": \"2022-08-15T02:17:06+0000\",", "    \"masterHealth\": \"normal\",", "    \"masterState\": \"deployed\",", "    \"keyProtectEnabled\": false,", "    \"pullSecretApplied\": true,", "    \"crn\": \"crn:v1:bluemix:public:containers-kubernetes:jp-tok:a/ead8711ba2cc4d08a16fd37427f4f01a:cbsqftpt0thsm9b20hig::\",", "    \"privateServiceEndpointEnabled\": false,", "    \"privateServiceEndpointURL\": \"\",", "    \"publicServiceEndpointEnabled\": true,", "    \"publicServiceEndpointURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",", "    \"podSubnet\": \"172.30.0.0/16\",", "    \"podSubnets\": \"\",", "    \"serviceSubnet\": \"172.21.0.0/16\",", "    \"type\": \"openshift\",", "    \"imageSecurityEnabled\": false,", "    \"vpcNativeRoutingEnabled\": false", "}"]}

TASK [ibm.mas_devops.ocp_provision : roks : Debug current cluster status] **********************************************************************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:22.176)       0:01:05.810 *********
ok: [localhost] => {
    "msg": "itzroks-2700034gbd-ug2zv49t already exists, skipping cluster creation"
}

TASK [ibm.mas_devops.ocp_provision : roks : Lookup VLANs automatically (ibmcloud ks vlan ls --zone tok05 --output json)] ***********************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:00.054)       0:01:05.865 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_provision : roks : Configure Private and Public Vlans] ****************************************************************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:00.023)       0:01:05.888 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_provision : roks : Create IBM Cloud ROKS cluster] *********************************************************************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:00.023)       0:01:05.911 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_provision : roks : Debug Information for GPU worker] ******************************************************************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:00.020)       0:01:05.932 *********
skipping: [localhost] => {}

TASK [ibm.mas_devops.ocp_provision : roks : Check if GPU worker pool already exists] ***********************************************************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:00.031)       0:01:05.963 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_provision : roks : Debug worker pool status] **************************************************************************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:00.029)       0:01:05.992 *********
skipping: [localhost] => {}

TASK [ibm.mas_devops.ocp_provision : roks : Add a GPU worker pool] *****************************************************************************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:00.040)       0:01:06.032 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_provision : roks : Get GPU worker pool zone information] **************************************************************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:00.033)       0:01:06.066 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_provision : roks : Add GPU worker pool to zone] ***********************************************************************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:00.030)       0:01:06.097 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_provision : roks : status of GPU worker node provisioning] ************************************************************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:00.031)       0:01:06.128 *********
skipping: [localhost] => {}

TASK [ibm.mas_devops.ocp_provision : roks : Wait until the Roks cluster is deployed] ***********************************************************************************************
Monday 15 August 2022  04:52:06 +0000 (0:00:00.060)       0:01:06.188 *********
changed: [localhost] => {"attempts": 1, "changed": true, "cmd": "ibmcloud oc cluster get --cluster itzroks-2700034gbd-ug2zv49t --output json\n", "delta": "0:00:06.080907", "end": "2022-08-15 04:52:12.862586", "msg": "", "rc": 0, "start": "2022-08-15 04:52:06.781679", "stderr": "", "stderr_lines": [], "stdout": "{\n    \"location\": \"Tokyo\",\n    \"dataCenter\": \"tok02\",\n    \"multiAzCapable\": true,\n    \"vlans\": [],\n    \"worker_vlans\": [],\n    \"workerZones\": [\n        \"tok02\"\n    ],\n    \"id\": \"cbsqftpt0thsm9b20hig\",\n    \"name\": \"itzroks-2700034gbd-ug2zv49t\",\n    \"region\": \"jp-tok\",\n    \"resourceGroup\": \"e1b56d2b248544e3a3549e838c1224b2\",\n    \"resourceGroupName\": \"dteroks\",\n    \"serverURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",\n    \"state\": \"normal\",\n    \"status\": \"All Workers Normal\",\n    \"createdDate\": \"2022-08-15T01:57:11+0000\",\n    \"modifiedDate\": \"2022-08-15T01:58:00+0000\",\n    \"workerCount\": 3,\n    \"isPaid\": true,\n    \"masterKubeVersion\": \"4.8.46_1566_openshift\",\n    \"targetVersion\": \"4.8.46_1566_openshift\",\n    \"ingressHostname\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud\",\n    \"ingressSecretName\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000\",\n    \"ingressStatus\": \"\",\n    \"ingressMessage\": \"\",\n    \"logOrg\": \"\",\n    \"logOrgName\": \"\",\n    \"logSpace\": \"\",\n    \"logSpaceName\": \"\",\n    \"apiUser\": \"\",\n    \"monitoringURL\": \"\",\n    \"addons\": [],\n    \"versionEOS\": \"\",\n    \"disableAutoUpdate\": false,\n    \"etcdPort\": \"\",\n    \"masterStatus\": \"Ready\",\n    \"masterStatusModifiedDate\": \"2022-08-15T02:17:06+0000\",\n    \"masterHealth\": \"normal\",\n    \"masterState\": \"deployed\",\n    \"keyProtectEnabled\": false,\n    \"pullSecretApplied\": true,\n    \"crn\": \"crn:v1:bluemix:public:containers-kubernetes:jp-tok:a/ead8711ba2cc4d08a16fd37427f4f01a:cbsqftpt0thsm9b20hig::\",\n    \"privateServiceEndpointEnabled\": false,\n    \"privateServiceEndpointURL\": \"\",\n    \"publicServiceEndpointEnabled\": true,\n    \"publicServiceEndpointURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",\n    \"podSubnet\": \"172.30.0.0/16\",\n    \"podSubnets\": \"\",\n    \"serviceSubnet\": \"172.21.0.0/16\",\n    \"type\": \"openshift\",\n    \"imageSecurityEnabled\": false,\n    \"vpcNativeRoutingEnabled\": false\n}", "stdout_lines": ["{", "    \"location\": \"Tokyo\",", "    \"dataCenter\": \"tok02\",", "    \"multiAzCapable\": true,", "    \"vlans\": [],", "    \"worker_vlans\": [],", "    \"workerZones\": [", "        \"tok02\"", "    ],", "    \"id\": \"cbsqftpt0thsm9b20hig\",", "    \"name\": \"itzroks-2700034gbd-ug2zv49t\",", "    \"region\": \"jp-tok\",", "    \"resourceGroup\": \"e1b56d2b248544e3a3549e838c1224b2\",", "    \"resourceGroupName\": \"dteroks\",", "    \"serverURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",", "    \"state\": \"normal\",", "    \"status\": \"All Workers Normal\",", "    \"createdDate\": \"2022-08-15T01:57:11+0000\",", "    \"modifiedDate\": \"2022-08-15T01:58:00+0000\",", "    \"workerCount\": 3,", "    \"isPaid\": true,", "    \"masterKubeVersion\": \"4.8.46_1566_openshift\",", "    \"targetVersion\": \"4.8.46_1566_openshift\",", "    \"ingressHostname\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud\",", "    \"ingressSecretName\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000\",", "    \"ingressStatus\": \"\",", "    \"ingressMessage\": \"\",", "    \"logOrg\": \"\",", "    \"logOrgName\": \"\",", "    \"logSpace\": \"\",", "    \"logSpaceName\": \"\",", "    \"apiUser\": \"\",", "    \"monitoringURL\": \"\",", "    \"addons\": [],", "    \"versionEOS\": \"\",", "    \"disableAutoUpdate\": false,", "    \"etcdPort\": \"\",", "    \"masterStatus\": \"Ready\",", "    \"masterStatusModifiedDate\": \"2022-08-15T02:17:06+0000\",", "    \"masterHealth\": \"normal\",", "    \"masterState\": \"deployed\",", "    \"keyProtectEnabled\": false,", "    \"pullSecretApplied\": true,", "    \"crn\": \"crn:v1:bluemix:public:containers-kubernetes:jp-tok:a/ead8711ba2cc4d08a16fd37427f4f01a:cbsqftpt0thsm9b20hig::\",", "    \"privateServiceEndpointEnabled\": false,", "    \"privateServiceEndpointURL\": \"\",", "    \"publicServiceEndpointEnabled\": true,", "    \"publicServiceEndpointURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",", "    \"podSubnet\": \"172.30.0.0/16\",", "    \"podSubnets\": \"\",", "    \"serviceSubnet\": \"172.21.0.0/16\",", "    \"type\": \"openshift\",", "    \"imageSecurityEnabled\": false,", "    \"vpcNativeRoutingEnabled\": false", "}"]}

TASK [ibm.mas_devops.ocp_provision : roks: Debug final cluster state] **************************************************************************************************************
Monday 15 August 2022  04:52:12 +0000 (0:00:06.428)       0:01:12.617 *********
ok: [localhost] => {
    "msg": {
        "addons": [],
        "apiUser": "",
        "createdDate": "2022-08-15T01:57:11+0000",
        "crn": "crn:v1:bluemix:public:containers-kubernetes:jp-tok:a/ead8711ba2cc4d08a16fd37427f4f01a:cbsqftpt0thsm9b20hig::",
        "dataCenter": "tok02",
        "disableAutoUpdate": false,
        "etcdPort": "",
        "id": "cbsqftpt0thsm9b20hig",
        "imageSecurityEnabled": false,
        "ingressHostname": "itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud",
        "ingressMessage": "",
        "ingressSecretName": "itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000",
        "ingressStatus": "",
        "isPaid": true,
        "keyProtectEnabled": false,
        "location": "Tokyo",
        "logOrg": "",
        "logOrgName": "",
        "logSpace": "",
        "logSpaceName": "",
        "masterHealth": "normal",
        "masterKubeVersion": "4.8.46_1566_openshift",
        "masterState": "deployed",
        "masterStatus": "Ready",
        "masterStatusModifiedDate": "2022-08-15T02:17:06+0000",
        "modifiedDate": "2022-08-15T01:58:00+0000",
        "monitoringURL": "",
        "multiAzCapable": true,
        "name": "itzroks-2700034gbd-ug2zv49t",
        "podSubnet": "172.30.0.0/16",
        "podSubnets": "",
        "privateServiceEndpointEnabled": false,
        "privateServiceEndpointURL": "",
        "publicServiceEndpointEnabled": true,
        "publicServiceEndpointURL": "https://c115-e.jp-tok.containers.cloud.ibm.com:32726",
        "pullSecretApplied": true,
        "region": "jp-tok",
        "resourceGroup": "e1b56d2b248544e3a3549e838c1224b2",
        "resourceGroupName": "dteroks",
        "serverURL": "https://c115-e.jp-tok.containers.cloud.ibm.com:32726",
        "serviceSubnet": "172.21.0.0/16",
        "state": "normal",
        "status": "All Workers Normal",
        "targetVersion": "4.8.46_1566_openshift",
        "type": "openshift",
        "versionEOS": "",
        "vlans": [],
        "vpcNativeRoutingEnabled": false,
        "workerCount": 3,
        "workerZones": [
            "tok02"
        ],
        "worker_vlans": []
    }
}

TASK [ibm.mas_devops.ocp_login : Fail if type is not provided] *********************************************************************************************************************
Monday 15 August 2022  04:52:12 +0000 (0:00:00.036)       0:01:12.654 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.ocp_login : debug] ********************************************************************************************************************************************
Monday 15 August 2022  04:52:12 +0000 (0:00:00.040)       0:01:12.694 *********
ok: [localhost] => {
    "msg": [
        "Cluster name ................. itzroks-2700034gbd-ug2zv49t",
        "Cluster type ................. roks",
        "OCP server ................... https://c115-e.jp-tok.containers.cloud.ibm.com:32726"
    ]
}

TASK [ibm.mas_devops.ocp_login : include_tasks] ************************************************************************************************************************************
Monday 15 August 2022  04:52:13 +0000 (0:00:00.029)       0:01:12.724 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_login : include_tasks] ************************************************************************************************************************************
Monday 15 August 2022  04:52:13 +0000 (0:00:00.027)       0:01:12.751 *********
included: /opt/app-root/lib64/python3.9/site-packages/ansible_collections/ibm/mas_devops/roles/ocp_login/tasks/login.yml for localhost

TASK [ibm.mas_devops.ocp_login : login : Fail if ocp_server or ocp_token are not provided] *****************************************************************************************
Monday 15 August 2022  04:52:13 +0000 (0:00:00.040)       0:01:12.792 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.ocp_login : login : Login to OCP] *****************************************************************************************************************************
Monday 15 August 2022  04:52:13 +0000 (0:00:00.038)       0:01:12.830 *********
changed: [localhost] => {"attempts": 1, "changed": true, "cmd": "oc login --token=sha256~uXQGjIu-EfltVtTiVmeYO8lDiwatVQpXkglGW9Tgjq8 --server=https://c115-e.jp-tok.containers.cloud.ibm.com:32726", "delta": "0:00:08.164111", "end": "2022-08-15 04:52:21.522609", "msg": "", "rc": 0, "start": "2022-08-15 04:52:13.358498", "stderr": "", "stderr_lines": [], "stdout": "Logged into \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\" as \"IAM#aha03926@jp.ibm.com\" using the token provided.\n\nYou have access to 67 projects, the list has been suppressed. You can list all projects with 'oc projects'\n\nUsing project \"default\".\nWelcome! See 'oc help' to get started.", "stdout_lines": ["Logged into \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\" as \"IAM#aha03926@jp.ibm.com\" using the token provided.", "", "You have access to 67 projects, the list has been suppressed. You can list all projects with 'oc projects'", "", "Using project \"default\".", "Welcome! See 'oc help' to get started."]}

TASK [ibm.mas_devops.ocp_login : login : Debug OCP login] **************************************************************************************************************************
Monday 15 August 2022  04:52:21 +0000 (0:00:08.434)       0:01:21.265 *********
ok: [localhost] => {
    "msg": [
        "Logged into \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\" as \"IAM#aha03926@jp.ibm.com\" using the token provided.",
        "",
        "You have access to 67 projects, the list has been suppressed. You can list all projects with 'oc projects'",
        "",
        "Using project \"default\".",
        "Welcome! See 'oc help' to get started."
    ]
}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Fail if cpd_entitlement_key has not been provided] ***********************************************************************************
Monday 15 August 2022  04:52:21 +0000 (0:00:00.037)       0:01:21.303 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Fail if cluster_type is not set to roks] *********************************************************************************************
Monday 15 August 2022  04:52:21 +0000 (0:00:00.038)       0:01:21.342 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Fail if cluster_name has not been provided] ******************************************************************************************
Monday 15 August 2022  04:52:21 +0000 (0:00:00.036)       0:01:21.378 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Fail if ibmcloud_apikey has not been provided] ***************************************************************************************
Monday 15 August 2022  04:52:21 +0000 (0:00:00.035)       0:01:21.414 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Debug information] *******************************************************************************************************************
Monday 15 August 2022  04:52:21 +0000 (0:00:00.034)       0:01:21.448 *********
ok: [localhost] => {
    "msg": [
        "CPD registry ................. cp.icr.io",
        "CPD username ................. cp",
        "Cluster Name ................. itzroks-2700034gbd-ug2zv49t",
        "Cluster Type ................. roks"
    ]
}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Set new secret content] **************************************************************************************************************
Monday 15 August 2022  04:52:21 +0000 (0:00:00.034)       0:01:21.483 *********
ok: [localhost] => {"ansible_facts": {"new_secret": {"auths": {"cp.icr.io": {"auth": "Y3A6ZXlKaGJHY2lPaUpJVXpJMU5pSjkuZXlKcGMzTWlPaUpKUWswZ1RXRnlhMlYwY0d4aFkyVWlMQ0pwWVhRaU9qRTJNVFl3TlRVME1Ea3NJbXAwYVNJNklqSmhNamMwWlRnMVpUbGtOalJsT0dZNU1UY3haRFZqWWpVd09UYzNPVEJsSW4wLlptYkw5UkhuM085MXNUNUpGNDJXV3lsR2d5SzVYVHFyWDJXQ2lrUTZfd2c=", "email": "cp", "password": "eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJJQk0gTWFya2V0cGxhY2UiLCJpYXQiOjE2MTYwNTU0MDksImp0aSI6IjJhMjc0ZTg1ZTlkNjRlOGY5MTcxZDVjYjUwOTc3OTBlIn0.ZmbL9RHn3O91sT5JF42WWylGgyK5XTqrX2WCikQ6_wg", "username": "cp"}}}}, "changed": false}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Retrieve existing pull-secret content] ***********************************************************************************************
Monday 15 August 2022  04:52:21 +0000 (0:00:00.040)       0:01:21.523 *********
ok: [localhost] => {"api_found": true, "changed": false, "resources": [{"apiVersion": "v1", "data": {".dockerconfigjson": "eyJhdXRocyI6eyJjbG91ZC5vcGVuc2hpZnQuY29tIjp7ImF1dGgiOiJiM0JsYm5Ob2FXWjBMWEpsYkdWaGMyVXRaR1YySzI5amJWOWhZMk5sYzNOZll6UmlZbU5pWkRrd05URTROR1V6TURrNU5ETTFaV0UwWWpGaFl6QTBNVE02VGxnd1NVOVpPREF5UWtFME9GWkNTRWxCV1U1VVR6STRObE5hUzFKUVZFTk9XbFV5VWtSWlNrd3dXVVpFU2tsUVNWQlBTRXcwVkRVeE1FOUpUa2xUT1E9PSIsImVtYWlsIjoiaWJtb2NwQHVzLmlibS5jb20ifSwicXVheS5pbyI6eyJhdXRoIjoiYjNCbGJuTm9hV1owTFhKbGJHVmhjMlV0WkdWMksyOWpiVjloWTJObGMzTmZZelJpWW1OaVpEa3dOVEU0TkdVek1EazVORE0xWldFMFlqRmhZekEwTVRNNlRsZ3dTVTlaT0RBeVFrRTBPRlpDU0VsQldVNVVUekk0TmxOYVMxSlFWRU5PV2xVeVVrUlpTa3d3V1VaRVNrbFFTVkJQU0V3MFZEVXhNRTlKVGtsVE9RPT0iLCJlbWFpbCI6ImlibW9jcEB1cy5pYm0uY29tIn0sInJlZ2lzdHJ5LmNvbm5lY3QucmVkaGF0LmNvbSI6eyJhdXRoIjoiZkhWb1l5MXdiMjlzTFRSaFpqRXhPREk1TFdaak1EWXROREJoTlMwNE1UbGxMV1F6WkdGa1pUTXlaamt5WWpwbGVVcG9Za2RqYVU5cFNsTlZlbFY0VFdsS09TNWxlVXA2WkZkSmFVOXBTWGhPZWswMVRsUk9hMXBYV1RGT2VsRXdUVzFWTkZscVJUQk9WMDVxV1cxSk5FNXRXVE5aVkVwb1RVTktPUzVpVGtkNWIzaFVSMmRKYlVGRmNVZExjMjlYY1d3eGJWTlpiR05oT0ZaVWIwOXllblJLTUZOMFpHSjVNbVZITFdkQlMwNWpkRFU1VjNCVmRqTTBZa3h3YzBOT1NrNXFNazB0VEY5V1VFNHRUbmcxYjNKcFgxZDZXRFZVUjJoNlNuVkplRzVIVnpkS1RsRnVaV3RVVEdFMGNuaE5jVGx2YWxCd2FWQlJTR1pQUlRrd2QyUTRielJDVDFKclEwNU5lbTFQVFRsTFdEaHdURTF2TkVKYVNUQXlNRFZYUWpkdmJUSlBSSEY1ZGs1SVRHTklNSEo0V0hBemFIWk5hWFI2WW1kV2NYcDNXSE5EWW5sU1YxZG9XVEpIVTJOSUxVUTFhVXA0WW14cE9YTnVUMGxTYm5STmVFWnRRekJWYVU4Mk5GSm5RVmx0UkhWV1NtMWZWM3A1VXpOd1NIVXpUMDVzV1RWbFVYTnhiRlUwTjFCMVdHZHdkVzFTYjBaVVNWWTFUM1ZqUmw5c2N6UmxURVpEUWt0MFh6WlVNbXhTV0dkU2RtNDJUSEI2TkMxcmFXSTVjV0Z5V0VwWkxXMWtRV1Y0ZEhrdFRtbG9abEJsUTAxU1QzSmZTR28yWDNaSlpUZ3dNWEJCZFdOV2VtbHdlWHBxUmtZeFpVWnlURk01ZEZOVVUwTjVlVWw2TmxsUlprRTJlWFZ2TFhwWGEzVktkbVYwWWt3MVJrVmhlRFJKYUdSUVNqSk9ZVk5oVFdGZk1XczVVRXgxYjB4NlQwVjVZM1JoY2xaRWREQlZTWFJoTVZOeVp6TlBlbTVQTTBreFgzbGpWVmhXVTJJM2NpMWhSWGxqVHpCV2RVTkxkWGgzZVVkak9FTjNVRGt5YUMxRVkwbzBiMjFFVkhaNk4zVXhRekY0Ykd0bFZWTkxNRGxMVkhaaE1ITmtTRlpSYkU5T1FWZGZXWFkzU0V0WGVFcENaVUZtUmxWcWNHZ3lXSGh1YTFGbVExWjNPVzUzZVdGTVpHUkZTakoxYW1sYVJ6aHdiRlowWjNOYVdWcFlTSGRuWlZKb1R6ZGZkVlprTjJOUWNHeDFTbGd6YTBOYWMwMTNVMmRGTmpCclpGOU5Va3RYUW1Nek1FbzRURWhtYm5kMFVVcHRXakYwWTA0dFFXb3hXV3cyZVU0NVZTMU9NVlV5UjJkd0xVUmhOVEU0UWxoNU1XeElZdz09IiwiZW1haWwiOiJpYm1vY3BAdXMuaWJtLmNvbSJ9LCJyZWdpc3RyeS5yZWRoYXQuaW8iOnsiYXV0aCI6ImZIVm9ZeTF3YjI5c0xUUmhaakV4T0RJNUxXWmpNRFl0TkRCaE5TMDRNVGxsTFdRelpHRmtaVE15WmpreVlqcGxlVXBvWWtkamFVOXBTbE5WZWxWNFRXbEtPUzVsZVVwNlpGZEphVTlwU1hoT2VrMDFUbFJPYTFwWFdURk9lbEV3VFcxVk5GbHFSVEJPVjA1cVdXMUpORTV0V1ROWlZFcG9UVU5LT1M1aVRrZDViM2hVUjJkSmJVRkZjVWRMYzI5WGNXd3hiVk5aYkdOaE9GWlViMDl5ZW5SS01GTjBaR0o1TW1WSExXZEJTMDVqZERVNVYzQlZkak0wWWt4d2MwTk9TazVxTWswdFRGOVdVRTR0VG5nMWIzSnBYMWQ2V0RWVVIyaDZTblZKZUc1SFZ6ZEtUbEZ1Wld0VVRHRTBjbmhOY1RsdmFsQndhVkJSU0daUFJUa3dkMlE0YnpSQ1QxSnJRMDVOZW0xUFRUbExXRGh3VEUxdk5FSmFTVEF5TURWWFFqZHZiVEpQUkhGNWRrNUlUR05JTUhKNFdIQXphSFpOYVhSNlltZFdjWHAzV0hORFlubFNWMWRvV1RKSFUyTklMVVExYVVwNFlteHBPWE51VDBsU2JuUk5lRVp0UXpCVmFVODJORkpuUVZsdFJIVldTbTFmVjNwNVV6TndTSFV6VDA1c1dUVmxVWE54YkZVME4xQjFXR2R3ZFcxU2IwWlVTVlkxVDNWalJsOXNjelJsVEVaRFFrdDBYelpVTW14U1dHZFNkbTQyVEhCNk5DMXJhV0k1Y1dGeVdFcFpMVzFrUVdWNGRIa3RUbWxvWmxCbFEwMVNUM0pmU0dvMlgzWkpaVGd3TVhCQmRXTldlbWx3ZVhwcVJrWXhaVVp5VEZNNWRGTlVVME41ZVVsNk5sbFJaa0UyZVhWdkxYcFhhM1ZLZG1WMFlrdzFSa1ZoZURSSmFHUlFTakpPWVZOaFRXRmZNV3M1VUV4MWIweDZUMFY1WTNSaGNsWkVkREJWU1hSaE1WTnlaek5QZW01UE0wa3hYM2xqVlZoV1UySTNjaTFoUlhsalR6QldkVU5MZFhoM2VVZGpPRU4zVURreWFDMUVZMG8wYjIxRVZIWjZOM1V4UXpGNGJHdGxWVk5MTURsTFZIWmhNSE5rU0ZaUmJFOU9RVmRmV1hZM1NFdFhlRXBDWlVGbVJsVnFjR2d5V0hodWExRm1RMVozT1c1M2VXRk1aR1JGU2pKMWFtbGFSemh3YkZaMFozTmFXVnBZU0hkblpWSm9UemRmZFZaa04yTlFjR3gxU2xnemEwTmFjMDEzVTJkRk5qQnJaRjlOVWt0WFFtTXpNRW80VEVobWJuZDBVVXB0V2pGMFkwNHRRV294V1d3MmVVNDVWUzFPTVZVeVIyZHdMVVJoTlRFNFFsaDVNV3hJWXc9PSIsImVtYWlsIjoiaWJtb2NwQHVzLmlibS5jb20ifX19"}, "kind": "Secret", "metadata": {"annotations": {"kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"data\":{\".dockerconfigjson\":\"eyJhdXRocyI6eyJjbG91ZC5vcGVuc2hpZnQuY29tIjp7ImF1dGgiOiJiM0JsYm5Ob2FXWjBMWEpsYkdWaGMyVXRaR1YySzI5amJWOWhZMk5sYzNOZll6UmlZbU5pWkRrd05URTROR1V6TURrNU5ETTFaV0UwWWpGaFl6QTBNVE02VGxnd1NVOVpPREF5UWtFME9GWkNTRWxCV1U1VVR6STRObE5hUzFKUVZFTk9XbFV5VWtSWlNrd3dXVVpFU2tsUVNWQlBTRXcwVkRVeE1FOUpUa2xUT1E9PSIsImVtYWlsIjoiaWJtb2NwQHVzLmlibS5jb20ifSwicXVheS5pbyI6eyJhdXRoIjoiYjNCbGJuTm9hV1owTFhKbGJHVmhjMlV0WkdWMksyOWpiVjloWTJObGMzTmZZelJpWW1OaVpEa3dOVEU0TkdVek1EazVORE0xWldFMFlqRmhZekEwTVRNNlRsZ3dTVTlaT0RBeVFrRTBPRlpDU0VsQldVNVVUekk0TmxOYVMxSlFWRU5PV2xVeVVrUlpTa3d3V1VaRVNrbFFTVkJQU0V3MFZEVXhNRTlKVGtsVE9RPT0iLCJlbWFpbCI6ImlibW9jcEB1cy5pYm0uY29tIn0sInJlZ2lzdHJ5LmNvbm5lY3QucmVkaGF0LmNvbSI6eyJhdXRoIjoiZkhWb1l5MXdiMjlzTFRSaFpqRXhPREk1TFdaak1EWXROREJoTlMwNE1UbGxMV1F6WkdGa1pUTXlaamt5WWpwbGVVcG9Za2RqYVU5cFNsTlZlbFY0VFdsS09TNWxlVXA2WkZkSmFVOXBTWGhPZWswMVRsUk9hMXBYV1RGT2VsRXdUVzFWTkZscVJUQk9WMDVxV1cxSk5FNXRXVE5aVkVwb1RVTktPUzVpVGtkNWIzaFVSMmRKYlVGRmNVZExjMjlYY1d3eGJWTlpiR05oT0ZaVWIwOXllblJLTUZOMFpHSjVNbVZITFdkQlMwNWpkRFU1VjNCVmRqTTBZa3h3YzBOT1NrNXFNazB0VEY5V1VFNHRUbmcxYjNKcFgxZDZXRFZVUjJoNlNuVkplRzVIVnpkS1RsRnVaV3RVVEdFMGNuaE5jVGx2YWxCd2FWQlJTR1pQUlRrd2QyUTRielJDVDFKclEwNU5lbTFQVFRsTFdEaHdURTF2TkVKYVNUQXlNRFZYUWpkdmJUSlBSSEY1ZGs1SVRHTklNSEo0V0hBemFIWk5hWFI2WW1kV2NYcDNXSE5EWW5sU1YxZG9XVEpIVTJOSUxVUTFhVXA0WW14cE9YTnVUMGxTYm5STmVFWnRRekJWYVU4Mk5GSm5RVmx0UkhWV1NtMWZWM3A1VXpOd1NIVXpUMDVzV1RWbFVYTnhiRlUwTjFCMVdHZHdkVzFTYjBaVVNWWTFUM1ZqUmw5c2N6UmxURVpEUWt0MFh6WlVNbXhTV0dkU2RtNDJUSEI2TkMxcmFXSTVjV0Z5V0VwWkxXMWtRV1Y0ZEhrdFRtbG9abEJsUTAxU1QzSmZTR28yWDNaSlpUZ3dNWEJCZFdOV2VtbHdlWHBxUmtZeFpVWnlURk01ZEZOVVUwTjVlVWw2TmxsUlprRTJlWFZ2TFhwWGEzVktkbVYwWWt3MVJrVmhlRFJKYUdSUVNqSk9ZVk5oVFdGZk1XczVVRXgxYjB4NlQwVjVZM1JoY2xaRWREQlZTWFJoTVZOeVp6TlBlbTVQTTBreFgzbGpWVmhXVTJJM2NpMWhSWGxqVHpCV2RVTkxkWGgzZVVkak9FTjNVRGt5YUMxRVkwbzBiMjFFVkhaNk4zVXhRekY0Ykd0bFZWTkxNRGxMVkhaaE1ITmtTRlpSYkU5T1FWZGZXWFkzU0V0WGVFcENaVUZtUmxWcWNHZ3lXSGh1YTFGbVExWjNPVzUzZVdGTVpHUkZTakoxYW1sYVJ6aHdiRlowWjNOYVdWcFlTSGRuWlZKb1R6ZGZkVlprTjJOUWNHeDFTbGd6YTBOYWMwMTNVMmRGTmpCclpGOU5Va3RYUW1Nek1FbzRURWhtYm5kMFVVcHRXakYwWTA0dFFXb3hXV3cyZVU0NVZTMU9NVlV5UjJkd0xVUmhOVEU0UWxoNU1XeElZdz09IiwiZW1haWwiOiJpYm1vY3BAdXMuaWJtLmNvbSJ9LCJyZWdpc3RyeS5yZWRoYXQuaW8iOnsiYXV0aCI6ImZIVm9ZeTF3YjI5c0xUUmhaakV4T0RJNUxXWmpNRFl0TkRCaE5TMDRNVGxsTFdRelpHRmtaVE15WmpreVlqcGxlVXBvWWtkamFVOXBTbE5WZWxWNFRXbEtPUzVsZVVwNlpGZEphVTlwU1hoT2VrMDFUbFJPYTFwWFdURk9lbEV3VFcxVk5GbHFSVEJPVjA1cVdXMUpORTV0V1ROWlZFcG9UVU5LT1M1aVRrZDViM2hVUjJkSmJVRkZjVWRMYzI5WGNXd3hiVk5aYkdOaE9GWlViMDl5ZW5SS01GTjBaR0o1TW1WSExXZEJTMDVqZERVNVYzQlZkak0wWWt4d2MwTk9TazVxTWswdFRGOVdVRTR0VG5nMWIzSnBYMWQ2V0RWVVIyaDZTblZKZUc1SFZ6ZEtUbEZ1Wld0VVRHRTBjbmhOY1RsdmFsQndhVkJSU0daUFJUa3dkMlE0YnpSQ1QxSnJRMDVOZW0xUFRUbExXRGh3VEUxdk5FSmFTVEF5TURWWFFqZHZiVEpQUkhGNWRrNUlUR05JTUhKNFdIQXphSFpOYVhSNlltZFdjWHAzV0hORFlubFNWMWRvV1RKSFUyTklMVVExYVVwNFlteHBPWE51VDBsU2JuUk5lRVp0UXpCVmFVODJORkpuUVZsdFJIVldTbTFmVjNwNVV6TndTSFV6VDA1c1dUVmxVWE54YkZVME4xQjFXR2R3ZFcxU2IwWlVTVlkxVDNWalJsOXNjelJsVEVaRFFrdDBYelpVTW14U1dHZFNkbTQyVEhCNk5DMXJhV0k1Y1dGeVdFcFpMVzFrUVdWNGRIa3RUbWxvWmxCbFEwMVNUM0pmU0dvMlgzWkpaVGd3TVhCQmRXTldlbWx3ZVhwcVJrWXhaVVp5VEZNNWRGTlVVME41ZVVsNk5sbFJaa0UyZVhWdkxYcFhhM1ZLZG1WMFlrdzFSa1ZoZURSSmFHUlFTakpPWVZOaFRXRmZNV3M1VUV4MWIweDZUMFY1WTNSaGNsWkVkREJWU1hSaE1WTnlaek5QZW01UE0wa3hYM2xqVlZoV1UySTNjaTFoUlhsalR6QldkVU5MZFhoM2VVZGpPRU4zVURreWFDMUVZMG8wYjIxRVZIWjZOM1V4UXpGNGJHdGxWVk5MTURsTFZIWmhNSE5rU0ZaUmJFOU9RVmRmV1hZM1NFdFhlRXBDWlVGbVJsVnFjR2d5V0hodWExRm1RMVozT1c1M2VXRk1aR1JGU2pKMWFtbGFSemh3YkZaMFozTmFXVnBZU0hkblpWSm9UemRmZFZaa04yTlFjR3gxU2xnemEwTmFjMDEzVTJkRk5qQnJaRjlOVWt0WFFtTXpNRW80VEVobWJuZDBVVXB0V2pGMFkwNHRRV294V1d3MmVVNDVWUzFPTVZVeVIyZHdMVVJoTlRFNFFsaDVNV3hJWXc9PSIsImVtYWlsIjoiaWJtb2NwQHVzLmlibS5jb20ifX19\"},\"kind\":\"Secret\",\"metadata\":{\"labels\":{\"addonmanager.kubernetes.io/mode\":\"EnsureExists\"},\"name\":\"pull-secret\",\"namespace\":\"openshift-config\"},\"type\":\"kubernetes.io/dockerconfigjson\"}"}, "creationTimestamp": "2022-08-15T02:13:46Z", "labels": {"addonmanager.kubernetes.io/mode": "EnsureExists"}, "managedFields": [{"apiVersion": "v1", "fieldsType": "FieldsV1", "fieldsV1": {"f:data": {".": {}, "f:.dockerconfigjson": {}}, "f:metadata": {"f:annotations": {".": {}, "f:kubectl.kubernetes.io/last-applied-configuration": {}}, "f:labels": {".": {}, "f:addonmanager.kubernetes.io/mode": {}}}, "f:type": {}}, "manager": "OpenAPI-Generator", "operation": "Update", "time": "2022-08-15T02:13:46Z"}], "name": "pull-secret", "namespace": "openshift-config", "resourceVersion": "5916", "uid": "ca1de4dd-a6ed-4822-9bf9-d92d296055c1"}, "type": "kubernetes.io/dockerconfigjson"}]}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Get the original cred secrets] *******************************************************************************************************
Monday 15 August 2022  04:52:30 +0000 (0:00:08.584)       0:01:30.107 *********
ok: [localhost] => (item=None) => {"censored": "the output has been hidden due to the fact that 'no_log: true' was specified for this result", "changed": false}
ok: [localhost] => {"censored": "the output has been hidden due to the fact that 'no_log: true' was specified for this result", "changed": false}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Get the dockerconfigjson info] *******************************************************************************************************
Monday 15 August 2022  04:52:30 +0000 (0:00:00.051)       0:01:30.159 *********
ok: [localhost] => {"ansible_facts": {"secret_string": {"auths": {"cloud.openshift.com": {"auth": "b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfYzRiYmNiZDkwNTE4NGUzMDk5NDM1ZWE0YjFhYzA0MTM6TlgwSU9ZODAyQkE0OFZCSElBWU5UTzI4NlNaS1JQVENOWlUyUkRZSkwwWUZESklQSVBPSEw0VDUxME9JTklTOQ==", "email": "ibmocp@us.ibm.com"}, "quay.io": {"auth": "b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfYzRiYmNiZDkwNTE4NGUzMDk5NDM1ZWE0YjFhYzA0MTM6TlgwSU9ZODAyQkE0OFZCSElBWU5UTzI4NlNaS1JQVENOWlUyUkRZSkwwWUZESklQSVBPSEw0VDUxME9JTklTOQ==", "email": "ibmocp@us.ibm.com"}, "registry.connect.redhat.com": {"auth": "fHVoYy1wb29sLTRhZjExODI5LWZjMDYtNDBhNS04MTllLWQzZGFkZTMyZjkyYjpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSXhOek01TlROa1pXWTFOelEwTW1VNFlqRTBOV05qWW1JNE5tWTNZVEpoTUNKOS5iTkd5b3hUR2dJbUFFcUdLc29XcWwxbVNZbGNhOFZUb09yenRKMFN0ZGJ5MmVHLWdBS05jdDU5V3BVdjM0Ykxwc0NOSk5qMk0tTF9WUE4tTng1b3JpX1d6WDVUR2h6SnVJeG5HVzdKTlFuZWtUTGE0cnhNcTlvalBwaVBRSGZPRTkwd2Q4bzRCT1JrQ05Nem1PTTlLWDhwTE1vNEJaSTAyMDVXQjdvbTJPRHF5dk5ITGNIMHJ4WHAzaHZNaXR6YmdWcXp3WHNDYnlSV1doWTJHU2NILUQ1aUp4YmxpOXNuT0lSbnRNeEZtQzBVaU82NFJnQVltRHVWSm1fV3p5UzNwSHUzT05sWTVlUXNxbFU0N1B1WGdwdW1Sb0ZUSVY1T3VjRl9sczRlTEZDQkt0XzZUMmxSWGdSdm42THB6NC1raWI5cWFyWEpZLW1kQWV4dHktTmloZlBlQ01ST3JfSGo2X3ZJZTgwMXBBdWNWemlweXpqRkYxZUZyTFM5dFNUU0N5eUl6NllRZkE2eXVvLXpXa3VKdmV0Ykw1RkVheDRJaGRQSjJOYVNhTWFfMWs5UEx1b0x6T0V5Y3RhclZEdDBVSXRhMVNyZzNPem5PM0kxX3ljVVhWU2I3ci1hRXljTzBWdUNLdXh3eUdjOEN3UDkyaC1EY0o0b21EVHZ6N3UxQzF4bGtlVVNLMDlLVHZhMHNkSFZRbE9OQVdfWXY3SEtXeEpCZUFmRlVqcGgyWHhua1FmQ1Z3OW53eWFMZGRFSjJ1amlaRzhwbFZ0Z3NaWVpYSHdnZVJoTzdfdVZkN2NQcGx1Slgza0Nac013U2dFNjBrZF9NUktXQmMzMEo4TEhmbnd0UUptWjF0Y04tQWoxWWw2eU45VS1OMVUyR2dwLURhNTE4Qlh5MWxIYw==", "email": "ibmocp@us.ibm.com"}, "registry.redhat.io": {"auth": "fHVoYy1wb29sLTRhZjExODI5LWZjMDYtNDBhNS04MTllLWQzZGFkZTMyZjkyYjpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSXhOek01TlROa1pXWTFOelEwTW1VNFlqRTBOV05qWW1JNE5tWTNZVEpoTUNKOS5iTkd5b3hUR2dJbUFFcUdLc29XcWwxbVNZbGNhOFZUb09yenRKMFN0ZGJ5MmVHLWdBS05jdDU5V3BVdjM0Ykxwc0NOSk5qMk0tTF9WUE4tTng1b3JpX1d6WDVUR2h6SnVJeG5HVzdKTlFuZWtUTGE0cnhNcTlvalBwaVBRSGZPRTkwd2Q4bzRCT1JrQ05Nem1PTTlLWDhwTE1vNEJaSTAyMDVXQjdvbTJPRHF5dk5ITGNIMHJ4WHAzaHZNaXR6YmdWcXp3WHNDYnlSV1doWTJHU2NILUQ1aUp4YmxpOXNuT0lSbnRNeEZtQzBVaU82NFJnQVltRHVWSm1fV3p5UzNwSHUzT05sWTVlUXNxbFU0N1B1WGdwdW1Sb0ZUSVY1T3VjRl9sczRlTEZDQkt0XzZUMmxSWGdSdm42THB6NC1raWI5cWFyWEpZLW1kQWV4dHktTmloZlBlQ01ST3JfSGo2X3ZJZTgwMXBBdWNWemlweXpqRkYxZUZyTFM5dFNUU0N5eUl6NllRZkE2eXVvLXpXa3VKdmV0Ykw1RkVheDRJaGRQSjJOYVNhTWFfMWs5UEx1b0x6T0V5Y3RhclZEdDBVSXRhMVNyZzNPem5PM0kxX3ljVVhWU2I3ci1hRXljTzBWdUNLdXh3eUdjOEN3UDkyaC1EY0o0b21EVHZ6N3UxQzF4bGtlVVNLMDlLVHZhMHNkSFZRbE9OQVdfWXY3SEtXeEpCZUFmRlVqcGgyWHhua1FmQ1Z3OW53eWFMZGRFSjJ1amlaRzhwbFZ0Z3NaWVpYSHdnZVJoTzdfdVZkN2NQcGx1Slgza0Nac013U2dFNjBrZF9NUktXQmMzMEo4TEhmbnd0UUptWjF0Y04tQWoxWWw2eU45VS1OMVUyR2dwLURhNTE4Qlh5MWxIYw==", "email": "ibmocp@us.ibm.com"}}}}, "changed": false}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Combine new secret content] **********************************************************************************************************
Monday 15 August 2022  04:52:30 +0000 (0:00:00.035)       0:01:30.195 *********
ok: [localhost] => {"ansible_facts": {"new_secret_string": {"auths": {"cloud.openshift.com": {"auth": "b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfYzRiYmNiZDkwNTE4NGUzMDk5NDM1ZWE0YjFhYzA0MTM6TlgwSU9ZODAyQkE0OFZCSElBWU5UTzI4NlNaS1JQVENOWlUyUkRZSkwwWUZESklQSVBPSEw0VDUxME9JTklTOQ==", "email": "ibmocp@us.ibm.com"}, "cp.icr.io": {"auth": "Y3A6ZXlKaGJHY2lPaUpJVXpJMU5pSjkuZXlKcGMzTWlPaUpKUWswZ1RXRnlhMlYwY0d4aFkyVWlMQ0pwWVhRaU9qRTJNVFl3TlRVME1Ea3NJbXAwYVNJNklqSmhNamMwWlRnMVpUbGtOalJsT0dZNU1UY3haRFZqWWpVd09UYzNPVEJsSW4wLlptYkw5UkhuM085MXNUNUpGNDJXV3lsR2d5SzVYVHFyWDJXQ2lrUTZfd2c=", "email": "cp", "password": "eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJJQk0gTWFya2V0cGxhY2UiLCJpYXQiOjE2MTYwNTU0MDksImp0aSI6IjJhMjc0ZTg1ZTlkNjRlOGY5MTcxZDVjYjUwOTc3OTBlIn0.ZmbL9RHn3O91sT5JF42WWylGgyK5XTqrX2WCikQ6_wg", "username": "cp"}, "quay.io": {"auth": "b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfYzRiYmNiZDkwNTE4NGUzMDk5NDM1ZWE0YjFhYzA0MTM6TlgwSU9ZODAyQkE0OFZCSElBWU5UTzI4NlNaS1JQVENOWlUyUkRZSkwwWUZESklQSVBPSEw0VDUxME9JTklTOQ==", "email": "ibmocp@us.ibm.com"}, "registry.connect.redhat.com": {"auth": "fHVoYy1wb29sLTRhZjExODI5LWZjMDYtNDBhNS04MTllLWQzZGFkZTMyZjkyYjpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSXhOek01TlROa1pXWTFOelEwTW1VNFlqRTBOV05qWW1JNE5tWTNZVEpoTUNKOS5iTkd5b3hUR2dJbUFFcUdLc29XcWwxbVNZbGNhOFZUb09yenRKMFN0ZGJ5MmVHLWdBS05jdDU5V3BVdjM0Ykxwc0NOSk5qMk0tTF9WUE4tTng1b3JpX1d6WDVUR2h6SnVJeG5HVzdKTlFuZWtUTGE0cnhNcTlvalBwaVBRSGZPRTkwd2Q4bzRCT1JrQ05Nem1PTTlLWDhwTE1vNEJaSTAyMDVXQjdvbTJPRHF5dk5ITGNIMHJ4WHAzaHZNaXR6YmdWcXp3WHNDYnlSV1doWTJHU2NILUQ1aUp4YmxpOXNuT0lSbnRNeEZtQzBVaU82NFJnQVltRHVWSm1fV3p5UzNwSHUzT05sWTVlUXNxbFU0N1B1WGdwdW1Sb0ZUSVY1T3VjRl9sczRlTEZDQkt0XzZUMmxSWGdSdm42THB6NC1raWI5cWFyWEpZLW1kQWV4dHktTmloZlBlQ01ST3JfSGo2X3ZJZTgwMXBBdWNWemlweXpqRkYxZUZyTFM5dFNUU0N5eUl6NllRZkE2eXVvLXpXa3VKdmV0Ykw1RkVheDRJaGRQSjJOYVNhTWFfMWs5UEx1b0x6T0V5Y3RhclZEdDBVSXRhMVNyZzNPem5PM0kxX3ljVVhWU2I3ci1hRXljTzBWdUNLdXh3eUdjOEN3UDkyaC1EY0o0b21EVHZ6N3UxQzF4bGtlVVNLMDlLVHZhMHNkSFZRbE9OQVdfWXY3SEtXeEpCZUFmRlVqcGgyWHhua1FmQ1Z3OW53eWFMZGRFSjJ1amlaRzhwbFZ0Z3NaWVpYSHdnZVJoTzdfdVZkN2NQcGx1Slgza0Nac013U2dFNjBrZF9NUktXQmMzMEo4TEhmbnd0UUptWjF0Y04tQWoxWWw2eU45VS1OMVUyR2dwLURhNTE4Qlh5MWxIYw==", "email": "ibmocp@us.ibm.com"}, "registry.redhat.io": {"auth": "fHVoYy1wb29sLTRhZjExODI5LWZjMDYtNDBhNS04MTllLWQzZGFkZTMyZjkyYjpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSXhOek01TlROa1pXWTFOelEwTW1VNFlqRTBOV05qWW1JNE5tWTNZVEpoTUNKOS5iTkd5b3hUR2dJbUFFcUdLc29XcWwxbVNZbGNhOFZUb09yenRKMFN0ZGJ5MmVHLWdBS05jdDU5V3BVdjM0Ykxwc0NOSk5qMk0tTF9WUE4tTng1b3JpX1d6WDVUR2h6SnVJeG5HVzdKTlFuZWtUTGE0cnhNcTlvalBwaVBRSGZPRTkwd2Q4bzRCT1JrQ05Nem1PTTlLWDhwTE1vNEJaSTAyMDVXQjdvbTJPRHF5dk5ITGNIMHJ4WHAzaHZNaXR6YmdWcXp3WHNDYnlSV1doWTJHU2NILUQ1aUp4YmxpOXNuT0lSbnRNeEZtQzBVaU82NFJnQVltRHVWSm1fV3p5UzNwSHUzT05sWTVlUXNxbFU0N1B1WGdwdW1Sb0ZUSVY1T3VjRl9sczRlTEZDQkt0XzZUMmxSWGdSdm42THB6NC1raWI5cWFyWEpZLW1kQWV4dHktTmloZlBlQ01ST3JfSGo2X3ZJZTgwMXBBdWNWemlweXpqRkYxZUZyTFM5dFNUU0N5eUl6NllRZkE2eXVvLXpXa3VKdmV0Ykw1RkVheDRJaGRQSjJOYVNhTWFfMWs5UEx1b0x6T0V5Y3RhclZEdDBVSXRhMVNyZzNPem5PM0kxX3ljVVhWU2I3ci1hRXljTzBWdUNLdXh3eUdjOEN3UDkyaC1EY0o0b21EVHZ6N3UxQzF4bGtlVVNLMDlLVHZhMHNkSFZRbE9OQVdfWXY3SEtXeEpCZUFmRlVqcGgyWHhua1FmQ1Z3OW53eWFMZGRFSjJ1amlaRzhwbFZ0Z3NaWVpYSHdnZVJoTzdfdVZkN2NQcGx1Slgza0Nac013U2dFNjBrZF9NUktXQmMzMEo4TEhmbnd0UUptWjF0Y04tQWoxWWw2eU45VS1OMVUyR2dwLURhNTE4Qlh5MWxIYw==", "email": "ibmocp@us.ibm.com"}}}}, "changed": false}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Update new pull-secret] **************************************************************************************************************
Monday 15 August 2022  04:52:30 +0000 (0:00:00.036)       0:01:30.231 *********
changed: [localhost] => {"changed": true, "method": "patch", "result": {"apiVersion": "v1", "data": {".dockerconfigjson": "eyJhdXRocyI6IHsiY2xvdWQub3BlbnNoaWZ0LmNvbSI6IHsiYXV0aCI6ICJiM0JsYm5Ob2FXWjBMWEpsYkdWaGMyVXRaR1YySzI5amJWOWhZMk5sYzNOZll6UmlZbU5pWkRrd05URTROR1V6TURrNU5ETTFaV0UwWWpGaFl6QTBNVE02VGxnd1NVOVpPREF5UWtFME9GWkNTRWxCV1U1VVR6STRObE5hUzFKUVZFTk9XbFV5VWtSWlNrd3dXVVpFU2tsUVNWQlBTRXcwVkRVeE1FOUpUa2xUT1E9PSIsICJlbWFpbCI6ICJpYm1vY3BAdXMuaWJtLmNvbSJ9LCAicXVheS5pbyI6IHsiYXV0aCI6ICJiM0JsYm5Ob2FXWjBMWEpsYkdWaGMyVXRaR1YySzI5amJWOWhZMk5sYzNOZll6UmlZbU5pWkRrd05URTROR1V6TURrNU5ETTFaV0UwWWpGaFl6QTBNVE02VGxnd1NVOVpPREF5UWtFME9GWkNTRWxCV1U1VVR6STRObE5hUzFKUVZFTk9XbFV5VWtSWlNrd3dXVVpFU2tsUVNWQlBTRXcwVkRVeE1FOUpUa2xUT1E9PSIsICJlbWFpbCI6ICJpYm1vY3BAdXMuaWJtLmNvbSJ9LCAicmVnaXN0cnkuY29ubmVjdC5yZWRoYXQuY29tIjogeyJhdXRoIjogImZIVm9ZeTF3YjI5c0xUUmhaakV4T0RJNUxXWmpNRFl0TkRCaE5TMDRNVGxsTFdRelpHRmtaVE15WmpreVlqcGxlVXBvWWtkamFVOXBTbE5WZWxWNFRXbEtPUzVsZVVwNlpGZEphVTlwU1hoT2VrMDFUbFJPYTFwWFdURk9lbEV3VFcxVk5GbHFSVEJPVjA1cVdXMUpORTV0V1ROWlZFcG9UVU5LT1M1aVRrZDViM2hVUjJkSmJVRkZjVWRMYzI5WGNXd3hiVk5aYkdOaE9GWlViMDl5ZW5SS01GTjBaR0o1TW1WSExXZEJTMDVqZERVNVYzQlZkak0wWWt4d2MwTk9TazVxTWswdFRGOVdVRTR0VG5nMWIzSnBYMWQ2V0RWVVIyaDZTblZKZUc1SFZ6ZEtUbEZ1Wld0VVRHRTBjbmhOY1RsdmFsQndhVkJSU0daUFJUa3dkMlE0YnpSQ1QxSnJRMDVOZW0xUFRUbExXRGh3VEUxdk5FSmFTVEF5TURWWFFqZHZiVEpQUkhGNWRrNUlUR05JTUhKNFdIQXphSFpOYVhSNlltZFdjWHAzV0hORFlubFNWMWRvV1RKSFUyTklMVVExYVVwNFlteHBPWE51VDBsU2JuUk5lRVp0UXpCVmFVODJORkpuUVZsdFJIVldTbTFmVjNwNVV6TndTSFV6VDA1c1dUVmxVWE54YkZVME4xQjFXR2R3ZFcxU2IwWlVTVlkxVDNWalJsOXNjelJsVEVaRFFrdDBYelpVTW14U1dHZFNkbTQyVEhCNk5DMXJhV0k1Y1dGeVdFcFpMVzFrUVdWNGRIa3RUbWxvWmxCbFEwMVNUM0pmU0dvMlgzWkpaVGd3TVhCQmRXTldlbWx3ZVhwcVJrWXhaVVp5VEZNNWRGTlVVME41ZVVsNk5sbFJaa0UyZVhWdkxYcFhhM1ZLZG1WMFlrdzFSa1ZoZURSSmFHUlFTakpPWVZOaFRXRmZNV3M1VUV4MWIweDZUMFY1WTNSaGNsWkVkREJWU1hSaE1WTnlaek5QZW01UE0wa3hYM2xqVlZoV1UySTNjaTFoUlhsalR6QldkVU5MZFhoM2VVZGpPRU4zVURreWFDMUVZMG8wYjIxRVZIWjZOM1V4UXpGNGJHdGxWVk5MTURsTFZIWmhNSE5rU0ZaUmJFOU9RVmRmV1hZM1NFdFhlRXBDWlVGbVJsVnFjR2d5V0hodWExRm1RMVozT1c1M2VXRk1aR1JGU2pKMWFtbGFSemh3YkZaMFozTmFXVnBZU0hkblpWSm9UemRmZFZaa04yTlFjR3gxU2xnemEwTmFjMDEzVTJkRk5qQnJaRjlOVWt0WFFtTXpNRW80VEVobWJuZDBVVXB0V2pGMFkwNHRRV294V1d3MmVVNDVWUzFPTVZVeVIyZHdMVVJoTlRFNFFsaDVNV3hJWXc9PSIsICJlbWFpbCI6ICJpYm1vY3BAdXMuaWJtLmNvbSJ9LCAicmVnaXN0cnkucmVkaGF0LmlvIjogeyJhdXRoIjogImZIVm9ZeTF3YjI5c0xUUmhaakV4T0RJNUxXWmpNRFl0TkRCaE5TMDRNVGxsTFdRelpHRmtaVE15WmpreVlqcGxlVXBvWWtkamFVOXBTbE5WZWxWNFRXbEtPUzVsZVVwNlpGZEphVTlwU1hoT2VrMDFUbFJPYTFwWFdURk9lbEV3VFcxVk5GbHFSVEJPVjA1cVdXMUpORTV0V1ROWlZFcG9UVU5LT1M1aVRrZDViM2hVUjJkSmJVRkZjVWRMYzI5WGNXd3hiVk5aYkdOaE9GWlViMDl5ZW5SS01GTjBaR0o1TW1WSExXZEJTMDVqZERVNVYzQlZkak0wWWt4d2MwTk9TazVxTWswdFRGOVdVRTR0VG5nMWIzSnBYMWQ2V0RWVVIyaDZTblZKZUc1SFZ6ZEtUbEZ1Wld0VVRHRTBjbmhOY1RsdmFsQndhVkJSU0daUFJUa3dkMlE0YnpSQ1QxSnJRMDVOZW0xUFRUbExXRGh3VEUxdk5FSmFTVEF5TURWWFFqZHZiVEpQUkhGNWRrNUlUR05JTUhKNFdIQXphSFpOYVhSNlltZFdjWHAzV0hORFlubFNWMWRvV1RKSFUyTklMVVExYVVwNFlteHBPWE51VDBsU2JuUk5lRVp0UXpCVmFVODJORkpuUVZsdFJIVldTbTFmVjNwNVV6TndTSFV6VDA1c1dUVmxVWE54YkZVME4xQjFXR2R3ZFcxU2IwWlVTVlkxVDNWalJsOXNjelJsVEVaRFFrdDBYelpVTW14U1dHZFNkbTQyVEhCNk5DMXJhV0k1Y1dGeVdFcFpMVzFrUVdWNGRIa3RUbWxvWmxCbFEwMVNUM0pmU0dvMlgzWkpaVGd3TVhCQmRXTldlbWx3ZVhwcVJrWXhaVVp5VEZNNWRGTlVVME41ZVVsNk5sbFJaa0UyZVhWdkxYcFhhM1ZLZG1WMFlrdzFSa1ZoZURSSmFHUlFTakpPWVZOaFRXRmZNV3M1VUV4MWIweDZUMFY1WTNSaGNsWkVkREJWU1hSaE1WTnlaek5QZW01UE0wa3hYM2xqVlZoV1UySTNjaTFoUlhsalR6QldkVU5MZFhoM2VVZGpPRU4zVURreWFDMUVZMG8wYjIxRVZIWjZOM1V4UXpGNGJHdGxWVk5MTURsTFZIWmhNSE5rU0ZaUmJFOU9RVmRmV1hZM1NFdFhlRXBDWlVGbVJsVnFjR2d5V0hodWExRm1RMVozT1c1M2VXRk1aR1JGU2pKMWFtbGFSemh3YkZaMFozTmFXVnBZU0hkblpWSm9UemRmZFZaa04yTlFjR3gxU2xnemEwTmFjMDEzVTJkRk5qQnJaRjlOVWt0WFFtTXpNRW80VEVobWJuZDBVVXB0V2pGMFkwNHRRV294V1d3MmVVNDVWUzFPTVZVeVIyZHdMVVJoTlRFNFFsaDVNV3hJWXc9PSIsICJlbWFpbCI6ICJpYm1vY3BAdXMuaWJtLmNvbSJ9LCAiY3AuaWNyLmlvIjogeyJ1c2VybmFtZSI6ICJjcCIsICJwYXNzd29yZCI6ICJleUpoYkdjaU9pSklVekkxTmlKOS5leUpwYzNNaU9pSkpRazBnVFdGeWEyVjBjR3hoWTJVaUxDSnBZWFFpT2pFMk1UWXdOVFUwTURrc0ltcDBhU0k2SWpKaE1qYzBaVGcxWlRsa05qUmxPR1k1TVRjeFpEVmpZalV3T1RjM09UQmxJbjAuWm1iTDlSSG4zTzkxc1Q1SkY0MldXeWxHZ3lLNVhUcXJYMldDaWtRNl93ZyIsICJlbWFpbCI6ICJjcCIsICJhdXRoIjogIlkzQTZaWGxLYUdKSFkybFBhVXBKVlhwSk1VNXBTamt1WlhsS2NHTXpUV2xQYVVwS1VXc3daMVJYUm5saE1sWXdZMGQ0YUZreVZXbE1RMHB3V1ZoUmFVOXFSVEpOVkZsM1RsUlZNRTFFYTNOSmJYQXdZVk5KTmtscVNtaE5hbU13V2xSbk1WcFViR3RPYWxKc1QwZFpOVTFVWTNoYVJGWnFXV3BWZDA5VVl6TlBWRUpzU1c0d0xscHRZa3c1VWtodU0wODVNWE5VTlVwR05ESlhWM2xzUjJkNVN6VllWSEZ5V0RKWFEybHJVVFpmZDJjPSJ9fX0="}, "kind": "Secret", "metadata": {"annotations": {"kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"data\":{\".dockerconfigjson\":\"eyJhdXRocyI6eyJjbG91ZC5vcGVuc2hpZnQuY29tIjp7ImF1dGgiOiJiM0JsYm5Ob2FXWjBMWEpsYkdWaGMyVXRaR1YySzI5amJWOWhZMk5sYzNOZll6UmlZbU5pWkRrd05URTROR1V6TURrNU5ETTFaV0UwWWpGaFl6QTBNVE02VGxnd1NVOVpPREF5UWtFME9GWkNTRWxCV1U1VVR6STRObE5hUzFKUVZFTk9XbFV5VWtSWlNrd3dXVVpFU2tsUVNWQlBTRXcwVkRVeE1FOUpUa2xUT1E9PSIsImVtYWlsIjoiaWJtb2NwQHVzLmlibS5jb20ifSwicXVheS5pbyI6eyJhdXRoIjoiYjNCbGJuTm9hV1owTFhKbGJHVmhjMlV0WkdWMksyOWpiVjloWTJObGMzTmZZelJpWW1OaVpEa3dOVEU0TkdVek1EazVORE0xWldFMFlqRmhZekEwTVRNNlRsZ3dTVTlaT0RBeVFrRTBPRlpDU0VsQldVNVVUekk0TmxOYVMxSlFWRU5PV2xVeVVrUlpTa3d3V1VaRVNrbFFTVkJQU0V3MFZEVXhNRTlKVGtsVE9RPT0iLCJlbWFpbCI6ImlibW9jcEB1cy5pYm0uY29tIn0sInJlZ2lzdHJ5LmNvbm5lY3QucmVkaGF0LmNvbSI6eyJhdXRoIjoiZkhWb1l5MXdiMjlzTFRSaFpqRXhPREk1TFdaak1EWXROREJoTlMwNE1UbGxMV1F6WkdGa1pUTXlaamt5WWpwbGVVcG9Za2RqYVU5cFNsTlZlbFY0VFdsS09TNWxlVXA2WkZkSmFVOXBTWGhPZWswMVRsUk9hMXBYV1RGT2VsRXdUVzFWTkZscVJUQk9WMDVxV1cxSk5FNXRXVE5aVkVwb1RVTktPUzVpVGtkNWIzaFVSMmRKYlVGRmNVZExjMjlYY1d3eGJWTlpiR05oT0ZaVWIwOXllblJLTUZOMFpHSjVNbVZITFdkQlMwNWpkRFU1VjNCVmRqTTBZa3h3YzBOT1NrNXFNazB0VEY5V1VFNHRUbmcxYjNKcFgxZDZXRFZVUjJoNlNuVkplRzVIVnpkS1RsRnVaV3RVVEdFMGNuaE5jVGx2YWxCd2FWQlJTR1pQUlRrd2QyUTRielJDVDFKclEwNU5lbTFQVFRsTFdEaHdURTF2TkVKYVNUQXlNRFZYUWpkdmJUSlBSSEY1ZGs1SVRHTklNSEo0V0hBemFIWk5hWFI2WW1kV2NYcDNXSE5EWW5sU1YxZG9XVEpIVTJOSUxVUTFhVXA0WW14cE9YTnVUMGxTYm5STmVFWnRRekJWYVU4Mk5GSm5RVmx0UkhWV1NtMWZWM3A1VXpOd1NIVXpUMDVzV1RWbFVYTnhiRlUwTjFCMVdHZHdkVzFTYjBaVVNWWTFUM1ZqUmw5c2N6UmxURVpEUWt0MFh6WlVNbXhTV0dkU2RtNDJUSEI2TkMxcmFXSTVjV0Z5V0VwWkxXMWtRV1Y0ZEhrdFRtbG9abEJsUTAxU1QzSmZTR28yWDNaSlpUZ3dNWEJCZFdOV2VtbHdlWHBxUmtZeFpVWnlURk01ZEZOVVUwTjVlVWw2TmxsUlprRTJlWFZ2TFhwWGEzVktkbVYwWWt3MVJrVmhlRFJKYUdSUVNqSk9ZVk5oVFdGZk1XczVVRXgxYjB4NlQwVjVZM1JoY2xaRWREQlZTWFJoTVZOeVp6TlBlbTVQTTBreFgzbGpWVmhXVTJJM2NpMWhSWGxqVHpCV2RVTkxkWGgzZVVkak9FTjNVRGt5YUMxRVkwbzBiMjFFVkhaNk4zVXhRekY0Ykd0bFZWTkxNRGxMVkhaaE1ITmtTRlpSYkU5T1FWZGZXWFkzU0V0WGVFcENaVUZtUmxWcWNHZ3lXSGh1YTFGbVExWjNPVzUzZVdGTVpHUkZTakoxYW1sYVJ6aHdiRlowWjNOYVdWcFlTSGRuWlZKb1R6ZGZkVlprTjJOUWNHeDFTbGd6YTBOYWMwMTNVMmRGTmpCclpGOU5Va3RYUW1Nek1FbzRURWhtYm5kMFVVcHRXakYwWTA0dFFXb3hXV3cyZVU0NVZTMU9NVlV5UjJkd0xVUmhOVEU0UWxoNU1XeElZdz09IiwiZW1haWwiOiJpYm1vY3BAdXMuaWJtLmNvbSJ9LCJyZWdpc3RyeS5yZWRoYXQuaW8iOnsiYXV0aCI6ImZIVm9ZeTF3YjI5c0xUUmhaakV4T0RJNUxXWmpNRFl0TkRCaE5TMDRNVGxsTFdRelpHRmtaVE15WmpreVlqcGxlVXBvWWtkamFVOXBTbE5WZWxWNFRXbEtPUzVsZVVwNlpGZEphVTlwU1hoT2VrMDFUbFJPYTFwWFdURk9lbEV3VFcxVk5GbHFSVEJPVjA1cVdXMUpORTV0V1ROWlZFcG9UVU5LT1M1aVRrZDViM2hVUjJkSmJVRkZjVWRMYzI5WGNXd3hiVk5aYkdOaE9GWlViMDl5ZW5SS01GTjBaR0o1TW1WSExXZEJTMDVqZERVNVYzQlZkak0wWWt4d2MwTk9TazVxTWswdFRGOVdVRTR0VG5nMWIzSnBYMWQ2V0RWVVIyaDZTblZKZUc1SFZ6ZEtUbEZ1Wld0VVRHRTBjbmhOY1RsdmFsQndhVkJSU0daUFJUa3dkMlE0YnpSQ1QxSnJRMDVOZW0xUFRUbExXRGh3VEUxdk5FSmFTVEF5TURWWFFqZHZiVEpQUkhGNWRrNUlUR05JTUhKNFdIQXphSFpOYVhSNlltZFdjWHAzV0hORFlubFNWMWRvV1RKSFUyTklMVVExYVVwNFlteHBPWE51VDBsU2JuUk5lRVp0UXpCVmFVODJORkpuUVZsdFJIVldTbTFmVjNwNVV6TndTSFV6VDA1c1dUVmxVWE54YkZVME4xQjFXR2R3ZFcxU2IwWlVTVlkxVDNWalJsOXNjelJsVEVaRFFrdDBYelpVTW14U1dHZFNkbTQyVEhCNk5DMXJhV0k1Y1dGeVdFcFpMVzFrUVdWNGRIa3RUbWxvWmxCbFEwMVNUM0pmU0dvMlgzWkpaVGd3TVhCQmRXTldlbWx3ZVhwcVJrWXhaVVp5VEZNNWRGTlVVME41ZVVsNk5sbFJaa0UyZVhWdkxYcFhhM1ZLZG1WMFlrdzFSa1ZoZURSSmFHUlFTakpPWVZOaFRXRmZNV3M1VUV4MWIweDZUMFY1WTNSaGNsWkVkREJWU1hSaE1WTnlaek5QZW01UE0wa3hYM2xqVlZoV1UySTNjaTFoUlhsalR6QldkVU5MZFhoM2VVZGpPRU4zVURreWFDMUVZMG8wYjIxRVZIWjZOM1V4UXpGNGJHdGxWVk5MTURsTFZIWmhNSE5rU0ZaUmJFOU9RVmRmV1hZM1NFdFhlRXBDWlVGbVJsVnFjR2d5V0hodWExRm1RMVozT1c1M2VXRk1aR1JGU2pKMWFtbGFSemh3YkZaMFozTmFXVnBZU0hkblpWSm9UemRmZFZaa04yTlFjR3gxU2xnemEwTmFjMDEzVTJkRk5qQnJaRjlOVWt0WFFtTXpNRW80VEVobWJuZDBVVXB0V2pGMFkwNHRRV294V1d3MmVVNDVWUzFPTVZVeVIyZHdMVVJoTlRFNFFsaDVNV3hJWXc9PSIsImVtYWlsIjoiaWJtb2NwQHVzLmlibS5jb20ifX19\"},\"kind\":\"Secret\",\"metadata\":{\"labels\":{\"addonmanager.kubernetes.io/mode\":\"EnsureExists\"},\"name\":\"pull-secret\",\"namespace\":\"openshift-config\"},\"type\":\"kubernetes.io/dockerconfigjson\"}"}, "creationTimestamp": "2022-08-15T02:13:46Z", "labels": {"addonmanager.kubernetes.io/mode": "EnsureExists"}, "managedFields": [{"apiVersion": "v1", "fieldsType": "FieldsV1", "fieldsV1": {"f:data": {".": {}, "f:.dockerconfigjson": {}}, "f:metadata": {"f:annotations": {".": {}, "f:kubectl.kubernetes.io/last-applied-configuration": {}}, "f:labels": {".": {}, "f:addonmanager.kubernetes.io/mode": {}}}, "f:type": {}}, "manager": "OpenAPI-Generator", "operation": "Update", "time": "2022-08-15T02:13:46Z"}], "name": "pull-secret", "namespace": "openshift-config", "resourceVersion": "73942", "uid": "ca1de4dd-a6ed-4822-9bf9-d92d296055c1"}, "type": "kubernetes.io/dockerconfigjson"}}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Debug change state] ******************************************************************************************************************
Monday 15 August 2022  04:52:32 +0000 (0:00:01.629)       0:01:31.860 *********
ok: [localhost] => {
    "msg": "Default Pull Secret Changed ....... True"
}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Get cluster worker node list] ********************************************************************************************************
Monday 15 August 2022  04:52:32 +0000 (0:00:00.045)       0:01:31.906 *********
changed: [localhost] => {"changed": true, "cmd": "ibmcloud oc worker ls -c itzroks-2700034gbd-ug2zv49t -q | awk '{print $1}'", "delta": "0:00:07.394867", "end": "2022-08-15 04:52:39.856932", "failed_when_result": false, "msg": "", "rc": 0, "start": "2022-08-15 04:52:32.462065", "stderr": "", "stderr_lines": [], "stdout": "\nkube-cbsqftpt0thsm9b20hig-itzroks2700-default-000001df\nkube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000260\nkube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000360", "stdout_lines": ["", "kube-cbsqftpt0thsm9b20hig-itzroks2700-default-000001df", "kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000260", "kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000360"]}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Reload all worker nodes] *************************************************************************************************************
Monday 15 August 2022  04:52:39 +0000 (0:00:07.697)       0:01:39.603 *********
skipping: [localhost] => (item=)  => {"ansible_loop_var": "item", "changed": false, "item": "", "skip_reason": "Conditional result was False"}
changed: [localhost] => (item=kube-cbsqftpt0thsm9b20hig-itzroks2700-default-000001df) => {"ansible_loop_var": "item", "changed": true, "cmd": "ibmcloud oc worker reload -c itzroks-2700034gbd-ug2zv49t -w kube-cbsqftpt0thsm9b20hig-itzroks2700-default-000001df -f", "delta": "0:00:07.665242", "end": "2022-08-15 04:52:47.817911", "item": "kube-cbsqftpt0thsm9b20hig-itzroks2700-default-000001df", "msg": "", "rc": 0, "start": "2022-08-15 04:52:40.152669", "stderr": "", "stderr_lines": [], "stdout": "Reloading workers for cluster itzroks-2700034gbd-ug2zv49t...\nProcessing kube-cbsqftpt0thsm9b20hig-itzroks2700-default-000001df...\nProcessing on kube-cbsqftpt0thsm9b20hig-itzroks2700-default-000001df complete.\nOK\nOK", "stdout_lines": ["Reloading workers for cluster itzroks-2700034gbd-ug2zv49t...", "Processing kube-cbsqftpt0thsm9b20hig-itzroks2700-default-000001df...", "Processing on kube-cbsqftpt0thsm9b20hig-itzroks2700-default-000001df complete.", "OK", "OK"]}
changed: [localhost] => (item=kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000260) => {"ansible_loop_var": "item", "changed": true, "cmd": "ibmcloud oc worker reload -c itzroks-2700034gbd-ug2zv49t -w kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000260 -f", "delta": "0:00:07.088835", "end": "2022-08-15 04:52:55.226069", "item": "kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000260", "msg": "", "rc": 0, "start": "2022-08-15 04:52:48.137234", "stderr": "", "stderr_lines": [], "stdout": "Reloading workers for cluster itzroks-2700034gbd-ug2zv49t...\nProcessing kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000260...\nProcessing on kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000260 complete.\nOK\nOK", "stdout_lines": ["Reloading workers for cluster itzroks-2700034gbd-ug2zv49t...", "Processing kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000260...", "Processing on kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000260 complete.", "OK", "OK"]}
changed: [localhost] => (item=kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000360) => {"ansible_loop_var": "item", "changed": true, "cmd": "ibmcloud oc worker reload -c itzroks-2700034gbd-ug2zv49t -w kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000360 -f", "delta": "0:00:07.098327", "end": "2022-08-15 04:53:02.594929", "item": "kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000360", "msg": "", "rc": 0, "start": "2022-08-15 04:52:55.496602", "stderr": "", "stderr_lines": [], "stdout": "Reloading workers for cluster itzroks-2700034gbd-ug2zv49t...\nProcessing kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000360...\nProcessing on kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000360 complete.\nOK\nOK", "stdout_lines": ["Reloading workers for cluster itzroks-2700034gbd-ug2zv49t...", "Processing kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000360...", "Processing on kube-cbsqftpt0thsm9b20hig-itzroks2700-default-00000360 complete.", "OK", "OK"]}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Wait until the ROKS cluster status is 'pending' (30 minute timeout)] *****************************************************************
Monday 15 August 2022  04:53:02 +0000 (0:00:22.741)       0:02:02.345 *********
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'pending' (30 minute timeout) (30 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'pending' (30 minute timeout) (29 retries left).

FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'pending' (30 minute timeout) (28 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'pending' (30 minute timeout) (27 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'pending' (30 minute timeout) (26 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'pending' (30 minute timeout) (25 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'pending' (30 minute timeout) (24 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'pending' (30 minute timeout) (23 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'pending' (30 minute timeout) (22 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'pending' (30 minute timeout) (21 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'pending' (30 minute timeout) (20 retries left).
changed: [localhost] => {"attempts": 12, "changed": true, "cmd": "ibmcloud oc cluster get --cluster itzroks-2700034gbd-ug2zv49t --output json", "delta": "0:00:05.994026", "end": "2022-08-15 05:05:29.063509", "msg": "", "rc": 0, "start": "2022-08-15 05:05:23.069483", "stderr": "", "stderr_lines": [], "stdout": "{\n    \"location\": \"Tokyo\",\n    \"dataCenter\": \"tok02\",\n    \"multiAzCapable\": true,\n    \"vlans\": [],\n    \"worker_vlans\": [],\n    \"workerZones\": [\n        \"tok02\"\n    ],\n    \"id\": \"cbsqftpt0thsm9b20hig\",\n    \"name\": \"itzroks-2700034gbd-ug2zv49t\",\n    \"region\": \"jp-tok\",\n    \"resourceGroup\": \"e1b56d2b248544e3a3549e838c1224b2\",\n    \"resourceGroupName\": \"dteroks\",\n    \"serverURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",\n    \"state\": \"pending\",\n    \"status\": \"The worker nodes are being provisioned and are not available in the cluster\",\n    \"createdDate\": \"2022-08-15T01:57:11+0000\",\n    \"modifiedDate\": \"2022-08-15T01:58:00+0000\",\n    \"workerCount\": 3,\n    \"isPaid\": true,\n    \"masterKubeVersion\": \"4.8.46_1566_openshift\",\n    \"targetVersion\": \"4.8.46_1566_openshift\",\n    \"ingressHostname\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud\",\n    \"ingressSecretName\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000\",\n    \"ingressStatus\": \"\",\n    \"ingressMessage\": \"\",\n    \"logOrg\": \"\",\n    \"logOrgName\": \"\",\n    \"logSpace\": \"\",\n    \"logSpaceName\": \"\",\n    \"apiUser\": \"\",\n    \"monitoringURL\": \"\",\n    \"addons\": [],\n    \"versionEOS\": \"\",\n    \"disableAutoUpdate\": false,\n    \"etcdPort\": \"\",\n    \"masterStatus\": \"Ready\",\n    \"masterStatusModifiedDate\": \"2022-08-15T02:17:06+0000\",\n    \"masterHealth\": \"normal\",\n    \"masterState\": \"deployed\",\n    \"keyProtectEnabled\": false,\n    \"pullSecretApplied\": true,\n    \"crn\": \"crn:v1:bluemix:public:containers-kubernetes:jp-tok:a/ead8711ba2cc4d08a16fd37427f4f01a:cbsqftpt0thsm9b20hig::\",\n    \"privateServiceEndpointEnabled\": false,\n    \"privateServiceEndpointURL\": \"\",\n    \"publicServiceEndpointEnabled\": true,\n    \"publicServiceEndpointURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",\n    \"podSubnet\": \"172.30.0.0/16\",\n    \"podSubnets\": \"\",\n    \"serviceSubnet\": \"172.21.0.0/16\",\n    \"type\": \"openshift\",\n    \"imageSecurityEnabled\": false,\n    \"vpcNativeRoutingEnabled\": false\n}", "stdout_lines": ["{", "    \"location\": \"Tokyo\",", "    \"dataCenter\": \"tok02\",", "    \"multiAzCapable\": true,", "    \"vlans\": [],", "    \"worker_vlans\": [],", "    \"workerZones\": [", "        \"tok02\"", "    ],", "    \"id\": \"cbsqftpt0thsm9b20hig\",", "    \"name\": \"itzroks-2700034gbd-ug2zv49t\",", "    \"region\": \"jp-tok\",", "    \"resourceGroup\": \"e1b56d2b248544e3a3549e838c1224b2\",", "    \"resourceGroupName\": \"dteroks\",", "    \"serverURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",", "    \"state\": \"pending\",", "    \"status\": \"The worker nodes are being provisioned and are not available in the cluster\",", "    \"createdDate\": \"2022-08-15T01:57:11+0000\",", "    \"modifiedDate\": \"2022-08-15T01:58:00+0000\",", "    \"workerCount\": 3,", "    \"isPaid\": true,", "    \"masterKubeVersion\": \"4.8.46_1566_openshift\",", "    \"targetVersion\": \"4.8.46_1566_openshift\",", "    \"ingressHostname\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud\",", "    \"ingressSecretName\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000\",", "    \"ingressStatus\": \"\",", "    \"ingressMessage\": \"\",", "    \"logOrg\": \"\",", "    \"logOrgName\": \"\",", "    \"logSpace\": \"\",", "    \"logSpaceName\": \"\",", "    \"apiUser\": \"\",", "    \"monitoringURL\": \"\",", "    \"addons\": [],", "    \"versionEOS\": \"\",", "    \"disableAutoUpdate\": false,", "    \"etcdPort\": \"\",", "    \"masterStatus\": \"Ready\",", "    \"masterStatusModifiedDate\": \"2022-08-15T02:17:06+0000\",", "    \"masterHealth\": \"normal\",", "    \"masterState\": \"deployed\",", "    \"keyProtectEnabled\": false,", "    \"pullSecretApplied\": true,", "    \"crn\": \"crn:v1:bluemix:public:containers-kubernetes:jp-tok:a/ead8711ba2cc4d08a16fd37427f4f01a:cbsqftpt0thsm9b20hig::\",", "    \"privateServiceEndpointEnabled\": false,", "    \"privateServiceEndpointURL\": \"\",", "    \"publicServiceEndpointEnabled\": true,", "    \"publicServiceEndpointURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",", "    \"podSubnet\": \"172.30.0.0/16\",", "    \"podSubnets\": \"\",", "    \"serviceSubnet\": \"172.21.0.0/16\",", "    \"type\": \"openshift\",", "    \"imageSecurityEnabled\": false,", "    \"vpcNativeRoutingEnabled\": false", "}"]}

TASK [ibm.mas_devops.cp4d_hack_worker_nodes : Wait until the ROKS cluster status is 'normal' again (1 hour timeout)] ***************************************************************
Monday 15 August 2022  05:05:29 +0000 (0:12:26.471)       0:14:28.817 *********
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'normal' again (1 hour timeout) (60 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'normal' again (1 hour timeout) (59 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'normal' again (1 hour timeout) (58 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'normal' again (1 hour timeout) (57 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'normal' again (1 hour timeout) (56 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'normal' again (1 hour timeout) (55 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'normal' again (1 hour timeout) (54 retries left).
FAILED - RETRYING: [localhost]: Wait until the ROKS cluster status is 'normal' again (1 hour timeout) (53 retries left).
changed: [localhost] => {"attempts": 9, "changed": true, "cmd": "ibmcloud oc cluster get --cluster itzroks-2700034gbd-ug2zv49t --output json", "delta": "0:00:11.337020", "end": "2022-08-15 05:14:43.260585", "msg": "", "rc": 0, "start": "2022-08-15 05:14:31.923565", "stderr": "", "stderr_lines": [], "stdout": "{\n    \"location\": \"Tokyo\",\n    \"dataCenter\": \"tok02\",\n    \"multiAzCapable\": true,\n    \"vlans\": [],\n    \"worker_vlans\": [],\n    \"workerZones\": [\n        \"tok02\"\n    ],\n    \"id\": \"cbsqftpt0thsm9b20hig\",\n    \"name\": \"itzroks-2700034gbd-ug2zv49t\",\n    \"region\": \"jp-tok\",\n    \"resourceGroup\": \"e1b56d2b248544e3a3549e838c1224b2\",\n    \"resourceGroupName\": \"dteroks\",\n    \"serverURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",\n    \"state\": \"normal\",\n    \"status\": \"All Workers Normal\",\n    \"createdDate\": \"2022-08-15T01:57:11+0000\",\n    \"modifiedDate\": \"2022-08-15T01:58:00+0000\",\n    \"workerCount\": 3,\n    \"isPaid\": true,\n    \"masterKubeVersion\": \"4.8.46_1566_openshift\",\n    \"targetVersion\": \"4.8.46_1566_openshift\",\n    \"ingressHostname\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud\",\n    \"ingressSecretName\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000\",\n    \"ingressStatus\": \"\",\n    \"ingressMessage\": \"\",\n    \"logOrg\": \"\",\n    \"logOrgName\": \"\",\n    \"logSpace\": \"\",\n    \"logSpaceName\": \"\",\n    \"apiUser\": \"\",\n    \"monitoringURL\": \"\",\n    \"addons\": [],\n    \"versionEOS\": \"\",\n    \"disableAutoUpdate\": false,\n    \"etcdPort\": \"\",\n    \"masterStatus\": \"Ready\",\n    \"masterStatusModifiedDate\": \"2022-08-15T02:17:06+0000\",\n    \"masterHealth\": \"normal\",\n    \"masterState\": \"deployed\",\n    \"keyProtectEnabled\": false,\n    \"pullSecretApplied\": true,\n    \"crn\": \"crn:v1:bluemix:public:containers-kubernetes:jp-tok:a/ead8711ba2cc4d08a16fd37427f4f01a:cbsqftpt0thsm9b20hig::\",\n    \"privateServiceEndpointEnabled\": false,\n    \"privateServiceEndpointURL\": \"\",\n    \"publicServiceEndpointEnabled\": true,\n    \"publicServiceEndpointURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",\n    \"podSubnet\": \"172.30.0.0/16\",\n    \"podSubnets\": \"\",\n    \"serviceSubnet\": \"172.21.0.0/16\",\n    \"type\": \"openshift\",\n    \"imageSecurityEnabled\": false,\n    \"vpcNativeRoutingEnabled\": false\n}", "stdout_lines": ["{", "    \"location\": \"Tokyo\",", "    \"dataCenter\": \"tok02\",", "    \"multiAzCapable\": true,", "    \"vlans\": [],", "    \"worker_vlans\": [],", "    \"workerZones\": [", "        \"tok02\"", "    ],", "    \"id\": \"cbsqftpt0thsm9b20hig\",", "    \"name\": \"itzroks-2700034gbd-ug2zv49t\",", "    \"region\": \"jp-tok\",", "    \"resourceGroup\": \"e1b56d2b248544e3a3549e838c1224b2\",", "    \"resourceGroupName\": \"dteroks\",", "    \"serverURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",", "    \"state\": \"normal\",", "    \"status\": \"All Workers Normal\",", "    \"createdDate\": \"2022-08-15T01:57:11+0000\",", "    \"modifiedDate\": \"2022-08-15T01:58:00+0000\",", "    \"workerCount\": 3,", "    \"isPaid\": true,", "    \"masterKubeVersion\": \"4.8.46_1566_openshift\",", "    \"targetVersion\": \"4.8.46_1566_openshift\",", "    \"ingressHostname\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud\",", "    \"ingressSecretName\": \"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000\",", "    \"ingressStatus\": \"\",", "    \"ingressMessage\": \"\",", "    \"logOrg\": \"\",", "    \"logOrgName\": \"\",", "    \"logSpace\": \"\",", "    \"logSpaceName\": \"\",", "    \"apiUser\": \"\",", "    \"monitoringURL\": \"\",", "    \"addons\": [],", "    \"versionEOS\": \"\",", "    \"disableAutoUpdate\": false,", "    \"etcdPort\": \"\",", "    \"masterStatus\": \"Ready\",", "    \"masterStatusModifiedDate\": \"2022-08-15T02:17:06+0000\",", "    \"masterHealth\": \"normal\",", "    \"masterState\": \"deployed\",", "    \"keyProtectEnabled\": false,", "    \"pullSecretApplied\": true,", "    \"crn\": \"crn:v1:bluemix:public:containers-kubernetes:jp-tok:a/ead8711ba2cc4d08a16fd37427f4f01a:cbsqftpt0thsm9b20hig::\",", "    \"privateServiceEndpointEnabled\": false,", "    \"privateServiceEndpointURL\": \"\",", "    \"publicServiceEndpointEnabled\": true,", "    \"publicServiceEndpointURL\": \"https://c115-e.jp-tok.containers.cloud.ibm.com:32726\",", "    \"podSubnet\": \"172.30.0.0/16\",", "    \"podSubnets\": \"\",", "    \"serviceSubnet\": \"172.21.0.0/16\",", "    \"type\": \"openshift\",", "    \"imageSecurityEnabled\": false,", "    \"vpcNativeRoutingEnabled\": false", "}"]}

TASK [ibm.mas_devops.ocp_roks_upgrade_registry_storage : Check that IBMCloud API Key was provided] *********************************************************************************
Monday 15 August 2022  05:14:43 +0000 (0:09:14.201)       0:23:43.018 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.ocp_roks_upgrade_registry_storage : Lookup openshift-image-registry PVC] **************************************************************************************
Monday 15 August 2022  05:14:43 +0000 (0:00:00.041)       0:23:43.060 *********
ok: [localhost] => {"api_found": true, "attempts": 1, "changed": false, "resources": [{"apiVersion": "v1", "kind": "PersistentVolumeClaim", "metadata": {"annotations": {"ibm.io/provisioning-status": "{\"status\":\"complete\",\"time\":\"2022-08-15T02:33:20Z\",\"attempt\":2,\"retry\":false,\"pluginid\":\"ibm-file-plugin-1660530508\",\"pvcid\":\"e16f2f7e-1037-4596-a370-c41674b0f7b5\"}", "imageregistry.openshift.io": "true", "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"PersistentVolumeClaim\",\"metadata\":{\"annotations\":{\"imageregistry.openshift.io\":\"true\",\"volume.beta.kubernetes.io/storage-provisioner\":\"ibm.io/ibmc-file\"},\"finalizers\":[\"kubernetes.io/pvc-protection\"],\"labels\":{\"billingType\":\"hourly\"},\"name\":\"image-registry-storage\",\"namespace\":\"openshift-image-registry\"},\"spec\":{\"accessModes\":[\"ReadWriteMany\"],\"resources\":{\"requests\":{\"storage\":\"100Gi\"}},\"storageClassName\":\"ibmc-file-gold\",\"volumeMode\":\"Filesystem\"}}", "pv.kubernetes.io/bind-completed": "yes", "pv.kubernetes.io/bound-by-controller": "yes", "volume.beta.kubernetes.io/storage-provisioner": "ibm.io/ibmc-file"}, "creationTimestamp": "2022-08-15T02:14:21Z", "finalizers": ["kubernetes.io/pvc-protection"], "labels": {"billingType": "hourly", "region": "jp-tok", "zone": "tok02"}, "managedFields": [{"apiVersion": "v1", "fieldsType": "FieldsV1", "fieldsV1": {"f:metadata": {"f:annotations": {".": {}, "f:imageregistry.openshift.io": {}, "f:kubectl.kubernetes.io/last-applied-configuration": {}, "f:volume.beta.kubernetes.io/storage-provisioner": {}}, "f:finalizers": {".": {}, "v:\"kubernetes.io/pvc-protection\"": {}}, "f:labels": {".": {}, "f:billingType": {}}}, "f:spec": {"f:accessModes": {}, "f:resources": {"f:requests": {".": {}, "f:storage": {}}}, "f:storageClassName": {}, "f:volumeMode": {}}}, "manager": "OpenAPI-Generator", "operation": "Update", "time": "2022-08-15T02:14:21Z"}, {"apiVersion": "v1", "fieldsType": "FieldsV1", "fieldsV1": {"f:metadata": {"f:annotations": {"f:ibm.io/provisioning-status": {}}, "f:labels": {"f:region": {}, "f:zone": {}}}}, "manager": "armada-storage-file-plugin", "operation": "Update", "time": "2022-08-15T02:28:28Z"}, {"apiVersion": "v1", "fieldsType": "FieldsV1", "fieldsV1": {"f:metadata": {"f:annotations": {"f:pv.kubernetes.io/bind-completed": {}, "f:pv.kubernetes.io/bound-by-controller": {}}}, "f:spec": {"f:volumeName": {}}, "f:status": {"f:accessModes": {}, "f:capacity": {".": {}, "f:storage": {}}, "f:phase": {}}}, "manager": "kube-controller-manager", "operation": "Update", "time": "2022-08-15T02:33:21Z"}], "name": "image-registry-storage", "namespace": "openshift-image-registry", "resourceVersion": "19324", "uid": "e16f2f7e-1037-4596-a370-c41674b0f7b5"}, "spec": {"accessModes": ["ReadWriteMany"], "resources": {"requests": {"storage": "100Gi"}}, "storageClassName": "ibmc-file-gold", "volumeMode": "Filesystem", "volumeName": "pvc-e16f2f7e-1037-4596-a370-c41674b0f7b5"}, "status": {"accessModes": ["ReadWriteMany"], "capacity": {"storage": "100Gi"}, "phase": "Bound"}}]}

TASK [ibm.mas_devops.ocp_roks_upgrade_registry_storage : Set PVC ID] ***************************************************************************************************************
Monday 15 August 2022  05:14:44 +0000 (0:00:01.270)       0:23:44.330 *********
ok: [localhost] => {"ansible_facts": {"registry_pvc_id": "pvc-e16f2f7e-1037-4596-a370-c41674b0f7b5"}, "changed": false}

TASK [ibm.mas_devops.ocp_roks_upgrade_registry_storage : Lookup Persistent Volume] *************************************************************************************************
Monday 15 August 2022  05:14:44 +0000 (0:00:00.033)       0:23:44.364 *********
ok: [localhost] => {"api_found": true, "attempts": 1, "changed": false, "resources": [{"apiVersion": "v1", "kind": "PersistentVolume", "metadata": {"annotations": {"ibmFileProvisionerIdentity": "21151ff1-16f0-4d71-82fd-6a9be65fc756", "pv.kubernetes.io/provisioned-by": "ibm.io/ibmc-file"}, "creationTimestamp": "2022-08-15T02:33:20Z", "finalizers": ["kubernetes.io/pv-protection"], "labels": {"CapacityGb": "100", "Datacenter": "tok02", "Iops": "10", "StorageType": "ENDURANCE", "Username": "IBM02SEV2112072_109818", "billingType": "hourly", "failure-domain.beta.kubernetes.io/region": "jp-tok", "failure-domain.beta.kubernetes.io/zone": "tok02", "path": "IBM02SEV2112072_109818data01", "server": "fsf-tok0201a-fz.service.softlayer.com", "volumeId": "393613680"}, "managedFields": [{"apiVersion": "v1", "fieldsType": "FieldsV1", "fieldsV1": {"f:metadata": {"f:annotations": {".": {}, "f:ibmFileProvisionerIdentity": {}, "f:pv.kubernetes.io/provisioned-by": {}}, "f:labels": {".": {}, "f:CapacityGb": {}, "f:Datacenter": {}, "f:Iops": {}, "f:StorageType": {}, "f:Username": {}, "f:billingType": {}, "f:failure-domain.beta.kubernetes.io/region": {}, "f:failure-domain.beta.kubernetes.io/zone": {}, "f:path": {}, "f:server": {}, "f:volumeId": {}}}, "f:spec": {"f:accessModes": {}, "f:capacity": {".": {}, "f:storage": {}}, "f:claimRef": {".": {}, "f:apiVersion": {}, "f:kind": {}, "f:name": {}, "f:namespace": {}, "f:resourceVersion": {}, "f:uid": {}}, "f:nfs": {".": {}, "f:path": {}, "f:server": {}}, "f:persistentVolumeReclaimPolicy": {}, "f:storageClassName": {}, "f:volumeMode": {}}}, "manager": "armada-storage-file-plugin", "operation": "Update", "time": "2022-08-15T02:33:20Z"}, {"apiVersion": "v1", "fieldsType": "FieldsV1", "fieldsV1": {"f:status": {"f:phase": {}}}, "manager": "kube-controller-manager", "operation": "Update", "time": "2022-08-15T02:33:21Z"}], "name": "pvc-e16f2f7e-1037-4596-a370-c41674b0f7b5", "resourceVersion": "19321", "uid": "28fe772f-6ec8-4082-8db9-d92b980e9fed"}, "spec": {"accessModes": ["ReadWriteMany"], "capacity": {"storage": "100Gi"}, "claimRef": {"apiVersion": "v1", "kind": "PersistentVolumeClaim", "name": "image-registry-storage", "namespace": "openshift-image-registry", "resourceVersion": "15944", "uid": "e16f2f7e-1037-4596-a370-c41674b0f7b5"}, "nfs": {"path": "/IBM02SEV2112072_109818/data01", "server": "fsf-tok0201a-fz.service.softlayer.com"}, "nodeAffinity": {"required": {"nodeSelectorTerms": [{"matchExpressions": [{"key": "failure-domain.beta.kubernetes.io/zone", "operator": "In", "values": ["tok02"]}, {"key": "failure-domain.beta.kubernetes.io/region", "operator": "In", "values": ["jp-tok"]}]}]}}, "persistentVolumeReclaimPolicy": "Delete", "storageClassName": "ibmc-file-gold", "volumeMode": "Filesystem"}, "status": {"phase": "Bound"}}]}

TASK [ibm.mas_devops.ocp_roks_upgrade_registry_storage : Check that we could find the PV] ******************************************************************************************
Monday 15 August 2022  05:14:45 +0000 (0:00:01.152)       0:23:45.516 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [ibm.mas_devops.ocp_roks_upgrade_registry_storage : Set Volume ID] ************************************************************************************************************
Monday 15 August 2022  05:14:45 +0000 (0:00:00.043)       0:23:45.560 *********
ok: [localhost] => {"ansible_facts": {"registry_volume_id": "393613680"}, "changed": false}

TASK [ibm.mas_devops.ocp_roks_upgrade_registry_storage : Debug Lookups] ************************************************************************************************************
Monday 15 August 2022  05:14:45 +0000 (0:00:00.037)       0:23:45.598 *********
ok: [localhost] => {
    "msg": [
        "Registry PVC ID ........................ pvc-e16f2f7e-1037-4596-a370-c41674b0f7b5",
        "Registry IBMCloud Volume ID ............ 393613680"
    ]
}

TASK [ibm.mas_devops.ocp_roks_upgrade_registry_storage : login-roks : Login to IBM Cloud] ******************************************************************************************
Monday 15 August 2022  05:14:45 +0000 (0:00:00.037)       0:23:45.635 *********
changed: [localhost] => {"attempts": 1, "changed": true, "cmd": "ibmcloud login --apikey \"JhNR7aJ_FqAoRtywDjj4Eg9I97Xb8-TCgIDAkq1pskkH\" -q --no-region\n", "delta": "0:00:17.606344", "end": "2022-08-15 05:15:03.809413", "msg": "", "rc": 0, "start": "2022-08-15 05:14:46.203069", "stderr": "", "stderr_lines": [], "stdout": "                      \nAPI endpoint:      https://cloud.ibm.com   \nRegion:               \nUser:              AHA03926@jp.ibm.com   \nAccount:           ITZ - V2 (ead8711ba2cc4d08a16fd37427f4f01a) <-> 2112072   \nResource group:    No resource group targeted, use 'ibmcloud target -g RESOURCE_GROUP'   \nCF API endpoint:      \nOrg:                  \nSpace:                ", "stdout_lines": ["                      ", "API endpoint:      https://cloud.ibm.com   ", "Region:               ", "User:              AHA03926@jp.ibm.com   ", "Account:           ITZ - V2 (ead8711ba2cc4d08a16fd37427f4f01a) <-> 2112072   ", "Resource group:    No resource group targeted, use 'ibmcloud target -g RESOURCE_GROUP'   ", "CF API endpoint:      ", "Org:
        ", "Space:                "]}

TASK [ibm.mas_devops.ocp_roks_upgrade_registry_storage : Increase cluster image registry storage] **********************************************************************************
Monday 15 August 2022  05:15:03 +0000 (0:00:17.944)       0:24:03.580 *********
changed: [localhost] => {"changed": true, "cmd": ["bash", "/opt/app-root/lib64/python3.9/site-packages/ansible_collections/ibm/mas_devops/roles/ocp_roks_upgrade_registry_storage/files/configImgRegStorage.sh", "393613680", "400"], "delta": "0:06:20.241869", "end": "2022-08-15 05:21:24.376858", "msg": "", "rc": 0, "start": "2022-08-15 05:15:04.134989", "stderr": "Failed to get details of volume 393613680.\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)\nFAILED\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)\n\nFailed to get details of volume 393613680.\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)\nFailed to get details of volume 393613680.\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)\nFailed to get details of volume 393613680.\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)\nFailed to get details of volume 393613680.\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)\nFailed to get details of volume 393613680.\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)\nFailed to get details of volume 393613680.\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)\nFailed to get details of volume 393613680.\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)\nFailed to get details of volume 393613680.\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)\nFailed to get details of volume 393613680.\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)\nFailed to get details of volume 393613680.\nSoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "stderr_lines": ["Failed to get details of volume 393613680.", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "FAILED", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "", "Failed to get details of volume 393613680.", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "Failed to get details of volume 393613680.", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "Failed to get details of volume 393613680.", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "Failed to get details of volume 393613680.", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "Failed to get details of volume 393613680.", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "Failed to get details of volume 393613680.", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "Failed to get details of volume 393613680.", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "Failed to get details of volume 393613680.", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "Failed to get details of volume 393613680.", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)", "Failed to get details of volume 393613680.", "SoftLayer_Exception_ObjectNotFound: Unable to find object with id of '393613680'. (HTTP 404)"], "stdout": "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 1)\nLooks like it is taking time to reflect the updated size for Image Registry volume (check count 2)\nLooks like it is taking time to reflect the updated size for Image Registry volume (check count 3)\nLooks like it is taking time to reflect the updated size for Image Registry volume (check count 4)\nLooks like it is taking time to reflect the updated size for Image Registry volume (check count 5)\nLooks like it is taking time to reflect the updated size for Image Registry volume (check count 6)\nLooks like it is taking time to reflect the updated size for Image Registry volume (check count 7)\nLooks like it is taking time to reflect the updated size for Image Registry volume (check count 8)\nLooks like it is taking time to reflect the updated size for Image Registry volume (check count 9)\nLooks like it is taking time to reflect the updated size for Image Registry volume (check count 10)\nBefore proceeding verify that the Image Registy volume 393613680 size has been modified to the desired 400 GB", "stdout_lines": ["Looks like it is taking time to reflect the updated size for Image Registry volume (check count 1)", "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 2)", "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 3)", "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 4)", "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 5)", "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 6)", "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 7)", "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 8)", "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 9)", "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 10)", "Before proceeding verify that the Image Registy volume 393613680 size has been modified to the desired 400 GB"]}

TASK [ibm.mas_devops.ocp_roks_upgrade_registry_storage : Debug script output] ******************************************************************************************************
Monday 15 August 2022  05:21:24 +0000 (0:06:20.563)       0:30:24.144 *********
ok: [localhost] => {
    "msg": [
        "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 1)",
        "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 2)",
        "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 3)",
        "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 4)",
        "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 5)",
        "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 6)",
        "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 7)",
        "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 8)",
        "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 9)",
        "Looks like it is taking time to reflect the updated size for Image Registry volume (check count 10)",
        "Before proceeding verify that the Image Registy volume 393613680 size has been modified to the desired 400 GB"
    ]
}

TASK [ibm.mas_devops.ocp_verify : Check if Red Hat Catalog is ready] ***************************************************************************************************************
Monday 15 August 2022  05:21:24 +0000 (0:00:00.045)       0:30:24.189 *********
ok: [localhost] => {"api_found": true, "attempts": 1, "changed": false, "resources": [{"apiVersion": "operators.coreos.com/v1alpha1", "kind": "CatalogSource", "metadata": {"annotations": {"operatorframework.io/managed-by": "marketplace-operator", "operatorframework.io/priorityclass": "system-cluster-critical", "target.workload.openshift.io/management": "{\"effect\": \"PreferredDuringScheduling\"}"}, "creationTimestamp": "2022-08-15T02:31:35Z", "generation": 1, "managedFields": [{"apiVersion": "operators.coreos.com/v1alpha1", "fieldsType": "FieldsV1", "fieldsV1": {"f:metadata": {"f:annotations": {".": {}, "f:operatorframework.io/managed-by": {}, "f:operatorframework.io/priorityclass": {}, "f:target.workload.openshift.io/management": {}}}, "f:spec": {".": {}, "f:displayName": {}, "f:icon": {".": {}, "f:base64data": {}, "f:mediatype": {}}, "f:image": {}, "f:priority": {}, "f:publisher": {}, "f:sourceType": {}, "f:updateStrategy": {".": {}, "f:registryPoll": {".": {}, "f:interval": {}}}}}, "manager": "marketplace-operator", "operation": "Update", "time": "2022-08-15T02:31:35Z"}, {"apiVersion": "operators.coreos.com/v1alpha1", "fieldsType": "FieldsV1", "fieldsV1": {"f:status": {".": {}, "f:connectionState": {".": {}, "f:address": {}, "f:lastConnect": {}, "f:lastObservedState": {}}, "f:latestImageRegistryPoll": {}, "f:registryService": {".": {}, "f:createdAt": {}, "f:port": {}, "f:protocol": {}, "f:serviceName": {}, "f:serviceNamespace": {}}}}, "manager": "catalog", "operation": "Update", "time": "2022-08-15T02:41:50Z"}], "name": "redhat-operators", "namespace": "openshift-marketplace", "resourceVersion": "88194", "uid": "4cbbecf4-8f8e-44f1-a13c-d6dabc323585"}, "spec": {"displayName": "Red Hat Operators", "icon": {"base64data": "", "mediatype": ""}, "image": "registry.redhat.io/redhat/redhat-operator-index:v4.8", "priority": -100, "publisher": "Red Hat", "sourceType": "grpc", "updateStrategy": {"registryPoll": {"interval": "10m0s"}}}, "status": {"connectionState": {"address": "redhat-operators.openshift-marketplace.svc:50051", "lastConnect": "2022-08-15T05:20:17Z", "lastObservedState": "READY"}, "latestImageRegistryPoll": "2022-08-15T05:18:46Z", "registryService": {"createdAt": "2022-08-15T02:31:36Z", "port": "50051", "protocol": "grpc", "serviceName": "redhat-operators", "serviceNamespace": "openshift-marketplace"}}}]}

TASK [ibm.mas_devops.ocp_verify : Lookup for router-certs-default secret] **********************************************************************************************************
Monday 15 August 2022  05:21:25 +0000 (0:00:01.417)       0:30:25.607 *********
ok: [localhost] => {"censored": "the output has been hidden due to the fact that 'no_log: true' was specified for this result", "changed": false}

TASK [ibm.mas_devops.ocp_verify : Record that we have found the router default cert secret] ****************************************************************************************
Monday 15 August 2022  05:21:27 +0000 (0:00:01.186)       0:30:26.793 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_verify : Get cluster subdomain] ***************************************************************************************************************************
Monday 15 August 2022  05:21:27 +0000 (0:00:00.029)       0:30:26.823 *********
ok: [localhost] => {"api_found": true, "changed": false, "resources": [{"apiVersion": "config.openshift.io/v1", "kind": "Ingress", "metadata": {"annotations": {"kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"config.openshift.io/v1\",\"kind\":\"Ingress\",\"metadata\":{\"annotations\":{},\"creationTimestamp\":null,\"name\":\"cluster\"},\"spec\":{\"domain\":\"itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud\"},\"status\":{}}\n"}, "creationTimestamp": "2022-08-15T02:04:15Z", "generation": 1, "managedFields": [{"apiVersion": "config.openshift.io/v1", "fieldsType": "FieldsV1", "fieldsV1": {"f:metadata": {"f:annotations": {".": {}, "f:kubectl.kubernetes.io/last-applied-configuration": {}}}, "f:spec": {".": {}, "f:domain": {}}}, "manager": "kubectl-client-side-apply", "operation": "Update", "time": "2022-08-15T02:04:15Z"}], "name": "cluster", "resourceVersion": "726", "uid": "e7bd1b0b-c67e-4de8-a7f0-a695bba8dba6"}, "spec": {"domain": "itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud"}}]}

TASK [ibm.mas_devops.ocp_verify : Lookup for cluster ingress secret] ***************************************************************************************************************
Monday 15 August 2022  05:21:28 +0000 (0:00:01.377)       0:30:28.200 *********
ok: [localhost] => {"censored": "the output has been hidden due to the fact that 'no_log: true' was specified for this result", "changed": false}

TASK [ibm.mas_devops.ocp_verify : Record that we have found the cluster ingress cert secret] ***************************************************************************************
Monday 15 August 2022  05:21:29 +0000 (0:00:01.332)       0:30:29.533 *********
ok: [localhost] => {"ansible_facts": {"found_cluster_ingress_secret": true}, "changed": false}

TASK [ibm.mas_devops.ocp_verify : Get cluster subdomain] ***************************************************************************************************************************
Monday 15 August 2022  05:21:29 +0000 (0:00:00.039)       0:30:29.572 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_verify : Record that we have found the cluster primary cert secret] ***************************************************************************************
Monday 15 August 2022  05:21:29 +0000 (0:00:00.028)       0:30:29.600 *********
skipping: [localhost] => {"changed": false, "skip_reason": "Conditional result was False"}

TASK [ibm.mas_devops.ocp_verify : Fail if one of the cluster required secrets does not exist] **************************************************************************************
Monday 15 August 2022  05:21:29 +0000 (0:00:00.028)       0:30:29.628 *********
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *************************************************************************************************************************************************************************
localhost                  : ok=55   changed=14   unreachable=0    failed=0    skipped=16   rescued=0    ignored=0

Monday 15 August 2022  05:21:30 +0000 (0:00:00.102)       0:30:29.731 *********
===============================================================================
ibm.mas_devops.cp4d_hack_worker_nodes : Wait until the ROKS cluster status is 'pending' (30 minute timeout) --------------------------------------------------------------- 746.47s
ibm.mas_devops.cp4d_hack_worker_nodes : Wait until the ROKS cluster status is 'normal' again (1 hour timeout) ------------------------------------------------------------- 554.20s
ibm.mas_devops.ocp_roks_upgrade_registry_storage : Increase cluster image registry storage -------------------------------------------------------------------------------- 380.56s
ibm.mas_devops.cp4d_hack_worker_nodes : Reload all worker nodes ------------------------------------------------------------------------------------------------------------ 22.74s
ibm.mas_devops.ocp_provision : roks : Check if cluster already exists ------------------------------------------------------------------------------------------------------ 22.18s
ibm.mas_devops.ocp_provision : roks : Login to IBM Cloud ------------------------------------------------------------------------------------------------------------------- 21.65s
ibm.mas_devops.ocp_roks_upgrade_registry_storage : login-roks : Login to IBM Cloud ----------------------------------------------------------------------------------------- 17.94s
ibm.mas_devops.cp4d_hack_worker_nodes : Retrieve existing pull-secret content ----------------------------------------------------------------------------------------------- 8.58s
ibm.mas_devops.ocp_login : login : Login to OCP ----------------------------------------------------------------------------------------------------------------------------- 8.43s
ibm.mas_devops.cp4d_hack_worker_nodes : Get cluster worker node list -------------------------------------------------------------------------------------------------------- 7.70s
ibm.mas_devops.ocp_provision : roks : Create resource group if it does not exist -------------------------------------------------------------------------------------------- 6.92s
ibm.mas_devops.ocp_provision : roks : Switch to chosen resource group ------------------------------------------------------------------------------------------------------- 6.75s
ibm.mas_devops.ocp_provision : roks : Check resource group exists ----------------------------------------------------------------------------------------------------------- 6.65s
ibm.mas_devops.ocp_provision : roks : Wait until the Roks cluster is deployed ----------------------------------------------------------------------------------------------- 6.43s
ibm.mas_devops.cp4d_hack_worker_nodes : Update new pull-secret -------------------------------------------------------------------------------------------------------------- 1.63s
ibm.mas_devops.ocp_verify : Check if Red Hat Catalog is ready --------------------------------------------------------------------------------------------------------------- 1.42s
ibm.mas_devops.ocp_verify : Get cluster subdomain --------------------------------------------------------------------------------------------------------------------------- 1.38s
ibm.mas_devops.ocp_verify : Lookup for cluster ingress secret --------------------------------------------------------------------------------------------------------------- 1.33s
ibm.mas_devops.ocp_roks_upgrade_registry_storage : Lookup openshift-image-registry PVC -------------------------------------------------------------------------------------- 1.27s
ibm.mas_devops.ocp_verify : Lookup for router-certs-default secret ---------------------------------------------------------------------------------------------------------- 1.19s
Gathering Facts ------------------------------------------------------------------------------------------------------------------------------------------------------------- 1.16s
ibm.mas_devops.ocp_roks_upgrade_registry_storage : Lookup Persistent Volume ------------------------------------------------------------------------------------------------- 1.15s
ibm.mas_devops.ocp_verify : Fail if one of the cluster required secrets does not exist -------------------------------------------------------------------------------------- 0.10s
ibm.mas_devops.ocp_provision : include_tasks -------------------------------------------------------------------------------------------------------------------------------- 0.09s
ibm.mas_devops.ocp_provision : Fail if no OCP version is provided ----------------------------------------------------------------------------------------------------------- 0.07s
ibm.mas_devops.ocp_provision : roks : status of GPU worker node provisioning ------------------------------------------------------------------------------------------------ 0.06s
ibm.mas_devops.ocp_provision : roks : Debug current cluster status ---------------------------------------------------------------------------------------------------------- 0.05s
ibm.mas_devops.cp4d_hack_worker_nodes : Get the original cred secrets ------------------------------------------------------------------------------------------------------- 0.05s
ibm.mas_devops.ocp_provision : roks : Debug information --------------------------------------------------------------------------------------------------------------------- 0.05s
ibm.mas_devops.ansible_version_check : Verify minimum Ansible version is 2.10.3 --------------------------------------------------------------------------------------------- 0.05s
ibm.mas_devops.cp4d_hack_worker_nodes : Debug change state ------------------------------------------------------------------------------------------------------------------ 0.05s
ibm.mas_devops.ocp_roks_upgrade_registry_storage : Debug script output ------------------------------------------------------------------------------------------------------ 0.05s
ibm.mas_devops.ocp_roks_upgrade_registry_storage : Check that we could find the PV ------------------------------------------------------------------------------------------ 0.04s
ibm.mas_devops.ocp_provision : Fail if cluster type is not supported -------------------------------------------------------------------------------------------------------- 0.04s
Check for required environment variables ------------------------------------------------------------------------------------------------------------------------------------ 0.04s
ibm.mas_devops.ocp_roks_upgrade_registry_storage : Check that IBMCloud API Key was provided --------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.ocp_provision : Fail if cluster name & type is not provided -------------------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.ocp_login : include_tasks ------------------------------------------------------------------------------------------------------------------------------------ 0.04s
ibm.mas_devops.cp4d_hack_worker_nodes : Set new secret content -------------------------------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.ocp_login : Fail if type is not provided --------------------------------------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.ocp_provision : roks : Debug worker pool status -------------------------------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.ocp_provision : roks : Debug current resource group status --------------------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.ocp_verify : Record that we have found the cluster ingress cert secret --------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.cp4d_hack_worker_nodes : Fail if cpd_entitlement_key has not been provided ----------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.ocp_login : login : Fail if ocp_server or ocp_token are not provided ----------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.ocp_roks_upgrade_registry_storage : Set Volume ID ------------------------------------------------------------------------------------------------------------ 0.04s
ibm.mas_devops.ocp_login : login : Debug OCP login -------------------------------------------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.ocp_roks_upgrade_registry_storage : Debug Lookups ------------------------------------------------------------------------------------------------------------ 0.04s
ibm.mas_devops.ocp_provision : roks: Debug final cluster state -------------------------------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.cp4d_hack_worker_nodes : Fail if cluster_type is not set to roks --------------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.cp4d_hack_worker_nodes : Combine new secret content ---------------------------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.cp4d_hack_worker_nodes : Fail if cluster_name has not been provided ------------------------------------------------------------------------------------------ 0.04s
ibm.mas_devops.cp4d_hack_worker_nodes : Get the dockerconfigjson info ------------------------------------------------------------------------------------------------------- 0.04s
ibm.mas_devops.cp4d_hack_worker_nodes : Debug information ------------------------------------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.cp4d_hack_worker_nodes : Fail if ibmcloud_apikey has not been provided --------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_provision : roks : Add a GPU worker pool ----------------------------------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_roks_upgrade_registry_storage : Set PVC ID --------------------------------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_provision : roks : Fail if ibmcloud_apikey is not provided ----------------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_provision : roks : Add GPU worker pool to zone ----------------------------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_provision : roks : Debug Information for GPU worker ------------------------------------------------------------------------------------------------------ 0.03s
ibm.mas_devops.ocp_provision : roks : Get GPU worker pool zone information -------------------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_login : debug -------------------------------------------------------------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_verify : Record that we have found the router default cert secret ---------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_provision : roks : Check if GPU worker pool already exists ----------------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_provision : Backwards compatability for 'quickburn' cluster type ----------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_verify : Record that we have found the cluster primary cert secret --------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_verify : Get cluster subdomain --------------------------------------------------------------------------------------------------------------------------- 0.03s
ibm.mas_devops.ocp_login : include_tasks ------------------------------------------------------------------------------------------------------------------------------------ 0.03s
ibm.mas_devops.ocp_provision : roks : Lookup VLANs automatically (ibmcloud ks vlan ls --zone tok05 --output json) ----------------------------------------------------------- 0.02s
ibm.mas_devops.ocp_provision : roks : Configure Private and Public Vlans ---------------------------------------------------------------------------------------------------- 0.02s
ibm.mas_devops.ocp_provision : roks : Create IBM Cloud ROKS cluster --------------------------------------------------------------------------------------------------------- 0.02s

IBM Cloud ROKS cluster is ready to use
Connected to OCP cluster: https://console-openshift-console.itzroks-2700034gbd-ug2zv4-6ccd7f378ae819553d37d5f2ee142bd6-0000.jp-tok.containers.appdomain.cloud
[ibmmas/cli:2.1.2]local$
```

</details>

### 前項
- [ 01 事前準備 ](../01_prereqs/index.md)


### 次項
- [ 03_MASのインストール ](../03_masinstall/index.md)
