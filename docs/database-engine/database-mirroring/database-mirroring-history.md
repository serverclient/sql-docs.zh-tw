---
title: 資料庫鏡像見證 | Microsoft Docs
description: 了解如何在 SQL Server Management Studio 中，檢視指定伺服器執行個體上鏡像資料庫的鏡像狀態記錄。
ms.custom: ''
ms.date: 03/07/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
f1_keywords:
- sql13.swb.dbmmonitor.databasemirroringhistory.f1
ms.assetid: 1d6e4b10-4a23-47d7-9918-c417992f09d3
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: eb42f9910acbc412117713b7be7d08fe4c6ee78f
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642006"
---
# <a name="database-mirroring-history"></a>資料庫鏡像記錄
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  使用這個對話方塊，可檢視指定的伺服器執行個體上某個鏡像資料庫的鏡像狀態記錄。  
  
 **若要使用 SQL Server Management Studio 監視資料庫鏡像**  
  
-   [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)  
  
## <a name="options"></a>選項。  
 **伺服器執行個體**  
 正在報告記錄的伺服器執行個體名稱。  
  
 **Database**  
 正在報告之記錄所屬的資料庫名稱。  
  
 **依下列項目篩選清單**  
 列出篩選值。 如果選擇新值，就會自動重新整理方格。  
  
 可能的篩選值如下：  
  
-   最後兩小時  
  
-   最後四小時  
  
-   最後八小時  
  
-   最後一天  
  
-   最後兩天  
  
-   最後 100 筆記錄  
  
-   最後 500 筆記錄  
  
-   最後 1000 筆記錄  
  
-   所有記錄  
  
 **[重新整理]**  
 按一下即可重新整理記錄清單。  
  
> [!NOTE]  
>  這個對話方塊不會自動重新整理記錄清單。 若要重新整理清單，請按一下 **[重新整理]** 或選擇其他篩選選項。 只有 **sysadmin** 固定伺服器角色的成員才能更新鏡像記錄。  
  
 **History**  
 顯示記錄清單。 按一下資料行標題，就會依據該資料行排序方格。 此清單包含下列資料行：  
  
|資料行名稱|描述|  
|-----------------|-----------------|  
|**記錄的時間**|記錄資料列的時間戳記。|  
|**角色**|對這個資料庫而言，伺服器執行個體目前的鏡像角色，可為 [主體] 或 [鏡像]。|  
|**鏡像狀態**|資料庫狀態：<br /><br /> 已中斷連接<br /><br /> 暫止容錯移轉<br /><br /> 暫止<br /><br /> 已同步處理<br /><br /> 正在同步處理<br /><br /> Unknown|  
|**見證連接**|資料庫鏡像工作階段中的見證連接狀態，可為 [已連接] 或 [已中斷連接]。 如果沒有見證，則此值為 NULL。|  
|**未傳送的記錄**|主體伺服器執行個體上傳送佇列中未傳送記錄的大小 (以 KB 為單位)。|  
|**傳送的時間**|主體伺服器執行個體將目前在傳送佇列中的記錄，傳送到鏡像伺服器執行個體所需的大約時間量 ( *傳送速率*)。 由於內送交易的速率可能會有極大的差異，因此傳送記錄的時間只是估計值。 但是，傳送速率對於粗略估計手動容錯移轉所需的時間可能會很有用。|  
|**Send Rate**|將交易傳送到鏡像伺服器執行個體的速率 (以每秒 KB 數為單位)。|  
|**新交易的速率**|將內送交易輸入到主體記錄中的速率 (以每秒 KB 數為單位)。 若要判斷鏡像是落後、超前，還是趕上進度，請將這個值與 **[傳送的時間]** 值加以比較。|  
|**最舊尚未傳送的交易**|傳送佇列中最舊尚未傳送之交易的存在時間。 這項交易的存在時間會指出尚未傳送到鏡像伺服器執行個體的交易分鐘數。 這個值有助於從時間方面來測量資料遺失的可能性。|  
|**未還原的記錄**|在重做佇列中等待的記錄量 (以 KB 為單位)。|  
|**還原的時間**|將目前在重做佇列中的記錄套用至鏡像資料庫所需的大約分鐘數。|  
|**還原速率**|將交易還原到鏡像資料庫中的速率 (以每秒 KB 數為單位)。|  
|**鏡像認可負擔**|每項交易的平均延遲毫秒數 (只限於同步模式)。 這項延遲是當主體伺服器執行個體等待鏡像伺服器執行個體將交易記錄寫入重做佇列中時所產生的負擔量。|  
  
## <a name="see-also"></a>另請參閱  
 [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)   
 [監視資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/monitoring-database-mirroring-sql-server.md)   
 [啟動設定資料庫鏡像安全性精靈 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-the-configuring-database-mirroring-security-wizard.md)  
  
  
