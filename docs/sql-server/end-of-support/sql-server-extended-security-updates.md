---
title: 什麼是延伸安全性更新？
description: 了解如何使用 SQL Server 登錄，為您終止支援和生命週期結束的 SQL Server 產品 (例如 SQL Server 2008 和 SQL Server 2008 R2) 取得延伸安全性更新。
ms.custom: ''
ms.date: 11/24/2020
ms.prod: sql
ms.technology: install
ms.topic: conceptual
author: cawrites
ms.author: chadam
ms.reviewer: pmasl
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 94939adb2934e16ed7d1c81c33ebab4631c4b114
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172510"
---
# <a name="what-are-extended-security-updates-for-sql-server"></a>什麼是 SQL Server 的延伸安全性更新？
[!INCLUDE [SQL Server Windows Only - ASDBMI ](../../includes/applies-to-version/sql-windows-only-asdbmi.md)]

本文提供資訊，讓您了解如何使用 SQL Server 登錄服務來接收 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 的延伸安全性更新。 如需其他選項的詳細資訊，請參閱[終止支援選項](sql-server-end-of-life-overview.md)。 

## <a name="overview"></a>概觀
您可以在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 已達其支援週期的尾端之後，選擇為您的伺服器註冊延伸安全性更新 (ESU) 訂閱，並保持受保護狀態長達三年，直到您準備好升級至較新版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或移轉到 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。 此訂閱有兩種方式：
-  可以為您的內部部署或託管環境伺服器購買。
-  將內部部署伺服器移轉至 Azure 虛擬機器時，預設為免費和啟用。 然後，您可以在 Azure 入口網站中，使用 **SQL Server 登錄** 服務來註冊終止支援的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，並在有更新可用時予以下載。 

Microsoft 建議在有 ESU 修補程式可用時立即套用，以確保您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體受到保護。 如需 ESU 的詳細資訊，請參閱 [ESU 常見問題集頁面](https://www.microsoft.com/cloud-platform/extended-security-updates)。

> [!IMPORTANT]
> [SQL Server 2008 和 SQL Server 2008 R2 的延伸支援已於 2019 年 7 月 10 日終止](https://www.microsoft.com/cloud-platform/windows-sql-server-2008)。 針對這些版本，請考慮使用本文所述的延伸安全性更新或其他移轉選項。 如需詳細資訊，請參閱[終止支援選項](sql-server-end-of-life-overview.md)。

## <a name="what-are-extended-security-updates"></a>什麼是延伸安全性更新
[!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 的延伸安全性更新 (ESU) 包含安全性更新的佈建，適用於已購買延伸支援更新訂閱的客戶。

一旦發現資訊安全弱點，且 [Microsoft 安全回應中心 (MSRC)](https://portal.msrc.microsoft.com) 將其分級為 **重大** 時，就可以使用 ESU (**如有需要**)。 因此，不會定期發行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ESU。

ESU 不包含：
- 新功能
- 功能改善
- 客戶要求的修正

### <a name="support"></a>支援
ESU 不包含技術支援，但您可以使用有效的支援合約 (例如[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)或 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] / [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 的頂級/統一支援)，以取得 ESU 所涵蓋工作負載的技術支援 (如果您選擇保持內部部署)。 或者，如果您想要在 Azure 上裝載，則可使用 Azure 支援方案來取得技術支援。 

  > [!NOTE]
  > Microsoft 無法對 ESU 訂閱未涵蓋的 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 執行個體 (在內部部署和主控環境中) 提供技術支援。 

## <a name="esu-availability-and-deployment"></a>ESU 可用性和部署
ESU 可供在 Azure、內部部署或主控環境中執行其工作負載的客戶使用。

### <a name="azure-virtual-machines"></a>Azure 虛擬機器
如果您將工作負載移轉到 Azure 虛擬機器 (IaaS)，則將可以在終止支援後存取長達三年的 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 延伸安全性更新，除了執行虛擬機器的費用以外，**沒有額外費用**。 客戶不需要軟體保證，即可在 Azure 中收到延伸安全性更新。 

當虛擬機器設定為使用 [自動修補](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-patching)時，在 **Windows Server 2008 R2 和更新版本** 上執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 Azure 虛擬機器會透過現有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 更新通道來自動接收 ESU。

在 **Windows Server 2008** 上執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 Azure 虛擬機器 (VM)，或 **「尚未」  設定使用 [自動修補](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-patching)** 的 VM，將需要如 [內部部署或託管環境](#on-premises-or-hosted-environments)一節中所述，手動下載並部署 ESU 修補程式。

### <a name="on-premises-or-hosted-environments"></a>內部部署或託管環境
如果您有軟體保證，則可在終止支援日期後，根據 Enterprise 合約 (EA)、Enterprise Subscription 合約 (EAS)、伺服器和雲端註冊 (SCE) 或註冊教育解決方案 (EES) 購買長達三年的延伸安全性更新 (ESU)。 您可以只針對需要涵蓋的伺服器購買 ESU。 您可以直接向 Microsoft 或 Microsoft 授權合作夥伴購買 ESU。 

ESU 合約涵蓋的客戶必須遵循下列步驟來下載和部署 ESU 修補程式：
-  向 **[SQL Server 登錄](#create-sql-server-registry)** [註冊符合資格的執行個體](#register-instances-for-esus)。 
-  註冊之後，一旦發行 ESU 修補程式時，Azure 入口網站就會提供下載套件的下載連結。 
-  您可以將下載的套件手動部署到內部部署或託管環境，或透過組織中使用的任何更新協調流程解決方案來部署，例如 Microsoft Endpoint Configuration Manager (前稱為 System Center Configuration Manager)。 

> [!NOTE]
> 這也是客戶針對未設定接收自動更新的 Azure Stack 和 Azure 虛擬機器所需遵循的程序。

如需詳細資訊，請參閱[延伸安全性更新常見問題集](https://www.microsoft.com/cloud-platform/extended-security-updates)。 

## <a name="create-sql-server-registry"></a>建立 SQL Server 登錄
若要註冊已啟用 ESU 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，您必須先在 Azure 入口網站中建立 SQL Server 登錄。 

> [!IMPORTANT]
> 當執行設定為[自動更新](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-patching)的 Azure 虛擬機器時，則不需要為 ESU 註冊 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 

若要建立 SQL Server 登錄，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 選取 [建立資源]  選項。 
1. 在搜尋方塊中鍵入 `SQL Server registry`。  
1. 選擇由 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 所發佈的 [SQL Server 登錄]  選項，然後選取 [建立]  。 

   ![顯示如何建立 SQL Server 登錄的 Auzre 入口網站螢幕擷取畫面。](media/sql-server-extended-security-updates/sql-server-registry-service.png)

1. 在 [專案詳細資料]  下，從下拉式清單選擇您的訂用帳戶。 然後選擇現有的 [資源群組]  ，或選取 [新建]  為新 SQL Server 登錄服務建立新的資源群組。 
1. 在 [服務詳細資料]  下，提供新 **SQL Server 登錄** 資源的名稱和區域： 

   ![顯示 [基本] 索引標籤的 SQL Server 登錄螢幕擷取畫面。](media/sql-server-extended-security-updates/create-new-sql-server-registry.png)

1. 選取 [檢閱 + 建立]  ，以檢閱 **SQL Server 登錄** 的詳細資料。 通過驗證之後，選取 [建立]  。 

## <a name="register-instances-for-esus"></a>針對 ESU 註冊執行個體

部署 **SQL Server 登錄** 資源之後，您可以選擇註冊 [單一](#single-sql-server-instance) [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，也可以 [大量](#multiple-sql-server-instances-in-bulk)註冊多個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 您必須在 SQL Server 登錄範圍內註冊至少一個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，才能下載任何 ESU 套件。 

### <a name="single-sql-server-instance"></a>單一 SQL Server 執行個體

若要註冊單一 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 移至 [SQL Server 登錄]  資源。 
1. 從 [概觀]  窗格選取 [+ 註冊]  ： 

   ![選擇 [註冊] 以註冊單一 SQL Server 執行個體](media/sql-server-extended-security-updates/register-single-sql-server-instance.png)

1. 如下表所述提供必要的資訊，然後選取 [註冊]  ： 

   |**ReplTest1**| **說明**|
   | :-------| :------------- |
   | **執行個體** | 輸入命令 `SELECT @@SERVERNAME` 的輸出，例如 `MyServer\Instance01`。 | 
   | **SQL 版本** | 從下拉式清單選取 2008 或 2008 R2。 | 
   | **版本(Edition)** | 從下拉式清單選取適用的版本：Datacenter、Developer (如果購買 ESU 則可免費部署)、Enterprise、Standard、Web、Workgroup。 | 
   | **核心** | 輸入此執行個體的核心數目 | 
   | **主機類型** | 從下拉式清單選取適用的主機類型：虛擬機器 (內部部署)、實體伺服器 (內部部署)、Azure 虛擬機器、Amazon EC2、Google Compute Engine 等。 |
   | **訂用帳戶 ID**<sup>1</sup> | 輸入要在其中建立 VM 的訂用帳戶 ID。  |
   | **資源群組**<sup>1</sup> | 輸入要在其中建立 VM 的資源群組。  | 
   | **Azure VM 名稱**<sup>1</sup>  | 輸入 VM 資源名稱。  | 
   | **Azure VM 作業系統**<sup>1</sup> | 從下拉式清單選取適用的 Windows Server 作業系統版本。 | 

   <sup>1</sup> 只有 Azure 虛擬機器才需要。 

所新註冊 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體現在會顯示在 [概觀]  窗格的 [註冊 SQL Server 執行個體]  區段中： 

![已註冊 SQL Server 執行個體](media/sql-server-extended-security-updates/registered-sql-instance.png)

註冊 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之後，[安全性更新]  區段就會變成可用。 任何可用的 ESU 都會張貼在該處。 

### <a name="multiple-sql-server-instances-in-bulk"></a>大量的多個 SQL Server 執行個體

您可以藉由上傳 .CSV 檔案來大量註冊多個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 一旦您的 [.CSV 檔案已正確格式化](#formatting-requirements-for-csv-file)，即可遵循下列步驟，向 SQL Server 登錄資源大量註冊 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 移至 [SQL Server 登錄]  資源。 
1. 從 [概觀]  窗格選取 [大量註冊]  ：  

   ![選擇 [大量註冊] 以註冊多個 SQL Server 執行個體](media/sql-server-extended-security-updates/bulk-register-sql-server-instances.png)

1. 選取檔案圖示以瀏覽至 .CSV 檔案位置。 選取 .CSV 檔案。 然後選取 [註冊]  以上傳檔案，並註冊多個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 

   ![上傳 CSV 檔案以註冊多個 SQL Server 執行個體](media/sql-server-extended-security-updates/upload-csv-file-for-bulk-registration.png)

### <a name="formatting-requirements-for-csv-file"></a>CSV 檔案的格式需求
- 以逗號分隔值
- 不可使用單引號或雙引號括住值
- 資料行名稱不區分大小寫，但必須 **命名** 如下： 
  - NAME
  - version
  - edition
  - cores
  - hostType
  - subscriptionID<sup>1</sup>
  - resourceGroup<sup>1</sup>
  - azureVmName<sup>1</sup>
  - AzureVmOS<sup>1</sup>

<sup>1</sup> 只有 Azure 虛擬機器才需要。 

#### <a name="csv-example-1---on-premises"></a>CSV 範例 1 - 內部部署

針對內部部署 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，CSV 檔案應該如下所示： 

```csv
name,version,edition,cores,hostType
Server1\SQL2008,2008,Enterprise,12,Physical Server
Server1\SQL2008R2,2008 R2,Enterprise,12,Physical Server
Server2\SQL2008R2,2008 R2,Enterprise,24,Physical Server
Server3\SQL2008R2,2008 R2,Enterprise,12,Virtual Machine
Server4\SQL2008,2008,Developer,8,Physical Server  
```

如需 CSV 檔案範例，請參閱 [MyPhysicalServers.csv](https://github.com/microsoft/sql-server-samples/blob/master/samples/manage/sql-server-extended-security-updates/scripts/MyPhysicalServers.csv)。

#### <a name="csv-example-2---azure-vm"></a>CSV 範例 2 - Azure VM

針對 Azure 虛擬機器 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，CSV 檔案應該如下所示： 

```csv
name,version,edition,cores,hostType,subscriptionId,resourceGroup,azureVmName,azureVmOS    
ProdServerUS1\SQL01,2008 R2,Enterprise,12,Azure Virtual Machine,61868ab8-16d4-44ec-a9ff-f35d05922847,RG,VM1,2012    
ProdServerUS1\SQL02,2008 R2,Enterprise,24,Azure Virtual Machine,61868ab8-16d4-44ec-a9ff-f35d05922847,RG,VM1,2012    
ServerUS2\SQL01,2008,Enterprise,12,Azure Virtual Machine,61868ab8-16d4-44ec-a9ff-f35d05922847,RG,VM2,2012 R2    
ServerUS2\SQL02,2008,Enterprise,8,Azure Virtual Machine,61868ab8-16d4-44ec-a9ff-f35d05922847,RG,VM2,2012 R2    
SalesServer\SQLProdSales,2008 R2,Developer,8,Azure Virtual Machine,61868ab8-16d4-44ec-a9ff-f35d05922847,RG,VM3,2008 R2  
```

如需 Azure VM 目標 CSV 檔案範例，請參閱 [MyAzureVMs.csv](https://github.com/microsoft/sql-server-samples/blob/master/samples/manage/sql-server-extended-security-updates/scripts/MyAzureVMs.csv)。 


> [!TIP]
> 如需可在 .CSV 檔案中產生必要 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體註冊資訊的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 和 PowerShell 範例指令碼，請參閱 [ESU 註冊指令碼範例](https://github.com/microsoft/sql-server-samples/blob/master/samples/manage/sql-server-extended-security-updates/scripts.md) (英文)。 

## <a name="download-esus"></a>下載 ESU

向 SQL Server 登錄服務註冊您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之後，您就可以使用 Azure 入口網站中的連結來下載延伸安全性更新套件 (如果適用的話)。 

若要下載 ESU，請遵循下列步驟： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 移至 [SQL Server 登錄]  資源。 
1. 在瀏覽窗格中選取 [安全性更新]  。 

   ![檢查 [安全性更新] 窗格中的可用更新](media/sql-server-extended-security-updates/security-updates-sql-registry.png)

1. 從這裡下載安全性更新 (如果適用的話)。 

## <a name="supported-regions-and-data-residency"></a>支援的區域與資料落地

**SQL Server 登錄** 服務 (預覽) 可在 Azure 區域的子集中找到。 下表顯示支援的區域與每個區域中的資料落地類型。

| **區域** | **資料存留處** |
|:--|:--|
|澳大利亞東部|地理區域|
|澳大利亞東南部|地理區域|
|加拿大中部|地理區域|
|法國中部|地理區域|
|日本東部|地理區域|
|日本西部|地理區域|
|南韓中部|地理區域|
|南韓南部|地理區域|
|美國中北部|地理區域|
|北歐|地理區域|
|美國中南部|地理區域|
|東南亞|單一區域|
|印度南部|地理區域|
|南非北部|地理區域|
|英國南部|地理區域|
|英國西部|地理區域|
|美國西部|地理區域|
|美國東部|地理區域|
|美國中部|地理區域|
|東亞|地理區域|
|西歐|地理區域|
|美國中西部|地理區域|
|美國西部 2|地理區域|
|美國東部 2|地理區域|

在具有地理落地的區域中，SQL 登錄服務會在異地備援儲存體帳戶 (GRS) 中維護資料備份。  在具有單一區域落地的區域中，SQL 登錄服務會在區域備援儲存體帳戶 (ZRS) 中維護資料備份。 如需詳細資訊，請參閱[信任中心](https://azuredatacentermap.azurewebsites.net/)。

## <a name="configure-regional-redundancy"></a>設定區域備援 

需要針對其 **SQL Server 登錄** 區域備援的客戶可以在兩個不同區域中建立註冊資料。 客戶接著可以根據 **SQL Server 登錄** 服務可用性，從任一區域下載安全性更新。 

針對區域備援，必須在兩個不同的區域中建立 **SQL Server registry** 服務，而且您的 SQL Server 清查必須在這兩個服務之間分割。 如此一來，您的 SQL Server 其中一半會向一個區域的登錄服務中註冊，然後您的 SQL Server 其中另一半會向另一個區域中的登錄服務註冊。 

若要設定區域備援，請遵循這些步驟：

1. 將您的 SQL Server 2008 或 2008 R2 清查分割成兩個檔案，例如 upload1.csv 和 upload2.csv。 
  
   :::image type="content" source="media/sql-server-extended-security-updates/two-upload-files-for-regional-redundancy.png" alt-text="範例上傳檔案":::

1. 在一個區域中建立第一個 **SQL Server 登錄** 服務，然後向其大量註冊其中一個 CSV 檔案。 例如，在 **美國西部** 區域中建立第一個 **SQL Server 登錄** 服務，然後使用 upload1.csv 檔案來大量註冊您的 SQL 伺服器。 
1. 在第二個區域中建立第二個 **SQL Server 登錄** 服務，然後向其大量註冊另一個 CSV 檔案。 例如，在 **美國東部** 區域中建立第二個 **SQL Server 登錄** 服務，然後使用 upload2.csv 檔案大量註冊您的 SQL Server。 


當您的資料註冊了兩個不同的 **SQL Server 登錄** 資源之後，您就能夠根據服務可用性從任一區域下載安全性更新。 


## <a name="faq"></a>常見問題集

您可以在[延伸安全性更新常見問題集](https://www.microsoft.com/cloud-platform/extended-security-updates)中，找到有關延伸安全性更新的一般常見問題集。 以下列出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 特定的常見問題集。 

**SQL Server 2008 和 2008 R2 的支援何時終止？**

[!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 的終止支援日期為 2019 年 7 月 9 日。 

**終止支援是什麼意思？**

Microsoft 週期原則為 Business 和 Developer 產品 (例如 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 Windows Server)，提供 10 年的支援 (5 年的主要支援及 5 年的延伸支援)。 根據原則，在延伸支援期間結束後，不會有修補程式或安全性更新，這可能會造成安全性與合規性問題，並使得客戶的應用程式和業務暴露在嚴重的安全性風險下。

**哪些版本的 SQL Server 符合延伸安全性更新的資格？**

Enterprise、Datacenter、Standard、Web 和 Workgroup 版的 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 都符合延伸安全性更新 (x86 和 x64 版) 的資格。 

**何時可以使用延伸安全性更新供應項目？**

延伸安全性更新現在可供購買，並可向 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 或 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 授權合作夥伴訂購。 延伸安全性更新會在終止支援日期後開始提供 (如果適用的話)。 想要移轉到 Azure 的客戶可以立即這麼做。 

**延伸安全性更新包含什麼內容？** 

延伸安全性更新包含 [Microsoft 安全回應中心 (MSRC)](https://portal.msrc.microsoft.com/) 分級為 **重大** 的安全性更新佈建和佈告欄，自 2019 年 7 月 9 日起最多提供三年。 如果適用的話，就會散發延伸安全性更新。 延伸安全性更新不包含技術支援，但您可以使用其他 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 支援方案來協助解決延伸安全性更新所涵蓋工作負載的 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 問題。 延伸安全性更新不包含新功能、功能改善或客戶要求的修正。 不過，[!INCLUDE[msCoName](../../includes/msconame-md.md)] 可能會包含經判定為必要的非安全性問題修正。

**為什麼 SQL Server 2008 和 2008 R2 的延伸安全性更新只會提供「重大」更新？**

針對過去的終止支援事件，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 只會提供重大安全性更新，以符合企業客戶的合規性準則。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不會提供一般的每月安全性更新。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 只會針對 MSRC 佈告欄視需要提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安全性更新 (GDR)，其中 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 是受影響的產品。
如果在某些情況下無法提供新的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 重要更新，且該更新是由客戶而不是 MSRC 判定為重大，則我們會依個案與客戶合作，以建議適當的緩和措施。

**哪些授權方案符合延伸安全性更新的資格？**

軟體保證客戶可以根據 Enterprise 合約 (EA)、Enterprise Subscription 合約 (EAS)、伺服器和雲端註冊 (SCE) 或註冊教育解決方案 (EES) 來購買內部部署延伸安全性更新。 軟體保證不一定要在相同的註冊上。

**SQL Server 客戶必須執行最新的 Service Pack，才能受益於延伸安全性更新嗎？**

是的，客戶必須執行具有最新 Service Pack 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 Windows Server 2008 和 2008 R2，才能套用延伸安全性更新。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 只會產生可套用至最新 Service Pack 的更新。

**沒有軟體保證的 SQL Server 客戶有哪些選項？** 

沒有軟體保證的客戶可以改為選擇移轉到 Azure，以存取延伸安全性更新。 如果工作負載不定，建議客戶透過隨用隨付在 Azure 上進行移轉，以便隨時相應增加或減少。 如果工作負載可以預測，建議客戶透過主訂閱和保留執行個體來移轉到 Azure。
  
**此供應項目也適用於 SQL Server 2005 嗎？**

否。 針對這些較舊版本，建議升級至最新版本，但客戶可以升級至 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 或 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 版本來利用此供應項目。

**我可以在 Azure 上部署全新的 SQL Server 2008 或 2008 R2 執行個體，並仍取得延伸安全性更新嗎？**

是的，客戶可以在 Azure 虛擬機器上啟動新的 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 或 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 執行個體，並存取延伸安全性更新。

**我可以在終止支援日期後取得 SQL Server 2008 或 2008 R2 的內部部署技術支援，而不需要購買延伸安全性更新嗎？**

否。 如果客戶具有 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 或 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)]，並選擇在移轉期間保持內部部署而不需要延伸安全性更新，則無法記錄支援票證，即使具有支援方案也一樣。 不過，如果他們移轉到 Azure，則可以使用其 Azure 支援方案取得支援。

**如果 SQL Server 2008 和 2008 R2 客戶想要自備授權 (BYOL)，是否必須具有軟體保證範圍？**

是的，客戶必須具有軟體保證，才能將 Azure 虛擬機器上 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 BYOL 方案視為授權行動性方案的一部分來使用。 若客戶沒有軟體保證，建議客戶移至 Azure SQL 受控執行個體，以使用其 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 環境。 客戶也可以移轉到隨用隨付的 Azure 虛擬機器。 依核心授權 SQL 的軟體保證客戶可以選擇使用 Azure Hybrid Benefit (AHB) 來移轉到 Azure。

Azure SQL 受控執行個體是一項 Azure 服務，提供與內部部署 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 幾近 100% 的相容性。 受控執行個體提供內建的高可用性/災害復原功能，以及智慧型效能功能和即時調整的能力。 受控執行個體也提供不需要手動進行安全性修補和升級的無版本體驗。 如需 BYOL 方案的詳細資訊，請參閱 Azure 定價指導方針頁面。

**客戶有哪些選項可在 Azure 中執行 SQL Server？**

客戶可以將舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 環境移至 Azure SQL 受控執行個體，這是一項完全受控的資料平台服務 (PaaS)，提供「無版本」選項，因此不必擔心終止支援日期；客戶也可以移至 Azure 虛擬機器，來存取安全性更新。 移轉後資料庫會保留其與舊版系統的相容性。 如需詳細資訊，請參閱[相容性憑證](../../database-engine/install-windows/compatibility-certification.md)。

延伸安全性更新會在終止支援日期 2019 年 7 月 9 日後的未來三年，提供給 Azure 虛擬機器中的 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)]。 對於想要從 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 升級的客戶，我們將支援 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的所有後續版本。 針對 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 到 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]，客戶必須在最新支援的 Service Pack 上。 從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始，建議客戶在最新的累積更新上。 請注意，從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始，不會提供 Service Pack，只會提供累積更新和一般發行版本 (GDR)。

Azure SQL 受控執行個體是 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 中的執行個體範圍部署選項，其提供最廣泛的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 引擎相容性和原生虛擬網路 (VNET) 支援，讓您不必變更應用程式，即可將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫移轉到受控執行個體。 其結合豐富的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 介面區，以及智慧型、完全受控服務的營運和財務優勢。 利用新的 Azure 資料庫移轉服務，在最少或沒有應用程式程式碼變更的情況下，將 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 移至 Azure SQL 受控執行個體。

**客戶可以針對 SQL Server 2008 和 2008 R2 版本使用 Azure Hybrid Benefit 嗎？**

是的，具有使用中軟體保證或對等主訂閱的客戶可以使用 Azure Hybrid Benefit，利用現有的內部部署授權投資，以折扣價格在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 和 Azure 虛擬機器上執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。

**客戶可以在 Azure Government 區域取得免費的延伸安全性更新嗎？**

是的，您可以在 Azure Government 區域的 Azure 虛擬機器上取得延伸安全性更新。

**客戶可以在 Azure Stack 取得免費的延伸安全性更新嗎？**

是的，客戶可以將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、Windows Server 2008 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 移轉到 Azure Stack，並在終止支援日期後免費收到延伸安全性更新。

**針對使用共用儲存體的 2008 和 2008 R2 SQL 叢集客戶，移轉到 Azure 的指導方針為何？**

Azure 目前不支援共用儲存體叢集。 如需如何在 Azure 上設定高可用性 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的建議，請參閱 [SQL Server 高可用性指南](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr)。

**客戶可以搭配協力廠商主機服務提供者使用 SQL Server 的延伸安全性更新嗎？**

如果客戶將其 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 環境移至其他雲端提供者的 PaaS 實作，則無法使用延伸安全性更新。 如果客戶想要移至虛擬機器 (IaaS)，則可以透過軟體保證對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 運用授權行動性進行移動，並向 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 購買延伸安全性更新，以手動將修補程式套用至已授權 SPLA 主機服務提供者伺服器上 VM (IaaS) 中執行的 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)] 執行個體。 不過，Azure 中的免費更新是更具吸引力的供應項目。

**提升 Azure 虛擬機器中 SQL Server 效能的最佳做法為何？** 

如需如何將 Azure 虛擬機器上 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 效能最佳化的建議，請參閱 [SQL Server 最佳化指南](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)。 

## <a name="see-also"></a>另請參閱

- [SQL Server 2008/2008 R2 週期頁面](https://support.microsoft.com/lifecycle/search?alpha=sql%20server%202008)
- [SQL Server 2008/2008 R2 終止支援頁面](https://aka.ms/sqleos)
- [延伸安全性更新常見問題集 (FAQ)](https://aka.ms/sqleosfaq)
- [Microsoft 安全性回應中心 (MSRC)](https://portal.msrc.microsoft.com/security-guidance/summary)
- [使用 Azure 自動化來管理 Windows 更新](/azure/automation/update-management/overview)
- [SQL Server VM 自動修補](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-patching)
- [Microsoft 資料移轉指南](https://datamigration.microsoft.com/)
- [Azure Migrate：將目前 SQL Server 2008/2008 R2 移至 Azure VM 的隨即轉移選項](https://azure.microsoft.com/services/azure-migrate/)
- [適用於 SQL 移轉的雲端採用架構](/azure/cloud-adoption-framework/migrate/expanded-scope/sql-migration)
- [GitHub 上的 ESU 相關指令碼](https://github.com/microsoft/sql-server-samples/tree/master/samples/manage/sql-server-extended-security-updates/scripts)

