## 3.Service Binding Operator のインストール

下記手順をibm-masディレクトリ内で実行します。oc loginを実行します。



コマンド:

```bash 
./install-sbo.sh
```

実行結果:

```bash 
./install-sbo.sh

Service Binding Operator Installer
======================================================================
 - Service Binding Operator Channel ... stable
 - Service Binding Operator Version ...... 1.0.0
 - Log File ............... /work/mas-infra87/ibm-mas/sboinstall/logs/install-sbo.log
OCP clustger version: 4.8.26
OCP minor version: 4.8 proceeding with SBO install

Install the Service Binding Operator
----------------------------------------------------------------------
Creating subscription rh-service-binding-operator
subscription.operators.coreos.com/rh-service-binding-operator created
 - Searching for install plan  .....

Service Bindign Operator Installation Complete
```

### 次項
- [04_Cert Managermentのインストール](../04_cm/index.md)
