---
description: ALTER PARTITION FUNCTION (Transact-SQL)
title: ALTER PARTITION FUNCTION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER PARTITION FUNCTION
- ALTER_PARTITION_FUNCTION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- splitting partitions [SQL Server]
- partitioned tables [SQL Server], splitting
- ALTER PARTITION FUNCTION statement
- merging partitions [SQL Server]
- partitioned indexes [SQL Server], merging
- partitioned indexes [SQL Server], splitting
- modifying partition functions
- partition functions [SQL Server], modifying
- partitioned tables [SQL Server], merging
ms.assetid: 70866dac-0a8f-4235-8108-51547949ada4
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: c3ae0c860bc31a228ce3a842d7e1ba8a861ea1e5
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092105"
---
# <a name="alter-partition-function-transact-sql"></a>ALTER PARTITION FUNCTION (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

藉由分割或合併資料分割函數的界限值來變更資料分割函數。 執行 ALTER PARTITION FUNCTION 陳述式，可將一個使用資料分割函數的資料表分割區或索引分割為兩個分割區。 此陳述式也可以將兩個分割區合併成一個分割區。  
  
> [!CAUTION]  
>  多份資料表或索引可以使用相同的資料分割函數。 ALTER PARTITION FUNCTION 會在單一交易中影響所有的資料表或索引。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
ALTER PARTITION FUNCTION partition_function_name()  
{   
    SPLIT RANGE ( boundary_value )  
  | MERGE RANGE ( boundary_value )   
} [ ; ]  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*partition_function_name*  
這是您要修改的資料分割函數名稱。  
  
SPLIT RANGE ( *boundary_value* )  
將一個資料分割加入資料分割函數。 *boundary_value* 會決定新資料分割的範圍，而此範圍必須不同於資料分割函數現有的界限範圍。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會以 *boundary_value* 為基礎，將現有的一個範圍分割成兩個。 在這兩個範圍中，具有新 *boundary_value* 的範圍是新的分割區。  
  
線上必須有檔案群組存在。 而且，使用資料分割函數作為 NEXT USED 以保存新分割區的資料分割配置必須標示檔案群組。 CREATE PARTITION SCHEME 陳述式會將檔案群組指派給分割區。 CREATE PARTITION FUNCTION 陳述式會建立少於檔案群組數目的分割區數目以利保存。 CREATE PARTITION SCHEME 陳述式所保留的檔案群組數目可能比所需的還多。 如果發生這種情況，則您最終將不會獲指派檔案群組。 此外，資料分割配置會將其中一個檔案群組標示為 NEXT USED。 這個檔案群組會保存新的分割區。 如果沒有資料分割配置標示為 NEXT USED 的檔案群組，則您必須使用 ALTER PARTITION SCHEME 陳述式。 

ALTER PARTITION SCHEME 陳述式可以新增檔案群組或選取現有的檔案群組，來保存新的分割區。 您可以指定已經保存分割區的檔案群組來保存其他分割區。 資料分割函數可以參與多個資料分割配置。 基於這個理由，使用您要新增分割區之資料分割函數的所有資料分割配置都必須具有 NEXT USED 檔案群組。 否則，ALTER PARTITION FUNCTION 陳述式就會失敗並出現一則錯誤，顯示一個或多個缺少 NEXT USED 檔案群組的資料分割配置。  
  
如果您在相同的檔案群組中建立所有資料分割，會在一開始時自動將該檔案群組指派為 NEXT USED 檔案群組。 但在分割作業執行之後，即不再有選取的 NEXT USED 檔案群組。 使用 ALTER PARTITION SCHEME 來將檔案群組明確指派為 NEXT USED 檔案群組，否則後續的分割作業將會失敗。  
  
> [!NOTE]  
>  資料行存放區索引的限制：當資料表上存在資料行存放區索引時，僅可分割空的資料分割。 執行此作業之前，您必須先卸除或停用資料行存放區索引。  
  
MERGE [ RANGE ( *boundary_value*) ]  
卸除分割區，並將該分割區中現有的所有值合併到剩餘的分割區中。 RANGE (*boundary_value*) 必須是待捨棄分割區的現有界限值。 除非有剩餘的分割區會使用最初保存 *boundary_value* 的檔案群組，或者已用 NEXT USED 屬性來標示這個檔案群組，否則，此引數會從資料分割配置中移除該檔案群組。 合併的分割區存在於一開始未保存 *boundary_value* 的檔案群組中。 *boundary_value* 是可以參考變數 (包括使用者定義型別變數) 或函數 (包括使用者自訂函數) 的常數運算式。 它無法參考 [!INCLUDE[tsql](../../includes/tsql-md.md)] 運算式。 *boundary_value* 必須完全符合或可隱含轉換成其對應分割資料行的資料類型。 您也無法以值的大小和小數位數不符合其對應 *input_parameter_type* 之值的方式，在隱含轉換期間截斷 *boundary_value*。  
  
> [!NOTE]  
>  資料行存放區索引的限制：包含資料行存放區索引的兩個非空白分割區無法合併。 在執行此作業之前，您必須卸除或停用資料行存放區索引  
  
## <a name="best-practices"></a>最佳做法  
一律在分割區範圍的兩端保留空的分割區。 在這兩端保留分割區，以確保分割區會分割，而且分割區合併不會產生任何資料移動。 分割區分割會在一開始發生，而分割區合併會在結束時發生。 請避免分割或合併已擴展的資料分割。 分割或合併已擴展的分割區有時候很沒效率。 之所以缺乏效率，是因為分割與合併有時能使記錄產生作業延長四倍以上，也可能造成嚴重鎖定。  
  
## <a name="limitations-and-restrictions"></a>限制事項  
ALTER PARTITION FUNCTION 會在單一不可部分完成的作業中，重新分割任何使用函數的資料表和索引。 不過，這項作業是離線進行的，可能非常需要資源，這會隨著重新分割的範圍而不同。  
  
只使用 ALTER PARTITION FUNCTION 來將一個分割區分成兩個，或將兩個分割區合併成一個。 若要變更資料表的分割方式 (例如，從 10 個分割區到五個分割區)，請練習下列任意選項。 這些選項的資源耗用程度各異，可能會隨著系統的設定而不同：  
  
-   使用所需的資料分割函數來建立新的資料分割資料表。 然後，使用 INSERT INTO...SELECT FROM 陳述式，將舊資料表的資料插入新的資料表。  
  
-   建立堆積的資料分割叢集索引。  
  
    > [!NOTE]  
    >  卸除資料分割叢集索引會產生資料分割堆積。  
  
-   利用設定了 DROP EXISTING = ON 子句的 [!INCLUDE[tsql](../../includes/tsql-md.md)] CREATE INDEX 陳述式來卸除和重建現有的資料分割索引。  
  
-   執行 ALTER PARTITION FUNCTION 陳述式的序列。  
  
ALTER PARTITION FUNCTION 所影響的所有檔案群組都必須在線上。  
  
當使用資料分割函數的任何資料表上有已停用的叢集索引時，ALTER PARTITION FUNCTION 就會失敗。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不提供修改資料分割函數的複寫支援。 您必須在訂閱資料庫中，手動套用發行集資料庫中的資料分割函數變更。  
  
## <a name="permissions"></a>權限  
下列中的任何權限都可用來執行 ALTER PARTITION FUNCTION：  
  
-   ALTER ANY DATASPACE 權限。 這個權限預設會授與 **sysadmin** 固定伺服器角色以及 **db_owner** 和 **db_ddladmin** 固定資料庫角色的成員。  
  
-   對於建立資料分割函數之資料庫的 CONTROL 或 ALTER 權限。  
  
-   對於建立資料分割函數之資料庫伺服器的 CONTROL SERVER 或 ALTER ANY DATABASE 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-splitting-a-partition-of-a-partitioned-table-or-index-into-two-partitions"></a>A. 將資料分割或分割資料表或索引的分割分成兩個資料分割  
下列範例會建立一個資料分割函數，將資料表或索引分割成四個資料分割。 `ALTER PARTITION FUNCTION` 會將其中一個資料分割分成兩個，以建立總計五個的資料分割。  
  
```sql  
IF EXISTS (SELECT * FROM sys.partition_functions  
    WHERE name = 'myRangePF1')  
DROP PARTITION FUNCTION myRangePF1;  
GO  
CREATE PARTITION FUNCTION myRangePF1 (int)  
AS RANGE LEFT FOR VALUES ( 1, 100, 1000 );  
GO  
--Split the partition between boundary_values 100 and 1000  
--to create two partitions between boundary_values 100 and 500  
--and between boundary_values 500 and 1000.  
ALTER PARTITION FUNCTION myRangePF1 ()  
SPLIT RANGE (500);  
```  
  
### <a name="b-merging-two-partitions-of-a-partitioned-table-into-one-partition"></a>B. 將分割資料表的兩個資料分割合併成一個資料分割  
下列範例會建立上述中的相同資料分割函數，再將兩份資料分割合併成一份資料分割，總共三份的資料分割。  
  
```sql  
IF EXISTS (SELECT * FROM sys.partition_functions  
    WHERE name = 'myRangePF1')  
DROP PARTITION FUNCTION myRangePF1;  
GO  
CREATE PARTITION FUNCTION myRangePF1 (int)  
AS RANGE LEFT FOR VALUES ( 1, 100, 1000 );  
GO  
--Merge the partitions between boundary_values 1 and 100  
--and between boundary_values 100 and 1000 to create one partition  
--between boundary_values 1 and 1000.  
ALTER PARTITION FUNCTION myRangePF1 ()  
MERGE RANGE (100);  
```  
  
## <a name="see-also"></a>另請參閱  
[資料分割資料表與索引](../../relational-databases/partitions/partitioned-tables-and-indexes.md)   
[CREATE PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-function-transact-sql.md)   
[DROP PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-partition-function-transact-sql.md)   
[CREATE PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-scheme-transact-sql.md)   
[ALTER PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/alter-partition-scheme-transact-sql.md)   
[DROP PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/drop-partition-scheme-transact-sql.md)   
[CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)   
[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)   
[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
[sys.partition_functions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partition-functions-transact-sql.md)   
[sys.partition_parameters &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partition-parameters-transact-sql.md) [sys.partition_range_values &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partition-range-values-transact-sql.md)   
[sys.partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md)   
[sys.tables &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-tables-transact-sql.md)   
[sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
[sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)  
  
  
