---
title: SQL 寫入器服務 | Microsoft Docs
description: 了解 SQL 寫入器服務。 查看此服務如何在 SQL Server 中，透過磁碟區陰影複製服務架構來提供新增的備份和還原功能。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- VDI [SQL Server]
- restoring [SQL Server], SQL Writer Service
- backups [SQL Server], while SQL Server running
- Volume Shadow Copy Service
- volume backups while running [SQL Server]
- Virtual Backup Device Interface [SQL Server]
- SQL Writer Service
- services [SQL Server], SQL Writer
- MSDE Writer
- VSS
ms.assetid: 0f299867-f499-4c2a-ad6f-b2ef1869381d
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 8bd9e1cacb36ce4906e30c2b8030b5e6b0b09787
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170910"
---
# <a name="sql-writer-service"></a>SQL 寫入器服務
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  SQL 寫入器服務能透過「磁碟區陰影複製服務」架構，提供附加功能給 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的備份和還原。  
  
 系統會自動安裝 SQL 寫入器服務。 當磁碟區陰影複製服務 (VSS) 應用程式要求備份或還原時，此服務必須已在執行中。 若要設定此服務，請使用 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows Services Applet。 SQL 寫入器服務會安裝在所有作業系統上。  
  
## <a name="purpose"></a>目的  
 執行時， [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會鎖定並對資料檔案取得獨佔式存取。 未執行 SQL 寫入器服務時，Windows 中執行的備份程式沒有資料檔案的存取權，而且備份必須使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 備份來執行。  
  
 使用 SQL 寫入器服務，以允許 Windows 備份程式於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行時複製 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料檔案。  
  
## <a name="volume-shadow-copy-service"></a>磁碟區陰影複製服務  
 VSS 是一組實作架構的 COM API，允許當系統上的應用程式寫入磁碟區的同時，仍能同時進行磁碟區備份作業。 VSS 提供一致的介面，讓更新磁碟資料 (寫入者) 與備份應用程式 (要求者) 的使用者應用程式間可以取得協調。  
  
 VSS 能在不過度降低所提供服務的效能與穩定性之下，在執行中的系統，特別是伺服器上，擷取和複製可靠的影像以供備份。 如需有關 VSS 的詳細資訊，請參閱 Windows 文件集。  

> [!NOTE]
> 使用 VSS 來備份裝載基本可用性群組的虛擬機器時，若虛擬機器目前裝載處於次要狀態的資料庫，從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 CU2 與 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU9 開始，那些資料庫將「不」會 隨著虛擬機器備份。  這是因為基本可用性群組不支援備份次要複本上的資料庫。  在這些 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本之前的版本上，備份將會因為發生錯誤而失敗。
  
## <a name="virtual-backup-device-interface-vdi"></a>虛擬備份裝置介面 (VDI)  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 提供一種稱為「虛擬備份裝置介面 (VDI)」的 API，可讓獨立軟體廠商將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 整合到他們的產品中，以對備份和還原作業提供支援。 這些 API 可提供最高的可靠性與效能，並能支援所有的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 備份和還原功能，包括所有熱備份與快照集備份能力。 如果協力廠商應用程式要求快照集 (VSS) 備份，則 SQL 寫入器服務會呼叫 VDI API 函式來執行實際備份。 請注意，VDI API 與 VSS 無關，且經常用於不採用 VSS API 的軟體解決方案。
  
## <a name="permissions"></a>權限  
 SQL 寫入器服務必須以 **本機系統** 帳戶執行。 SQL 寫入器服務使用 **NT Service\SQLWriter** 登入連接至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 使用 **NT Service\SQLWriter** 登入可讓 SQL 寫入器處理序在指定為 **沒有登入** 的帳戶中，以較低權限層級執行，藉此限制漏洞。 如果停用 SQL 寫入器服務，則依賴 VSS 快照集的任何公用程式 (例如 System Center Data Protection Manager) 以及其他一些協力廠商產品都將損毀，更糟的情況會導致資料庫備份不一致的風險。 如果執行所在的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]系統或主機系統 (在虛擬機器的情況下) 只需要使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 備份，則可以放心停用 SQL 寫入器服務並移除登入。  請注意，系統或磁碟區層級備份都可能叫用 SQL 寫入器服務，而不論備份是否直接以快照集為基礎。 某些系統備份產品使用 VSS 避免遭到開啟或鎖定檔案的封鎖。 由於 SQL 寫入器服務在活動過程中會暫時凍結 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的所有 I/O，因此 SQL 寫入器服務需要 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的更高權限。  
  
## <a name="features"></a>特性  
 SQL 寫入器支援：  
  
-   完整的資料庫備份和還原，包括全文檢索目錄  
  
-   差異備份和還原  
  
-   以移動的方式還原  
  
-   重新命名資料庫  
  
-   僅複製備份  
  
-   自動復原資料庫快照集  
  
 SQL 寫入器不支援：  
  
-   記錄備份  
  
-   檔案與檔案群組備份  
  
-   分頁還原  
  
## <a name="remarks"></a>備註
「SQL 寫入器」服務與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 引擎不同，而且跨不同的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本共用，而且跨相同伺服器上的不同 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體共用。  「SQL 寫入器」服務檔案隨著 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝套件提供，而且其版本號碼會與其所隨著提供之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 引擎的版本號碼相同。  當新的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體安裝在伺服器上或現有的執行個體升級時，若要安裝或升級之執行個體的版本號碼高於伺服器上目前「SQL 寫入器」服務的版本號碼，伺服器上的檔案將會被安裝套件中的檔案取代。  請注意，若「SQL 寫入器」服務已由 Service Pack 或 Cumulative Update 更新，且正在安裝 RTM 版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，可以使用較舊的「SQL 寫入器」服務取代較新的版本，前提是該安裝必須有較高的版本號碼。  例如，「SQL 寫入器」服務已在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 CU2 中更新。  若將該執行個體升級到 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] RTM，更新的「SQL 寫入器」服務將會被較舊的版本取代。  在此案例中，您將必須套用最新的 CU 到該新執行個體，以取得較新的「SQL 寫入器」服務版本。

