---
title: 基數估計 (SQL Server) | Microsoft Docs
description: SQL Server 查詢最佳化工具會選取估計處理成本最低的查詢計畫，這會根據處理的資料列和成本模型來判斷。
ms.custom: ''
ms.date: 02/24/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- cardinality estimator
- CE (cardinality estimator)
- estimating cardinality
ms.assetid: baa8a304-5713-4cfe-a699-345e819ce6df
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: fcbc86a73fb6ac70fce78c381a5aac1f1accc108
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171780"
---
# <a name="cardinality-estimation-sql-server"></a>基數估計 (SQL Server)

[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢最佳化工具是以成本為基礎的查詢最佳化工具。 這表示它會選取估計處理成本最低的查詢計畫來執行。 查詢最佳化工具根據兩個主要因素來判斷執行查詢計劃的成本：

- 在查詢計畫的每一個層級進行處理的資料列總數，此稱為計畫的基數。
- 查詢中使用的運算子所指定的演算法成本模型。

第一個因數 (基數) 會作為第二個因數 (成本模型) 的輸入參數。 因此，如果改善基數，便能產生更好的估計成本，進而可有更快的執行計畫。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的基數估計主要衍生自於建立索引或統計資料時，手動或自動建立的長條圖。 有時候，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也會使用條件約束資訊及查詢的邏輯重寫來判斷基數。

在下列情況中，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無法精確地計算基數。 這會導致不精確的成本計算，使得產生並非最佳的查詢計畫。 避免在查詢中使用這些建構可以提升查詢效能。 有時也可以使用替代的查詢公式或其他方法，它們是：

- 查詢的述詞，在相同資料表的不同資料行之間使用比較運算子。
- 查詢的述詞使用運算子，且下列任一情況為真：
  - 運算子任一邊所關聯的資料行中，沒有任何統計資料。
  - 統計資料中的值分佈並不平均，但查詢會搜尋具有高度選擇性的值集。 如果運算子不是等號 (=) 運算子，此情況會特別明顯。
  - 述詞使用不等於 (!=) 比較運算子或 `NOT` 邏輯運算子。
- 查詢，其使用任一個 SQL Server 內建函式，或引數不是常數值之純量值的使用者定義函式。
- 查詢透過算術或字串串連運算子，與聯結資料行相關聯。
- 查詢所比較的變數，在編譯及最佳化查詢時其值不明。

本文說明如何評估及選擇最適合您系統的 CE 設定。 大多數系統皆可從最新的 CE 中受益，因為它的精確度最高。 CE 會預測您的查詢可能傳回的資料列數目。 查詢最佳化工具使用基數預測，來產生最佳查詢計劃。 由於估計較精確，因此查詢最佳化工具通常較有機會產生較佳的查詢計劃。  
  
應用程式系統可能有項重要的查詢，因為 CE 在版本中的變更，所以該查詢的計劃將變更為速度較慢的計劃。 您可使用幾種方法與工具來辨別因為 CE 問題而執行較慢的查詢。 您也可以選擇要如何解決後續的效能問題。
  
## <a name="versions-of-the-ce"></a>CE 的版本

在組建 1998 中，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 7.0 隨附提供 CE 的一項重大更新，其相容性層級為 70。 這個版本的 CE 模型會根據四個基本假設設定：

-  **獨立性：** 在不同資料行散發的資料會假設為各自獨立，除非提供可用的相互關聯資訊。
-  **一致性：** 相異值會平均分佈，使其全都具有相同頻率。 更明確地說，在每個[長條圖](../../relational-databases/statistics/statistics.md#histogram)步驟中，相異值會平均分布且各值都具有相同頻率。 
-  **內含項目 (簡單)：** 使用者查詢存在的資料。 舉例來說，就兩個資料表間的相等聯結而言，會在聯結長條圖以預估聯結選擇性前，將各輸入長條圖中的述詞選擇性<sup>1</sup> 納入考量。 
-  **包含詞/句：** 對於 `Column = Constant` 的篩選述詞而言，會假設相關聯資料行的常數實際存在。 若對應的長條圖步驟為非空白，則其中一個步驟的相異值會假設為符合來自述詞的值。

  <sup>1</sup> 滿足述詞的資料列計數。

後續更新從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始，表示相容性層級為 120 及以上。 層級 120 及以上的 CE 更新併入已更新假設和演算法，適用於新式資料倉儲和 OLTP 工作負載。 根據 CE 70 假設，下列模型假設已從 CE 120 起變更：

-  **獨立性** 變成 **相互關聯**：不同資料行值的結合不需要獨立。 這可能會更類似實際資料查詢。
-  **簡單內含項目** 變成 **基本內含項目**：使用者可查詢不存在的資料。 舉例來說，就兩個資料表間的相等聯結而言，我們會使用基底資料表長條圖來預估聯結選擇性，然後將述詞選擇性納入考量。
  
**相容性層級：** 您可為 [COMPATIBILITY_LEVEL](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md) 使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 程式碼，來確認資料庫處於特定層級。  

```sql  
SELECT ServerProperty('ProductVersion');  
GO  
  
ALTER DATABASE <yourDatabase>  
SET COMPATIBILITY_LEVEL = 130;  
GO  
  
SELECT d.name, d.compatibility_level  
FROM sys.databases AS d  
WHERE d.name = 'yourDatabase';  
GO  
```  
  
若是相容性層級設定為 120 或以上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫，啟用[追蹤旗標 9481](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) 會強制系統使用 CE 版本 70。  
  
**舊版 CE：** 若是相容性層級設定為 120 以上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫，可使用 [ALTER DATABASE SCOPED CONFIGURATION](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md) 在資料庫層級啟用 CE 版本 70。
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION 
SET LEGACY_CARDINALITY_ESTIMATION = ON;  
GO  
  
SELECT name, value  
FROM sys.database_scoped_configurations  
WHERE name = 'LEGACY_CARDINALITY_ESTIMATION';  
GO
```  
 
或者從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 開始，使用[查詢提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint) `USE HINT ('FORCE_LEGACY_CARDINALITY_ESTIMATION')`。
 
 ```sql  
SELECT CustomerId, OrderAddedDate  
FROM OrderTable  
WHERE OrderAddedDate >= '2016-05-01'
OPTION (USE HINT ('FORCE_LEGACY_CARDINALITY_ESTIMATION'));  
```
 
**查詢存放區：** 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，提供查詢存放區工具，方便您檢查查詢的效能。 在 [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] 中，啟用查詢存放區的情況下，**物件總管** 中的資料庫節點下會顯示 **查詢存放區** 節點。  
  
```sql  
ALTER DATABASE <yourDatabase>  
SET QUERY_STORE = ON;  
GO  
  
SELECT q.actual_state_desc AS [actual_state_desc_of_QueryStore],  
        q.desired_state_desc,  
        q.query_capture_mode_desc  
FROM sys.database_query_store_options AS q;  
GO  
  
ALTER DATABASE <yourDatabase>  
SET QUERY_STORE CLEAR;  
```  
  
> [!TIP] 
> 建議您安裝最新版本的 [Management Studio](../../ssms/download-sql-server-management-studio-ssms.md)，並經常進行更新。  

> [!IMPORTANT] 
> 請確定已經為資料庫和工作負載正確設定查詢存放區。 如需詳細資訊，請參閱[查詢存放區的最佳做法](../../relational-databases/performance/best-practice-with-the-query-store.md)。 
  
追蹤基數估計處理序的另一個做法，是使用名為 **query_optimizer_estimate_cardinality** 的擴充事件。 下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 程式碼範例會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上執行。 其會將 .xel 檔案寫入 `C:\Temp\` (不過您可變更路徑)。 當您在 [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] 中開啟 .xel 檔案時，會以使用方便的方式來顯示其詳細資訊。  
  
```sql  
DROP EVENT SESSION Test_the_CE_qoec_1 ON SERVER;  
go  
  
CREATE EVENT SESSION Test_the_CE_qoec_1  
ON SERVER  
ADD EVENT sqlserver.query_optimizer_estimate_cardinality  
    (  
        ACTION (sqlserver.sql_text)  
            WHERE (  
                sql_text LIKE '%yourTable%'  
                and sql_text LIKE '%SUM(%'  
            )  
    )  
ADD TARGET package0.asynchronous_file_target   
        (SET  
            filename = 'c:\temp\xe_qoec_1.xel',  
            metadatafile = 'c:\temp\xe_qoec_1.xem'  
        );  
GO  
  
ALTER EVENT SESSION Test_the_CE_qoec_1  
ON SERVER  
STATE = START;  --STOP;  
GO  
```  
  
如需為 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 量身訂做之擴充事件的相關資訊，請參閱 [SQL Database 中的擴充事件](/azure/azure-sql/database/xevent-db-diff-from-svr)。  
  
## <a name="steps-to-assess-the-ce-version"></a>評估 CE 版本的步驟  
  
接下來的步驟可讓您用來評估是否有任何最重要查詢在最新 CE 下的執行效能不佳。 其中一些步驟是透過執行上一節所示的程式碼範例來進行。  
  
1.  開啟 [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)]。 確定您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫已設定為最高可用的相容性層級。  
  
2.  執行下列預備步驟：  
  
    1.  開啟 [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)]。  
  
    2.  執行 T-SQL，確定您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫已設定為最高可用的相容性層級。  
  
    3.  確定您的資料庫已關閉其 `LEGACY_CARDINALITY_ESTIMATION` 組態。  
  
    4.  清除查詢存放區。 確認查詢存放區處於開啟狀態。  
  
    5.  執行陳述式：`SET NOCOUNT OFF;`  
  
3.  執行陳述式：`SET STATISTICS XML ON;`  
  
4.  執行您的重要查詢。  
  
5.  記下結果窗格中 [訊息]  索引標籤上實際受影響的資料列數目。  
  
6.  在結果窗格的 [結果]  索引標籤上，按兩下包含 XML 格式統計資料的資料格。 圖形查詢計劃隨即顯示。  
  
7.  以滑鼠右鍵按一下圖形查詢計劃中的第一個方塊，然後按一下 [屬性]  。  
  
8.  為了在稍後比較不同的組態，請記下下列屬性的值：  
  
    -   [CardinalityEstimationModelVersion]  。  
  
    -   [估計的資料列數目]  。  
  
    -   [估計的 I/O 成本]  ，以及涉及實際效能 (而不是資料列計數預測) 的幾個類似 [估計]  屬性。  
  
    -   [邏輯作業]  和 [實體作業]  。 [平行處理原則]  是正確值。  
  
    -   [實際的執行模式]  。 [批次]  是正確值，優於 [資料列]  。  
  
9. 比較估計的資料列數目與實際的資料列數目。 CE 誤差是 1% (高或低)，或是 10%？  
  
10. 執行：`SET STATISTICS XML OFF;`  
  
11. 執行 T-SQL 將您的資料庫相容性層級降低一個層級 (例如從 130 降低到 120)。  
  
12. 重新執行所有非預備步驟。  
  
13. 比較兩次執行的 CE 屬性值。  
  
    - 最新 CE 的誤差百分比是否小於舊版 CE？  
  
14. 最後，比較這兩次執行的各種效能屬性值。  
  
    -   您的查詢是否在這兩個不同的 CE 估計下使用不同的計劃？  
  
    -   您的查詢在最新的 CE 下是否執行得較慢？  
  
    -   除非您的查詢在舊版 CE 下的執行效果更佳且使用不同的計劃，否則幾乎可以確定您需要最新的 CE。  
  
    -   不過，如果您的查詢在舊版 CE 下執行更快速的計劃，請考慮強制系統使用更快速的計劃並忽略此 CE。 如此一來，您不但可以針對所有項目使用最新的 CE，同時也可以在偶爾的情況下保留更快速的計劃。  
  
## <a name="how-to-activate-the-best-query-plan"></a>如何啟用最佳查詢計劃  
  
假設 CE120 或以上版本為您的查詢產生了效率較低的查詢計劃。 以下選項可讓您啟用更佳的計劃：  
  
1. 您可以將整個資料庫的相容性層級設定成比最新可用的值更低。  
  
   - 例如，將相容性層級設為 110 或更低來啟用 CE 70，不過這會使所有查詢受限於先前的 CE 模型。  
  
   - 此外，設定較低的相容性層級，也會錯過最新版本中查詢最佳化工具的一些改善。  
  
2. 您可以使用 `LEGACY_CARDINALITY_ESTIMATION` 資料庫選項讓整個資料庫使用舊版 CE，同時保留查詢最佳化工具的改善。   

3. 您可以使用 `LEGACY_CARDINALITY_ESTIMATION` 查詢提示，讓單一資料庫使用舊版 CE，同時保留查詢最佳化工具的改善。  
  
若要進行最精細的控制，您可以「強制」  系統在測試期間使用透過 CE 70 所產生的計劃。 「固定」  您慣用的計劃之後，您可以將整個資料庫設定為使用最新的相容性層級和 CE。 接下來將會詳細說明這個選項。  
  
### <a name="how-to-force-a-particular-query-plan"></a>如何強制執行特定查詢計劃  
  
查詢存放區提供不同的方式，讓您強制系統使用特定查詢計劃：  
  
- 執行 **sp_query_store_force_plan**。  
  
- 在 [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)] 中，展開您的 [查詢存放區]  節點，以滑鼠右鍵按一下 [Top Resource Consuming Nodes] \(資源耗用量排名在前的節點)  ，然後按一下 [View Top Resource Consuming Nodes] \(檢視資源耗用量排名在前的節點)  。 這會顯示標示為 **[強制執行計畫]** 和 **[取消強制執行計畫]** 的按鈕。  
  
如需查詢存放區的詳細資訊，請參閱 [使用查詢存放區監視效能](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)。  
  
## <a name="examples-of-ce-improvements"></a>CE 改善範例  
  
本節描述因實作最新版 CE 的增強功能而受益的範例查詢。 這是背景資訊，不需要您執行特定動作。  
  
### <a name="example-a-ce-understands-maximum-value-might-be-higher-than-when-statistics-were-last-gathered"></a>範例 A：CE 認為最大值可能比上次收集統計資料時還要高  
  
假設統計資料是在 `OrderAddedDate` 上限為 `2016-04-30`時，於 `2016-04-30`為 `OrderTable` 收集的狀況。 CE 120 (和更新版本) 會認知具有「遞增」  資料之 `OrderTable` 中的資料行，可能會具有大於統計資料所記錄之最大值的值。 此認知會改善 [!INCLUDE[tsql](../../includes/tsql-md.md)] SELECT 陳述式的查詢計劃，如下所示。  
  
```sql  
SELECT CustomerId, OrderAddedDate  
FROM OrderTable  
WHERE OrderAddedDate >= '2016-05-01';  
```  
  
### <a name="example-b-ce-understands-that-filtered-predicates-on-the-same-table-are-often-correlated"></a>範例 B：CE 認為相同資料表上的篩選述詞通常相互關聯  
  
在下列 SELECT 中，我們可看到 `Model` 和 `ModelVariant` 的篩選述詞。 我們直覺認為當 `Model` 為 'Xbox' 時，`ModelVariant` 有可能為 'One' (有鑑於 Xbox 有一個名為 One 的變數)。  
  
CE 120 起，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可認知相同資料表上的兩個資料行 [`Model`] 和 [`ModelVariant`] 之間可能會相互關聯。 CE 可更準確地的估計查詢所要傳回的資料列數目，而且[查詢最佳化工具](../../relational-databases/query-processing-architecture-guide.md#optimizing-select-statements)能產生更佳的計劃。  
  
```sql  
SELECT Model, Purchase_Price  
FROM dbo.Hardware  
WHERE Model = 'Xbox' AND  
      ModelVariant = 'One';  
```  
  
### <a name="example-c-ce-no-longer-assumes-any-correlation-between-filtered-predicates-from-different-tables"></a>範例 C：CE 不會再假設來自不同資料表的篩選述詞之間有任何相互關聯 
對新式工作負載和實際商務資料的最新詳細研究顯示，來自不同資料表的述詞篩選條件通常彼此沒有關聯。 在下列查詢中，CE 假設 `s.type` 和 `r.date` 之間沒有相互關聯。 因此，CE 所估計的傳回資料列數目比較低。  
  
```sql  
SELECT s.ticket, s.customer, r.store  
FROM dbo.Sales AS s  
CROSS JOIN dbo.Returns AS r  
WHERE s.ticket = r.ticket AND  
      s.type = 'toy' AND  
      r.date = '2016-05-11';  
```  
  
## <a name="see-also"></a>另請參閱  
 [效能的監視與微調](../../relational-databases/performance/monitor-and-tune-for-performance.md)   
 [Optimizing Your Query Plans with the SQL Server 2014 Cardinality Estimator](/previous-versions/dn673537(v=msdn.10)) (使用 SQL Server 2014 基數估算程式最佳化您的查詢計劃)  
 [查詢提示](../../t-sql/queries/hints-transact-sql-query.md)     
 [USE HINT 查詢提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint)       
 [使用查詢調整小幫手來升級資料庫](../../relational-databases/performance/upgrade-dbcompat-using-qta.md)           
 [相關檢視、函數與程序](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)    
 [查詢處理架構指南](../../relational-databases/query-processing-architecture-guide.md)