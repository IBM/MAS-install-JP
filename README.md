# Maximo Application Suite 8.8/Manage8.4の導入手順書

## 目次
- [はじめに](#はじめに)
- [00_構成と前提](00_architecture/index.md)
- [01_事前準備](01_prereqs/index.md)
- [02_MASインストール前準備](02_preparation/index.md)
- [03_MASインストール](03_masinstall/index.md)
- [04_管理者ユーザーの作成](04_maxadmin/index.md)
- [05_導入後環境の確認](05_confirm/index.md)
- [11_参考.Manageの構成変更](11_reactivate/index.md)
- [50_補足.SQLクライアントの接続](50_dbclient/index.md)
- [51_補足.DB2インスタンスへのアクセス](51_dbinstance/index.md)
- [FAQ](90_faq/index.md)
- [参考.用語解説](99_yougo/index.md)
## はじめに
本資料は、IBM Maximo Application Suite(MAS) 8.8 を Red Hat OpenShift on IBM Cloud (ROKS)上へ導入し、MAS Manage 8.4 のデプロイを行う手順書です。  
IBM Cloud 上に テストやPoC利用を目的とした Health付きMAS Manage の最小構成の環境構築することを想定しております。


当手順では、下記の「IBM Maximo Application Suite CLI Utility」を利用し、ansible collectionを利用して導入します。

* IBM Maximo Application Suite CLI Utility
  	
	https://github.com/ibm-mas/cli

* Ansible collection

	https://www.ibm.com/docs/en/mas-cd/continuous-delivery?topic=installing-ansible-collection

Maximo Application Suite の環境別の導入方法の詳細については以下を御覧ください。  
MAS Manage のみ導入する場合も以下参照ください。

* Supported installation paths

    https://www.ibm.com/docs/en/mas-cd/continuous-delivery?topic=suite-supported-installation-paths


## 注意事項
- 当手順は2022年8月に、Maximo Application Suite 8.8/Manage8.4の構築・アクティベーションをTechzoneのROKS環境にて実施した際の作業ログをベースに記述しております。
- 本資料の記載内容は、正式な IBM のテストやレビューを受けておりません。内容について、できる限り正確を期すよう努めておりますが、いかなる明示または暗黙の保証も責任も負いかねます。
- 本資料の情報は、使用先の責任において使用されるべきものであることを、あらかじめご了承ください
- 掲載内容は不定期に変更されることもあります。他のメディア等に無断で転載する事はご遠慮ください。
- 本資料の著作権は日本アイ・ビー・エムにあります。非営利目的の個人利用の場合において、自由に使用してもかまいませんが、営利目的の使用は禁止させていただきます。
- 本資料は日本アイ・ビー・エム株式会社ならびに日本アイ・ビー・エム・システムズ・エンジニアリング株式会社の正式なレビューを受けておりません。当資料は正式なマニュアルをはじめとするドキュメントの補完資料として参照して下さい。
- 本資料は、製品の特定バージョンを使ってテストをした結果をもとに記述しています。今後のバージョンおよびメンテナンスリリース、Feature Packなどの適用により動作が当資料に記述された内容とは異なってくる可能性がありますのでご了承下さい。
- 本資料は、「本資料にて導入する環境」記載の環境でのみ稼働確認を行っており、他の環境での動作確認は実施しておりません。
　他の環境での手順につきましては、マニュアルを参照ください。
- 構成によってはIBM Cloudの課金が発生することをご留意ください。
- step by stepの手順ではありません。前提知識を必要とします。
- 資料中、以下の略称を使用する場合があります。

| 略称     | 正式名称                       |
| -------- | ------------------------------ |
| MAS      | IBM Maximo Application Suite   |
| ROKS     | Red Hat OpenShift on IBM Cloud |
| OCP      | OpenShift Container Platform   |
| UDS      | IBM User Data Service          |
| SLS      | Suite License Service          |
| DB2      | IBM Db2                        |
| Manage   | IBM Maximo Manage              |
| Pipeline | Red Hat Openshift Pipelines    |

## 本資料にて導入する環境
本資料にて利用する各種ソフトウェアバージョンは以下の通りです。


| 略称                                | バージョン |
| ----------------------------------- | ---------- |
| Red Hat OpenShift                   | 4.8        |
| Red Hat OpenShift CLI               | 4.8        |
| IBM Maximo Application Suite        | 8.8.0      |
| IBM Maximo Manage                   | 8.4.0      |
| IBM Cloud Pack foundational Service | 3.19.1     |
| DB2                                 | 11.5       |
| UDS                                 | 2.0.8      |
| SLS                                 | 3.4.0      |

## 参考.IBM Maximo Application Suite 8.8 前提ソフトウェア
Supported software versions

https://www.ibm.com/docs/en/mas-cd/continuous-delivery?topic=suite-supported-software-versions

Compatibility matrix

https://www.ibm.com/docs/en/mas-cd/continuous-delivery?topic=suite-compatibility-matrix

各ソフトウェアの採用にあたり詳細な注意事項がある場合がありす。
サポート対象となるハードウェア/ソフトウェアバージョンに関しての最新情報や、今回のガイドで用いない構成に関わるソフトウェアのサポート状況に関しては製品システム要件をご確認ください。

IBM Maximo Application Requirements and capacity planning  
https://www.ibm.com/docs/en/mas-cd/continuous-delivery?topic=suite-requirements-capacity-planning

IBM Maximo Manage system requirements  
https://www.ibm.com/docs/en/maximo-manage/continuous-delivery?topic=deploy-system-requirements


## 構築手順時間の目安
| ステップ                        | 所要目安時間                      |
| ------------------------------- | --------------------------------- |
| 01_事前準備                     | 2時間                             |
| 02_MASインストール前準備        | 1時間                             |
| 03_MAS CLIによるMASインストール | 15分,アクティベート完了まで 4時間 |
| 04_管理者ユーザーの作成         | 1時間                             |


## 参考リンク
* インストール手順のビデオ  
  
    https://ibm.seismic.com/Link/Content/DCWSlMgL_Rz0iPnxfnsq5W8g

* Ansible を利用したMASインストール自動化
Ansibleのスキルをお持ちの方は以下を利用してMASのインストールを自動で行うことができます。  
その場合はこのインストール手順書のような手動設定はほとんど不要となります。  
ただしAnsibleに関する不明点は自己解決をして頂く必要があります。

	https://github.com/ibm-mas/ansible-devops

* IBM Documentation：MAS8.8 and later
  
	https://www.ibm.com/docs/en/mas-cd/continuous-delivery


* IBM Documentation：Manage 8.4 and later
  
	https://www.ibm.com/docs/en/maximo-manage/continuous-delivery

* 前バージョン(MAS8.7/Manage8.3)の構築手順書

	https://github.com/IBM/MAS-install-JP/tree/archive/mas87manage83

* その他リンク集

	https://ibm.box.com/s/82ypn8va3yi37sz8jw1c01vmh2vcwmgt

* 参考サイト
  
    Maximo Programming

	https://maximopro.tumblr.com/

	Playbook for Maximo EAM Upgrade

	https://ibm-mas-manage.github.io/playbook/upgrade/deploymentmodel/

	MAS MS Data Sheet

	https://www.ibm.com/software/reports/compatibility/clarity-reports/report/html/softwareReqsForProduct?deliverableId=3F0E2B305E7111EABE1C939145D7672E

	Deployment Guide  for Maximo Application Suite

	https://mam-hol.eu-gb.mybluemix.net/

	MAS Folder @Seismic

	https://ibm.seismic.com/Link/Folder/DC6b9f97


	MAS Demo Movie @Seismic

	https://ibm.seismic.com/Link/Folder/DCcdfd8b


	MAS お客様向けライセンス・ガイド @Seismic

	https://ibm.seismic.com/Link/Content/DC8fdwbEQVi0-ClDMMN66Elw


	MAS Pricing Guide @Seismic

	https://ibm.seismic.com/Link/Content/DCV7TR4HYCgUaD2mrkzrxzAQ

	以下、IBM社員のみ

	https://pages.github.ibm.com/maximoappsuite/deployment-guide/install/installation/


	MAS MS Wiki 

	https://github.ibm.com/maximoappsuite/tracker-masms/wiki/Blueline-Assessment#as-a-service-capabilities

	Maximo Performance Wiki

	https://pages.github.ibm.com/maximo/performance-wiki/

	Reset Manage Users Password
	
	https://pages.github.ibm.com/maximo/performance-wiki/manage-8/reset-manage-user-password/

### 次項
- [00_構成と前提](./00_architecture/index.md)
