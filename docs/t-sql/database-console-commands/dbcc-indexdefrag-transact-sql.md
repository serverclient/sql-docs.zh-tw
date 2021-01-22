---
description: DBCC INDEXDEFRAG (Transact-SQL)
title: DBCC INDEXDEFRAG (Transact-SQL)
ms.custom: ''
ms.date: 07/16/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- INDEXDEFRAG
- DBCC INDEXDEFRAG
- DBCC_INDEXDEFRAG_TSQL
- INDEXDEFRAG_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- defragmenting indexes
- DBCC INDEXDEFRAG statement
- leaf level defragmenting
- fragmentation [SQL Server]
- index defragmenting [SQL Server]
ms.assetid: 3c7df676-4843-44d0-8c1c-a9ab7e593b70
author: pmasl
ms.author: umajay
ms.openlocfilehash: 7bc27698a8ddb0cffe294a9caf07ec2b307961fe
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596640"
---
# <a name="dbcc-indexdefrag-transact-sql"></a>DBCC INDEXDEFRAG (Transact-SQL)

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

重組指定資料表或檢視的索引。
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)] 請改用 [ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md)。  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DBCC INDEXDEFRAG  
(  
    { database_name | database_id | 0 }   
    , { table_name | table_id | view_name | view_id }   
    [ , { index_name | index_id } [ , { partition_number | 0 } ] ]  
)  
    [ WITH NO_INFOMSGS ]   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *database_name* \| *database_id* \| 0  
 包含要重組之索引的資料庫。 如果指定 0，就會使用目前的資料庫。 資料庫名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 *table_name* \| *table_id* \| *view_name* \| *view_id*  
 包含要重組之索引的資料表或檢視。 資料表和檢視表名稱必須符合識別碼的規則。  
  
 *index_name* \| *index_id*  
 要重組之索引的名稱或識別碼。 若未指定，陳述式會重組指定之資料表或檢視表的所有索引。 索引名稱必須符合識別碼的規則。  
  
 *partition_number* \| 0  
 這是要重組之索引的資料分割編號。 若未指定，或指定 0，陳述式會重組指定索引中的所有資料分割。  
  
 WITH NO_INFOMSGS  
 抑制所有嚴重性層級在 0 到 10 的參考用訊息。  
  
## <a name="remarks"></a>備註  
DBCC INDEXDEFRAG 會重新組織索引的分葉層級，使頁面的實體順序符合分葉節點由左至右的邏輯順序，以改進索引掃描的效能。
  
> [!NOTE]  
>  當執行 DBCC INDEXDEFRAG 時，會循序重新組織索引。 這表示單一索引的作業是利用單一執行緒來執行。 不會有任何平行處理原則。 另外，相同 DBCC INDEXDEFRAG 陳述式多重索引的作業，每次只會處理一個索引。  
  
DBCC INDEXDEFRAG 也會壓縮索引的頁面，它會將建立索引時所指定的填滿因數考慮在內。 因這項壓縮而建立的任何空白頁面都會被移除。 如需詳細資訊，請參閱 [指定索引的填滿因素](../../relational-databases/indexes/specify-fill-factor-for-an-index.md)。
  
如果索引跨越多個檔案，DBCC INDEXDEFRAG 每次會重組一個檔案。 在檔案之間，不會進行頁面的移轉。
  
DBCC INDEXDEFRAG 每五分鐘會報告一次估計的完成百分比。 在這個處理序中，隨時可以停止 DBCC INDEXDEFRAG，任何已完成的工作都會保留下來。
  
DBCC INDEXDEFRAG 不像 DBCC DBREINDEX (一般而言，是建立索引的作業)，它是一項線上作業。 它不會長期保留鎖定。 因此，DBCC INDEXDEFRAG 不會封鎖查詢或更新的執行。 由於重組的時間與片段化的層級相關，因此，重組部分片段化索引的速度會比建立新索引快。 對於嚴重片段化的索引，重組可能比重建要花更多的時間。
  
重組一律會有完整的記錄，不論資料庫復原模式設定為何，都是如此。 如需詳細資訊，請參閱 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)。 重組嚴重片段化的索引所產生的記錄，可能會超過建立索引的完整記錄。 不過，重組是以一系列的短交易來執行的；因此，如果經常建立記錄備份或復原模式設定是 SIMPLE，就不需要大型記錄。
  
## <a name="restrictions"></a>限制  
DBCC INDEXDEFRAG 會就地移動分葉頁。 因此，如果有索引與磁碟中的其他索引交錯，針對這個索引來執行 DBCC INDEXDEFRAG，並無法使索引中的所有分葉頁成為連續的。 若要改進頁面的叢集，請重建索引。
DBCC INDEXDEFRAG 無法用於重組下列索引：
-   停用的索引。  
-   頁面鎖定設定為 OFF 的索引。  
-   空間索引。  
  
不支援系統資料表使用 DBCC INDEXDEFRAG。
  
## <a name="result-sets"></a>結果集  
如果在陳述式中指定了索引，DBCC INDEXDEFRAG 會傳回下列結果集 (值可能會不同) (除非指定了 WITH NO_INFOMSGS)：
  
```
Pages Scanned Pages Moved Pages Removed  
------------- ----------- -------------  
359           346         8  
  
(1 row(s) affected)  
  
DBCC execution completed. If DBCC printed error messages, contact your system administrator.  
```  
  
## <a name="permissions"></a>權限  
呼叫端必須擁有資料表，或是系統管理員 **sysadmin** 固定伺服器角色、**db_owner** 固定資料庫角色，或 **db_ddladmin** 固定資料庫角色的成員。
  
## <a name="examples"></a>範例  
### <a name="a-using-dbcc-indexdefrag-to-defragment-an-index"></a>A. 使用 DBCC INDEXDEFRAG 重組索引  
下列範例會重組 `PK_Product_ProductID` 資料庫之 `Production.Product`資料表中 `AdventureWorks` 索引的所有分割區。
  
```sql  
DBCC INDEXDEFRAG (AdventureWorks2012, 'Production.Product', PK_Product_ProductID);  
GO  
```  
  
### <a name="b-using-dbcc-showcontig-and-dbcc-indexdefrag-to-defragment-the-indexes-in-a-database"></a>B. 使用 DBCC SHOWCONTIG 和 DBCC INDEXDEFRAG 來重組資料庫中的索引  
 下列範例會顯示一種簡單的重組方法，在宣告之臨界值上方片段化的資料庫中重組所有索引。  
  
```sql  
/*Perform a 'USE <database name>' to select the database in which to run the script.*/  
-- Declare variables  
SET NOCOUNT ON;  
DECLARE @tablename VARCHAR(255);  
DECLARE @execstr   VARCHAR(400);  
DECLARE @objectid  INT;  
DECLARE @indexid   INT;  
DECLARE @frag      DECIMAL;  
DECLARE @maxfrag   DECIMAL;  
  
-- Decide on the maximum fragmentation to allow for.  
SELECT @maxfrag = 30.0;  
  
-- Declare a cursor.  
DECLARE tables CURSOR FOR  
   SELECT TABLE_SCHEMA + '.' + TABLE_NAME  
   FROM INFORMATION_SCHEMA.TABLES  
   WHERE TABLE_TYPE = 'BASE TABLE';  
  
-- Create the table.  
CREATE TABLE #fraglist (  
   ObjectName CHAR(255),  
   ObjectId INT,  
   IndexName CHAR(255),  
   IndexId INT,  
   Lvl INT,  
   CountPages INT,  
   CountRows INT,  
   MinRecSize INT,  
   MaxRecSize INT,  
   AvgRecSize INT,  
   ForRecCount INT,  
   Extents INT,  
   ExtentSwitches INT,  
   AvgFreeBytes INT,  
   AvgPageDensity INT,  
   ScanDensity DECIMAL,  
   BestCount INT,  
   ActualCount INT,  
   LogicalFrag DECIMAL,  
   ExtentFrag DECIMAL);  
  
-- Open the cursor.  
OPEN tables;  
  
-- Loop through all the tables in the database.  
FETCH NEXT  
   FROM tables  
   INTO @tablename;  
  
WHILE @@FETCH_STATUS = 0  
BEGIN  
-- Do the showcontig of all indexes of the table  
   INSERT INTO #fraglist   
   EXEC ('DBCC SHOWCONTIG (''' + @tablename + ''')   
      WITH FAST, TABLERESULTS, ALL_INDEXES, NO_INFOMSGS');  
   FETCH NEXT  
      FROM tables  
      INTO @tablename;  
END;  
  
-- Close and deallocate the cursor.  
CLOSE tables;  
DEALLOCATE tables;  
  
-- Declare the cursor for the list of indexes to be defragged.  
DECLARE indexes CURSOR FOR  
   SELECT ObjectName, ObjectId, IndexId, LogicalFrag  
   FROM #fraglist  
   WHERE LogicalFrag >= @maxfrag  
      AND INDEXPROPERTY (ObjectId, IndexName, 'IndexDepth') > 0;  
  
-- Open the cursor.  
OPEN indexes;  
  
-- Loop through the indexes.  
FETCH NEXT  
   FROM indexes  
   INTO @tablename, @objectid, @indexid, @frag;  
  
WHILE @@FETCH_STATUS = 0  
BEGIN  
   PRINT 'Executing DBCC INDEXDEFRAG (0, ' + RTRIM(@tablename) + ',  
      ' + RTRIM(@indexid) + ') - fragmentation currently '  
       + RTRIM(CONVERT(varchar(15),@frag)) + '%';  
   SELECT @execstr = 'DBCC INDEXDEFRAG (0, ' + RTRIM(@objectid) + ',  
       ' + RTRIM(@indexid) + ')';  
   EXEC (@execstr);  
  
   FETCH NEXT  
      FROM indexes  
      INTO @tablename, @objectid, @indexid, @frag;  
END;  
  
-- Close and deallocate the cursor.  
CLOSE indexes;  
DEALLOCATE indexes;  
  
-- Delete the temporary table.  
DROP TABLE #fraglist;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
[DBCC &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-transact-sql.md)  
[sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)  
[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)  
[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)  
[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)
  
