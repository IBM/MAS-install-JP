## 8.Maximo Application Suite のインストール

下記手順をibm-masディレクトリ内で実行します。oc loginを実行します。


### 1. Entitlement keyのSecret作成
```bash
ENTITLEMENT_KEY=eyxxxx....
# ENTITLEMENT_KEYは https://myibm.ibm.com/products-services/containerlibrary から取得
```

### 2. ./install-mas.shを実行

* もしインストール途中で、ENTITLEMENT_KEYの入力が求められる場合、1.で設定した値を再度入力します。
* instanceIDについては [参考.用語解説](../99_yougo/index.md) 参照
* 途中、Superuser account ready  [OK] になるまでに15分、Admin dashboard ready [OK]で5分程度かかります。

```bash
./install-mas.sh

LICENSE INFORMATION

The Programs listed below are licensed under the following
License Information terms and conditions in addition to the
Program license terms previously agreed to by Client and
IBM. If Client does not have previously agreed to license
terms in effect for the Program, the International Program
License Agreement (i125-3301-15) applies.

Program Name (Program Number):
IBM Maximo Application Suite v8.7 (5737-M66)

The following standard terms apply to Licensee's use of the
Program.

Press Enter to continue viewing the license agreement, or
enter "1" to accept the agreement, "2" to decline it, "3"
to print it, "4" to read non-IBM terms, or "99" to go back
to the previous screen.
1

License information is available in the license/ directory

================================================================================
Maximo Application Suite Installer
================================================================================
 - Instance ID: > mas87infra
MAS version:                8.7.0
Truststore Manager version: 1.2.2
Instance ID:                mas87infra
Namespace:                  mas-mas87infra-core
ICR cp content:             cp.icr.io/cp
ICR cpopen content:         icr.io/cpopen

Domain: (Default: mas87infra.xxxxx-1c23480da6d8f4e560816232d3da3f63-xxxx.jp-tok.containers.appdomain.cloud) >
Using domain: mas87infra.xxxxx-1c23480da6d8f4e560816232d3da3f63-xxxx.jp-tok.containers.appdomain.cloud


Switching to mas-mas87infra-core namespace
--------------------------------------------------------------------------------


1. Installing ibm-truststore-mgr operator
--------------------------------------------------------------------------------
Warning: oc apply should be used on resource created by either oc create --save-config or oc apply
customresourcedefinition.apiextensions.k8s.io/truststores.truststore-mgr.ibm.com configured
role.rbac.authorization.k8s.io/ibm-truststore-mgr-manager-role created
rolebinding.rbac.authorization.k8s.io/ibm-truststore-mgr-manager-rolebinding created
serviceaccount/ibm-truststore-mgr-operator created
role.rbac.authorization.k8s.io/ibm-truststore-mgr-worker created
rolebinding.rbac.authorization.k8s.io/ibm-truststore-mgr-worker created
serviceaccount/ibm-truststore-mgr-worker created
deployment.apps/ibm-truststore-mgr-controller-manager created

1.1 Check and Install IBM operator catalog and common services catalog if they don't exist.
--------------------------------------------------------------------------------
IBM common service already exists, skipping.
2. Installing ibm-mas operator
--------------------------------------------------------------------------------
Warning: oc apply should be used on resource created by either oc create --save-config or oc apply
namespace/mas-mas87infra-core configured
customresourcedefinition.apiextensions.k8s.io/appconnects.addons.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/humai.addons.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/mviedges.addons.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/bascfgs.config.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/idpcfgs.config.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/jdbccfgs.config.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/kafkacfgs.config.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/mongocfgs.config.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/objectstoragecfgs.config.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/pushnotificationcfgs.config.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/scimcfgs.config.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/slscfgs.config.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/smtpcfgs.config.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/watsonstudiocfgs.config.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/suites.core.mas.ibm.com created
customresourcedefinition.apiextensions.k8s.io/workspaces.core.mas.ibm.com created
clusterrole.rbac.authorization.k8s.io/ibm-mas-operator:mas87infra created
role.rbac.authorization.k8s.io/ibm-mas-operator created
rolebinding.rbac.authorization.k8s.io/ibm-mas-config-editor created
role.rbac.authorization.k8s.io/ibm-mas-config-editor created
serviceaccount/ibm-mas-config-editor created
rolebinding.rbac.authorization.k8s.io/ibm-mas-config-reader created
role.rbac.authorization.k8s.io/ibm-mas-config-reader created
serviceaccount/ibm-mas-config-reader created
rolebinding.rbac.authorization.k8s.io/ibm-mas-coreapi created
role.rbac.authorization.k8s.io/ibm-mas-coreapi created
serviceaccount/ibm-mas-coreapi created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-addons created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-addons created
serviceaccount/ibm-mas-entitymgr-addons created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-bascfg created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-bascfg created
serviceaccount/ibm-mas-entitymgr-bascfg created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-coreidp created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-coreidp created
serviceaccount/ibm-mas-entitymgr-coreidp created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-idpcfg created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-idpcfg created
serviceaccount/ibm-mas-entitymgr-idpcfg created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-jdbccfg created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-jdbccfg created
serviceaccount/ibm-mas-entitymgr-jdbccfg created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-kafkacfg created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-kafkacfg created
serviceaccount/ibm-mas-entitymgr-kafkacfg created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-mongocfg created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-mongocfg created
serviceaccount/ibm-mas-entitymgr-mongocfg created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-objectstoragecfg created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-objectstoragecfg created
serviceaccount/ibm-mas-entitymgr-objectstoragecfg created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-pushcfg created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-pushcfg created
serviceaccount/ibm-mas-entitymgr-pushcfg created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-scimcfg created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-scimcfg created
serviceaccount/ibm-mas-entitymgr-scimcfg created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-slscfg created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-slscfg created
serviceaccount/ibm-mas-entitymgr-slscfg created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-smtpcfg created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-smtpcfg created
serviceaccount/ibm-mas-entitymgr-smtpcfg created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-watsonstudiocfg created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-watsonstudiocfg created
serviceaccount/ibm-mas-entitymgr-watsonstudiocfg created
rolebinding.rbac.authorization.k8s.io/ibm-mas-entitymgr-ws created
role.rbac.authorization.k8s.io/ibm-mas-entitymgr-ws created
serviceaccount/ibm-mas-entitymgr-ws created
rolebinding.rbac.authorization.k8s.io/ibm-mas-internalapi created
role.rbac.authorization.k8s.io/ibm-mas-internalapi created
serviceaccount/ibm-mas-internalapi created
rolebinding.rbac.authorization.k8s.io/ibm-mas-licensing-mediator created
role.rbac.authorization.k8s.io/ibm-mas-licensing-mediator created
serviceaccount/ibm-mas-licensing-mediator created
rolebinding.rbac.authorization.k8s.io/ibm-mas-mobileapi created
role.rbac.authorization.k8s.io/ibm-mas-mobileapi created
serviceaccount/ibm-mas-mobileapi created
rolebinding.rbac.authorization.k8s.io/ibm-mas-monagent-mas created
role.rbac.authorization.k8s.io/ibm-mas-monagent-mas created
serviceaccount/ibm-mas-monagent-mas created
serviceaccount/ibm-mas-nopriv created
rolebinding.rbac.authorization.k8s.io/ibm-mas-scim-agent created
role.rbac.authorization.k8s.io/ibm-mas-scim-agent created
serviceaccount/ibm-mas-scim-agent created
customresourcedefinition.apiextensions.k8s.io/coreidps.internal.mas.ibm.com created
clusterrolebinding.rbac.authorization.k8s.io/ibm-mas-operator:mas87infra created
rolebinding.rbac.authorization.k8s.io/ibm-mas-operator created
serviceaccount/ibm-mas-operator created
deployment.apps/ibm-mas-operator created

3. Deploying IBM Maximo Application Suite
--------------------------------------------------------------------------------
suite.core.mas.ibm.com/mas87infra created

4. Waiting for core systems to be ready
--------------------------------------------------------------------------------
Operator ready              [OK]
Superuser account ready     [OK]
Admin dashboard ready       [OK]
Core API ready              [OK]

================================================================================
Installation Summary
================================================================================

Administration Dashboard URL
--------------------------------------------------------------------------------
https://admin.mas87infra.xxx-1c23480da6d8f4e560816232d3da3f63-xxxx.jp-tok.containers.appdomain.cloud

Super User Credentials
--------------------------------------------------------------------------------
Username: gLjb1rp5vN6VQ5NzCcHKmK6BFxxxxxxx
Password: mIUsBJs6amKCZ2ich3C2WkOpfxxxxxxx

Please make a record of the superuser credentials.

If this is a new installation, you can now complete the initial setup
Sign in as the superuser at this URL:
https://admin.mas87infra.xxxx-1c23480da6d8f4e560816232d3da3f63-xxx.jp-tok.containers.appdomain.cloud/initialsetup
```

インストールが完了するとAdministration Dashboard URL,Super User Credentialが表示されるので控えておく

参考.Administration Dashboard URLはmasのプロジェクトのルートから、Super User Credentialはmasのプロジェクトのシークレットから確認可能
![](2022-04-03-12-33-52.png)

![](2022-04-03-12-34-23.png)

### 3. Initialsetupにログイン
当手順では Administration Dashboard URLにブラウザからアクセスすると、自己署名証明書によるエラーが表示されます。各ブラウザの機能で自己署名証明書を許可する必要があります。

また、複数のドメイン(admin.~ ,api.~ )に対して許可を行う必要があります。


1. Administration Dashboard URLの"admin"を"api"に変更してアクセス
   
    例.「https://<strong>api</strong>.mas87infra.xxxx-1c23480da6d8f4e560816232d3da3f63-xxx.jp-tok.containers.appdomain.cloud/initialsetup」
  ![](2022-04-21-10-26-41.png)

2. 自己署名証明書によるエラーが表示されるため、各ブラウザの手順にて許可します(以下のようなjsonが応答します。当手順を実施しないでログインした場合、admin dashboardにコンテンツが表示されません)
![](2022-04-21-10-27-10.png)

3. Administration Dashboard URLへアクセス。この際も、自己署名証明書によるエラーが表示されるため、各ブラウザの手順にて許可します。
![](2022-04-21-10-28-40.png)

4. Super User Credentialの「Username」「Password」を用いてログイン
![](login.png)
![](Initialsetup.png)



### 4. MongoDB構成
- ホスト
  - mas-mongo-ce-0.mas-mongo-ce-svc.mongo.svc.cluster.local
  - mas-mongo-ce-1.mas-mongo-ce-svc.mongo.svc.cluster.local
  - mas-mongo-ce-2.mas-mongo-ce-svc.mongo.svc.cluster.local
  - Port: 27017
- 認証メカニズム
  - DEFAULT
- 認証データベース
  - admin
- ユーザー名
  - admin
- パスワード
  - MongoAdminPassword
![](MongoDB構成.png)
- 証明書
  - ca.crtを追加する。証明書の内容はOpenShift Webコンソール > WorkLoads > Config Maps > project:mongo　の中にあるmas-mongo-ce-cert-map から取得
![](証明書.png)
  - 確認を選択

上記の入力が完了したら保存

### 5. BAS構成
- URL
  - https://event-api-service.<BASのproject名>.svc:8443 (注.URLの最後に/が入るとエラーになるので削除)
  - 手順書通りに進めた場合、https://event-api-service.bas-mas01.svc:8443
- APIキー
  - BASインストール手順で発行したAPIキーを使用
![](APIキー.png)
- Eメール
  - <自身のメールアドレス>
- 名
  - BAS
- 性
  - Admin
- 証明書
  - tls.crtを追加する。証明書の内容は WorkLoads > Secrets > project:bas~~ > event-api-certのtls.crt
から取得する
![](BAS構成.png)

### 6. SLS構成
![](SLS構成.png)
- URL
  - SLSインストール手順で確認できるURL (注.URLの最後に/が入るとエラーになるので削除)
![](URL.png)

- 登録キー
  - SLSインストール手順で確認できる登録キー
- 証明書
  - sls-cert-ca Secretから取得
![](sls-cert-ca.png)

正しく設定できると5分程度で構成が完了します。
  ![](2022-04-21-09-57-17.png)


以下のように、ライセンスキーの登録に必要な情報が取得されます。
(注 8.7.0時点で、既知の障害で表示されない場合があります。その際には、後述の方法で必要な情報を取得します)

![](2022-04-21-10-07-43.png)

### 7. ライセンスキーの取得


1. ライセンス・キーの項目に記載されている[リンク](https://www.ibm.com/support/pages/ibm-support-licensing-start-page)に飛び、[Log in to the License Key Center](https://licensing.subscribenet.com/control/ibmr/login)をクリック
![](ライセンス・キー.png)
2. ログイン
![](ログイン.png)
3. IBM AppPoint Suitesを選択
![](IBMAppPointSuites.png)
4. IBM MAXIMO APPLICATION SUITE AppPOINT LIC
![](AppPOINT.png)
5. Next
![](Next.png)
6. 以下の情報を入力し、Generate
- Version
  - Maximo Application Suite AppPoint
- Existing hosts
  - Use existing host
- Server Configuration
  - Single Lisence Server
- Host ID Type
  - Ethernet Address
- Host ID
  -  Licence ID を設定 (前項でSLS構成後に画面に表示されない場合、Openshift Webコンソール > インストールされたOperator > SLS > Licenseタブ >作成したSLSを開き「SLS LicenseID」をから取得します)
![](2022-04-21-10-11-23.png)
- Hostname
  -  MASのホスト名を記載
-  Port
   -  27000
-  Number of keys to generate
   -  200 (アプリケーションの必要数に応じて入力)
1. Download License Keys
![](Download.png)
### 8. コンプライアンスの適応とワークスペース設定
- コンプライアンスの適応
  - ON
- アクセス否認メッセージ
  - AIUCO0020I: 最大数の同時ユーザーが既にログインしています。しばらくしてから再度ログインを試みてください。
  - 保存をクリック
- ワークスペースID(任意.当手順では以下を設定。 [参考.用語解説](../99_yougo/index.md) 参照)
  - masinfratest
- ワークスペースの表示名(任意.当手順では以下を設定。 [参考.用語解説](../99_yougo/index.md) 参照)
  - masinfratest

### 9. 初期セットアップ完了
1. 右上の終了を選択
   
   MASの初期セットアップ完了までは3時間程度かかります。

2. 設定が完了すると下記画面が表示
スイート管理とスイートナビゲーターのURLを控えておきます。
![](終了.png)

### 10. ユーザー"masadmin"の登録
1. 正常終了画面に表示されるスイート管理のURLにアクセス、superuserでログイン

![](2022-04-03-12-41-13.png)

2. 左のメニュー > ユーザー > ユーザーの追加を選択

![](2022-04-03-12-41-50.png)

3. 新規ユーザーとして masadmin を作成

![](2022-04-03-12-44-34.png)
![](2022-04-03-12-45-01.png)
![](2022-04-03-12-45-18.png)
![](2022-04-03-12-45-59.png)


### 次項
- [09_IBM CloudPak for Data (CP4D) のインストール](../09_cp4d/index.md)
