## 4.Cert Managermentのインストール

下記手順をibm-masディレクトリ内で実行します。oc loginを実行します。


コマンド:
```bash 
./install-cert-manager.sh
```

実行結果:
```bash 
./install-cert-manager.sh

cert-manager Installer
======================================================================

Install cert-manager
----------------------------------------------------------------------

1.1 Check and Install IBM operator catalog and common services catalog if they dont exist.
----------------------------------------------------------------------
No resources found in ibm-common-services namespace.
catalogsource.operators.coreos.com/ibm-operator-catalog created
catalogsource.operators.coreos.com/opencloud-operators created
namespace/ibm-common-services created
operatorgroup.operators.coreos.com/operatorgroup created
subscription.operators.coreos.com/ibm-common-service-operator created

1.2. Waiting for OperandRequest resource
----------------------------------------------------------------------
Operand request CRD ready              [OK]

1.3 Installing IBM Common Services operand request for cert-manager
----------------------------------------------------------------------
operandrequest.operator.ibm.com/common-service created

IBM Common Service cert-manager Installation Complete
```
### 次項
- [05_Behaviour Analytics Service (BAS) のインストール](../05_bas/index.md)
