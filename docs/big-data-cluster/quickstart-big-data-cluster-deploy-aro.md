---
title: 於 Azure Red Hat OpenShift Python 指令碼上部署
titleSuffix: SQL Server Big Data Clusters
description: 了解如何使用部署指令碼在 Azure Red Hat OpenShift (ARO) 上部署 SQL Server 巨量資料叢集。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 06/22/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: c5889a9411c54312dd52ee1a5e9f2b7917da6838
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97637427"
---
# <a name="use-a-python-script-to-deploy-a-sql-server-big-data-cluster-on-azure-red-hat-openshift-aro"></a>使用 Python 指令碼在 Azure Red Hat OpenShift (ARO) 上部署 SQL Server 巨量資料叢集

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

在本教學課程中，您將使用範例 Python 部署指令碼將 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)] 部署至 [Azure Red Hat OpenShift (ARO)](/azure/virtual-machines/linux/openshift-get-started)。 自 SQL Server 2019 CU5 開始，支援此部署選項。

> [!TIP]
> ARO 是唯一針對巨量資料叢集裝載 Kubernetes 的選項。 若要深入了解其他部署選項，以及如何自訂部署選項，請參閱[如何在 Kubernetes 上部署 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]](deployment-guidance.md)。


> [!WARNING]
> 使用內建儲存類別 *managed-premium* 建立的永久性磁碟區具有 *Delete* (刪除) 的回收原則。 因此，當您刪除 SQL Server 巨量資料叢集時，永久性磁碟區宣告會遭到刪除，然後永久性磁碟區也是。 您應該使用 azure-disk 佈建程式搭配 *Retain* (保留) 回收原則來建立自訂儲存類別，如 [概念儲存體](/azure/aks/concepts-storage/#storage-classes)中所述。 下列指令碼是使用 *managed-premium* 儲存類別。 如需詳細資訊，請參閱[資料持續性](concept-data-persistence.md)主題。

此處使用的預設巨量資料叢集部署，包含 SQL 主要執行個體、一個計算集區執行個體、兩個資料集區執行個體以及兩個存放集區執行個體。 資料以使用 ARO 預設儲存類別的 Kubernetes 持續性磁碟區保存。 本教學課程中使用的預設設定適用於開發/測試環境。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。
- [oc](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html)
- [Python 最低版本 3.0](https://www.python.org/downloads)
- [`az` CLI](/cli/azure/install-azure-cli/)
- [[!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)]](../azdata/install/deploy-install-azdata.md)
- **Azure Data Studio**

## <a name="log-in-to-your-azure-account"></a>登入您的 Azure 帳戶

該指令碼使用 Azure CLI 自動化 ARO 叢集的建立流程。 執行指令碼之前，您必須至少使用 Azure CLI 登入您的 Azure 帳戶一次。 從命令提示字元執行下列命令。

```terminal
az login
```

## <a name="instructions"></a>Instructions

1. 下載 Python 指令碼 `deploy-sql-big-data-aro.py` 與 yaml 檔案 `bdc-scc.yaml`。

   >這些檔案都在本文的這兩小節下：
   - [`deploy-sql-big-data-aro.py`](#deploy-sql-big-data-aropy)
   - [`bdc-scc.yaml`](#bdc-sccyaml)

1. 使用下列項目執行指令碼：

```terminal
python deploy-sql-big-data-aro.py
```

提示出現時，請輸入 Azure 訂閱識別碼與要建立資源的 Azure 資源群組。 或者，您也可以選擇輸入其他組態，或使用提供的預設值。 例如：

- `azure_region`
- OpenShift 背景工作節點的 `vm_size`。 若要在驗證基本案例期間獲得最佳體驗，建議叢集中所有的背景工作節點至少配備 8 顆 vCPU 與 64 GB 記憶體。 該指令碼預設使用 `Standard_D8s_v3` 與 3 個背景工作節點。 巨量資料叢集的預設大小組態也使用約 24 個磁碟，以滿足整體所有元件的持續性磁碟區需求。
- OpenShift 叢集部署的網路組態 - 如需每個參數的詳細資料，請參閱 [ARO 部署文章](\azure\openshift\tutorial-create-cluster)。
- `cluster_name` - 此值可用在 ARO 叢集及以 ARO 為基礎的 SQL Server 巨量資料叢集。 請注意，SQL 巨量資料叢集的名稱將是 Kubernetes 命名空間。
- `username `- 這是在部署控制站管理帳戶、SQL Server 主要執行個體帳戶與閘道期間，佈建帳戶的使用者名稱。 請注意，最佳做法是系統為您自動停用 `sa` SQL Server 帳戶。
- `password` - 所有帳戶皆會使用相同的值。

SQL Server 巨量資料叢集現已部署在 ARO 上。 您現在可以使用 Azure Data Studio 連線至叢集。 如需詳細資訊，請參閱[使用 Azure Data Studio 連線至 SQL Server 巨量資料叢集](connect-to-big-data-cluster.md)。

## <a name="clean-up"></a>清除

若要在 Azure 中測試 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]，完成後應刪除 ARO 叢集，以免產生未預期的費用。 如果您希望繼續使用叢集，請勿移除叢集。

> [!WARNING]
> 下列步驟會卸除 ARO 叢集，也會移除 SQL Server 巨量資料叢集。 如果您有任何想要保留的資料庫或 HDFS 資料，請在刪除叢集之前先備份該資料。

執行下列 Azure CLI 命令以移除巨量資料叢集與 Azure 中的 ARO 服務 (以您在部署指令碼中指定的 **Azure 資源群組** 取代 `<resource group name>`)：

```terminal
az group delete -n <resource group name>
```

## `deploy-sql-big-data-aro.py` 

此節中的指令碼會將 SQL Server 巨量資料叢集部署至 Azure Red Hat OpenShift。 將指令碼複製到您的工作站，並在部署開始前將其儲存為 `deploy-sql-big-data-aro.py`。

```python
#
# Prerequisites: 
# 
# Azure CLI (https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), Azure Data CLI (`azdata`) (https://docs.microsoft.com/en-us/sql/big-data-cluster/deploy-install-azdata), oc CLI (https://www.openshift.com/blog/installing-oc-tools-windows)
#
# Run `az login` at least once BEFORE running this script
#

from subprocess import check_output, CalledProcessError, STDOUT, Popen, PIPE, getoutput
from time import sleep
import os
import getpass
import json

def executeCmd (cmd):
    if os.name=="nt":
        process = Popen(cmd.split(),stdin=PIPE, shell=True)
    else:
        process = Popen(cmd.split(),stdin=PIPE)
    stdout, stderr = process.communicate()
    if (stderr is not None):
        raise Exception(stderr)

#
# MUST INPUT THESE VALUES!!!!!
#
SUBSCRIPTION_ID = input("Provide your Azure subscription ID:").strip()
GROUP_NAME = input("Provide Azure resource group name to be created:").strip()
#
# This password will be use for Controller user, Gateway user and SQL Server Master SA accounts
AZDATA_USERNAME=input("Provide username to be used for Controller, SQL Server and Gateway endpoints - Press ENTER for using  `admin`:").strip() or "admin"
AZDATA_PASSWORD = getpass.getpass("Provide password to be used for Controller user, Gateway user and SQL Server Master accounts:")
#
# Optionally change these configuration settings
#
AZURE_REGION=input("Provide Azure region - Press ENTER for using `westus2`:").strip() or "westus2"
# MASTER_VM_SIZE=input("Provide VM size for master nodes for the ARO cluster - Press ENTER for using  `Standard_D2s_v3`:").strip() or "Standard_D2s_v3"
WORKER_VM_SIZE=input("Provide VM size for the worker nodes for the ARO cluster - Press ENTER for using  `Standard_D8s_v3`:").strip() or "Standard_D8s_v3"
OC_NODE_COUNT=input("Provide number of worker nodes for ARO cluster - Press ENTER for using  `3`:").strip() or "3"
VNET_NAME=input("Provide name of Virtual Network for ARO cluster - Press ENTER for using  `aro-vnet`:").strip() or "aro-vnet"
VNET_ADDRESS_SPACE=input("Provide Virtual Network Address Space for ARO cluster - Press ENTER for using  `10.0.0.0/16`:").strip() or "10.0.0.0/16"
MASTER_SUBNET_NAME=input("Provide Master Subnet Name for ARO cluster - Press ENTER for using  `master-subnet`:").strip() or "master-subnet"
MASTER_SUBNET_IP_RANGE=input("Provide address range of Master Subnet for ARO cluster - Press ENTER for using  `10.0.0.0/23`:").strip() or "10.0.0.0/23"
WORKER_SUBNET_NAME=input("Provide Worker Subnet Name for ARO cluster - Press ENTER for using  `worker-subnet`:").strip() or "worker-subnet"
WORKER_SUBNET_IP_RANGE=input("Provide address range of Worker Subnet for ARO cluster - Press ENTER for using  `10.0.2.0/23`:").strip() or "10.0.2.0/23"
#
# This is both Kubernetes cluster name and SQL Big Data cluster name
CLUSTER_NAME=input("Provide name of OpenShift cluster and SQL big data cluster - Press ENTER for using  `sqlbigdata`:").strip() or "sqlbigdata"
#
# Deploy the ARO cluster
#  
print ("Set azure context to subscription: "+SUBSCRIPTION_ID)
command = "az account set -s "+ SUBSCRIPTION_ID
executeCmd (command)
print ("Creating azure resource group: "+GROUP_NAME)
command="az group create --name "+GROUP_NAME+" --location "+AZURE_REGION
executeCmd (command)
command = "az network vnet create --resource-group "+GROUP_NAME+" --name "+VNET_NAME+" --address-prefixes "+VNET_ADDRESS_SPACE
print("Creating Virtual Network: "+VNET_NAME)
executeCmd(command)
command = "az network vnet subnet create --resource-group "+GROUP_NAME+" --vnet-name "+VNET_NAME+" --name "+MASTER_SUBNET_NAME+" --address-prefixes "+MASTER_SUBNET_IP_RANGE+" --service-endpoints Microsoft.ContainerRegistry"
print("Creating Master Subnet: "+MASTER_SUBNET_NAME)
executeCmd(command)
command = "az network vnet subnet create --resource-group "+GROUP_NAME+" --vnet-name "+VNET_NAME+" --name "+WORKER_SUBNET_NAME+" --address-prefixes "+WORKER_SUBNET_IP_RANGE+" --service-endpoints Microsoft.ContainerRegistry"
print("Creating Worker Subnet: "+WORKER_SUBNET_NAME)
executeCmd(command)
command = "az network vnet subnet update --name "+MASTER_SUBNET_NAME+" --resource-group "+GROUP_NAME+" --vnet-name "+VNET_NAME+" --disable-private-link-service-network-policies true"
print("Updating Master Subnet by disabling Private Link Policies")
executeCmd(command)
command = "az aro create --resource-group "+GROUP_NAME+" --name "+CLUSTER_NAME+" --vnet "+VNET_NAME+" --master-subnet "+MASTER_SUBNET_NAME+" --worker-subnet "+WORKER_SUBNET_NAME+" --worker-count "+OC_NODE_COUNT+" --worker-vm-size "+WORKER_VM_SIZE +" --only-show-errors"
print("Creating OpenShift cluster: "+CLUSTER_NAME)
executeCmd (command)
#
# Login to oc console
#
command = "az aro list-credentials --name "+CLUSTER_NAME+" --resource-group "+GROUP_NAME +" --only-show-errors"
output=json.loads(getoutput(command))
OC_CLUSTER_USERNAME = str(output['kubeadminUsername'])
OC_CLUSTER_PASSWORD = str(output['kubeadminPassword'])
command = "az aro show --name "+CLUSTER_NAME+" --resource-group "+GROUP_NAME +" --only-show-errors"
output=json.loads(getoutput(command))
APISERVER = str(output['apiserverProfile']['url'])
command = "oc login "+ APISERVER+ " -u " + OC_CLUSTER_USERNAME + " -p "+ OC_CLUSTER_PASSWORD
executeCmd (command)
#
# Setup pre-requisites for deploying BDC on OpenShift
#
#
#Creating new project/namespace
command = "oc new-project "+ CLUSTER_NAME
executeCmd (command)
#
# create custom SCC for BDC
command = "oc apply -f bdc-scc.yaml"
executeCmd (command)
#
#Adding the custom scc to BDC namespace
command = "oc adm policy add-scc-to-group bdc-scc system:serviceaccounts:" + CLUSTER_NAME
executeCmd (command)
#
# Deploy big data cluster
#
print ('Set environment variables for credentials')
os.environ['AZDATA_PASSWORD'] = AZDATA_PASSWORD
os.environ['AZDATA_USERNAME'] = AZDATA_USERNAME
os.environ['ACCEPT_EULA']="Yes"
#
#Creating azdata configuration with aro-dev-test profile
command = "azdata bdc config init --source aro-dev-test --target custom --force"
executeCmd (command)
command="azdata bdc config replace -c custom/bdc.json -j ""metadata.name=" + CLUSTER_NAME + ""
executeCmd (command)
#
# Create BDC
command = "azdata bdc create --config-profile custom --accept-eula yes"
executeCmd(command)
#login into BDC cluster and list endpoints
command="azdata login -n " + CLUSTER_NAME
executeCmd (command)
print("")
print("SQL Server big data cluster endpoints: ")
command="azdata bdc endpoint list -o table"
executeCmd(command)
```

## `bdc-scc.yaml`

下列 .yaml 資訊清單會定義巨量資料叢集部署的自訂安全性內容條件約束 (SCC)。 將其複製到與 `deploy-sql-big-data-aro.py` 相同的目錄。

```yaml
allowHostDirVolumePlugin: false
allowHostIPC: false
allowHostNetwork: false
allowHostPID: false
allowHostPorts: false
allowPrivilegeEscalation: true
allowPrivilegedContainer: false
allowedCapabilities:
  - SETUID
  - SETGID
  - CHOWN
  - SYS_PTRACE
apiVersion: security.openshift.io/v1
defaultAddCapabilities: null
fsGroup:
  type: RunAsAny
kind: SecurityContextConstraints
metadata:
  annotations:
    kubernetes.io/description: SQL Server BDC custom scc is based on 'nonroot' scc plus additional capabilities required by BDC.
  generation: 2
  name: bdc-scc
readOnlyRootFilesystem: false
requiredDropCapabilities:
  - KILL
  - MKNOD
runAsUser:
  type: MustRunAsNonRoot
seLinuxContext:
  type: MustRunAs
supplementalGroups:
  type: RunAsAny
volumes:
  - configMap
  - downwardAPI
  - emptyDir
  - persistentVolumeClaim
  - projected
  - secret
```

## <a name="next-steps"></a>後續步驟

部署指令碼已設定 Azure Kubernetes Service，同時也部署了 SQL Server 2019 巨量資料叢集。 您也可以選擇透過手動安裝來自訂未來的部署。 若要深入了解如何部署巨量資料叢集以及如何自訂部署，請參閱[如何在 Kubernetes 上部署[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]](deployment-guidance.md)。

現在 SQL Server 巨量資料叢集已完成部署，您可以載入範例資料並探索教學課程：

> [!div class="nextstepaction"]
> [教學課程：將範例資料載入 SQL Server 2019 巨量資料叢集](tutorial-load-sample-data.md)