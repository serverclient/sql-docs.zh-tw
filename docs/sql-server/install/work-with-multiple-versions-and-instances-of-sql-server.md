---
title: 使用多個版本與執行個體
description: 您可安裝多個 SQL Server 執行個體，或在已安裝舊版 SQL Server 的電腦上安裝 SQL Server。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
helpviewer_keywords:
- concurrent installations [SQL Server]
- versions [SQL Server], multiple
- side-by-side installations [SQL Server]
- multiple SQL Server component versions
- installing SQL Server, side-by-side installations
- Setup [SQL Server], side-by-side installations
- 64-bit edition [SQL Server]
- 32-bit edition [SQL Server]
- editions [SQL Server], side-by-side installations
ms.assetid: 93acefa8-bb41-4ccc-b763-7801f51134e0
author: cawrites
ms.author: chadam
ms.openlocfilehash: eea074af5270046bae6bb153cfb6fe79053335a4
ms.sourcegitcommit: 3ec49252e82590de0fe559a8574606ae213f6f3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97975490"
---
# <a name="work-with-multiple-versions-and-instances-of-sql-server"></a>使用 SQL Server 的多個版本與執行個體

[!INCLUDE [SQL Server Windows Only - ASDBMI ](../../includes/applies-to-version/sql-windows-only-asdbmi.md)]

您可以安裝多個 SQL Server 執行個體，或在已安裝舊版 SQL Server 的電腦上安裝 SQL Server。

下列 SQL Server 相關項目，相容於在相同電腦上多個執行個體的安裝：

- Database Engine

- Analysis Services

- Reporting Services (在 SQL Server 2016 和先前版本中)。 從 SQL Server 2016 開始。 SQL Server Reporting Services (SSRS) 有個別的安裝。 


您可以在已安裝其他版本 SQL Server 的電腦上升級舊版 SQL Server。 如需支援的升級案例，請參閱 [支援的版本與版本升級](../../database-engine/install-windows/supported-version-and-edition-upgrades.md)。
  
## <a name="version-components-and-numbering"></a>版本元件和編號

 下列概念對於了解 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]並存執行個體的行為很有幫助。
  
 適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的標準產品版本格式為 MM.nn.bbbb.rr，每一個區段定義如下：
  
 MM - 主要版本  
  
 nn - 次要版本  
  
 bbbb - 組建編號  
  
 rr - 組建修訂編號  
  
 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的每一個主要或次要版本中，版本號碼都會遞增，以便與之前的版本區別。 這項版本變更可用於許多用途。 其中包括在使用者介面中顯示版本資訊、控制升級期間如何取代檔案、套用 Service Pack，同時也是在後續版本之間區分功能的一項機制。
  
### <a name="components-shared-by-all-versions-of-ssnoversion"></a>所有版本共用的元件 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

 所有已安裝之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本的所有執行個體會共用某些元件。 當您在同一部機器上並存安裝不同版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時，這些元件會自動升級到最新的版本。 當解除安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的最後一個執行個體時，通常會自動解除安裝這類元件。
  
 範例：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser 和 Microsoft [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] VSS Writer。
  
### <a name="components-shared-across-all-instances-of-the-same-major-version-of-ssnoversion"></a>在相同主要版本的所有執行個體之間共用的元件 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 具有相同主要版本的版本會在所有執行個體之間共用某些元件。 如果在升級期間選取共用元件，現有的元件都會升級到最新的版本。
  
範例： [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]、 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)]、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]、 [!INCLUDE[ssBIDevStudioFull](../../includes/ssbidevstudiofull-md.md)]及 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 線上叢書。
  
### <a name="components-shared-across-minor-versions"></a>次要版本之間共用的元件

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 具有相同 major.minor 版本共用元件的版本。
  
範例：安裝程式支援檔案。
  
### <a name="components-specific-to-an-instance-of-ssnoversion"></a>執行個體特有的元件 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

某些 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件或服務是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體所特有， 也可感知執行個體。 這些元件或服務也會與裝載它們的執行個體共用相同的版本，並專門用於該執行個體。
  
範例： [!INCLUDE[ssDE](../../includes/ssde-md.md)]、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]和 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]。  
  
### <a name="components-that-are-independent-of-the-ssnoversion-versions"></a>與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本無關的元件

某些元件會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝期間安裝，但是與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的版本無關。 這些元件可能會在主要版本之間共用，或是由所有的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本共用。  

範例：Microsoft Sync Framework、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Compact。  
  
如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Compact 安裝的詳細資訊，請參閱 [從安裝精靈安裝 SQL Server 2016 &#40;安裝程式&#41;](../../database-engine/install-windows/install-sql-server-from-the-installation-wizard-setup.md)。 如需如何解除安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Compact 的詳細資訊，請參閱[解除安裝現有的 SQL Server 執行個體 &#40;安裝程式&#41;](../../sql-server/install/uninstall-an-existing-instance-of-sql-server-setup.md)。  
  
## <a name="using-ssnoversion-side-by-side-with-previous-versions-of-ssnoversion"></a>並存使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 與舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]

您可以在已執行舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的電腦上安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 如果預設執行個體已存在於電腦上， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 必須安裝成具名執行個體。  

下表顯示在已安裝必要 .NET 版本的常見受支援 Windows 上，每個 SQL Server 版本的並存支援：

| 現有執行個體 | 並存支援| 
|-------------------|----------------------------|
| SQL Server 2019 | SQL Server 2008 到 SQL Server 2017| 
| SQL Server 2017 | SQL Server 2008 到 SQL Server 2016| 
| SQL Server 2016 | SQL Server 2008 到 SQL Server 2014| 

如需詳細資訊，請參閱[在 Windows 8 和更新版本中使用 SQL Server](https://support.microsoft.com/help/2681562/using-sql-server-in-windows-8-and-later-versions-of-windows-operating) \(機器翻譯\)。 

  
> [!CAUTION]  
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep 不支援在同一部電腦上並存安裝 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 的備妥執行個體和舊版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 例如，您無法在並存準備 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 執行個體和備妥的 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]執行個體。 不過，您可以在同一部電腦上合併安裝屬於相同主要版本之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的多個備妥執行個體。 如需詳細資訊，請參閱＜ [Considerations for Installing SQL Server Using SysPrep](../../database-engine/install-windows/considerations-for-installing-sql-server-using-sysprep.md)＞。  
>
> SQL Server 2016 無法在執行 Windows Server 2008 R2 Server Core SP1 的電腦上，與舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 並存安裝。 如需 Server Core 安裝的詳細資訊，請參閱 [在 Server Core 上安裝 SQL Server 2016](../../database-engine/install-windows/install-sql-server-on-server-core.md)。  
  


## <a name="preventing-ip-address-conflicts"></a>防止 IP 位址衝突

當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體與 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]的獨立執行個體並行安裝時，務必避免 IP 位址發生 TCP 連接埠號碼衝突。 通常兩個 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體同樣設定為使用預設 TCP 通訊埠 (1433) 時，就會發生衝突。 為避免發生衝突，請將其中一個執行個體設定為使用非預設固定通訊埠。 在獨立執行個體上設定固定通訊埠通常最為簡單。 將 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 設定為使用不同的通訊埠，就能防止發生非預期的 IP 位址/TCP 通訊埠衝突，以免造成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體失敗而移轉至待命節點時，執行個體無法啟動。
  
## <a name="see-also"></a>另請參閱

* [安裝 SQL Server 的硬體與軟體需求](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)
* [從安裝精靈 &#40;安裝程式&#41; 安裝 SQL Server](../../database-engine/install-windows/install-sql-server-from-the-installation-wizard-setup.md)
* [支援的版本與版本升級](../../database-engine/install-windows/supported-version-and-edition-upgrades.md)
* [升級 SQL Server](../../database-engine/install-windows/upgrade-sql-server.md)
* [SQL Server 2019 的版本和支援功能](../../sql-server/editions-and-components-of-sql-server-version-15.md) 
* [版本及支援的 SQL Server 2017 功能](../../sql-server/editions-and-components-of-sql-server-2017.md)
* [版本及支援的 SQL Server 2016 功能](../../sql-server/editions-and-components-of-sql-server-2016.md)
* [回溯相容性_已刪除](/previous-versions/sql/sql-server-2016/cc280407(v=sql.130))