# Maximo Application Suite 8.7/Manage8.3の導入手順書

## 目次
- [はじめに](#はじめに)
- [00_構成と前提](00_architecture/index.md)
- [01_事前準備](01_prereqs/index.md)
- [02_IBM Operator Catalog の追加](02_isc/index.md)
- [03_Service Binding Operator(SBO) のインストール](03_sbo/index.md)
- [04_Cert Managermentのインストール](04_cm/index.md)
- [05_Behaviour Analytics Service (BAS) のインストール](05_bas/index.md)
- [06_MongoDB のインストール](06_mongo/index.md)
- [07_IBM Suite License Service (SLS) のインストール](07_sls/index.md)
- [08_Maximo Application Suite(MAS) のインストール](08_mas_core/index.md)
- [09_IBM CloudPak for Data (CP4D) のインストール](09_cp4d/index.md)
- [10_DB2warehouseのインストールと構成](10_db2/index.md)
- [11_Manage Operatorのデプロイ](11_manageop/index.md)
- [12_永続ボリュームの作成](12_pvc/index.md)
- [13_Manageのアクティブ化](13_activate/index.md)
- [参考.用語解説](99_yougo/index.md)
## はじめに
本資料は、IBM Maximo Application Suite(MAS) 8.7 を Red Hat OpenShift on IBM Cloud (ROKS)上へ導入し、MAS Manage 8.3 のデプロイを行う手順書です。IBM Cloud 上に テストやPoC利用を目的とした MAS Manage の最小構成の環境構築することを想定しております。												

## 注意事項
- 当手順は2022年3月から4月にかけて、Maximo Application Suite 8.7/Manage8.3の構築・アクティベーションをTechzoneのROKS環境にて実施した際の作業ログをベースに記述しております。
- 本資料の記載内容は、正式な IBM のテストやレビューを受けておりません。内容について、できる限り正確を期すよう努めておりますが、いかなる明示または暗黙の保証も責任も負いかねます。
- 本資料の情報は、使用先の責任において使用されるべきものであることを、あらかじめご了承ください
- 掲載内容は不定期に変更されることもあります。他のメディア等に無断で転載する事はご遠慮ください。
- 本資料の著作権は日本アイ・ビー・エムにあります。非営利目的の個人利用の場合において、自由に使用してもかまいませんが、営利目的の使用は禁止させていただきます。
- 本資料は日本アイ・ビー・エム株式会社ならびに日本アイ・ビー・エム・システムズ・エンジニアリング株式会社の正式なレビューを受けておりません。当資料は正式なマニュアルをはじめとするドキュメントの補完資料として参照して下さい。
- 本資料は、製品の特定バージョンを使ってテストをした結果をもとに記述しています。今後のバージョンおよびメンテナンスリリース、Feature Packなどの適用により動作が当資料に記述された内容とは異なってくる可能性があります のでご了承下さい。
- 本資料は、「本資料にて導入する環境」記載の環境でのみ稼働確認を行っており、他の環境での動作確認は実施しておりません。
　他の環境での手順につきましては、マニュアルを参照ください。
- 構成によってはIBM Cloudの課金が発生することをご留意ください。
- 公開対象はIBM Business Partner、IBM社員とさせていただきます。
- step by stepの手順ではありません。前提知識を必要とします。
- 資料中、以下の略称を使用する場合があります。

| 略称       |  正式名称 |
| ------------- | ----- |
MAS  | IBM Maximo Application Suite
ROKS  |  Red Hat OpenShift on IBM Cloud
OCP  | OpenShift Container Platform
BAS | Behavior Analytics Services
SBO |  Service Binding Operator
SLS  | Suite License Service
CP4D  | IBM Cloud Pak for Data
DB2WH  | IBM Db2 Warehouse
Manage  | IBM Maximo Manage

## 本資料にて導入する環境
本資料にて利用する各種ソフトウェアバージョンは以下の通りです。

| 略称       |  バージョン |
| ------------- | ----- |
Red Hat OpenShift  | 4.8
Red Hat OpenShift CLI  |  4.8
IBM Maximo Application Suite  | 8.7.0
IBM Maximo Manage | 8.3.0
BAS |  1.1.4
SBO |  1.0.1
SLS  | 3.3.1
CP4D  | 4.0
DB2WH  | 11.5.7
Manage  | 8.3

## 参考.IBM Maximo Application Suite 8.7 前提ソフトウェア
https://www.ibm.com/software/reports/compatibility/clarity-reports/report/html/prereqsForProduct?deliverableId=956BBF70416D11EC80A8306957D94E96&osPlatforms=&duComponentIds=C018%7CC015%7CC014%7CC012%7CC013%7CC011%7CC017%7CC010%7CC016&mandatoryCapIds=30%7C62%7C9%7C132%7C42%7C184%7C27&optionalCapIds=30%7C9%7C20%7C26

| Required software        |  Version |
| ------------- | ----- |
Red Hat OpenShift  | 4.6 or 4.8 (新規インストールは 4.8 推奨)
Service Binding Operator  |  0.8.0 ( OCP4.6 ) , with future 0.8.x and packs<br />1.0.0 ( OCP4.8 ) and future 1.0.x fix packs 
Cert-manager  | 1.5 and future fix packs
Behavior Analytics Service | 1.1 and future fix packs
MongoDB |  4.2 and future fix packs
IBM Suite License Service  | 3.2 , 3.3	
IBM Cloud Pak for Data  | 4.0


各ソフトウェアの採用にあたり詳細な注意事項がある場合があります。										
MAS Manage 8.3.0のサポート対象となるハードウェア/ソフトウェアバージョンに関しての最新情報や、今回の										
ガイドで用いない構成に関わるソフトウェアのサポート状況に関しては製品システム要件をご確認ください。

IBM Maximo Application Suite 8.7.0 system requirements									
https://www.ibm.com/docs/en/mas87/8.7.0?topic=installation-system-requirements#sysreqs
							
										
IBM Maximo Manage 8.3.0 system requirements	

https://www.ibm.com/docs/en/maximo-manage/8.3.0?topic=deploy-system-requirements

## 構築の流れ
MASおよびManageについて、構築の大きな流れは以下の通りです。										
1. MASの前提ソフトウェアを導入					
2. MASのインストールと初期セットアップ					
3. MAS Manage前提のソフトウェアとして、Db2WH導入のため、CP4Dの導入
4. Db2WHの構築とManage用にセットアップ					
5. MAS Manageのデプロイ、アクティブ化												

## 構築手順時間の目安
| ステップ       |  所要目安時間 |
| ------------- | ----- |
01_事前準備  | 2時間
02_IBM Operator Catalog の追加  |  15分
03_Service Binding Operator(SBO) のインストール  | 15分
04_Cert Managermentのインストール | 15分
05_Behaviour Analytics Service (BAS) のインストール |  1時間
06_MongoDB のインストール |  1時間
07_IBM Suite License Service (SLS) のインストール  | 1時間
08_Maximo Application Suite(MAS) のインストール  | 4時間
09_IBM CloudPak for Data (CP4D) のインストール  | 2時間
10_Db2WareHouseのインストールと構成  | 1時間
11_Manage Operatorのデプロイ  | 30分
12_永続ボリュームの作成  | 15分
13_Manageのアクティブ化  | 2時間〜

## 参考リンク
* インストール手順のビデオ
https://ibm.seismic.com/Link/Content/DCWSlMgL_Rz0iPnxfnsq5W8g

* Ansible を利用したMASインストール自動化
Ansibleのスキルをお持ちの方は以下を利用してMASのインストールを自動で行うことができます。その場合はこのインストール手順書のような手動設定はほとんど不要となります。ただしAnsibleに関する不明点は自己解決をして頂く必要があります。

	https://github.com/ibm-mas/ansible-devops

* IBM Documentation：MAS8.7
  
	https://www.ibm.com/docs/en/mas87

* Deployment Guide  for Maximo Application Suite

	Business Partner

	https://mam-hol.eu-gb.mybluemix.net/

	IBM社員のみ

	https://pages.github.ibm.com/maximoappsuite/deployment-guide/install/installation/

* IBM Documentation：Manage
  
	https://www.ibm.com/docs/en/maximo-manage/8.3.0

* その他リンク集

	https://ibm.box.com/s/82ypn8va3yi37sz8jw1c01vmh2vcwmgt
	
	### 次項
  [01_事前準備](../main/01_prereqs/index.md)
