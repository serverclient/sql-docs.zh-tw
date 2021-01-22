---
description: DBCC SHOWCONTIG (Transact-SQL)
title: DBCC SHOWCONTIG (Transact-SQL)
ms.custom: ''
ms.date: 07/17/2017
ms.prod: sql
ms.prod_service: sql-database
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DBCC_SHOWCONTIG_TSQL
- DBCC SHOWCONTIG
- SHOWCONTIG
- SHOWCONTIG_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- displaying defragmentation information
- DBCC SHOWCONTIG statement
- defragmenting indexes
- leaf level defragmenting
- fragmentation [SQL Server]
- index defragmenting [SQL Server]
ms.assetid: 1df2123a-1197-4fff-91a3-25e3d8848aaa
author: pmasl
ms.author: umajay
ms.openlocfilehash: ed1ba4d3fb6114a74dbf972ff0a04f84a463af06
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98597084"
---
# <a name="dbcc-showcontig-transact-sql"></a>DBCC SHOWCONTIG (Transact-SQL)

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

顯示指定資料表或檢視之資料與索引的片段資訊。
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)] 請改用 [sys.dm_db_index_physical_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)。  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DBCC SHOWCONTIG   
[ (   
    { table_name | table_id | view_name | view_id }   
    [ , index_name | index_id ]   
) ]   
    [ WITH   
        {   
         [ , [ ALL_INDEXES ] ]   
         [ , [ TABLERESULTS ] ]   
         [ , [ FAST ] ]  
         [ , [ ALL_LEVELS ] ]   
         [ NO_INFOMSGS ]  
         }  
    ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *table_name* \| *table_id* \| *view_name* \| *view_id*  
 檢查片段資訊的資料表或檢視。 若未指定，就會檢查目前資料庫中的所有資料表和索引檢視表。 若要取得資料表或檢視識別碼，請使用 [OBJECT_ID](../../t-sql/functions/object-id-transact-sql.md) 函式。  
  
 *index_name* \| *index_id*  
 檢查片段資訊的索引。 若未指定，陳述式會處理指定之資料表或檢視表的基本索引。 若要取得索引識別碼，請使用 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) 目錄檢視。  
  
 WITH  
 指定 DBCC 陳述式所傳回之資訊類型的選項。  
  
 FAST  
 指定是否執行索引和輸出最小資訊的快速掃描。 快速掃描不會讀取索引的分葉或資料層級頁面。  
  
 ALL_INDEXES  
 顯示指定資料表和檢視之所有索引的結果，即使指定了特定索引也是如此。  
  
 TABLERESULTS  
 將結果顯示成含其他資訊的資料列集。  
  
 ALL_LEVELS  
 維護這個項目的目的，只是為了與舊版相容。 即使指定了 ALL_LEVELS，也只會處理索引分葉層級或資料表資料層級。  
  
 NO_INFOMSGS  
 抑制所有嚴重性層級在 0 到 10 的參考用訊息。  
  
## <a name="result-sets"></a>結果集  
下表描述結果集中的資訊。
  
|統計資料|描述|  
|---|---|
|**掃描頁數**|資料表或索引中的頁數。|  
|**掃描範圍**|資料表或索引中的範圍數目。|  
|**範圍切換**|當 DBCC 陳述式往返資料表或索引頁面時，在各範圍之間的移動次數。|  
|**平均範圍平均頁數**|在頁面鏈結中，每個範圍的頁數。|  
|**掃描密度 [最佳計數:實際計數]**|這是一個百分比。 它是 **最佳次數** 與 **實際次數** 的比例。 如果每個項目都是連續的，這個值就是 100；如果這個值小於 100，就會有某些片段存在。<br /><br /> **最佳次數** 是每個項目都連續連結時，理想的範圍變更數目。 **實際次數** 是實際的範圍變更數目。|  
|**邏輯掃描片段**|掃描索引分葉頁時所傳回失序頁面的百分比。 這個數字與堆積無關。 失序頁面是指配置給索引之下一個實體頁面的頁面，而不是目前分葉頁中下一頁  指標所指向的頁面。|  
|**範圍掃描片段**|掃描索引分葉頁時之失序範圍的百分比。 這個數字與堆積無關。 失序範圍是索引目前頁面所在之範圍，實際上不是索引上一頁所在範圍之下一範圍的範圍。<br /><br /> 注意:當索引跨越許多檔案時，這個數目沒有意義。|  
|**平均平均可用位元組**|掃描頁面的平均可用位元組數。 數目愈大，頁面的飽和度愈低。 如果索引沒有許多隨機的插入，數目低會比較好。 這個數目也受到資料列大小的影響；資料列愈大，這個數目也愈大。|  
|**平均頁面密度 (全滿)**|平均頁面密度，這是一個百分比。 這個值將資料列大小考慮在內。 因此，這個值是更精確的頁面飽和度指示。 百分比愈大，愈好。|  
  
指定 *table_id* 和 FAST 時，DBCC SHOWCONTIG 會傳回只含下列資料行的結果集。
-   **掃描頁數**  
-   **範圍切換**  
-   **掃描密度 [Best Count:Actual Count]**  
-   **範圍掃描片段**  
-   **邏輯掃描片段**  
  
當指定 TABLERESULTS 時，DBCC SHOWCONTIG 會傳回下列資料行以及上一表格所描述的 9 個資料行。
  
|統計資料|描述|  
|---|---|
|**Object Name**|所處理之資料表或檢視的名稱。|  
|**ObjectId**|物件名稱的識別碼。|  
|**IndexName**|所處理之索引的名稱。 堆積的這個值是 NULL。|  
|**IndexId**|索引的識別碼。 堆積的這個值是 0。|  
|**Level**|索引的層級。 層級 0 是索引的分葉層級或資料層級。<br /><br /> 堆積的層級是 0。|  
|**頁面**|組成索引或整個堆積的層級之頁數。|  
|**資料列**|索引層級的資料或索引記錄數目。 堆積的這個值是整個堆積中的資料記錄數目。<br /><br /> 若是堆積，從此函數傳回的記錄數目可能與針對該堆積執行 SELECT COUNT(*) 時所傳回的資料列數目不符。 這是因為一個資料列可能包含數筆記錄。 例如，在某些更新情況下，單一的堆積資料列可能有一筆轉送記錄以及一筆當做更新作業結果的轉送記錄。 同時，在 LOB_DATA 儲存體中，會將多數大型的 LOB 資料列分割為多筆記錄。|  
|**MinimumRecordSize**|索引或整個堆積的層級之最小記錄大小。|  
|**MaximumRecordSize**|索引或整個堆積的層級之最大記錄大小。|  
|**AverageRecordSize**|索引或整個堆積的層級之平均記錄大小。|  
|**ForwardedRecords**|索引或整個堆積的層級之轉送記錄數目。|  
|**Extents**|索引或整個堆積的層級之範圍數目。|  
|**ExtentSwitches**|當 DBCC 陳述式往返資料表或索引頁面時，在各範圍之間的移動次數。|  
|**AverageFreeBytes**|掃描頁面的平均可用位元組數。 數目愈大，頁面的飽和度愈低。 如果索引沒有許多隨機的插入，數目低會比較好。 這個數目也受到資料列大小的影響；資料列愈大，這個數目也愈大。|  
|**AveragePageDensity**|平均頁面密度，這是一個百分比。 這個值將資料列大小考慮在內。 因此，這個值是更精確的頁面飽和度指示。 百分比愈大，愈好。|  
|**ScanDensity**|這是一個百分比。 它是 **BestCount** 與 **ActualCount** 的比例。 如果每個項目都是連續的，這個值就是 100；如果這個值小於 100，就會有某些片段存在。|  
|**BestCount**|這是每個項目都連續連結時，理想的範圍變更數目。|  
|**ActualCount**|這是實際的範圍變更數目。|  
|**LogicalFragmentation**|掃描索引分葉頁時所傳回失序頁面的百分比。 這個數字與堆積無關。 失序頁面是指配置給索引之下一個實體頁面的頁面，而不是目前分葉頁中下一頁  指標所指向的頁面。|  
|**ExtentFragmentation**|掃描索引分葉頁時之失序範圍的百分比。 這個數字與堆積無關。 失序範圍是索引目前頁面所在之範圍，實際上不是索引上一頁所在範圍之下一範圍的範圍。<br /><br /> 注意:當索引跨越許多檔案時，這個數目沒有意義。|  
  
當指定 WITH TABLERESULTS 和 FAST 時，結果集與指定 WITH TABLERESULTS 時相同，不過，下列資料行含有 Null 值：

| 資料列| Extents |
|---|---|
|**MinimumRecordSize**|**AverageFreeBytes**|  
|**MaximumRecordSize**|**AveragePageDensity**|  
|**AverageRecordSize**|**ExtentFragmentation**|  
|**ForwardedRecords**||  
  
## <a name="remarks"></a>備註  
當指定 *index_id* 時，DBCC SHOWCONTIG 陳述式會往返於指定索引之分葉層級的頁面鏈結。 如果只指定 *table_id*，或是 *index_id* 為 0，便會掃描指定資料表的資料頁面。 這個作業僅需要意圖共用 (IS) 資料表鎖定。 這個方式可以執行所有更新和插入，但需要獨佔 (X) 資料表鎖定者除外。 這可讓您在執行速度和充分並行傳回的統計資料數目之間進行取捨。 不過，如果這個命令只用來量測片段化，我們建議您使用 WITH FAST 選項以獲得最佳效能。 快速掃描不會讀取索引的分葉或資料層級頁面。 WITH FAST 選項不會套用到堆積。
  
## <a name="restrictions"></a>限制  
DBCC SHOWCONTIG 不會顯示 **ntext**、**text** 和 **image** 資料類型的資料。 這是因為儲存文字和影像資料的文字索引已不存在。
  
另外，DBCC SHOWCONTIG 不支援某些新功能。 例如：
-   如果指定的資料表或索引進行資料分割，DBCC SHOWCONTIG 只會顯示指定資料表或索引的第一個資料分割。  
-   DBCC SHOWCONTIG 不會顯示資料列溢位儲存資訊及其他新的非資料列資料類型，如 **nvarchar(max)** 、**varchar(max)** 、**varbinary(max)** 和 **xml**。  
-   DBCC SHOWCONTIG 不支援空間索引。  
  
[sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md) 動態管理檢視可完全支援所有新功能。
  
## <a name="table-fragmentation"></a>資料表片段  
DBCC SHOWCONTIG 會判斷資料表是否嚴重片段化。 資料表的片段化是在資料表的資料修改 (INSERT、UPDATE 和 DELETE 陳述式) 過程中發生的。 由於這些修改通常不會平均散發在資料表的各個資料列上，因此，各頁面的飽和度可能會隨著時間而不同。 對於掃描部分或完整資料表的查詢而言，這類資料表片段化可能會造成額外的頁面讀取。 這會防礙資料的平行掃描。
  
當索引片段化很嚴重時，您可以利用下列選項來減少片段化：
-   卸除和重建叢集索引。  
     重建叢集索引會重新組織資料，造成飽和的資料頁面。 您可以在 CREATE INDEX 中使用 FILLFACTOR 選項來設定飽和度的層級。 這個方法的缺點是在卸除或重建周期內索引是離線的，作業不可部分完成。 如果中斷了索引建立，就不會重建索引。  
-   依照邏輯順序來重新排序索引的分葉層級頁面。  
     請利用 ALTER INDEX…REORGANIZE，依照邏輯順序來重新排序索引的分葉層級頁面。 由於這項作業是一個線上作業，因此，當執行陳述式時，可以使用索引。 這項作業可能在未失去已完成工作的情況下中斷。 這個方法的缺點是它的資料重新組織作業不如叢集索引卸除或重建作業的資料重新組織作業。  
-   重建索引。  
     請利用 ALTER INDEX 和 REBUILD 重建索引。 如需詳細資訊，請參閱 [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)。  
  
**每頁平均可用位元組** 和 **平均頁面密度 (全滿)** 統計資料能指出索引頁面的飽和度。 **每頁平均可用位元組** 的數目應該很小，而 **平均頁面密度 (全滿)** 的數目數應該很大，這樣索引才不會有許多隨機的插入。 指定 FILLFACTOR 選項來卸除和重建索引，可以改進統計資料。 另外，設定 REORGANIZE 的 ALTER INDEX 也會壓縮索引，將它的 FILLFACTOR 考量在內，可以改進統計資料。
  
> [!NOTE]  
>  有許多隨機插入且非常飽和的頁面之索引，頁面分割數會增加。 這會造成更多的片段。  
  
您可以利用下列方式來判斷索引的片段化層級：
-   比較 **範圍切換** 和 **掃描範圍** 的值。  
     **範圍切換** 值應該盡可能接近 **掃描範圍** 值。 這個比例會計算成 **掃描密度** 值。 這個值應該盡可能高，您可以縮減索引的片段化來改進它。  
  
    > [!NOTE]  
    >  如果索引跨越許多檔案時，這個方法便無法運作。  
  
-   了解 **邏輯掃描片段化** 和 **範圍掃描片段化** 值。  
     **邏輯掃描片段化** 和 (某種程度上的) **範圍掃描片段化** 值，是資料表片段化程度的最佳指標。 這兩個值都應該盡可能接近零，不過，百分比 0 至 10 的值可能比較合適。  
  
    > [!NOTE]  
    >  如果索引跨越多個檔案，**範圍掃描片段化** 值會比較高。 若要縮減這些值，您必須減少索引片段化。  
  
## <a name="permissions"></a>權限  
使用者必須擁有資料表，或是 **系統管理員 (sysadmin)** 固定伺服器角色、**db_owner** 固定資料庫角色，或 **db_ddladmin** 固定資料庫角色的成員。
  
## <a name="examples"></a>範例  
### <a name="a-displaying-fragmentation-information-for-a-table"></a>A. 顯示資料表的片段資訊  
下列範例會顯示 `Employee` 資料表的片段資訊。
  
```sql  
USE AdventureWorks2012;  
GO  
DBCC SHOWCONTIG ('HumanResources.Employee');  
GO  
```  
  
### <a name="b-using-object_id-to-obtain-the-table-id-and-sysindexes-to-obtain-the-index-id"></a>B. 利用 OBJECT_ID 取得資料表識別碼，利用 sys.indexes 取得索引識別碼  
下列範例會利用 `OBJECT_ID` 和 `sys.indexes` 目錄檢視來取得 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫中 `Production.Product` 資料表之 `AK_Product_Name` 索引的資料表識別碼和索引識別碼。
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @id INT, @indid INT  
SET @id = OBJECT_ID('Production.Product')  
SELECT @indid = index_id   
FROM sys.indexes  
WHERE object_id = @id   
   AND name = 'AK_Product_Name'  
DBCC SHOWCONTIG (@id, @indid);  
GO  
```  
  
### <a name="c-displaying-an-abbreviated-result-set-for-a-table"></a>C. 顯示資料表的縮寫結果集  
下列範例會傳回 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫中 `Product` 資料表的縮寫結果集。
  
```sql  
USE AdventureWorks2012;  
GO  
DBCC SHOWCONTIG ('Production.Product', 1) WITH FAST;  
GO  
```  
  
### <a name="d-displaying-the-full-result-set-for-every-index-on-every-table-in-a-database"></a>D. 顯示資料庫中每個資料表上之每個索引的完整結果集  
下列範例會傳回 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫中，每個資料表上各個索引的完整資料表結果集。
  
```sql  
USE AdventureWorks2012;  
GO  
DBCC SHOWCONTIG WITH TABLERESULTS, ALL_INDEXES;  
GO  
```  
  
### <a name="e-using-dbcc-showcontig-and-dbcc-indexdefrag-to-defragment-the-indexes-in-a-database"></a>E. 使用 DBCC SHOWCONTIG 和 DBCC INDEXDEFRAG 來重組資料庫中的索引  
下列範例會顯示一種簡單的重組方法，將在宣告的臨界值上片段化的資料庫中重組所有索引。
  
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
[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)  
[CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)  
[DBCC &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-transact-sql.md)  
[DROP INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/drop-index-transact-sql.md)  
[sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)  
[OBJECT_ID &#40;Transact-SQL&#41;](../../t-sql/functions/object-id-transact-sql.md)  
[sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)
  
