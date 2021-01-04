---
title: SQL Server 巨量資料叢集版本資訊
titleSuffix: SQL Server big data clusters
description: 本文說明 SQL Server 巨量資料叢集的最新更新和已知問題。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 10/19/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: f328e136bc0b07f23083153277ac98588702b373
ms.sourcegitcommit: 3bd188e652102f3703812af53ba877cce94b44a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97489658"
---
# <a name="sql-server-2019-big-data-clusters-release-notes"></a>SQL Server 2019 巨量資料叢集版本資訊

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

下列版本資訊適用於 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]。 此文章已針對每個版本細分為數個小節。 每個版本都具有描述 CU 變更的支援文章連結，以及能下載 Linux 套件的連結。 此文章也列出最新版 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] (BDC) 的[已知問題](#known-issues)。

## <a name="supported-platforms"></a>支援的平台

本節說明 BDC 支援的平台。

### <a name="kubernetes-platforms"></a>Kubernetes 平台

|平台|支援的版本|
|---------|---------|
|Vanilla (上游) Kubernetes|使用 Kubernetes 叢集版本 (最低 1.13 版) 部署內部部署的 BDC。 請參閱 [Kubernetes version and version skew support policy](https://kubernetes.io/docs/setup/release/version-skew-policy/) (Kubernetes 版本與版本誤差支援原則)。|
|Red Hat OpenShift|使用 OpenShift 叢集版本 (最低 4.3 版) 部署內部部署的 BDC。 請參閱 [Red Hat OpenShift Container Platform Life Cycle Policy](https://access.redhat.com/support/policy/updates/openshift) (Red Hat OpenShift Container Platform 生命週期原則)。<br><br> 在 SQL Server 2019 CU5 中引進的支援。|
|Azure Kubernetes Service (AKS)|在 AKS 叢集版本 (最低 1.13 版) 上部署 BDC。<br/>如需版本支援原則，請參閱 [AKS 中支援的 Kubernetes 版本](/azure/aks/supported-kubernetes-versions)。|
|Azure Red Hat OpenShift (ARO)|在 ARO 版本 (最低 4.3 版) 上部署 BDC。 請參閱 [Azure Red Hat OpenShift](/azure/openshift/)。 <br><br> 在 SQL Server 2019 CU5 中引進的支援。|

### <a name="host-os-for-kubernetes"></a>適用於 Kubernetes 的主機 OS

|平台|主機作業系統|支援的版本|
|---------|---------|---------|
|Kubernetes|Ubuntu|16.04|
|Kubernetes|Red Hat Enterprise Linux|7.3、7.4、7.5、7.6|
|OpenShift|Red Hat Enterprise Linux / CoreOS |請參閱 [OpenShift 版本資訊](https://docs.openshift.com/container-platform/4.3/release_notes/ocp-4-3-release-notes.html#ocp-4-3-about-this-release)|

### <a name="sql-server-editions"></a>SQL Server 版本

|版本|注意|
|---------|---------|
|Enterprise<br/>標準<br/>開發人員| 巨量資料叢集版本是由 SQL Server 主要執行個體版本所決定。 在部署時，預設會部署 Developer Edition。 在部署後可以變更此版本。 請參閱[設定 SQL Server 主要執行個體](./configure-sql-server-master-instance.md)。 |

## <a name="tools"></a>工具

|平台|支援的版本|
|---------|---------|
|[!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)]|最佳做法是使用可用的最新版本。 自 SQL Server 2019 CU5 版本開始，[!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] 與伺服器之間有獨立的語意版本。 <br/><br/>執行 `azdata –-version` 以驗證版本。<br/><br/>如需最新版本，請參閱[版本歷程記錄](#release-history)。|
|Azure Data Studio|取得 [Azure Data Studio](../azure-data-studio/download-azure-data-studio.md) 的最新組建。|

如需完整清單，請參閱[需要哪些工具？](deploy-big-data-tools.md#which-tools-are-required)

## <a name="release-history"></a>版本歷程記錄

下表列出 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)] 的版本歷程記錄。

| 發行版本 <sup>1</sup> | BDC 版本    | [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] 版本 <sup>2</sup>| 發行日期 |
|------------------|----------------|-----------------|--------------|
| [CU8](#cu8)      | 15.0.4073.23   | 20.2.2          | 2020-10-19   |
| [CU6](#cu6)      | 15.0.4053.23   | 20.0.1          | 2020-08-04   |
| [CU5](#cu5)      | 15.0.4043.16   | 20.0.0          | 2020-06-22   |
| [CU4](#cu4)      | 15.0.4033.1    | 15.0.4033       | 2020-03-31   |
| [CU3](#cu3)      | 15.0.4023.6    | 15.0.4023       | 2020-03-12   |
| [CU2](#cu2)      | 15.0.4013.40   | 15.0.4013       | 2020-02-13   |
| [CU1](#cu1)      | 15.0.4003.23   | 15.0.4003       | 2020-01-07   |
| [GDR1](#rtm)     | 15.0.2070.34   | 15.0.2070       | 2019-11-04   |

<sup>1</sup> BDC 無法使用 CU7。

<sup>2</sup> [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] 版本會反映在 CU 發行時的工具版本。 `azdata` 也可以獨立於伺服器版本之外發行，因此當您安裝最新的套件時，可能會取得較新的版本。 較新版本與先前發行的 CU 相容。

## <a name="how-to-install-updates"></a>如何安裝更新

若要安裝更新，請參閱[如何升級 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]](deployment-upgrade.md)。

## <a name="cu8-september-2020"></a><a id="cu8"></a> CU8 (2020 年 9 月)

SQL Server 2019 的累積更新 8 (CU8) 版本。

|套件版本 | 映像標籤 |
|-----|-----|
|15.0.4073.23 |[2019-CU8-ubuntu-16.04]

這個版本包含數個修正和一些增強功能。

### <a name="added-capabilities"></a>新增的功能

- 使用系統管理金鑰和憑證的 [SQL Server 巨量資料叢集待用加密](encryption-at-rest-concepts-and-configuration.md)。
   > [!CAUTION]
   > 這是 SQL Server BDC 待用加密的初始版本。 請檢閱下列文章： 
   > - [[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] 的安全性概念](concept-security.md)
   > - [待用加密概念和設定指南](encryption-at-rest-concepts-and-configuration.md)
- [Oracle Proxy 使用者](tutorial-query-oracle.md)對資料虛擬化案例的支援。

## <a name="cu6-july-2020"></a><a id="cu6"></a> CU6 (2020 年 7 月)

SQL Server 2019 的累積更新 6 (CU6) 版本。

|套件版本 | 映像標籤 |
|-----|-----|
|15.0.4053.23 |[2019-CU6-ubuntu-16.04]

此版本包含次要修正與增強功能。 下列文章包含與這些更新相關的資訊：

- [以 Active Directory 模式管理巨量資料叢集存取](manage-user-access.md)
- [在 Active Directory 模式中部署 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]](active-directory-deploy.md)
- [在 AKS 上以 Active Directory 模式部署 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]](active-directory-deployment-aks.md)
- [使用 Azure Kubernetes Service (AKS) 私人叢集部署巨量資料叢集](private-deploy.md)
- [限制 Azure Kubernetes Service (AKS) 私人叢集中巨量資料叢集 (BDC) 叢集的連出流量](private-restrict-egress-traffic.md)
- [部署高可用性 SQL Server 巨量資料叢集](deployment-high-availability.md)
- [設定 SQL Server 巨量資料叢集](configure-cluster.md)
- [在巨量資料叢集中設定 Apache Spark 和 Apache Hadoop](configure-spark-hdfs.md)
- [SQL Server 主要執行個體設定屬性](reference-config-master-instance.md)
- [Apache Spark 與 Apache Hadoop (HDFS) 設定屬性](reference-config-spark-hadoop.md)
- [Kubernetes RBAC 模型與其對管理 BDC 之使用者與服務帳戶的影響](kubernetes-rbac.md)

## <a name="cu5-june-2020"></a><a id="cu5"></a> CU5 (2020 年 6 月)

SQL Server 2019 的累積更新 5 (CU5) 版本。

|套件版本 | 映像標籤 |
|-----|-----|
|15.0.4043.16 |[2019-CU5-ubuntu-16.04]

### <a name="added-capabilities"></a>新增的功能

- 支援在 Red Hat OpenShift 上部署巨量資料叢集。 支援部署於內部部署版本 4.3 以上及 Azure Red Hat OpenShift 上的 OpenShift 容器平台。 請參閱[在 OpenShift 上部署 SQL Server 巨量資料叢集](deploy-openshift.md)
- 已更新 BDC 部署安全性模型，因此不再「需要」部署為 BDC 一部分的特殊權限容器。 針對所有使用 SQL Server 2019 CU5 的新部署，除了沒有特殊權限以外，容器還預設會以非根使用者的身分執行。 
- 已新增針對 Active Directory 網域部署多個巨量資料叢集的支援。
- [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] 有自己的語意版本，並獨立於伺服器。 用戶端與伺服器版本 azdata 之間的任何相依性都會被移除。 我們建議針對用戶端及伺服器使用最新版本，以確保您可受益於最新的增強功能及修正程式。
- 引進了兩個新的預存程序 (sp_data_source_objects 與 sp_data_source_table_columns)，以支援特定外部資料來源的自我檢查。 客戶可直接透過 T-SQL 來使用預存程式以進行結構描述探索，並查看哪些資料表可供虛擬化。 我們會在適用於 Azure Data Studio 的[資料虛擬化延伸模組](../azure-data-studio/extensions/data-virtualization-extension.md)外部資料表精靈中運用這些變更，這可供從 SQL Server、Oracle、MongoDB 及 Teradata 建立外部資料表。
- 新增持續在 Grafana 中執行自訂項目的支援。 在 CU5 之前，客戶會注意到在 Grafana 組態中進行的任何編輯，都會在 `metricsui` Pod (其裝載 Grafana 儀表板) 重新開機時遺失。 此問題已修正，且所有組態現在都會保存。 
- 已修正與使用 Telegraf (裝載於 `metricsdc` Pod 中) 來收集 Pod 及節點計量的 API 相關安全性問題。 因為此變更，Telegraf 現在需要服務帳戶、叢集角色和叢集繫結，才能擁有收集 Pod 及節點計量的必要權限。 如需詳細資料，請參閱[收集 Pod 和節點計量所需的叢集角色](kubernetes-rbac.md#cluster-role-required-for-pods-and-nodes-metrics-collection)。
- 新增兩個功能參數，以控制 Pod 和節點計量的收集。 如果正在使用不同的解決方案來監視 Kubernetes 基礎結構，則可將 control.json 部署組態檔中的 *allowNodeMetricsCollection* 和 *allowPodMetricsCollection* 設為 false，以關閉 Pod 與主機節點的內建計量集合。 在 OpenShift 環境中，因為收集 Pod 與節點計量需要特殊權限的功能，所以這些設定在內建部署設定檔中預設為 false。

## <a name="cu4-april-2020"></a><a id="cu4"></a> CU4 (2020 年 4 月)

SQL Server 2019 的累積更新 4 (CU4) 版本。 此版次的 SQL Server 資料庫引擎版本為 15.0.4033.1。

|套件版本 | 映像標籤 |
|-----|-----|
|15.0.4033.1 |[2019-CU4-ubuntu-16.04]

## <a name="cu3-march-2020"></a><a id="cu3"></a> CU3 (2020 年 3 月)

SQL Server 2019 的累積更新 3 (CU3) 版本。 此版本的 SQL Server 資料庫引擎版本為 15.0.4023.6。

|套件版本 | 映像標籤 |
|-----|-----|
|15.0.4023.6 |[2019-CU3-ubuntu-16.04]

### <a name="resolved-issues"></a>已解決的問題

SQL Server 2019 CU3 解決先前版本的下列問題。

- [使用私人存放庫進行部署](#deployment-with-private-repository)
- [升級可能會因為逾時而失敗](#upgrade-may-fail-due-to-timeout)

## <a name="cu2-february-2020"></a><a id="cu2"></a> CU2 (2020 年 2 月)

SQL Server 2019 的累積更新 2 (CU2) 版本。 此版本的 SQL Server 資料庫引擎版本為 15.0.4013.40。

|套件版本 | 映像標籤 |
|-----|-----|
|15.0.4013.40 |[2019-CU2-ubuntu-16.04]

## <a name="cu1-january-2020"></a><a id="cu1"></a> CU1 (2020年1月)

SQL Server 2019 的累積更新 1 (CU1) 版。 此版本的 SQL Server 資料庫引擎版本為 15.0.4003.23。

|套件版本 | 映像標籤 |
|-----|-----|
|15.0.4003.23|[2019-CU1-ubuntu-16.04]

## <a name="gdr1-november-2019"></a><a id="rtm"></a> GDR1 (2019 年 11 月)

SQL Server 2019 一般發行版本 1 (GDR1) - 引進 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-nover.md)] 的公開推出。 此版本的 SQL Server 資料庫引擎版本為 15.0.2070.34。

|套件版本 | 映像標籤 |
|-----|-----|
|15.0.2070.34|[2019-GDR1-ubuntu-16.04]

[!INCLUDE [sql-server-servicing-updates-version-15](../includes/sql-server-servicing-updates-version-15.md)]

## <a name="known-issues"></a>已知問題

### <a name="msdtc-capabilities-can-not-be-enabled-for-sql-server-master-instance-running-within-bdc"></a>無法為 BDC 內執行的 SQL Server 主要執行個體啟用 MSDTC 功能

- **受影響的版本**：所有巨量資料叢集部署組態 (不論版本為何)。

- **問題和對客戶的影響**︰當 SQL Server 在 BDC 內部署為 SQL Server 主要執行個體時，就無法啟用 MSDTC 功能。 此問題沒有因應措施。

### <a name="ha-sql-server-database-encryption-key-encryptor-rotation"></a>HA SQL Server 資料庫加密金鑰加密器輪替

- **受影響的版本**：所有巨量資料叢集 HA 部署 (不論版本為何)。

- **問題和對客戶的影響**︰使用隨 HA 部署的 SQL Server 時，已加密資料庫的憑證輪替會失敗。 在主要集區上執行下列命令時，將會出現錯誤訊息：
    ```
    ALTER DATABASE ENCRYPTION KEY
    ENCRYPTION BY SERVER
    CERTIFICATE <NewCertificateName>
    ```
    不會有任何影響，命令會失敗，但會使用先前的憑證保留目標資料庫加密。
    
### <a name="empty-livy-jobs-before-you-apply-cumulative-updates"></a>套用累積更新之前的空白 Livy 作業

- **受影響的版本**：上至 CU6 的所有版本。 已針對 CU8 解決。

- **問題和對客戶的影響**︰在升級期間，`sparkhead` 傳回 404 錯誤。

- **因應措施**：升級 BDC 之前，請確定沒有作用中的 Livy 工作階段或批次工作。 依照[從支援的版本升級](deployment-upgrade.md#upgrade-from-supported-release)底下的指示執行，以避免這種情況。 

   如果 Livy 在升級程序期間傳回 404 錯誤，請在這兩個 `sparkhead` 節點上重新啟動 Livy 伺服器。 例如：

   ```console
   kubectl -n <clustername> exec -it sparkhead-0/sparkhead-1 -c hadoop-livy-sparkhistory -- exec supervisorctl restart livy
   ```

### <a name="big-data-cluster-generated-service-accounts-passwords-expiration"></a>巨量資料叢集產生的服務帳戶密碼過期

- **受影響的版本**：與 Active Directory 整合的所有巨量資料叢集部署 (所有版本)

- **問題和對客戶的影響**︰在巨量資料叢集部署期間，工作流程會產生一組 [服務帳戶](active-directory-objects.md)。視網域控制站中設定的密碼到期原則而定，這些帳戶的密碼可能會過期 (預設為 42 天)。 目前沒有任何機制可輪替 BDC 中所有帳戶的認證，因此一旦符合到期期限，叢集將會變成無法運作。

- **因應措施**：將 BDC 服務帳戶的到期原則更新為網域控制站中的「密碼永不過期」。 如需這些帳戶的完整清單，請參閱[自動產生的 Active Directory 物件](active-directory-objects.md)。 此動作可以在到期時間之前或之後完成。 如果是後者，Active Directory 將會重新啟用過期的密碼。

### <a name="credentials-for-accessing-services-through-gateway-endpoint"></a>透過閘道端點存取服務的認證

- **受影響的版本**：從 CU5 開始部署的新叢集。

- **問題和對客戶的影響**︰針對使用 SQL Server 2019 CU5 部署的新巨量資料叢集，閘道使用者名稱不會是 **根**。 如果連線到閘道端點所用應用程式正在使用錯誤的認證，則將看到驗證錯誤。 這項變更是在大型資料叢集中以非根使用者身分執行應用程式的結果 (此為從 SQL Server 2019 CU5 版本開始的新預設行為，當使用 CU5 部署新的巨量資料叢集時，閘道端點其使用者名稱是以透過 **AZDATA_USERNAME** 環境變數傳遞的值為基礎。 這和控制器及 SQL Server 端點所用的使用者名稱相同。 這只會影響新的部署，使用任何舊版部署的現有巨量資料叢集會繼續使用 **根**。 當叢集使用 Active Directory 驗證進行部署時，不會對認證造成任何影響。 

- **因應措施**：Azure Data Studio 會以透明方式來控制對閘道變更的連線認證，以在 [物件總管] 中啟用 HDFS 瀏覽體驗。 您必須安裝[最新的 Azure Data Studio 版本](../azure-data-studio/download-azure-data-studio.md)，其中包含解決此使用案例所需的變更。
針對必須提供認證以透過閘道存取服務的其他案例 (例如，使用 [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] 登入、存取 Spark 的 Web 儀表板)，則必須確認使用正確的認證。 如果您的目標是在 CU5 之前部署的現有叢集，即使叢集升級至 CU5 之後，仍會繼續使用 **根** 使用者名稱來連線到閘道。 如果使用 CU5 組建部署新的叢集，則要提供對應至 **AZDATA_USERNAME** 環境變數的使用者名稱來登入。

### <a name="pods-and-nodes-metrics-not-being-collected"></a>未收集 Pod 及節點計量

- **受影響的版本**：使用 CU5 映像的新叢集及現有叢集

- **問題和對客戶的影響**︰因為與 `telegraf` 用來收集計量 Pod 及主機節點計量的 API 相關安全性修正，客戶可能會注意到計量未被收集。 這可能會發生在新的及現有 BDC 部署中 (升級至 CU5 之後)。 由於該修正程式，Telegraf 現在需要具有全叢集角色權限的服務帳戶。 部署會嘗試建立必要的服務帳戶與叢集角色，但如果部署叢集或執行升級的使用者沒有足夠權限，部署或升級仍會繼續進行 (且出現警告) 並成功，但不會收集 Pod 及節點計量。

- **因應措施**：您可要求系統管理員建立角色及服務帳戶 (在部署或升級前後)，以及會使用這些角色及帳戶的 BDC。 [本文](kubernetes-rbac.md#cluster-role-required-for-pods-and-nodes-metrics-collection)描述如何建立所需的成品。

### <a name="azdata-bdc-copy-logs-command-failure"></a>`azdata bdc copy-logs` 命令失敗

- **受影響的版本**：[!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] 版本 *20.0.0*

- **問題和對客戶的影響**︰實作 *copy-logs* 命令的前提，是基於假設 `kubectl` 用戶端工具安裝在發出命令的用戶端電腦上。 如果您是從僅安裝 `oc` 工具的用戶端，以針對安裝在 OpenShift 上的 BDC 叢集發出命令，則將收到錯誤：*收集記錄時發生錯誤：[WinError 2] 系統找不到指定的檔案*。

- **因應措施**：在相同的用戶端電腦上安裝 `kubectl` 工具，然後重新發出 `azdata bdc copy-logs` 命令。 如需了解如何安裝 `kubectl` 的指示，請參閱[這裡](deploy-big-data-tools.md)。

### <a name="deployment-with-private-repository"></a>使用私人存放庫進行部署

- **受影響的版本**：GDR1、CU1、CU2。 已針對 CU3 解決。

- **問題和對客戶的影響**︰從私人存放庫升級具有特定需求

- **因應措施**：如果您使用私人存放庫來預先提取要部署或升級 BDC 的映像，請確定目前的組建映像與目標組建映像都在私人存放庫中。 這可確保成功的復原 (如果有必要)。 此外，如果您在原始部署之後變更私人存放庫的認證，請在升級之前，先更新 Kubernetes 中對應的祕密。 [!INCLUDE [azure-data-cli-azdata](../includes/azure-data-cli-azdata.md)] 不支援透過 `AZDATA_PASSWORD` 與 `AZDATA_USERNAME` 環境變數來更新認證。 使用 [`kubectl edit secrets`](https://kubernetes.io/docs/concepts/configuration/secret/#editing-a-secret) 更新祕密。 

不支援使用不同的存放庫來進行目前與目標組建升級。

### <a name="upgrade-may-fail-due-to-timeout"></a>升級可能會因為逾時而失敗

- **受影響的版本**：GDR1、CU1、CU2。 已針對 CU3 解決。

- **問題和對客戶的影響**︰升級可能會因為逾時而失敗。

   下列程式碼顯示失敗看起來的樣子：

   ```
   >azdata.EXE bdc upgrade --name <mssql-cluster>
   Upgrading cluster to version 15.0.4003

   NOTE: Cluster upgrade can take a significant amount of time depending on
   configuration, network speed, and the number of nodes in the cluster.

   Upgrading Control Plane.
   Control plane upgrade failed. Failed to upgrade controller.
   ```

   當您在 Azure Kubernetes Service (AKS) 中升級 BDC 時，很可能會發生此錯誤。

- **因應措施**：增加升級的逾時。 

   若要增加升級的逾時，請編輯升級設定對應。 編輯升級設定對應：

   1. 執行以下命令：

      ```bash
      kubectl edit configmap controller-upgrade-configmap
      ```

   2. 編輯下列欄位：

       **`controllerUpgradeTimeoutInMinutes`** 指定等待控制器或控制器資料庫完成升級的分鐘數。 預設值為 5。 更新為至少 20。

       **`totalUpgradeTimeoutInMinutes`** ：指定控制器與控制器資料庫完成升級的合併時間量 (`controller` + `controllerdb` 升級)。 預設值為 10。 更新為至少 40。

       **`componentUpgradeTimeoutInMinutes`** ：指定升級必須完成之每個後續階段的時間量。 預設值為 30。 更新為 45。

   3. 儲存並結束

   下面的 python 指令碼是設定逾時的另一種方式：

   ```python
   from kubernetes import client, config
   import json

   def set_upgrade_timeouts(namespace, controller_timeout=20, controller_total_timeout=40, component_timeout=45):
       """ Set the timeouts for upgrades

       The timeout settings are as follows

       controllerUpgradeTimeoutInMinutes: sets the max amount of time for the controller
           or controllerdb to finish upgrading

       totalUpgradeTimeoutInMinutes: sets the max amount of time to wait for both the
           controller and controllerdb to complete their upgrade

       componentUpgradeTimeoutInMinutes: sets the max amount of time allowed for
           subsequent phases of the upgrade to complete
       """
       config.load_kube_config()

       upgrade_config_map = client.CoreV1Api().read_namespaced_config_map("controller-upgrade-configmap", namespace)

       upgrade_config = json.loads(upgrade_config_map.data["controller-upgrade"])

       upgrade_config["controllerUpgradeTimeoutInMinutes"] = controller_timeout

       upgrade_config["totalUpgradeTimeoutInMinutes"] = controller_total_timeout

       upgrade_config["componentUpgradeTimeoutInMinutes"] = component_timeout

       upgrade_config_map.data["controller-upgrade"] = json.dumps(upgrade_config)

       client.CoreV1Api().patch_namespaced_config_map("controller-upgrade-configmap", namespace, upgrade_config_map)
   ```

### <a name="livy-job-submission-from-azure-data-studio-ads-or-curl-fail-with-500-error"></a>從 Azure Data Studio (ADS) 或 curl 提交 Livy 作業失敗，並出現 500 錯誤

- **問題和對客戶的影響**︰在 HA 設定中，會使用多個複本來設定 Spark 共用資源 `sparkhead`。 在此情況下，您可能會遇到從 Azure Data Studio (ADS) 或 `curl` 提交 Livy 作業失敗。 若要確認，則對任何 `sparkhead` Pod 執行 `curl` 都會導致拒絕連線。 例如，`curl https://sparkhead-0:8998/` 或 `curl https://sparkhead-1:8998` 會傳回 500 錯誤。

   在下列案例中會發生這個情況：

   - 每個 Zookeeper 執行個體的 Zookeeper Pod 或處理序都會重新啟動幾次。
   - 當 `sparkhead` Pod 與 Zookeeper Pod 之間的網路連線不可靠時。

- **因應措施**：重新啟動這兩部 Livy 伺服器。

   ```bash
   kubectl -n <clustername> exec sparkhead-0 -c hadoop-livy-sparkhistory supervisorctl restart livy
   ```

   ```bash
   kubectl -n <clustername> exec sparkhead-1 -c hadoop-livy-sparkhistory supervisorctl restart livy
   ```

### <a name="create-memory-optimized-table-when-master-instance-in-an-availability-group"></a>當主要執行個體在可用性群組中時，建立記憶體最佳化資料表

- **問題和對客戶的影響**︰您不能使用公開用來連線到可用性群組資料庫 (接聽程式) 的主要端點來建立記憶體最佳化資料表。

- **因應措施**：當 SQL Server 主要執行個體是可用性群組設定時，若要建立記憶體最佳化資料表，請 [連線到 SQL Server 執行個體](deployment-high-availability.md#instance-connect)，公開端點，連線到 SQL Server 資料庫，然後在使用新連線所建立的工作階段中建立記憶體最佳化資料表。

### <a name="insert-to-external-tables-active-directory-authentication-mode"></a>插入外部資料表的 Active Directory 驗證模式

- **問題和對客戶的影響**︰當 SQL Server 主要執行個體處於 Active Directory 驗證模式時，查詢只會從外部資料表 (其中至少有一個外部資料表位於儲存集區) 進行選取，並插入另一個外部資料表中，而此查詢會傳回：

   ```
   Msg 7320, Level 16, State 102, Line 1
   Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "SQLNCLI11". Only domain logins can be used to query Kerberized storage pool.
   ```

- **因應措施**：使用下列其中一種方式來修改查詢。 將儲存集區資料表聯結至本機資料表，或先插入本機資料表，然後從本機資料表讀取以插入資料集區。

### <a name="transparent-data-encryption-capabilities-can-not-be-used-with-databases-that-are-part-of-the-availability-group-in-the-sql-server-master-instance"></a>透明資料加密功能無法搭配屬於 SQL Server 主要執行個體中可用性群組一部分的資料庫使用

- **問題和對客戶的影響**︰在 HA 設定中，已啟用加密的資料庫無法在容錯移轉之後使用，因為在每個複本上用來加密的主要金鑰都不同。 

- **因應措施**：此問題目前沒有任何因應措施。 建議您在修正程式準備就緒之前，不要在此設定中啟用加密。

## <a name="next-steps"></a>後續步驟

如需 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] 的詳細資訊，請參閱[什麼是 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]？](big-data-cluster-overview.md)
