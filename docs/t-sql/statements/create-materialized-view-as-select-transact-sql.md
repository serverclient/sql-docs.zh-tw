---
description: CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL)
title: CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/04/2020
ms.prod: sql
ms.prod_service: sql-data-warehouse
ms.reviewer: jrasnick
ms.technology: data-warehouse
ms.topic: language-reference
f1_keywords:
- CREATE VIEW
- VIEW_TSQL
- VIEW
- CREATE_VIEW_TSQL
- SCHEMABINDING_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- table creation [SQL Server], CREATE VIEW
- views [SQL Server], creating
- CREATE VIEW statement
- updatable partitioned views
- tables [SQL Server], virtual
- updatable views
- modifying partitioned views
- virtual tables [SQL Server]
- number of columns per view
- partitioned views [SQL Server], creating
- WITH ENCRYPTION clause
- WITH CHECK OPTION clause
- partitioned views [SQL Server], modifying
- partitioned views [SQL Server], replication
- distributed partitioned views [SQL Server]
- views [SQL Server], indexed views
- maximum number of columns per view
ms.assetid: aecc2f73-2ab5-4db9-b1e6-2f9e3c601fb9
author: XiaoyuMSFT
ms.author: xiaoyul
monikerRange: =azure-sqldw-latest
ms.openlocfilehash: dc48dc319a628b389479eacb0c68b702f796a836
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644394"
---
# <a name="create-materialized-view-as-select-transact-sql"></a>CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL)  

[!INCLUDE [Azure Synapse Analytics](../../includes/applies-to-version/asa.md)]

此文章說明 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 中用於開發解決方案的 CREATE MATERIALIZED VIEW AS SELECT T-SQL 陳述式。 此文章也提供程式碼範例。

具體化檢視會保存從檢視定義查詢傳回的資料，並在底層資料表中的資料變更時自動取得更新。   它可以改進複雜查詢 (通常是具有聯結與會總的查詢) 的效能，同時提供簡單的維護作業。   使用其執行計畫的自動比對功能，不需要在查詢中參考具體化檢視，最佳化工具就能考慮要替代的檢視。  這項功能可讓資料工程師將具體化檢視實作為改善查詢回應時間的機制，而不需要變更查詢。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE MATERIALIZED VIEW [ schema_name. ] materialized_view_name
    WITH (  
      <distribution_option>
    )
    AS <select_statement>
[;]

<distribution_option> ::=
    {  
        DISTRIBUTION = HASH ( distribution_column_name )  
      | DISTRIBUTION = ROUND_ROBIN  
    }

<select_statement> ::=
    SELECT select_criteria

```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]
  
## <a name="arguments"></a>引數

*schema_name*     
 這是檢視所屬的結構描述名稱。  
  
*materialized_view_name*   
這是檢視的名稱。 檢視名稱必須遵照識別碼的規則。 指定檢視擁有者名稱則是選擇性的。  

*分佈選項*     
只支援 HASH 與 ROUND_ROBIN 分佈。

*select_statement*   
具體化檢視定義中的 SELECT 清單必須符合下列兩個條件的至少其中一個：
- SELECT 清單包含彙總函式。
- GROUP BY 用於具體化檢視定義中，而且 GROUP BY 中的所有資料行都包括在 SELECT 清單中。  GROUP BY 子句中最多可以使用 32 個資料行。

具體化檢視定義的 SELECT 清單中需要彙總函式。  支援的彙總包括 MAX、MIN、AVG、COUNT、COUNT_BIG、SUM、VAR、STDEV。

當在具體化檢視定義的 SELECT 清單中使用 MIN/MAX 彙總時，必須符合下列需求：
 
- 需要 FOR_APPEND。  例如：
  ```sql 
  CREATE MATERIALIZED VIEW mv_test2  
  WITH (distribution = hash(i_category_id), FOR_APPEND)  
  AS
  SELECT MAX(i.i_rec_start_date) as max_i_rec_start_date, MIN(i.i_rec_end_date) as min_i_rec_end_date, i.i_item_sk, i.i_item_id, i.i_category_id
  FROM syntheticworkload.item i  
  GROUP BY i.i_item_sk, i.i_item_id, i.i_category_id
  ```

- 當參考的基底資料表中發生 UPDATE 或 DELETE 時，將停用具體化檢視。  此限制不適用於 INSERT。  若要重新啟用具體化檢視，請搭配 REBUILD 執行 ALTER MATERIALIZED VIEW。
  
## <a name="remarks"></a>備註

Azure 資料倉儲中具體化檢視類似 SQL Server 中的索引檢視表。它的限制幾乎與索引檢視表相同 (請參閱[建立索引檢視表](../../relational-databases/views/create-indexed-views.md)以取得詳資訊)，不過具體化檢視支援彙總函式。   

>[!Note]
>雖然 CREATE MATERIALIZED VIEW 不支援 COUNT、DISTINCT、COUNT(DISTINCT 運算式) 或 COUNT_BIG (DISTINCT 運算式)，但使用這些函數的 SELECT 查詢仍可因具體化檢視獲得更快效能，因為 Synapse SQL 最佳化工具可在使用者查詢中自動重新寫入這些彙總，以符合現有的具體化檢視。  如需詳細資料，請參閱本文中的範例一節。 

CREATE MATERIALIZED VIEW AS SELECT 中不支援 APPROX_COUNT_DISTINCT。

只有具體化檢視才支援 CLUSTERED COLUMNSTORE INDEX。 

具體化檢視無法參考其他檢視。  

您無法在具有動態資料遮罩 (DDM) 的資料表上建立具體化檢視，即使 DDM 資料行不是具體化檢視的一部分亦是如此。  如果資料表資料行是使用中具體化檢視或已停用具體化檢視的一部分，則無法將 DDM 新增至此資料行。  

無法在已啟用資料列層級安全性的資料表上建立具體化檢視。

具體化檢視可在資料分割資料表上建立。  具體化檢視的基底資料表支援分割區 SPLIT/MERGE，不支援分割區 SWITCH。  
 
具體化檢視中參考的資料表上不支援 ALTER TABLE SWITCH。 在使用 ALTER TABLE SWITCH 之前停用或捨棄具體化檢視。 在下列案例中，具體化檢視建立要求必須將新資料行新增到具體化檢視：

|狀況|新資料行必須新增到具體化檢視|註解|  
|-----------------|---------------|-----------------|
|在具體化檢視定義的 SELECT 清單中，遺漏 COUNT_BIG()| COUNT_BIG (*) |已由具體化檢視建立自動新增。  使用者不必採取任何動作。|
|SUM(a) 是由使用者在具體化檢視定義的 SELECT 清單中所指定，且 'a' 是可為 Null 的運算式 |COUNT_BIG (a) |使用者必須手動在具體化檢視定義中新增運算式 'a'。|
|AVG(a) 是由使用者在具體化檢視定義的 SELECT 清單中所指定，其中 'a' 是運算式。|SUM(a)、COUNT_BIG(a)|已由具體化檢視建立自動新增。  使用者不必採取任何動作。|
|STDEV(a) 是由使用者在具體化檢視定義的 SELECT 清單中所指定，其中 'a' 是運算式。|SUM(a)、COUNT_BIG(a)、SUM(square(a))|已由具體化檢視建立自動新增。  使用者不必採取任何動作。 |
| | | |

一旦建立，具體化檢視在 SQL Server Management Studio 內 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 執行個體的檢視資料夾下就是可見的。

使用者可以執行 [SP_SPACEUSED](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md?view=azure-sqldw-latest&preserve-view=true) 與 [DBCC PDW_SHOWSPACEUSED](../database-console-commands/dbcc-pdw-showspaceused-transact-sql.md?view=azure-sqldw-latest&preserve-view=true) 來判斷具體化檢視取用的空間。  

具體化檢視可透過 DROP VIEW 來捨棄。  您可以使用 ALTER MATERIALIZED VIEW 來停用或重建具體化檢視。   

SQL Server Management Studio 中的 EXPLAIN 計畫與圖形化預估執行計畫可以顯示具體化檢視是否由查詢最佳化工具考量為查詢執行使用。 SQL Server Management Studio 中的 與圖形化預估執行計畫可以顯示具體化檢視是否由查詢最佳化工具考量為查詢執行使用。

若要判斷 SQL 陳述式是否可從新的具體化檢視獲益，請搭配 `WITH_RECOMMENDATIONS` 執行 `EXPLAIN` 命令。  如需詳細資訊，請參閱 [EXPLAIN (Transact-SQL)](../queries/explain-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)。

## <a name="permissions"></a>權限

需要 1) REFERENCES 與 CREATE VIEW 權限，或 2) 要在其中建立檢視之結構描述上的 CONTROL 權限。 

## <a name="example"></a>範例
A. 此範例示範儘管查詢使用 CREATE MATERIALIZED VIEW 中不支援的函數 (例如 COUNT(DISTINCT 運算式))，Synapse SQL 最佳化工具也能自動使用具體化檢視來執行查詢，以提高效能。 原本需要數秒鐘才能完成的查詢，現在只需要不到一秒即可完成，且無須進行任何使用者查詢變更。   

``` sql 

-- Create a table with ~536 million rows
create table t(a int not null, b int not null, c int not null) with (distribution=hash(a), clustered columnstore index);

insert into t values(1,1,1);

declare @p int =1;
while (@P < 30)
    begin
    insert into t select a+1,b+2,c+3 from t;  
    select @p +=1;
end

-- A SELECT query with COUNT_BIG (DISTINCT expression) took multiple seconds to complete and it reads data directly from the base table a. 
select a, count_big(distinct b) from t group by a;

-- Create two materialized views, not using COUNT_BIG(DISTINCT expression).
create materialized view V1 with(distribution=hash(a)) as select a, b from dbo.t group by a, b;

-- Clear all cache.

DBCC DROPCLEANBUFFERS;
DBCC freeproccache;

-- Check the estimated execution plan in SQL Server Management Studio.  It shows the SELECT query is first step (GET operator) is to read data from the materialized view V1, not from base table a.
select a, count_big(distinct b) from t group by a;

-- Now execute this SELECT query.  This time it took sub-second to complete because Synapse SQL engine automatically matches the query with materialized view V1 and uses it for faster query execution.  There was no change in the user query.

DECLARE @timerstart datetime2, @timerend datetime2;
SET @timerstart = sysdatetime();

select a, count_big(distinct b) from t group by a;

SET @timerend = sysdatetime()
select DATEDIFF(ms,@timerstart,@timerend);

```

  
## <a name="see-also"></a>另請參閱

[使用具體化檢視進行效能調整](/azure/sql-data-warehouse/performance-tuning-materialized-views)   
[ALTER MATERIALIZED VIEW &#40;Transact-SQL&#41;](./alter-materialized-view-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)      
[DROP VIEW](./drop-view-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)  
[EXPLAIN &#40;Transact-SQL&#41;](../queries/explain-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_column_distribution_properties &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-column-distribution-properties-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_distribution_properties &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-distribution-properties-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[sys.pdw_materialized_view_mappings &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-pdw-materialized-view-mappings-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD &#40;Transact-SQL&#41;](../database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)   
[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 目錄檢視](../../relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views.md)   
[Azure [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 中支援的系統檢視](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views)   
[Azure [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 中支援的 T-SQL 陳述式](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)