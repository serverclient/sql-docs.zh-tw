---
description: DELETE (Transact-SQL)
title: DELETE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/19/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DELETE
- DELETE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- row removal [SQL Server]
- DELETE statement [SQL Server], about DELETE statement
- views [SQL Server], deleting rows
- removing rows
- tables [SQL Server], deleting rows
- DELETE statement [SQL Server]
- deleting rows
- row removal [SQL Server], DELETE statement
- deleting data
ms.assetid: ed6b2105-0f35-408f-ba51-e36ade7ad5b2
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b034dd3203324154d1a2742615df0f916916bfdc
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100832"
---
# <a name="delete-transact-sql"></a>DELETE (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的資料表或檢視移除一或多個資料列。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
[ WITH <common_table_expression> [ ,...n ] ]  
DELETE   
    [ TOP ( expression ) [ PERCENT ] ]   
    [ FROM ]   
    { { table_alias  
      | <object>   
      | rowset_function_limited   
      [ WITH ( table_hint_limited [ ...n ] ) ] }   
      | @table_variable  
    }  
    [ <OUTPUT Clause> ]  
    [ FROM table_source [ ,...n ] ]   
    [ WHERE { <search_condition>   
            | { [ CURRENT OF   
                   { { [ GLOBAL ] cursor_name }   
                       | cursor_variable_name   
                   }   
                ]  
              }  
            }   
    ]   
    [ OPTION ( <Query Hint> [ ,...n ] ) ]   
[; ]  
  
<object> ::=  
{   
    [ server_name.database_name.schema_name.   
      | database_name. [ schema_name ] .   
      | schema_name.  
    ]  
    table_or_view_name   
}  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics

[ WITH <common_table_expression> [ ,...n ] ] 
DELETE [database_name . [ schema ] . | schema. ] table_name  
FROM [database_name . [ schema ] . | schema. ] table_name 
JOIN {<join_table_source>}[ ,...n ]  
ON <join_condition>
[ WHERE <search_condition> ]   
[ OPTION ( <query_options> [ ,...n ]  ) ]  
[; ]  

<join_table_source> ::=   
{  
    [ database_name . [ schema_name ] . | schema_name . ] table_or_view_name [ AS ] table_or_view_alias 
    [ <tablesample_clause>]  
    | derived_table [ AS ] table_alias [ ( column_alias [ ,...n ] ) ]  
}  
```

```syntaxsql
-- Syntax for Parallel Data Warehouse  
  
DELETE 
    [ FROM [database_name . [ schema ] . | schema. ] table_name ]   
    [ WHERE <search_condition> ]   
    [ OPTION ( <query_options> [ ,...n ]  ) ]  
[; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 WITH \<common_table_expression>  
 指定定義在 DELETE 陳述式範圍內的暫存具名結果集，也稱為一般資料表運算式。 這個結果集是從 SELECT 陳述式衍生而來。  
  
 一般資料表運算式也可以搭配 SELECT、INSERT、UPDATE 和 CREATE VIEW 等陳述式來使用。 如需詳細資訊，請參閱 [WITH common_table_expression &#40;Transact-SQL&#41;](../../t-sql/queries/with-common-table-expression-transact-sql.md)。  
  
 TOP **(** _expression_ **)** [ PERCENT ]  
 指定要刪除的隨機資料列數或百分比。 *expression* 可以是一個數字，也可以是資料列的百分比。 搭配 INSERT、UPDATE 或 DELETE 使用的 TOP 運算式所參考的資料列並不依照任何順序來排列。 如需詳細資訊，請參閱 [TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md)。  
  
 FROM  
 這是一個選擇性的關鍵字，可用於 DELETE 關鍵字和目標 *table_or_view_name* 或 *rowset_function_limited* 之間。  
  
 *table_alias*  
 在 FROM *table_source* 子句中指定的別名，代表要刪除資料列的資料表或檢視。  
  
 *server_name*  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 資料表或檢視所在的伺服器名稱 (使用連結的伺服器名稱或 [OPENDATASOURCE](../../t-sql/functions/opendatasource-transact-sql.md) 函式當作伺服器名稱)。 若指定 *server_name*，則 *database_name* 和 *schema_name* 都為必要項目。  
  
 *database_name*  
 資料庫的名稱。  
  
 *schema_name*  
 資料表或檢視所屬之結構描述的名稱。  
  
 *table_or_view_name*  
 要移除資料列的資料表或檢視名稱。  
  
 資料表變數，在自身範圍內也可用來做為 DELETE 陳述式中的資料表來源。  
  
 *table_or_view_name* 所參考的檢視必須能夠更新，而且必須只參考該檢視定義之 FROM 子句中的單一基底資料表。 如需可更新檢視的詳細資訊，請參閱 [CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)。  
  
 *rowset_function_limited*  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 依提供者功能而定，會是 [OPENQUERY](../../t-sql/functions/openquery-transact-sql.md) 或 [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) 函式。  
  
 WITH **(** \<table_hint_limited> [... *n*] **)**  
 指定目標資料表允許使用的一個或多個資料表提示。 WITH 關鍵字和括號都是必要的。 不允許使用 NOLOCK 和 READUNCOMMITTED。 如需資料表提示的詳細資訊，請參閱[資料表提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)。  
  
 \<OUTPUT_Clause>  
 在 DELETE 作業中，傳回已刪除的資料列或以它們為基礎的運算式。 任何目標是檢視或遠端資料表的 DML 陳述式都不支援 OUTPUT 子句。 如需此子句引數和行為的詳細資訊，請參閱 [OUTPUT 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)。  
  
 FROM *table_source*  
 指定其他 FROM 子句。 DELETE 這個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 延伸模組可供指定 \<table_source> 中的資料，以及從第一個 FROM 子句的資料表中刪除對應資料列。  
  
 您可以利用這個指定聯結的延伸模組取代 WHERE 子句中的子查詢來識別要移除的資料列。  
  
 如需詳細資訊，請參閱 [FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)。  
  
 WHERE  
 指定用來限定刪除之資料列數的條件。 如果未提供 WHERE 子句，DELETE 會移除資料表中的所有資料列。  
  
 以 WHERE 子句指定的內容為基礎的刪除作業有兩種形式：  
  
-   搜尋刪除指定用來限定要刪除的資料列之搜尋條件。 例如，WHERE *column_name* = *value*。  
  
-   定位刪除利用 CURRENT OF 子句來指定資料指標。 刪除作業發生在資料指標目前的位置上。 這比使用 WHERE *search_condition* 子句來限定要刪除之資料列的搜尋 DELETE 陳述式還要精確。 如果搜尋條件並未唯一識別單一資料列，搜尋 DELETE 陳述式會刪除多個資料列。  
  
\<search_condition>  
 指定要刪除的資料列之限制條件。 搜尋條件中所能包括的述詞數目沒有限制。 如需詳細資訊，請參閱[搜尋條件 &#40;Transact-SQL&#41;](../../t-sql/queries/search-condition-transact-sql.md)。  
  
 CURRENT OF  
 指定在指定資料指標目前的位置執行 DELETE。  
  
 GLOBAL  
 指定 *cursor_name* 是全域資料指標。  
  
 *cursor_name*  
 這是從中提取資料的開啟資料指標名稱。 如果名稱為 *cursor_name* 的全域和本機資料指標同時存在，當指定 GLOBAL 時，這個引數會參考全域資料指標；否則，它會參考區域資料指標。 這個資料指標必須允許更新。  
  
 *cursor_variable_name*  
 資料指標變數的名稱。 資料指標變數必須參考允許更新的資料指標。  
  
 OPTION **(** \<query_hint> [ **,** ... *n*] **)**  
 關鍵字，它們會指出要使用哪一個最佳化工具提示來自訂 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 處理陳述式的方式。 如需詳細資訊，請參閱[查詢提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)。  
  
## <a name="best-practices"></a>最佳做法  
 若要刪除資料表中的所有資料列，請使用 TRUNCATE TABLE。 TRUNCATE TABLE 的速度比 DELETE 快，使用的系統資源和交易記錄資源也比較少。 TRUNCATE TABLE 有一些限制，例如，資料表不能參與複寫。 如需詳細資訊，請參閱 [TRUNCATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/truncate-table-transact-sql.md)  
  
 您可以使用 @@ROWCOUNT 函式，將刪除的資料列數目傳回用戶端應用程式。 如需詳細資訊，請參閱 [@@ROWCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/rowcount-transact-sql.md)。  
  
## <a name="error-handling"></a>錯誤處理  
 您可以在 TRY...CATCH 建構中指定 DELETE 陳述式，以實作此陳述式的錯誤處理。  
  
 如果 DELETE 陳述式違反觸發程序，或試圖移除含有 FOREIGN KEY 條件約束的另一個資料表中之資料所參考的資料列，DELETE 陳述式便可能失敗。 如果 DELETE 移除了多個資料列，且有任何移除的資料列違反了觸發程序或條件約束，就會取消陳述式，傳回錯誤，且不會移除任何資料列。  
  
 當 DELETE 陳述式遇到在運算式評估期間發生算術錯誤 (溢位、除以零或範圍錯誤) 時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會依照 SET ARITHABORT 設為 ON 的方式來處理這些錯誤。 此時會取消批次的其餘部分，且會傳回錯誤訊息。  
  
## <a name="interoperability"></a>互通性  
 如果修改的物件是資料表變數，就可以在使用者自訂函數的主體中使用 DELETE。  
  
 當您刪除包含 FILESTREAM 資料行的資料列時，也會刪除其基礎檔案系統的檔案。 基礎檔案會由 FILESTREAM 記憶體回收行程所移除。 如需詳細資訊，請參閱[使用 Transact-SQL 存取 FILESTREAM 資料](../../relational-databases/blob/access-filestream-data-with-transact-sql.md)。  
  
 在直接或間接參考定義了 INSTEAD OF 觸發程序的檢視之 DELETE 陳述式中，不能指定 FROM 子句。 如需 INSTEAD OF 觸發程序的詳細資訊，請參閱 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)。  
  
## <a name="limitations-and-restrictions"></a>限制事項  
 搭配 DELETE 使用 TOP 時，不會以任何順序排列參考的資料列，也不可以直接在這個陳述式中指定 ORDER BY 子句。 如果您需要使用 TOP 依有意義的時序來刪除資料列，就必須在 subselect 陳述式中搭配 ORDER BY 子句使用 TOP。 請參閱本主題稍後的＜範例＞一節。  
  
 TOP 不能用在針對資料分割檢視進行的 DELETE 陳述式。  
  
## <a name="locking-behavior"></a>鎖定行為  
 根據預設，DELETE 陳述式一律會在其修改的資料表物件中取得意圖獨佔 (IX) 鎖定，並保留該鎖定直到交易完成為止。 運用意圖獨佔 (IX) 鎖定，沒有其他交易可修改資料；只有使用 NOLOCK 提示或讀取未認可隔離等級，才能進行讀取作業。 您可以指定資料表提示，透過指定其他鎖定方法來覆寫 DELETE 陳述式持續時間的這個預設行為，但是，我們建議僅將提示做為由資深開發人員及資料庫系統管理員採取的最後手段。 如需詳細資訊，請參閱[資料表提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)。  
  
 當資料列從堆積中刪除時， [!INCLUDE[ssDE](../../includes/ssde-md.md)] 可能會在作業時使用資料列或頁面鎖定。 如此一來，由刪除作業清空的頁面仍然會配置給堆積。 如果未取消空白頁面的配置，資料庫中的其他物件就無法重複使用相關聯的空間。  
  
 若要刪除堆積中的資料列及取消配置頁面，請使用下列其中一個方法。  
  
-   在 DELETE 陳述式中指定 TABLOCK 提示。 使用 TABLOCK 提示會造成刪除作業對資料表進行獨佔鎖定，而非資料列或頁面鎖定。 如此可允許取消配置頁面。 如需 TABLOCK 的詳細資訊，請參閱[資料表提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)。  
  
-   如果要從資料表刪除所有資料列，請使用 `TRUNCATE TABLE`。  
  
-   請先在堆積上建立叢集索引之後，再刪除資料列。 您可以在刪除資料列之後卸除叢集索引。 這個方法會比之前的方法耗用更多的時間，而且會使用更多的暫存資源。  
  
> [!NOTE]  
>  您可以隨時使用 `ALTER TABLE <table_name> REBUILD` 陳述式將空白頁面從堆積移除。  
  
## <a name="logging-behavior"></a>記錄行為  
DELETE 陳述式一律會完整記錄。  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要目標資料表的 `DELETE` 權限。 如果陳述式包含 WHERE 子句，則也需要 `SELECT` 權限。  
  
 DELETE 權限預設為 `sysadmin` 固定伺服器角色、`db_owner` 和 `db_datawriter` 固定資料庫角色的成員，以及資料表擁有者。 `sysadmin`、`db_owner` 和 `db_securityadmin` 角色的成員及資料表擁有者可將權限移轉給其他使用者。  
  
## <a name="examples"></a>範例  
  
|類別|代表性語法元素|  
|--------------|------------------------------|  
|[基本語法](#BasicSyntax)|刪除|  
|[限制刪除的資料列](#LimitRows)|WHERE • FROM • 資料指標 •|  
|[從遠端資料表刪除資料列](#RemoteTables)|連結的伺服器 • OPENQUERY 資料列集函數 • OPENDATASOURCE 資料列集函數|  
|[擷取 DELETE 陳述式的結果](#CaptureResults)|OUTPUT 子句|  
  
###  <a name="basic-syntax"></a><a name="BasicSyntax"></a> 基本語法  
 本節的範例會使用最少的所需語法來示範 DELETE 陳述式的基本功能。  
  
#### <a name="a-using-delete-with-no-where-clause"></a>A. 使用不含 WHERE 子句的 DELETE  
 以下範例會刪除 `SalesPersonQuotaHistory` 資料庫中 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料表的所有資料列，因為並未利用 WHERE 子句來限制刪除的資料列數。  
  
```sql
DELETE FROM Sales.SalesPersonQuotaHistory;  
GO  
```  
  
###  <a name="limiting-the-rows-deleted"></a><a name="LimitRows"></a> 限制刪除的資料列  
 本節中的範例會顯示如何限制將會遭到刪除的資料列數目。  
  
#### <a name="b-using-the-where-clause-to-delete-a-set-of-rows"></a>B. 使用 WHERE 子句刪除一組資料列  
 以下範例會刪除 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `ProductCostHistory` 資料表中，所有 `StandardCost` 資料行值超過 `1000.00` 的資料列。  
  
```sql
DELETE FROM Production.ProductCostHistory  
WHERE StandardCost > 1000.00;  
GO  
```  
  
 下列範例會顯示更加複雜的 WHERE 子句。 WHERE 子句會定義為了判斷要刪除之資料列而必須符合的兩個條件。 `StandardCost` 資料行中的值必須介於 `12.00` 與 `14.00` 之間，而且 `SellEndDate` 資料行中的值必須為 Null。 這個範例也會印出來自 **\@\@ROWCOUNT** 函式的值，以傳回已刪除資料列的數目。  
  
```sql
DELETE Production.ProductCostHistory  
WHERE StandardCost BETWEEN 12.00 AND 14.00  
      AND EndDate IS NULL;  
PRINT 'Number of rows deleted is ' + CAST(@@ROWCOUNT as char(3));  
```  
  
#### <a name="c-using-a-cursor-to-determine-the-row-to-delete"></a>C. 使用資料指標來判斷要刪除的資料列  
 以下範例會使用名為 `complex_cursor` 的資料指標，從 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `EmployeePayHistory` 資料表中刪除單一資料列。 刪除作業只會影響目前從資料指標中提取的單一資料列。  
  
```sql
DECLARE complex_cursor CURSOR FOR  
    SELECT a.BusinessEntityID  
    FROM HumanResources.EmployeePayHistory AS a  
    WHERE RateChangeDate <>   
         (SELECT MAX(RateChangeDate)  
          FROM HumanResources.EmployeePayHistory AS b  
          WHERE a.BusinessEntityID = b.BusinessEntityID) ;  
OPEN complex_cursor;  
FETCH FROM complex_cursor;  
DELETE FROM HumanResources.EmployeePayHistory  
WHERE CURRENT OF complex_cursor;  
CLOSE complex_cursor;  
DEALLOCATE complex_cursor;  
GO  
```  
  
#### <a name="d-using-joins-and-subqueries-to-data-in-one-table-to-delete-rows-in-another-table"></a>D. 針對某個資料表中的資料使用聯結和子查詢，以刪除其他資料表中的資料列  
 下列範例會顯示兩種方法，可根據其他資料表中的資料來刪除某個資料表中的資料列。 這兩個範例都會根據 `SalesPerson` 資料表所儲存之年初至今銷售情況來刪除 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `SalesPersonQuotaHistory` 資料表中的資料列。 第一個 `DELETE` 陳述式會顯示 ISO 相容的子查詢方案，而第二個 `DELETE` 陳述式會顯示要聯結兩個資料表的 [!INCLUDE[tsql](../../includes/tsql-md.md)] FROM 延伸模組。  
  
```sql
-- SQL-2003 Standard subquery  
  
DELETE FROM Sales.SalesPersonQuotaHistory   
WHERE BusinessEntityID IN   
    (SELECT BusinessEntityID   
     FROM Sales.SalesPerson   
     WHERE SalesYTD > 2500000.00);  
GO  
```  
  
```sql
-- Transact-SQL extension  
  
DELETE FROM Sales.SalesPersonQuotaHistory   
FROM Sales.SalesPersonQuotaHistory AS spqh  
INNER JOIN Sales.SalesPerson AS sp  
ON spqh.BusinessEntityID = sp.BusinessEntityID  
WHERE sp.SalesYTD > 2500000.00;  
GO  
```  
  
```sql
-- No need to mention target table more than once.  
  
DELETE spqh  
  FROM  
        Sales.SalesPersonQuotaHistory AS spqh  
    INNER JOIN Sales.SalesPerson AS sp  
        ON spqh.BusinessEntityID = sp.BusinessEntityID  
  WHERE  sp.SalesYTD > 2500000.00;  
```  
  
#### <a name="e-using-top-to-limit-the-number-of-rows-deleted"></a>E. 使用 TOP 限制刪除的資料列數目  
 當 TOP (*n*) 子句與 DELETE 一起使用時，會隨機選取 *n* 個資料列來執行刪除作業。 以下範例會從 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `PurchaseOrderDetail` 資料表中刪除到期日早於 2006 年 7 月 1 日的 `20` 個隨機資料列。  
  
```sql
DELETE TOP (20)   
FROM Purchasing.PurchaseOrderDetail  
WHERE DueDate < '20020701';  
GO  
```  
  
 如果您必須使用 TOP 依有意義的時序來刪除資料列，就必須在 subselect 陳述式中同時使用 TOP 和 ORDER BY。 下列查詢會刪除 `PurchaseOrderDetail` 資料表中具有最早到期日的 10 個資料列。 為確保只刪除 10 個資料列，subselect 陳述式 (`PurchaseOrderID`) 中指定的資料行是資料表的主索引鍵。 如果指定的資料行包含重複值，則在 subselect 陳述式中使用非索引鍵資料行會造成刪除 10 個以上的資料列。  
  
```sql
DELETE FROM Purchasing.PurchaseOrderDetail  
WHERE PurchaseOrderDetailID IN  
   (SELECT TOP 10 PurchaseOrderDetailID   
    FROM Purchasing.PurchaseOrderDetail   
    ORDER BY DueDate ASC);  
GO  
```  
  
###  <a name="deleting-rows-from-a-remote-table"></a><a name="RemoteTables"></a> 從遠端資料表刪除資料列  
 本節的範例會顯示如何使用 [連結的伺服器](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)或[資料列集函式](../functions/opendatasource-transact-sql.md)來參考遠端資料表，以便刪除遠端資料表的資料列。 遠端資料表存在於 SQL Server 的不同伺服器或執行個體上。  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
#### <a name="f-deleting-data-from-a-remote-table-by-using-a-linked-server"></a>F. 使用連結的伺服器刪除遠端資料表的資料  
 下列範例會刪除遠端資料表的資料列。 此範例一開始會使用 [sp_addlinkedserver](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md) 建立遠端資料來源的連結。 接下來，會將連結的伺服器名稱 `MyLinkServer` 指定為 *server.catalog.schema.object* 格式之四部分物件名稱的一部分。  
  
```sql
USE master;  
GO  
-- Create a link to the remote data source.   
-- Specify a valid server name for @datasrc as 'server_name' or 'server_name\instance_name'.  
  
EXEC sp_addlinkedserver @server = N'MyLinkServer',  
    @srvproduct = N' ',  
    @provider = N'SQLNCLI',   
    @datasrc = N'server_name',  
    @catalog = N'AdventureWorks2012';  
GO  
```  
  
```sql
-- Specify the remote data source using a four-part name   
-- in the form linked_server.catalog.schema.object.  
  
DELETE MyLinkServer.AdventureWorks2012.HumanResources.Department 
WHERE DepartmentID > 16;  
GO  
```  
  
#### <a name="g-deleting-data-from-a-remote-table-by-using-the-openquery-function"></a>G. 使用 OPENQUERY 函數刪除遠端資料表的資料  
 下列範例會藉由指定 [OPENQUERY](../../t-sql/functions/openquery-transact-sql.md) 資料列集函式來刪除遠端資料表的資料列。 上一個範例所建立之連結的伺服器名稱會用於這個範例。  
  
```sql
DELETE OPENQUERY (MyLinkServer, 'SELECT Name, GroupName 
FROM AdventureWorks2012.HumanResources.Department  
WHERE DepartmentID = 18');  
GO  
```  
  
#### <a name="h-deleting-data-from-a-remote-table-by-using-the-opendatasource-function"></a>H. 使用 OPENDATASOURCE 函數刪除遠端資料表的資料  
 下列範例會藉由指定 [OPENDATASOURCE](../../t-sql/functions/opendatasource-transact-sql.md) 資料列集函式來刪除遠端資料表的資料列。 使用 *server_name* 或 *server_name\instance_name* 格式，為資料來源指定有效的伺服器名稱。  
  
```sql
DELETE FROM OPENDATASOURCE('SQLNCLI',  
    'Data Source= <server_name>; Integrated Security=SSPI')  
    .AdventureWorks2012.HumanResources.Department   
WHERE DepartmentID = 17;
```  
  
###  <a name="capturing-the-results-of-the-delete-statement"></a><a name="CaptureResults"></a> 擷取 DELETE 陳述式的結果  
  
#### <a name="i-using-delete-with-the-output-clause"></a>I. 搭配 OUTPUT 子句使用 DELETE  
 以下範例示範如何將 `DELETE` 陳述式的結果儲存到 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的資料表變數。  
  
```sql
DELETE Sales.ShoppingCartItem  
OUTPUT DELETED.*   
WHERE ShoppingCartID = 20621;  
  
--Verify the rows in the table matching the WHERE clause have been deleted.  
SELECT COUNT(*) AS [Rows in Table] 
FROM Sales.ShoppingCartItem 
WHERE ShoppingCartID = 20621;  
GO  
```  
  
#### <a name="j-using-output-with-from_table_name-in-a-delete-statement"></a>J. 在 DELETE 陳述式中，搭配 <from_table_name> 來使用 OUTPUT  
 以下範例根據 `DELETE` 陳述式的 `FROM` 子句所定義的搜尋準則來刪除 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `ProductProductPhoto` 資料表中的資料列。 `OUTPUT` 子句會傳回所刪除的資料表的 `DELETED.ProductID`、 `DELETED.ProductPhotoID`資料行及 `Product` 資料表中的資料行。 `FROM` 子句藉此來指定要刪除的資料列。  
  
```sql
DECLARE @MyTableVar table (  
    ProductID int NOT NULL,   
    ProductName nvarchar(50)NOT NULL,  
    ProductModelID int NOT NULL,   
    PhotoID int NOT NULL);  
  
DELETE Production.ProductProductPhoto  
OUTPUT DELETED.ProductID,  
       p.Name,  
       p.ProductModelID,  
       DELETED.ProductPhotoID  
    INTO @MyTableVar  
FROM Production.ProductProductPhoto AS ph  
JOIN Production.Product as p   
    ON ph.ProductID = p.ProductID   
    WHERE p.ProductModelID BETWEEN 120 and 130;  
  
--Display the results of the table variable.  
SELECT ProductID, ProductName, ProductModelID, PhotoID   
FROM @MyTableVar  
ORDER BY ProductModelID;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="k-delete-all-rows-from-a-table"></a>K. 刪除資料表中的所有資料列  
 下列範例會刪除 `Table1` 資料表中的所有資料列，因為並未利用 WHERE 子句來限制刪除的資料列數。  
  
```sql
DELETE FROM Table1;  
```  
  
### <a name="l-delete-a-set-of-rows-from-a-table"></a>L. 刪除資料表中的一組資料列

 下列範例會刪除 `Table1` 資料表中，所有 `StandardCost` 資料行值超過 1000.00 的資料列。  
  
```sql
DELETE FROM Table1  
WHERE StandardCost > 1000.00;  
```  
  
### <a name="m-using-label-with-a-delete-statement"></a>M. 搭配 DELETE 陳述式使用 LABEL

 下列範例會搭配 DELETE 陳述式使用標籤。  
  
```sql
DELETE FROM Table1  
OPTION ( LABEL = N'label1' );  
  
```  
  
### <a name="n-using-a-label-and-a-query-hint-with-the-delete-statement"></a>N. 搭配 DELETE 陳述式使用標籤及查詢提示

 此查詢示會示範查詢聯結提示與 DELETE 陳述式搭配使用的基本語法。 如需有關聯結提示及如何使用 OPTION 子句的詳細資訊，請參閱 [OPTION 子句 (Transact-SQL)](../queries/option-clause-transact-sql.md)。
  
```sql
-- Uses AdventureWorks  
  
DELETE FROM dbo.FactInternetSales  
WHERE ProductKey IN (   
    SELECT T1.ProductKey FROM dbo.DimProduct T1   
    JOIN dbo.DimProductSubcategory T2  
    ON T1.ProductSubcategoryKey = T2.ProductSubcategoryKey  
    WHERE T2.EnglishProductSubcategoryName = 'Road Bikes' )  
OPTION ( LABEL = N'CustomJoin', HASH JOIN ) ;  
```  

### <a name="o-delete-using-a-where-clause"></a>O. 使用 WHERE 子句刪除

此查詢會顯示如何使用 WHERE 子句刪除，而不是 使用 FROM 子句。

```sql
DELETE tableA WHERE EXISTS (
SELECT TOP 1 1 FROM tableB tb WHERE tb.col1 = tableA.col1
)
```

### <a name="p-delete-based-on-the-result-of-joining-with-another-table"></a>P. 根據聯結另一個資料表的結果進行刪除

這個範例示範如何根據與另一個資料表聯結的結果來從資料表中刪除。

```sql
CREATE TABLE dbo.Table1   
    (ColA int NOT NULL, ColB decimal(10,3) NOT NULL);  
GO  

CREATE TABLE dbo.Table2   
    (ColA int PRIMARY KEY NOT NULL, ColB decimal(10,3) NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES(1, 10.0), (1, 20.0);  
INSERT INTO dbo.Table2 VALUES(1, 0.0);  
GO  

DELETE dbo.Table2   
FROM dbo.Table2   
    INNER JOIN dbo.Table1   
    ON (dbo.Table2.ColA = dbo.Table1.ColA)
    WHERE dboTable2.ColA = 1;  
```

## <a name="see-also"></a>另請參閱

 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)   
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [TRUNCATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/truncate-table-transact-sql.md)   
 [UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)   
 [WITH common_table_expression &#40;Transact-SQL&#41;](../../t-sql/queries/with-common-table-expression-transact-sql.md)   
 [@@ROWCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/rowcount-transact-sql.md)  
  
