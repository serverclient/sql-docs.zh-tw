---
title: 設定警告臨界值 | Microsoft Docs
description: 了解如何使用 [設定警告臨界值] 對話方塊，在 SQL Server 中啟用及設定所選鏡像資料庫的警告臨界值。
ms.custom: ''
ms.date: 03/07/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
f1_keywords:
- sql13.swb.dbmmonitor.setwarningthreshold.f1
ms.assetid: 17f93147-e7d9-4092-b4c2-c11b38051171
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 999b5d9e1cf50a9ffc2ff349dafac73de9a1465b
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641938"
---
# <a name="set-warning-thresholds"></a>設定警告臨界值
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  使用這個對話方塊可針對 **[資料庫鏡像監視器]** 對話方塊內導覽樹狀目錄中選取的資料庫，啟用並設定一或多個警告臨界值。  
  
 這個對話方塊會嘗試連接到兩個伺服器執行個體。 這些連接是以非同步方式建立的。 對話方塊中會顯示每個夥伴的連接狀態。 如果夥伴處於未連接狀態，您可以按一下 **[連接]** 。  
  
 **若要使用 SQL Server Management Studio 監視資料庫鏡像**  
  
-   [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)  
  
## <a name="options"></a>選項。  
 *伺服器執行個體及其連接狀態*  
 格式為 *SYSTEM* **\\** _INSTANCE_NAME_ 的夥伴伺服器執行個體名稱。 如果是預設伺服器執行個體，則只會顯示系統名稱。  
  
 這個欄位也會指出監視器目前是否已連接到這個伺服器執行個體。 可能的連接狀態如下：  
  
-   **未連線到** *server_instance_name*  
  
-   **正在嘗試連線到** *server_instance_name*  
  
-   **已連線到** *server_instance_name*  
  
    > [!NOTE]  
    >  如果您不是 **系統管理員** 固定伺服器角色的成員，則此狀態會是 [已連線到 *server_instance_name* (有限的權限)] 。  
  
 每個夥伴伺服器執行個體的名稱都會顯示在個別的 *[伺服器執行個體及其連接狀態]* 欄位中。 當監視器開始執行時，最上面的欄位便會列出主體伺服器。  
  
 **連接**/**取消**  
 [連接]/[取消] 按鈕與各「伺服器執行個體及其連接狀態」欄位相關聯。 這個按鈕的狀態是依據連接狀態而定：  
  
-   如果伺服器執行個體沒有任何連接，則按鈕文字為 **[連接]** 。 按一下即可連接到伺服器執行個體。  
  
-   如果正在嘗試進行連接，則按鈕文字為 **[取消]** 。 按一下即可取消正在嘗試的連接。  
  
-   如果伺服器處於已連接狀態，則按鈕文字為 **[已連接]** ，而且按鈕會呈暗灰色。  
  
 **臨界值**  
 [臨界值] 方格會顯示這兩個伺服器執行個體的警告設定。  
  
> [!NOTE]  
>  如果伺服器執行個體處於未連接狀態，則其資料行會顯示空的資料格和灰色背景。 當連接開啟時，方格便會自動顯示執行個體的內容。  
  
 方格包含下列資料行：  
  
 **警告**  
 列出支援的警告：  
  
|警告|描述|  
|-------------|-----------------|  
|**如果未傳送的記錄超過臨界值，即發出警告**|這個臨界值會指出主體伺服器執行個體上傳送佇列中未傳送記錄的 KB 數。|  
|**如果未還原的記錄超過臨界值，即發出警告**|這個臨界值會指出鏡像伺服器執行個體上重做佇列中的 KB 數。|  
|**如果最舊未傳送交易的時間超過臨界值，即發出警告**|這個臨界值會指出尚未從傳送佇列中傳送到鏡像伺服器執行個體的交易分鐘數。 這個值有助於從時間方面來測量資料遺失的可能性。|  
|**如果鏡像認可負擔超過臨界值，即發出警告**|這個臨界值會指出每項交易的延遲毫秒數 (只有在高安全性模式中才相關)。 這項延遲是當主體伺服器執行個體等待鏡像伺服器執行個體將交易記錄寫入重做佇列中時所產生的負擔量。|  
  
 **已於 '** *\<server instance>* **' 啟用**  
 如果此核取方塊空白，則表示伺服器執行個體上的警告目前已停用。 若要啟用警告，請按一下其核取方塊。  
  
 **於 '** *\<server instance>* **' 的臨界值**  
 啟用警告時，請在這個資料行的左邊設定臨界值。 如果在更新狀態資料表時已達到指定的臨界值，則會發生事件。 如果您在設定值之後停用臨界值，則該值仍會保留在這個欄位中，只要重新啟用警告即可使用。  
  
 如果警告並未啟用，則這個欄位將處於非使用中狀態。  
  
 **確定**  
 按一下 [確定] 關閉這個對話方塊，並且將目前指定的警告臨界值顯示在 [警告] 索引標籤頁面上的 [臨界值] 方格中。  
  
## <a name="remarks"></a>備註  
 臨界值一次只能適用於一個夥伴，但是，建議您針對兩個夥伴上的特定事件都設定臨界值，以確保如果資料庫發生容錯移轉時，警告都能持續顯示。 適合於每個夥伴的臨界值需視該夥伴系統的效能功能而定。  
  
 在更新狀態資料表時，只有當效能值達到或超過臨界值時，才會將事件寫入該效能的事件記錄檔中。 如果尖峰值只在兩次狀態更新之間短暫達到臨界值，則會遺漏該尖峰值。  
  
## <a name="see-also"></a>另請參閱  
 [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)   
 [監視資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/monitoring-database-mirroring-sql-server.md)   
 [啟動設定資料庫鏡像安全性精靈 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-the-configuring-database-mirroring-security-wizard.md)  
  
  
