---
title: 設定 max worker threads 伺服器組態選項 | Microsoft Docs
description: 了解如何使用背景工作執行緒上限選項來設定可供 SQL Server 處理特定要求的背景工作執行緒數目。
ms.custom: contperf-fy20q4
ms.date: 04/14/2020
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- worker threads [SQL Server]
- max worker threads option
ms.assetid: abeadfa4-a14d-469a-bacf-75812e48fac1
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 89da087dd829642653fee6ae8648af3c1c00c2a0
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878884"
---
# <a name="configure-the-max-worker-threads-server-configuration-option"></a>設定 max worker threads 伺服器組態選項
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

此主題描述如何使用 **或** ，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中設定 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] max worker threads [!INCLUDE[tsql](../../includes/tsql-md.md)]伺服器組態選項。 **背景工作執行緒上限** 選項會設定在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 範圍內可用來處理查詢要求、登入、登出和類似應用程式要求的背景工作執行緒數目。


[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會使用作業系統的原生執行緒服務來確保下列條件：

- 一或多個執行緒同時支援 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所支援的每個網路。

- 一個執行緒處理資料庫檢查點。

- 一個集區的執行緒處理所有使用者。

**max worker threads** 的預設值是 0。 這會讓 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在啟動時自動設定工作者執行緒的數目。 此預設值對大多數系統都是最佳的。 但依系統組態而定，將 **max worker threads** 設為特定的值有時候可提高效能。  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項  
  
-   實際查詢要求數目可超過 **背景工作執行緒上限** 中設定的值，在此情況下，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將背景工作執行緒納入集區中，以便下一個可用的背景工作執行緒能處理要求。 背景工作執行緒只會指派給作用中的要求，並會在要求獲得服務之後釋放。 即使提出要求的使用者工作階段或連線保持開啟，也會發生此情況。 

-   **背景工作執行緒上限** 伺服器組態選項不會限制引擎內可能繁衍的所有執行緒。 LazyWriter、檢查點、記錄檔寫入器、Service Broker、鎖定管理員等工作所需的系統執行緒，或在此限制外繁衍的執行緒。 可用性群組會使用 **背景工作執行緒上限限制** 內的一部分背景工作執行緒，但也會使用系統執行緒 (請參閱 [可用性群組的執行緒使用量](../availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md#ThreadUsage))，如果超過了設定的執行緒數目，則下列查詢會提供產生額外執行緒的系統工作資訊。  
  
 ```sql  
 SELECT  s.session_id, r.command, r.status,  
    r.wait_type, r.scheduler_id, w.worker_address,  
    w.is_preemptive, w.state, t.task_state,  
    t.session_id, t.exec_context_id, t.request_id  
 FROM sys.dm_exec_sessions AS s  
 INNER JOIN sys.dm_exec_requests AS r  
    ON s.session_id = r.session_id  
 INNER JOIN sys.dm_os_tasks AS t  
    ON r.task_address = t.task_address  
 INNER JOIN sys.dm_os_workers AS w  
    ON t.worker_address = w.worker_address  
 WHERE s.is_user_process = 0;  
 ```  
  
  
###  <a name="recommendations"></a><a name="Recommendations"></a> 建議  
  
-   此選項是進階選項，只有具經驗的資料庫管理員或通過認證的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 專業人員才可變更。 如果您懷疑發生效能問題，很可能並非是否能使用背景工作執行緒所致。 此原因較可能與佔用背景工作執行緒的活動相關，且不會將其釋放。 範例包括長時間執行的查詢或系統瓶頸 (I/O、封鎖、閂鎖等候、網路等候) 等造成長時間等候查詢的因素。 建議您在變更背景工作執行緒設定的上限前，先找出效能問題的根本原因。 如需評定效能的詳細資訊，請參閱[監視和微調效能](../../relational-databases/performance/monitor-and-tune-for-performance.md)。
  
-   當大量用戶端連線至伺服器時，執行緒集區有助於將效能最佳化。 通常，會針對每一個查詢要求建立個別的作業系統執行緒。 然而，在數以百計的伺服器連接之下，若每個查詢要求都使用一個執行緒，反而會耗用大量的系統資源。 **max worker threads** 選項可讓 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 建立工作者執行緒集區，以服務更多的查詢要求數量，進而改善效能。  
  
-   下表顯示根據 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的不同 CPU、電腦架構和版本組合自動設定的最大背景工作執行緒數目 (值設為 0 時)，使用的公式為：**預設最大背景工作角色 + ((邏輯 CPU 數目** - 4) * 每個 CPU 的背景工作角色)**。  
  
    |CPU 數|32 位元電腦 (最多為 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)])|64 位元電腦 (最多為 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP1)|64 位元電腦 (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP2 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始)|   
    |------------|------------|------------|------------|  
    |\<= 4|256|512|512|   
    |8|288|576|576|   
    |16|352|704|704|   
    |32|480|960|960|   
    |64|736|1472|1472|   
    |128|1248|2496|4480|   
    |256|2272|4544|8576|   
    
    最多為 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP1，每個 CPU 的「背景工作角色」只取決於架構 (32 位元或 64 位元)：
    
    |CPU 數|32 位元電腦 <sup>1</sup>|64 位元電腦|   
    |------------|------------|------------|   
    |\<= 4|256|512|   
    |\> 4|256 + ((邏輯 CPU 數 - 4) * 8)|512 <sup>2</sup> + ((邏輯 CPU 數目 - 4) * 16)|   
    
    從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP2 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始，「每個 CPU 的背景工作角色」取決於架構和處理器數目 (介於 4 到 64，或大於 64)：
    
    |CPU 數|32 位元電腦 <sup>1</sup>|64 位元電腦|   
    |------------|------------|------------|   
    |\<= 4|256|512|   
    |\> 4 和 \<= 64|256 + ((邏輯 CPU 數 - 4) * 8)|512 <sup>2</sup> + ((邏輯 CPU 數目 - 4) * 16)|   
    |\> 64|256 + ((邏輯 CPU 數目 - 4) * 32)|512 <sup>2</sup> + ((邏輯 CPU 數目 - 4) * 32)|   
  
    <sup>1</sup> 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不可以安裝在 32 位元作業系統上。 列出 32 位元電腦值以協助客戶執行 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 或更早版本。 建議在 32 位元電腦上執行的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的背景工作執行緒最大數目設為 1,024。
    
    <sup>2</sup> 從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始，針對記憶體小於 2GB 的機器，「預設最大背景工作角色」值會除以 2。
  
    > [!TIP]  
    > 如需有關使用超過 64 個 CPU 的建議事項，請參閱 [在超過 64 個 CPU 之電腦上執行 SQL Server 的最佳作法](../../relational-databases/thread-and-task-architecture-guide.md#best-practices-for-running-sql-server-on-computers-that-have-more-than-64-cpus)。  
  
-   當所有的工作者執行緒都在進行長時間執行的查詢時， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可能會反應遲緩，直到工作者執行緒完成並恢復為可用狀態為止。 雖然這不算是瑕疵，但有時卻讓人困擾。 若處理序反應遲緩，而且無法處理新查詢，請使用專用管理員連接 (DAC) 來連接 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，然後清除處理序。 若要避免這個問題，請增加 max worker threads 的最大數目。  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 不含參數或只含第一個參數之 **sp_configure** 上的執行權限預設會授與所有使用者。 以同時設定兩個參數的 **sp_configure** 來變更組態選項或執行 `RECONFIGURE` 陳述式時，使用者必須取得 `ALTER SETTINGS` 伺服器層級權限。 **sysadmin** 和 **serveradmin** 固定伺服器角色隱含地持有 `ALTER SETTINGS` 權限。  
  
##  <a name="using-ssmanstudiofull"></a><a name="SSMSProcedure"></a> 使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]  
  
#### <a name="to-configure-the-max-worker-threads-option"></a>設定 max worker threads 選項  
  
1.  在物件總管中，請以滑鼠右鍵按一下伺服器，然後選取 [屬性]。  
  
2.  按一下 **[處理器]** 節點。  
  
3.  在 [最大背景工作執行緒] 方塊中，鍵入或選取 128 到 32,767 之間的任一數值。  
  
> [!TIP]
> **Max worker threads** 選項可用來設定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序可使用的工作者執行緒數目。 **max worker threads** 的預設值對大部份系統而言都是最合適的。 但依系統組態而定，將 **max worker threads** 設為較小的值有時候可提高效能。
> 如需詳細資訊，請參閱此頁面中的 [[建議]](#Recommendations)。
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
  
#### <a name="to-configure-the-max-worker-threads-option"></a>設定 max worker threads 選項  
  
1.  連接到 [!INCLUDE[ssDE](../../includes/ssde-md.md)]。  
  
2.  在標準列中，按一下 **[新增查詢]** 。  
  
3.  複製下列範例並將其貼到查詢視窗中，然後按一下 **[執行]** 。 此範例示範如何使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) 將 `max worker threads` 選項設定為 `900`。  
  
```sql  
USE AdventureWorks2012 ;  
GO  
EXEC sp_configure 'show advanced options', 1;  
GO  
RECONFIGURE ;  
GO  
EXEC sp_configure 'max worker threads', 900 ;  
GO  
RECONFIGURE;  
GO  
```  
  
##  <a name="follow-up-after-you-configure-the-max-worker-threads-option"></a><a name="FollowUp"></a> 後續操作：設定最大背景工作執行緒選項之後  
 無須重新啟動 [!INCLUDE[ssDE](../../includes/ssde-md.md)]，變更會在執行 [RECONFIGURE](../../t-sql/language-elements/reconfigure-transact-sql.md) 後立即生效。  
  
## <a name="see-also"></a>另請參閱  
 [伺服器組態選項 &#40;SQL Server&#41;](../../database-engine/configure-windows/server-configuration-options-sql-server.md)   
 [RECONFIGURE &#40;Transact-SQL&#41;](../../t-sql/language-elements/reconfigure-transact-sql.md)   
 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)   
 [資料庫管理員的診斷連接](../../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)
