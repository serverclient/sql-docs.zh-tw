---
title: 自動調整 |Microsoft Docs
description: 瞭解 SQL Server 和 Azure SQL Database 的自動調整
ms.custom: fasttrack-edit
ms.date: 09/28/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- performance tuning [SQL Server]
- automatic tuning
- aprc
- automatic plan regression correction
- last known good plan
ms.assetid: ''
author: jovanpop-msft
ms.author: jovanpop
monikerRange: =azuresqldb-current||>=sql-server-2017||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 9c01a9e02576d666c39df13dc6e7e01f6d622a7d
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98169870"
---
# <a name="automatic-tuning"></a>自動微調
[!INCLUDE[sqlserver2017-asdb](../../includes/applies-to-version/sqlserver2017-asdb.md)]

「自動調整」是一種資料庫功能，可深入探索潛在的查詢效能問題、建議解決方法，並且自動修正找到的問題。

中導入的自動調整 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 會在每次偵測到潛在的效能問題時通知您，並可讓您套用矯正措施，或讓您 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 自動修正效能問題。 自動調整可 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 讓您識別並修正 **查詢執行計畫選擇回歸** 所造成的效能問題。 中的自動調整 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 也會建立必要的索引，並卸載未使用的索引。 如需查詢執行計畫的詳細資訊，請參閱 [執行計畫](../../relational-databases/performance/execution-plans.md)。

會 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 監視在資料庫上執行的查詢，並自動改善工作負載的效能。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)]具有內建的智慧機制，可讓您以動態方式調整您工作負載的資料庫，以自動調整並改善您的查詢效能。 有兩個可用的自動調整功能：

-   **自動計畫更正** 可識別有問題的查詢執行計畫，例如 [參數敏感性或參數探查](../../relational-databases/query-processing-architecture-guide.md#ParamSniffing) 問題，並藉由在發生回歸之前強制執行最後一個已知的良好計畫，來修正查詢執行計畫相關的效能問題。 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

-   **自動索引管理** 會識別應該在資料庫中新增的索引，以及應該移除的索引。 **適用於**：[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

## <a name="why-automatic-tuning"></a>為何需要自動調整？

傳統資料庫管理中有三個主要工作，就是監視工作負載、識別重要 [!INCLUDE[tsql_md](../../includes/tsql-md.md)] 查詢，以及識別應新增以改善效能的索引，或是很少使用且可以移除的索引，以改善效能。 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]提供您需要監視之查詢和索引的詳細見解。 不過，持續監視資料庫是一項困難又繁瑣的工作，特別是在處理許多資料庫時。 可能無法有效率地管理大量資料庫。 您可以考慮 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 使用自動調整功能將一些監視和微調動作委派給，而不是手動監視和調整資料庫。

### <a name="how-does-automatic-tuning-work"></a>自動調整的運作方式為何？

自動調整是一種持續監視和分析程式，可不斷地瞭解工作負載的特性，並找出潛在的問題和改進。

![自動調整程序](./media/tuning-process.png)

此程式可讓資料庫藉由找出哪些索引和方案可以改善工作負載的效能，以及哪些索引會影響您的工作負載，以動態方式調整您的工作負載。 根據這些結果，自動調整會套用可改善工作負載效能的調整動作。 此外，自動調整會在執行任何變更之後，持續監視資料庫的效能，以確保能改善工作負載的效能。 未改善效能的任何動作都會自動還原。 此驗證程式是一項重要功能，可確保自動調整所做的任何變更都不會降低工作負載的整體效能。

## <a name="automatic-plan-correction"></a>自動計劃修正

自動計畫更正是自動調整功能，可識別 **執行計畫選擇回歸** ，並藉由強制執行最後一個已知的良好計畫來自動修正此問題。 如需查詢執行計畫和查詢最佳化工具的詳細資訊，請參閱 [查詢處理架構指南](../../relational-databases/query-processing-architecture-guide.md)。

> [!IMPORTANT]
> 自動計畫更正取決於在資料庫中針對工作負載追蹤啟用的查詢存放區。 

### <a name="what-is-execution-plan-choice-regression"></a>什麼是執行計畫選擇回歸？

[!INCLUDE[ssdenoversion_md](../../includes/ssdenoversion_md.md)]可能會使用不同的執行計畫來執行 [!INCLUDE[tsql_md](../../includes/tsql-md.md)] 查詢。 查詢計劃需視統計資料、索引和其他因素而定。 應該用來執行查詢的最佳計畫， [!INCLUDE[tsql_md](../../includes/tsql-md.md)] 視這些因素的變更而定，可能會隨時間而改變。 在某些情況下，新的方案可能無法比上一個方案更好，而且新的計畫可能會導致效能衰退，例如 [參數敏感性或參數探查](../../relational-databases/query-processing-architecture-guide.md#ParamSniffing) 相關的問題。 

 ![查詢執行計畫選擇回歸](media/plan-choice-regression.png "查詢執行計畫選擇回歸") 

每當您注意到計畫選擇回歸時，您應該會發現先前的良好計畫，並強制使用它，而不是目前的計畫。 您可以使用程式來完成此操作 `sp_query_store_force_plan` 。 中的會 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 提供回歸計畫的相關資訊，以及建議的更正動作。 此外，可 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 讓您將此程式完全自動化，並讓您 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 修正任何與方案變更相關的問題。

> [!IMPORTANT]
> 自動計畫更正應該在資料庫相容性層級升級的範圍中使用，並在捕獲到基準之後，自動減輕工作負載升級的風險。 如需此使用案例的詳細資訊，請參閱 [在升級至較新的 SQL Server 期間保持效能穩定性](../../relational-databases/performance/query-store-usage-scenarios.md#CEUpgrade)。 

### <a name="automatic-plan-choice-correction"></a>自動計劃選擇更正

只要偵測到 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 計畫選擇回歸，就可以自動切換到最後一個已知的良好計畫。

![查詢執行計畫選擇更正](media/force-last-good-plan.png "查詢執行計畫選擇更正") 

[!INCLUDE[ssde_md](../../includes/ssde_md.md)]會自動偵測任何可能的計畫選擇回歸，包括應使用的計畫，而不是錯誤的計畫。 當在 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 發生回歸之前套用最後一個已知的良好計畫時，它會自動監視強制計畫的效能。 如果強制計畫不符合回歸計畫，則會 unforced 新的計畫，而 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 將會編譯新的計畫。 如果 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 確認強制計畫優於回歸計畫，則會保留強制計畫。 它會保留直到重新編譯發生為止 (例如，在下一個統計資料更新或架構變更) 。 如需強制執行計畫的詳細資訊和可以強制執行的計畫類型，請參閱 [計畫強制限制](../../relational-databases/system-catalog-views/sys-query-store-plan-transact-sql.md#plan-forcing-limitations)。

> [!NOTE]
> 如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 實例在驗證計畫強制動作之前重新開機，則會自動 unforced 該計畫。 否則，會在重新開機時保存強制執行計畫 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。

### <a name="enabling-automatic-plan-choice-correction"></a>啟用自動計畫選擇更正

您可以針對每個資料庫啟用自動調整，並指定每次偵測到某些計畫變更回歸時，應強制執行最後一個良好的計畫。 自動調整已使用下列命令啟用：

```sql   
ALTER DATABASE <yourDatabase>
SET AUTOMATIC_TUNING ( FORCE_LAST_GOOD_PLAN = ON ); 
```

啟用此選項之後，將會 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 自動強制執行估計 CPU 增益超過10秒的任何建議，或是新方案中的錯誤數目高於建議計畫中的錯誤數目，並確認強制計畫是否優於目前的計畫。

### <a name="alternative---manual-plan-choice-correction"></a>其他選項 - 手動計畫選擇更正

如果沒有自動調整，使用者必須定期監視系統，並尋找具有回歸的查詢。 如果有任何計畫已回歸，則使用者應該會找到先前的良好計畫，並使用程式來強制執行，而不是目前的計畫 `sp_query_store_force_plan` 。 最佳做法是強制執行最後一個已知的良好計畫，因為較舊的計畫可能因為統計資料或索引變更而無效。 強制最後一個已知良好計畫的使用者應該監視使用強制計畫執行之查詢的效能，並確認強制計畫如預期般運作。 視監視和分析的結果而定，應該強制執行計畫，否則使用者應該會發現另一種將查詢優化的方法，例如重寫查詢。 手動強制的計畫不應永久強制，因為 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 應該能夠套用最佳的方案。 使用者或 DBA 最後應使用程式取消計畫 `sp_query_store_unforce_plan` ，並讓 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 尋找最佳計畫。 

> [!TIP]
> 或者，您也可以使用 **具有強制計畫的查詢** 查詢存放區 view 來尋找和取消計畫。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 提供在查詢存放區中監視效能和修正問題所需的所有必要的視圖和程式。

在中 [!INCLUDE[sssql15-md](../../includes/sssql16-md.md)] ，您可以使用查詢存放區系統檢視來尋找計畫選擇回歸。 從開始 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] ， [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會偵測並顯示可能的計畫選擇回歸，以及應該套用在 [sys.dm_db_tuning_recommendations &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-tuning-recommendations-transact-sql.md) DMV 中的建議動作。 DMV 會顯示問題的相關資訊、問題的重要性，以及詳細資料（例如識別的查詢）、回歸計畫的識別碼、做為比較基準的計畫識別碼，以及 [!INCLUDE[tsql_md](../../includes/tsql-md.md)] 可執行以修正問題的語句。

| 類型 | description | Datetime | score | 詳細資料 | ... |
| --- | --- | --- | --- | --- | --- |
| `FORCE_LAST_GOOD_PLAN` | CPU 時間從4毫秒變更為14毫秒 | 3/17/2017 | 83 | `queryId` `recommendedPlanId` `regressedPlanId` `T-SQL` |   |
| `FORCE_LAST_GOOD_PLAN` | CPU 時間從37毫秒變更為84毫秒 | 3/16/2017 | 26 | `queryId` `recommendedPlanId` `regressedPlanId` `T-SQL` |   |

下列清單將描述此視圖中的某些資料行：
 - 建議動作的類型 `FORCE_LAST_GOOD_PLAN` 。
 - 描述，其中包含有關 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 此計畫變更的原因可能是效能回歸的資訊。
 - 偵測到潛在回歸的日期時間。
 - 這項建議的分數。
 - 關於問題的詳細資料，例如偵測到之計畫的識別碼、回歸計畫的識別碼、應該強制修正問題的計畫識別碼、 [!INCLUDE[tsql_md](../../includes/tsql-md.md)] 可能套用以修正問題的腳本等等。詳細資料會以 [JSON 格式](../json/json-data-sql-server.md)儲存。

使用下列查詢來取得可修正問題的腳本，以及估計增益的其他相關資訊：

```sql   
SELECT reason, score,
      script = JSON_VALUE(details, '$.implementationDetails.script'),
      planForceDetails.*,
      estimated_gain = (regressedPlanExecutionCount + recommendedPlanExecutionCount)
                  * (regressedPlanCpuTimeAverage - recommendedPlanCpuTimeAverage)/1000000,
      error_prone = IIF(regressedPlanErrorCount > recommendedPlanErrorCount, 'YES','NO')
FROM sys.dm_db_tuning_recommendations
CROSS APPLY OPENJSON (Details, '$.planForceDetails')
    WITH (  [query_id] int '$.queryId',
            regressedPlanId int '$.regressedPlanId',
            recommendedPlanId int '$.recommendedPlanId',
            regressedPlanErrorCount int,
            recommendedPlanErrorCount int,
            regressedPlanExecutionCount int,
            regressedPlanCpuTimeAverage float,
            recommendedPlanExecutionCount int,
            recommendedPlanCpuTimeAverage float
          ) AS planForceDetails;
```

[!INCLUDE[ssresult-md](../../includes/ssresult-md.md)]     

| reason | score | 指令碼 | 查詢 \_ 識別碼 | 目前的計畫 \_ 識別碼 | 建議的計畫 \_ 識別碼 | 預估 \_ 增益 | \_容易出錯
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CPU 時間從3毫秒變更為46毫秒 | 36 | EXEC sp \_ 查詢 \_ 存放區 \_ 強制 \_ 方案12，17; | 12 | 28 | 17 | 11.59 | 0

`estimated_gain`如果建議的計畫用於查詢執行而非目前的計畫，則資料行代表要儲存的估計秒數。 如果增益超過10秒，則應該強制執行建議的計畫，而不是目前的計畫。 如果有更多錯誤 (例如，在目前計畫中) 超時或中止的執行，而不是建議的計畫，則會將資料行 `error_prone` 設定為值 `YES` 。 很容易出錯的方案是建議的計畫應該強制執行的另一個原因，而不是目前的計畫。

雖然 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 提供了找出計畫選擇回歸所需的所有資訊，但持續監視和修正效能問題可能會變得很繁瑣。 自動調整可讓此程式變得更容易。

> [!NOTE]
> DMV 中的資料 `sys.dm_db_tuning_recommendations` 不會在實例重新開機之間保存 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。

## <a name="automatic-index-management"></a>自動索引管理

在中 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] ，索引管理很容易，因為會 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 瞭解您的工作負載，並確保資料一律以最佳的方式編制索引。 您的工作負載要達到最佳效能，有適當的索引設計非常重要，而且自動索引管理可以協助您將索引最佳化。 自動索引管理可以修正資料庫中索引編製不正確所造成的效能問題，或維護和改善現有資料庫結構描述的索引。 中的自動調整會 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 執行下列動作：

 - 識別可改善 [!INCLUDE[tsql_md](../../includes/tsql-md.md)] 從資料表讀取資料之查詢效能的索引。
 - 識別未在較長時間內被移除的多餘索引或索引。 移除不必要的索引可改善在資料表中更新資料之查詢的效能。

### <a name="why-do-you-need-index-management"></a>為什麼需要索引管理？

索引會加速一些從資料表讀取資料的查詢，不過它們可能會使更新資料的查詢變慢。 您需要仔細分析建立索引的時機，以及要將哪些資料行包含在索引中。 某些索引在一段時間後可能不再需要。 因此，您需要定期找出並卸載這些不會帶來任何好處的索引。 如果您忽略未使用的索引，更新資料的查詢效能將會減少，而不會對讀取資料的查詢產生任何好處。 未使用的索引也會影響整體系統效能，因為其他的更新需要不必要的記錄。

找出一組最佳的索引，能改善查詢從資料表讀取資料時的效能，並對需要持續進行複雜分析的更新之影響降到最低。

[!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 使用內建智慧和先進的規則來分析您的查詢、找出最適合您目前工作負載的索引，以及識別可能需要移除的索引。 Azure SQL Database 可確保您有一組最少的必要索引，可將讀取資料的查詢優化，對其他查詢的影響降到最低。

### <a name="automatic-index-management"></a>自動索引管理

除了偵測之外， [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 也可以自動套用已識別的建議。 如果您發現內建規則會改善資料庫的效能，您可能會讓您 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 自動管理索引。

若要在中啟用自動調整 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 功能，並允許自動調整功能以完整管理您的工作負載，請參閱 [使用 Azure 入口網站在 Azure SQL Database 中啟用自動調整](/azure/sql-database/sql-database-automatic-tuning-enable)。

當 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 套用 CREATE INDEX 或 DROP INDEX 建議時，它會自動監視受索引影響之查詢的效能。 只有在改進受影響查詢的效能時，才會保留新的索引。 如果有一些查詢因為沒有索引而執行較慢，則會自動重新建立已卸載的索引。

### <a name="automatic-index-management-considerations"></a>自動索引管理考量

在中建立必要索引所需的動作 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 可能會耗用資源，而暫時會影響工作負載的效能。 若要將索引建立對工作負載效能的影響降到最低，請 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  為任何索引管理作業尋找適當的時間範圍。 如果資料庫需要資源才能執行您的工作負載，則調整動作會延後，並且會在資料庫有足夠的資源可用於維護工作時重新開機。 自動索引管理的其中一項重要功能是驗證動作成效。 當 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  建立或卸載索引時，監視程式會分析工作負載的效能，以確認該動作已提升整體效能。 如果沒有大幅改進，則會立即還原動作。 如此一來， [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  可確保自動調整動作不會對工作負載的效能造成負面影響。 基礎結構描述的維護作業清楚了解自動調整所建立的索引。 自動建立索引的存在並不會封鎖卸除或重新命名資料行這類的結構描述變更。 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]當相關的資料表或資料行卸載時，會立即卸載由自動建立的索引。

### <a name="alternative---manual-index-management"></a>替代-手動索引管理

如果沒有自動索引管理，使用者或 DBA 將需要以手動方式查詢 [sys.dm_db_missing_index_details &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md) view 或使用中的效能儀表板報表 [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] 尋找可改善效能的索引、使用此視圖中提供的詳細資料建立索引，以及手動監視查詢的效能。 為了找出應該卸載的索引，使用者應該監視索引的操作使用狀況統計資料，以找出很少使用的索引。

[!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 簡化此程式。 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 分析您的工作負載、識別可使用新索引更快執行的查詢，並識別未使用或重複的索引。 如需找出應變更的索引詳細資訊，可在 [Azure 入口網站中的尋找索引建議](/azure/sql-database/sql-database-advisor-portal)中找到。

## <a name="see-also"></a>另請參閱  
 [ALTER DATABASE SET AUTOMATIC_TUNING &#40;Transact-sql&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)   
 [sys.database_automatic_tuning_options &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-automatic-tuning-options-transact-sql.md)  
 [sys.dm_db_tuning_recommendations &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-tuning-recommendations-transact-sql.md)   
 [sys.dm_db_missing_index_details &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md)   
 [sp_query_store_force_plan &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-force-plan-transact-sql.md)     
 [sp_query_store_unforce_plan &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-query-store-unforce-plan-transact-sql.md)           
 [sys.database_query_store_options &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md)   
 [JSON 函數](../json/json-data-sql-server.md)    
 [執行計畫](../../relational-databases/performance/execution-plans.md)    
 [效能的監視與微調](../../relational-databases/performance/monitor-and-tune-for-performance.md)     
 [效能監視及微調工具](../../relational-databases/performance/performance-monitoring-and-tuning-tools.md)     
 [使用查詢存放區監視效能](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)  
 [查詢調整小幫手](../../relational-databases/performance/upgrade-dbcompat-using-qta.md)