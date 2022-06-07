## 14.添付ファイルダウンロードの設定

Manageの資産(Assets)アプリケーションを例に、添付ファイルのダウンロード機能を利用するために必要な設定について記載します。資産以外のアプリケーションの場合でも、ダウンロード機能を利用するためには、同様の設定が必要です。

### 1. システムプロパティの設定

ここでは、以下のようにManageのシステムプロパティを設定します。
| プロパティ名       |  グローバル値 |
| ------------- | ----- |
mxe.doclink.doctypes.topLevelPaths  | /mnt/doclinks
mxe.doclink.path01  |  /mnt/doclinks=ManageのURL/maximo<br>例 /mnt/doclinks=`https://masinfratest.manage.mas87infra.itzroks-6630035f1i-v8vw8j-6...6-0000.jp-tok.containers.appdomain.cloud/maximo`
mxe.doclink.securedAttachment  | true
mxe.doclink.securedAttachmentDebug | true

Manageのメニュー システムの構成 > プラットフォームの構成 > システムのプロパティを開いてください。
![](2022-06-07-11-56-27.png)

#### 1-1.mxe.doclink.doctypes.topLevelPathsプロパティの設定
mxe.doclink.doctypes.topLevelPathsを探して、グローバル値に``/mnt/doclinks``を設定
![](2022-06-07-12-20-52.png)

プロパティの保存を押下
![](2022-06-07-12-22-10.png)

CLIからPodを再起動
```
oc scale deployment mas87infra-masinfratest-uibundle -n mas-mas87infra-manage --replicas=0
oc scale deployment mas87infra-masinfratest-uibundle -n mas-mas87infra-manage --replicas=2
```
※Pod名については、OpenShift Webコンソール > ワークロード > Podにて、プロジェクト:mas-mas87infra-manageのmas87infra-masinfratestでフィルターして確認。名前にuibundleを含むPod。 (下のOpenShift Webコンソールのキャプチャも参照)
![](2022-06-07-12-22-57.png)

OpenShift Webコンソールより、Podの再起動が完了したことを確認。ステータスがRunning、準備状態が2/2になったらPodの再起動は完了している。
![](2022-06-07-12-24-32.png)

システムプロパティの現在値にグローバル値が反映されていることを確認
![](2022-06-07-12-25-22.png)

#### 1-2.mxe.doclink.path01プロパティの設定
mxe.doclink.path01を探して、グローバル値に`` /mnt/doclinks=ManageのURL/maximo``を設定
ここでは例として、/mnt/doclinks=https://masinfratest.manage.mas87infra.itzroks-6630035f1i-v8vw8j-6...6-0000.jp-tok.containers.appdomain.cloud/maximoを設定する。各自のManageのURLに読み替えること。
![](2022-06-07-13-11-27.png)

プロパティの保存を押下
![](2022-06-07-12-22-10.png)

実行中のリフレッシュを押下
![](2022-06-07-13-12-22.png)

OKを押下
![](2022-06-07-13-13-00.png)

現在値に反映されていることを確認
![](2022-06-07-13-13-33.png)

#### 1-3.mxe.doclink.securedAttachmentおよびmxe.doclink.securedAttachmentDebugプロパティの設定
mxe.doclink.securedAttachmentを探して、グローバル値に``true``を設定
グローバル値にtrueを設定
![](2022-06-07-13-15-39.png)

プロパティの保存を押下
![](2022-06-07-12-22-10.png)

実行中のリフレッシュを押下
![](2022-06-07-13-12-22.png)

現在値に反映されていることを確認
![](2022-06-07-13-16-45.png)

mxe.doclink.securedAttachmentDebugも同様の手順でグローバル値に``true``を設定
![](2022-06-07-13-18-09.png)


### 2.資産アプリケーションの設定

#### 2-1.文書フォルダーのパスを設定
Manageのナビゲーションより資産アプリケーションを選択
![](2022-06-07-14-03-29.png)

追加アクション - 添付ライブラリー/フォルダー - フォルダーの管理 を選択
![](2022-06-07-14-04-14.png)

デフォルトのファイル・パスに /mnt/doclinks/Attachments を設定し、OKを押下
![](2022-06-07-14-04-48.png)

#### 2-2.添付ファイルのアップロードの確認
資産を選択して、Enterを押下。ここでは 11400 を例に説明する。
![](2022-06-07-14-07-00.png)

添付アイコンを押下して、新規ファイルの追加を選択
![](2022-06-07-14-07-38.png)

ファイルの選択ボタンより、添付するファイルを選択して、OKを押下
![](2022-06-07-14-08-18.png)

OpenShift Webコンソールより、永続ボリューム要求(manage-doc-pvc)を確認すると、仕様済みのサイズが増えていることが分かる。
![](2022-06-07-14-09-30.png)

#### 2-3.添付ファイルのダウンロードの確認
添付リンクを押下して、添付したファイルのドキュメント名を押下
![](2022-06-07-14-10-47.png)

![](2022-06-07-14-11-12.png)
添付したファイルがダウンロードされて、正常に閲覧できることを確認