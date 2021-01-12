---
description: CREATE PARTITION FUNCTION (Transact-SQL)
title: CREATE PARTITION FUNCTION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/19/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE PARTITION FUNCTION
- PARTITION
- PARTITION FUNCTION
- PARTITION_FUNCTION_TSQL
- PARTITION_TSQL
- CREATE_PARTITION_FUNCTION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- RANGE RIGHT partition functions
- RANGE LEFT partition functions
- partitioned indexes [SQL Server], functions
- functions [SQL Server], creating
- partition functions [SQL Server], creating
- partitioned tables [SQL Server], functions
- CREATE PARTITION FUNCTION statement
ms.assetid: 9dfe8b76-721e-42fd-81ae-14e22258c4f2
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: d1bb72ca6736a49874050043b84a961928aed370
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093480"
---
# <a name="create-partition-function-transact-sql"></a>CREATE PARTITION FUNCTION (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  在目前資料庫中建立一個函數，根據指定資料行的各個值，將資料表或索引的資料列對應到資料分割中。 使用 CREATE PARTITION FUNCTION 是建立資料分割資料表或索引的第一步。 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中，一個資料表或索引最多可以有 15,000 個資料分割。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE PARTITION FUNCTION partition_function_name ( input_parameter_type )  
AS RANGE [ LEFT | RIGHT ]   
FOR VALUES ( [ boundary_value [ ,...n ] ] )   
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *partition_function_name*  
 這是資料分割函數的名稱。 資料分割函數名稱在資料庫內必須是唯一的，且必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 *input_parameter_type*  
 這是資料分割所用之資料行的資料類型。 除了 **text**、 **ntext**、 **image**、 **xml**、 **timestamp**、 **varchar(max)**、 **nvarchar(max)**、 **varbinary(max)**、別名資料類型或 CLR 使用者自訂資料類型，所有資料類型都能有效用在分割資料行上。  
  
 實際資料行稱為「資料分割資料行」，指定在 CREATE TABLE 或 CREATE INDEX 陳述式中。  
  
 *boundary_value*  
 針對每個資料分割資料表的資料分割或使用 *partition_function_name* 的索引指定界限值。 如果 *boundary_value* 為空白，資料分割函數會使用 *partition_function_name*，將整個資料表或索引對應到單一資料分割。 只能使用 CREATE TABLE 或 CREATE INDEX 陳述式中所指定的一個資料分割資料行。  
  
 *boundary_value* 是可以參考變數的常數運算式。 其中包括使用者自訂類型變數，或函數和使用者自訂函數。 它不能參考 [!INCLUDE[tsql](../../includes/tsql-md.md)] 運算式。 *boundary_value* 必須符合或可以隱含地轉換成 *input_parameter_type* 中提供的資料類型，且在隱含地轉換期間，不能因為值的大小和小數位數不符合對應 *input_parameter_type* 的大小和小數位數而被截斷。  

> [!NOTE]  
>  如果 *boundary_value* 由 **datetime** 或 **smalldatetime** 常值組成，則評估這些常值時會假設 us_english 為工作階段語言。 這個行為已被取代。 為了確定使用所有工作階段語言時資料分割函數定義的行為都可如所預期，建議您使用所有語言設定都會解譯成相同內容的常數，例如 yyyymmdd 格式；或是將常值明確轉換成特定樣式。 若要判斷伺服器的工作階段語言，請執行 `SELECT @@LANGUAGE`。
>
> 如需詳細資訊，請參閱[將常值日期字串轉換成 DATE 值的非決定性轉換](../data-types/nondeterministic-convert-date-literals.md)。
  
 *...n*  
 指定 *boundary_value* 所提供的數目值，但不可超過 14,999。 所建立的資料分割數目等於 *n* + 1。 這些值不必依照順序列出。 如果值沒有排序，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會將它們排序、建立函數，以及傳回未依序提供值的警告。 如果 *n* 包括任何重複的值，「資料庫引擎」會傳回錯誤。  
  
 **LEFT** | RIGHT  
 指定當 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 是按遞增順序由左至右來排序間隔值時，*boundary_value* [ **,** _...n_ ] 屬於每個界限值間隔的哪一側 (左或右)。 若未指定，LEFT 便是預設值。  
  
## <a name="remarks"></a>備註  
 資料分割函數的範圍只限於建立它的資料庫。 在這個資料庫內，資料分割函數是在不同於其他函數的個別命名空間中。  
  
 任何資料分割資料行含有 Null 值的資料列，都會放在最左側資料分割中，除非將 NULL 指定為界限值，且指示 RIGHT。 在這個情況下，最左側的資料分割是空的資料分割，NULL 值會放在下列資料分割中。  
  
## <a name="permissions"></a>權限  
 下列任何一個權限，都可以用來執行 CREATE PARTITION FUNCTION：  
  
-   ALTER ANY DATASPACE 權限。 這個權限預設會授與 **sysadmin** 固定伺服器角色以及 **db_owner** 和 **db_ddladmin** 固定資料庫角色的成員。  
  
-   對於建立資料分割函數之資料庫的 CONTROL 或 ALTER 權限。  
  
-   對於建立資料分割函數之資料庫伺服器的 CONTROL SERVER 或 ALTER ANY DATABASE 權限。  
  
##  <a name="examples"></a><a name="BKMK_examples"></a> 範例  
  
### <a name="a-creating-a-range-left-partition-function-on-an-int-column"></a>A. 建立 int 資料行的 RANGE LEFT 資料分割函數  
 下列資料分割函數會將資料表或索引分割成四份資料分割。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF1 (int)  
AS RANGE LEFT FOR VALUES (1, 100, 1000);  
```  
  
 下表顯示在分割資料行 **col1** 上使用這個資料分割函數的資料表如何進行分割。  
  
|資料分割|1|2|3|4|  
|---------------|-------|-------|-------|-------|  
|**值**|**col1** <= `1`|**col1** > `1` AND **col1** <= `100`|**col1** > `100` AND **col1** <=`1000`|**col1** > `1000`|  
  
### <a name="b-creating-a-range-right-partition-function-on-an-int-column"></a>B. 建立 int 資料行的 RANGE RIGHT 資料分割函數  
 下列資料分割函數使用前一個範例的相同 *boundary_value* [ **,** _...n_ ] 值，不過，它指定 RANGE RIGHT。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF2 (int)  
AS RANGE RIGHT FOR VALUES (1, 100, 1000);  
```  
  
 下表顯示在分割資料行 **col1** 上使用這個資料分割函數的資料表如何進行分割。  
  
|資料分割|1|2|3|4|  
|---------------|-------|-------|-------|-------|  
|**值**|**col1** \< `1`|**col1** >= `1` 且 **col1** \< `100`|**col1** >= `100` 且 **col1** \< `1000`|**col1** >= `1000`| 
  
### <a name="c-creating-a-range-right-partition-function-on-a-datetime-column"></a>C. 建立 datetime 資料行的 RANGE RIGHT 資料分割函數  
 下列資料分割函數會將資料表或是索引分割成為 12 個資料分割，分別在 **datetime** 資料行中顯示一年中各個月份的價值。  
  
```sql  
CREATE PARTITION FUNCTION [myDateRangePF1] (datetime)  
AS RANGE RIGHT FOR VALUES ('20030201', '20030301', '20030401',  
               '20030501', '20030601', '20030701', '20030801',   
               '20030901', '20031001', '20031101', '20031201');  
```  
  
 下表顯示在分割資料行 **datecol** 上使用這個資料分割函數的資料表或索引如何進行分割。  
  
|資料分割|1|2|...|11|12|  
|---------------|-------|-------|---------|--------|--------|  
|**值**|**datecol** \< `February 1, 2003`|**datecol** >= `February 1, 2003` 且 **datecol** \< `March 1, 2003`||**datecol** >= `November 1, 2003` 且 **col1** \< `December 1, 2003`|**datecol** >= `December 1, 2003`| 
  
### <a name="d-creating-a-partition-function-on-a-char-column"></a>D. 建立 char 資料行的資料分割函數  
 下列資料分割函數會將資料表或索引分割成四份資料分割。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF3 (char(20))  
AS RANGE RIGHT FOR VALUES ('EX', 'RXE', 'XR');  
```  
  
 下表顯示在分割資料行 **col1** 上使用這個資料分割函數的資料表如何進行分割。  
  
|資料分割|1|2|3|4|  
|---------------|-------|-------|-------|-------|  
|**值**|**col1** \< `EX`...|**col1** >= `EX` 且 **col1** \< `RXE`...|**col1** >= `RXE` 且 **col1** \< `XR`...|**col1** >= `XR`| 
  
### <a name="e-creating-15000-partitions"></a>E. 建立 15,000 個資料分割  
 下列資料分割函數會將資料表或索引分割成 15,000 個資料分割。  
  
```sql  
--Create integer partition function for 15,000 partitions.  
DECLARE @IntegerPartitionFunction nvarchar(max) = 
    N'CREATE PARTITION FUNCTION IntegerPartitionFunction (int) 
    AS RANGE RIGHT FOR VALUES (';  
DECLARE @i int = 1;  
WHILE @i < 14999  
BEGIN  
SET @IntegerPartitionFunction += CAST(@i as nvarchar(10)) + N', ';  
SET @i += 1;  
END  
SET @IntegerPartitionFunction += CAST(@i as nvarchar(10)) + N');';  
EXEC sp_executesql @IntegerPartitionFunction;  
GO  
```  
  
### <a name="f-creating-partitions-for-multiple-years"></a>F. 建立多個年度的資料分割  
 下列資料分割函數會將資料表或索引分割成 **datetime2** 資料行上的 50 個資料分割。 2007 年 1 月至 2011 年 1 月之間的每個月份都有一個資料分割。  
  
```sql  
--Create date partition function with increment by month.  
DECLARE @DatePartitionFunction nvarchar(max) = 
    N'CREATE PARTITION FUNCTION DatePartitionFunction (datetime2) 
    AS RANGE RIGHT FOR VALUES (';  
DECLARE @i datetime2 = '20070101';  
WHILE @i < '20110101'  
BEGIN  
SET @DatePartitionFunction += '''' + CAST(@i as nvarchar(10)) + '''' + N', ';  
SET @i = DATEADD(MM, 1, @i);  
END  
SET @DatePartitionFunction += '''' + CAST(@i as nvarchar(10))+ '''' + N');';  
EXEC sp_executesql @DatePartitionFunction;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [資料分割資料表與索引](../../relational-databases/partitions/partitioned-tables-and-indexes.md)   
 [$PARTITION &#40;Transact-SQL&#41;](../../t-sql/functions/partition-transact-sql.md)   
 [ALTER PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-partition-function-transact-sql.md)   
 [DROP PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-partition-function-transact-sql.md)   
 [CREATE PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-scheme-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)   
 [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sys.partition_functions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partition-functions-transact-sql.md)   
 [sys.partition_parameters &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partition-parameters-transact-sql.md)   
 [sys.partition_range_values &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partition-range-values-transact-sql.md)   
 [sys.partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md)   
 [sys.tables &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-tables-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)  
  
  

