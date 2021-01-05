---
title: 資料庫鏡像監視器 (警告頁面) | Microsoft Docs
description: 使用這個唯讀的資料庫鏡像監視器頁面，來檢視資料庫鏡像事件所支援的警告和警告閾值 (如果有的話)。
ms.custom: ''
ms.date: 03/07/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
f1_keywords:
- sql13.swb.dbmmonitor.warningsandalerts.f1
ms.assetid: 01936122-961d-436b-ba3c-5f79fefe5469
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 02acad8ee93c311c18c80e325450105ac47ee876
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642309"
---
# <a name="database-mirroring-monitor-warnings-page"></a>資料庫鏡像監視器 (警告頁面)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  會顯示一份唯讀清單，列出資料庫鏡像事件所支援的警告和指定的警告臨界值 (如果有的話)。  
  
 **若要使用 SQL Server Management Studio 監視資料庫鏡像**  
  
-   [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)  
  
## <a name="columns"></a>資料行  
 **警告**  
 可定義臨界值的警告包括：  
  
|警告|閾值|  
|-------------|---------------|  
|**如果未傳送的記錄超過臨界值，即發出警告**|指定會在主體伺服器執行個體上產生警告之未傳送記錄的 KB 數。 這個警告有助於依據 KB 數來測量資料遺失的可能性，與高效能模式特別有關係。 但是，當鏡像因為夥伴中斷連接而暫停或暫止時，這個警告也會與高安全性模式有關。|  
|**如果未還原的記錄超過臨界值，即發出警告**|指定會在鏡像伺服器執行個體上產生警告之未還原記錄的 KB 數。 這個警告對於依據 KB 數來測量容錯移轉時間很有用。 *容錯移轉時間* 主要包含先前的鏡像伺服器向前復原其重做佇列中剩餘之所有記錄所需的時間，再加上一段很短的額外時間。<br /><br /> 注意:如果是自動容錯移轉，則系統發現錯誤的時間與容錯移轉時間無關。<br /><br /> 如需詳細資訊，請參閱 [預估角色切換期間的服務中斷時間 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/estimate-the-interruption-of-service-during-role-switching-database-mirroring.md)的程序交換。|  
|**如果最舊未傳送交易的時間超過臨界值，即發出警告**|指定在主體伺服器執行個體上產生警告之前，傳送佇列中可以累積的交易分鐘數。 這個警告有助於從時間方面來測量資料遺失的可能性，與高效能模式特別有關係。 但是，當鏡像因為夥伴中斷連接而暫停或暫止時，這個警告也會與高安全性模式有關。|  
|**如果鏡像認可負擔超過臨界值，即發出警告**|指定在主體伺服器上產生警告之前，所容許之每項交易的平均延遲毫秒數。 這項延遲是當主體伺服器執行個體等待鏡像伺服器執行個體將交易記錄寫入重做佇列中時所產生的負擔量。 只有在高安全性模式中才會顯出這個值的重要性。|  
  
 **'** <伺服器執行個體> **' 的閾值**  
 針對每一個警告，顯示其中一個伺服器執行個體目前使用者指定的臨界值 (如果有的話)。 在對應的資料行標題中，會指出伺服器執行個體的完整執行個體名稱。  
  
 如需詳細資訊，請參閱此主題稍後的「備註」。  
  
 **設定臨界值**  
 按一下這個按鈕，即可在每個容錯移轉夥伴上設定其中一個警告的臨界值。  
  
 如需詳細資訊，請參閱此主題稍後的「備註」。  
  
## <a name="remarks"></a>備註  
 如果伺服器執行個體目前無法使用資訊，則對應之 **[於...的臨界值]** 資料行的資料格便會顯示灰色背景和浮水印文字。 如果監視器未連線到伺服器執行個體，則每個資料格中的方格會依據該執行個體是預設執行個體還是具名執行個體而顯示 [未連線到 <系統名稱>] 或 [未連線到<系統名稱>\\<執行個體名稱>]。 如果監視器正在等候查詢傳回資料，則每個資料格中的方格會顯示 [正在等候資料...]。  
  
 當資訊可以使用時，每個警告的資料格會顯示指定的臨界值 (和度量單位) 或 [未啟用]。  
  
 如果在重新整理狀態資料表時超過臨界值，在記錄狀態資料列時，就會在 Windows 事件記錄檔中記錄一個事件。 依預設，如果監視器不在執行中，則會每分鐘記錄一次狀態資料列。 您可以使用 SQL Server Agent 或如 Microsoft Management Operations Manager (MOM) 之類的其他程式，針對每種類型的記錄事件設定警示。  
  
 在特定的夥伴上，記錄的事件會依據其目前的角色是主體還是鏡像而定。 但是，建議您針對兩個夥伴上的特定事件都設定警告臨界值，以確保如果資料庫發生容錯移轉時，警告都能持續顯示。 適合於每個夥伴的臨界值需視該夥伴系統的效能功能而定。  
  
> [!NOTE]  
>  您也可以使用 **sp_dbmmonitorchangealert** 系統預存程序來設定對等事件的臨界值，例如未傳送的記錄、未復原的記錄、最舊尚未傳送的交易和鏡像認可負擔。 如需詳細資訊，請參閱 [sp_dbmmonitorchangealert &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbmmonitorchangealert-transact-sql.md)。  
  
 下表會顯示與各個警告相關的事件識別碼。  
  
|資料庫鏡像監視器警告|事件名稱|事件識別碼|  
|----------------------------------------|----------------|--------------|  
|**如果未傳送的記錄超過臨界值，即發出警告**|未傳送的記錄|32042|  
|**如果未還原的記錄超過臨界值，即發出警告**|未還原的記錄|32043|  
|**如果最舊未傳送交易的時間超過臨界值，即發出警告**|最舊尚未傳送的交易|32044|  
|**如果鏡像認可負擔超過臨界值，即發出警告**|鏡像認可負擔|32045|  
  
## <a name="permissions"></a>權限  
 如需完整存取權，需要 **sysadmin** 固定伺服器角色中的成員資格。 只有 **sysadmin** 的成員可以設定並檢視關鍵效能標準的警告臨界值。  
  
 **dbm_monitor** 角色中的成員資格可讓您僅檢視 [警告] 頁面上的最新狀態資料列。  
  
## <a name="see-also"></a>另請參閱  
 [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)   
 [監視資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/monitoring-database-mirroring-sql-server.md)   
 [啟動設定資料庫鏡像安全性精靈 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-the-configuring-database-mirroring-security-wizard.md)  
  
  
