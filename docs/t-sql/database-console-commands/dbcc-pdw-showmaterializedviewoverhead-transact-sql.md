---
description: DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD (Transact-SQL)
title: DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD  (Transact-SQL)
ms.custom: seo-dt-2019
ms.date: 07/03/2019
ms.prod: sql
ms.technology: data-warehouse
ms.prod_service: sql-data-warehouse, pdw
ms.reviewer: ''
ms.topic: language-reference
dev_langs:
- TSQL
author: XiaoyuMSFT
ms.author: xiaoyul
monikerRange: = azure-sqldw-latest
ms.openlocfilehash: 87f02544512f4cd33385242101204e3d30c81ffe
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643934"
---
# <a name="dbcc-pdw_showmaterializedviewoverhead-transact-sql"></a>DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD (Transact-SQL)  

[!INCLUDE [asa](../../includes/applies-to-version/asa.md)]

在針對 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 中具體化檢視保存的基底資料表中顯示增量變更。 額外負荷率的計算方式為 TOTAL_ROWS / MAX (1, BASE_VIEW_ROWS)。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例 &#40;Transact-SQL&#41;](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法

```syntaxsql
DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD ( " [ schema_name .] materialized_view_name  " )
[;]
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="arguments"></a>引數

 *schema_name*     
 這是檢視所屬的結構描述名稱。

*materialized_view_name*   
是具體化檢視的名稱。

## <a name="remarks"></a>備註

資料倉儲引擎會將追蹤資料列新增至每個受影響檢視以反映所做的變更，以便隨即重新整理具體化檢視。 從具體化檢視選取包括掃描檢視的叢集資料行存放區索引，以及套用這些增量變更。追蹤資料列 (TOTAL_ROWS-BASE_VIEW_ROWS) 在使用者 REBUILD 具體化檢視之前不會被刪除。  

Overhead_ratio 的計算方式為 TOTAL_ROWS/MAX(1, BASE_VIEW_ROWS)。  如果很高，則 SELECT 效能將會降低。使用者可以重建具體化檢視以降低其額外負荷率。

## <a name="permissions"></a>權限  
  
需要 VIEW DATABASE STATE 權限。  

## <a name="examples"></a>範例  

### <a name="a-this-example-returns-the-overhead-ratio-of-a-materialized-view"></a>A. 此範例會傳回具體化檢視的額外負荷率。

```sql
DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD ( "dbo.MyIndexedView" )
```

輸出：

|OBJECT_ID|BASE_VIEW_ROWS|TOTAL_ROWS|OVERHEAD_RATIO|
|--------|--------|--------|--------|  
|1234|1|3 |3.0 |

</br>

### <a name="b-this-example-shows-how-the-materialized-view-overhead-increases-as-data-changes-in-base-tables"></a>B. 這個範例會顯示當基底資料表的資料變更時，具體化檢視的額外負荷如何增加

建立資料表
```sql
CREATE TABLE t1 (c1 INT NOT NULL, c2 INT NOT NULL, c3 INT NOT NULL)
```
將五個資料列插入 t1
```sql
INSERT INTO t1 VALUES (1, 1, 1)
INSERT INTO t1 VALUES (2, 2, 2) 
INSERT INTO t1 VALUES (3, 3, 3) 
INSERT INTO t1 VALUES (4, 4, 4) 
INSERT INTO t1 VALUES (5, 5, 5) 
```
建立具體化檢視 MV1
```sql
CREATE MATERIALIZED VIEW MV1 
WITH (DISTRIBUTION = HASH(c1))  
AS
SELECT c1, COUNT(*) total_number 
FROM dbo.t1 WHERE c1 < 3
GROUP BY c1  
```
從具體化檢視中選取會傳回兩個資料列。

|c1|total_number|
|--------|--------| 
|1|1| 
|2|1|

在基底資料表的任何資料變更之前檢查具體化檢視的額外負荷。
```sql
DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD ("dbo.mv1")
```
輸出：

|OBJECT_ID|BASE_VIEW_ROWS|TOTAL_ROWS|OVERHEAD_RATIO|
|--------|--------|--------|--------|  
|587149137|2|2 |1.00000000000000000 |

更新基底資料表。  此查詢會將相同資料列中相同資料行更新為相同的值 100 次。  具體化檢視內容不會變更。
```sql
DECLARE @p INT
SELECT @p = 1
WHILE (@p < 101)
BEGIN
UPDATE t1 SET c1 = 1 WHERE c1 = 1
SELECT @p = @p+1
END  
```

從具體化檢視中選取，會傳回與之前相同的結果。  

|c1|total_number|
|--------|--------| 
|1|1| 
|2|1|

以下是 DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD ("dbo. mv1") 的輸出。  具體化檢視中會新增 100 個資料列 (total_row - base_view_rows)，且 overhead_ratio 也會增加。 

|OBJECT_ID|BASE_VIEW_ROWS|TOTAL_ROWS|OVERHEAD_RATIO|
|--------|--------|--------|--------|  
|587149137|2|102 |51.00000000000000000 |

重建具體化檢視之後，將刪除所有增量資料變更的追蹤資料列，並減少檢視額外負荷率。  

```sql
ALTER MATERIALIZED VIEW dbo.MV1 REBUILD
GO
DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD ("dbo.mv1")
```
輸出

|OBJECT_ID|BASE_VIEW_ROWS|TOTAL_ROWS|OVERHEAD_RATIO|
|--------|--------|--------|--------|  
|587149137|2|2 |1.00000000000000000 |

## <a name="see-also"></a>另請參閱

[使用具體化檢視進行效能調整](/azure/sql-data-warehouse/performance-tuning-materialized-views)   
[CREATE MATERIALIZED VIEW AS SELECT &#40;Transact-SQL&#41;](../statements/create-materialized-view-as-select-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[ALTER MATERIALIZED VIEW &#40;Transact-SQL&#41;](../statements/alter-materialized-view-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[EXPLAIN &#40;Transact-SQL&#41;](../queries/explain-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_column_distribution_properties &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-column-distribution-properties-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_distribution_properties &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-distribution-properties-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_mappings &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-mappings-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[Azure Synapse Analytics 和平行處理資料倉儲目錄檢視](../../relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views.md)   
[Azure Synapse Analytics 中支援的系統檢視](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views)   
[Azure Synapse Analytics 中支援的 T-SQL 陳述式](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)