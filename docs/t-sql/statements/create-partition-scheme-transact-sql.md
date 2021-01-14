---
description: CREATE PARTITION SCHEME (Transact-SQL)
title: CREATE PARTITION SCHEME (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE PARTITION SCHEME
- SCHEME
- PARTITION SCHEME
- CREATE_PARTITION_SCHEME_TSQL
- SCHEME_TSQL
- PARTITION_SCHEME_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- partitioned indexes [SQL Server], schemes
- partitioned tables [SQL Server], schemes
- CREATE PARTITION SCHEME statement
- partition schemes [SQL Server], creating
- filegroups [SQL Server], partitions
- partitioned indexes [SQL Server], filegroups
- partitioned tables [SQL Server], filegroups
- mapping partitions [SQL Server]
ms.assetid: 5b21c53a-b4f4-4988-89a2-801f512126e4
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 36b22f01bbc9bd089cf6f651d8f97d2d2d2b3d32
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093466"
---
# <a name="create-partition-scheme-transact-sql"></a>CREATE PARTITION SCHEME (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  在目前資料庫建立一項配置，將資料分割資料表或索引的資料分割，對應至檔案群組。 資料分割資料表或索引的資料分割數目和網域，由資料分割函數來決定。 在建立分割區配置之前，必須先在 [CREATE PARTITION FUNCTION](../../t-sql/statements/create-partition-function-transact-sql.md) 陳述式中建立分割區函數。  

>[!NOTE]
>在 Azure SQL Database 中，只支援主要檔案群組。  

 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE PARTITION SCHEME partition_scheme_name  
AS PARTITION partition_function_name  
[ ALL ] TO ( { file_group_name | [ PRIMARY ] } [ ,...n ] )  
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *partition_scheme_name*  
 這是資料分割結構描述的名稱。 分割區配置名稱在資料庫內必須是唯一的，且必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 *partition_function_name*  
 這是使用資料分割結構描述的資料分割函數名稱。 資料分割函數所建立的資料分割會對應至資料分割結構描述所指定的檔案群組。 *partition_function_name* 必須已存在於資料庫中。 單一資料分割無法同時包含 FILESTREAM 和非 FILESTREAM 檔案群組。  
  
 ALL  
 指定所有分割區都對應至 *file_group_name* 所提供的檔案群組，如果指定了 **[** PRIMARY **]**，便是對應至主要檔案群組。 如果指定 ALL，則只能指定一個 *file_group_name*。  
  
 *file_group_name* |  **[** PRIMARY **]** [ **,** _...n_]  
 指定用來存放 *partition_function_name* 所指定之分割區的檔案群組名稱。 *file_group_name* 必須已存在於資料庫中。  
  
 如果指定了 **[** PRIMARY **]**，就會將分割區儲存在主要檔案群組。 如果指定 ALL，則只能指定一個 *file_group_name*。 分割區是從分割區 1 開始，依照 [ **,** _...n_] 中列出檔案群組的順序來指派給各個檔案群組。 在 [ **,** _...n_] 中，可以重複指定相同的 *file_group_name*。 如果 *n* 不足無法存放 *partition_function_name* 中所指定的資料分割數目，則 CREATE PARTITION SCHEME 會失敗，並會出現一則錯誤。  
  
 如果 *partition_function_name* 產生的分割區數目比檔案群組少，第一個未指派的檔案群組會標示為 NEXT USED，且會出現一則命名 NEXT USED 檔案群組的參考訊息。 如果指定 ALL，唯一的 *file_group_name* 會維護它的 NEXT USED 屬性，以用於這個 *partition_function_name*。 如果在 ALTER PARTITION FUNCTION 陳述式中建立資料分割，NEXT USED 檔案群組便會收到其他資料分割。 若要建立其他未指派的檔案群組來存放新的資料分割，請使用 ALTER PARTITION SCHEME。  
  
 當您指定 *file_group_name* [ 1 **,** _...n_] 中的主要檔案群組時，必須依照 **[** PRIMARY **]** 中的相同方式來分隔 PRIMARY，因為它是一個關鍵字。  
  
 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]只支援使用 PRIMARY。 請參閱以下的範例 E。 
  
## <a name="permissions"></a>權限  
 下列權限可用來執行 CREATE PARTITION SCHEME：  
  
-   ALTER ANY DATASPACE 權限。 這個權限預設會授與 **sysadmin** 固定伺服器角色以及 **db_owner** 和 **db_ddladmin** 固定資料庫角色的成員。  
  
-   建立資料分割結構描述之資料庫的 CONTROL 或 ALTER 權限。  
  
-   在建立資料分割結構描述的資料庫中，其伺服器的 CONTROL SERVER 或 ALTER ANY DATABASE 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-a-partition-scheme-that-maps-each-partition-to-a-different-filegroup"></a>A. 建立將每個資料分割對應至不同檔案群組的資料分割結構描述  
 下列範例會建立一個資料分割函數，將資料表或索引分割成四個資料分割。 之後，會建立資料分割結構描述來指定分別存放這四份資料分割的檔案群組。 這個範例假設這些檔案群組已在資料庫中。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF1 (INT)  
AS RANGE LEFT FOR VALUES (1, 100, 1000);  
GO  
CREATE PARTITION SCHEME myRangePS1  
AS PARTITION myRangePF1  
TO (test1fg, test2fg, test3fg, test4fg);  
```  
  
 在資料分割資料行 **col1** 上使用分割區函數 `myRangePF1` 之資料表的分割區，會依照下表所示進行指派。  
  
||||||  
|-|-|-|-|-|  
|**檔案群組**|`test1fg`|`test2fg`|`test3fg`|`test4fg`|  
|**資料分割**|1|2|3|4|  
|**值**|**col1** <= `1`|**col1** > `1` AND **col1** <= `100`|**col1** > `100` AND **col1** <= `1000`|**col1** > `1000`|  
  
### <a name="b-creating-a-partition-scheme-that-maps-multiple-partitions-to-the-same-filegroup"></a>B. 建立將多個資料分割對應至相同檔案群組的資料分割結構描述  
 如果所有資料分割都對應至相同的檔案群組，請使用 ALL 關鍵字。 但如果不是全部，而只是多個資料分割對應至相同的檔案群組，就必須依照下列範例所示來重複檔案群組名稱。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF2 (INT)  
AS RANGE LEFT FOR VALUES (1, 100, 1000);  
GO  
CREATE PARTITION SCHEME myRangePS2  
AS PARTITION myRangePF2  
TO ( test1fg, test1fg, test1fg, test2fg );  
```  
  
 在資料分割資料行 **col1** 上使用分割區函數 `myRangePF2` 之資料表的分割區，會依照下表所示進行指派。  
  
||||||  
|-|-|-|-|-|  
|**檔案群組**|`test1fg`|`test1fg`|`test1fg`|`test2fg`|  
|**資料分割**|1|2|3|4|  
|**值**|**col1** <= `1`|**col1** > 1 AND **col1** <= `100`|**col1** > `100` AND **col1** <= `1000`|**col1** > `1000`|  
  
### <a name="c-creating-a-partition-scheme-that-maps-all-partitions-to-the-same-filegroup"></a>C. 建立將所有資料分割對應至相同檔案群組的資料分割結構描述  
 下列範例會建立上述各範例的相同資料分割函數，且會建立一項資料分割結構描述，將所有資料分割對應至相同的檔案群組。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF3 (INT)  
AS RANGE LEFT FOR VALUES (1, 100, 1000);  
GO  
CREATE PARTITION SCHEME myRangePS3  
AS PARTITION myRangePF3  
ALL TO ( test1fg );  
```  
  
### <a name="d-creating-a-partition-scheme-that-specifies-a-next-used-filegroup"></a>D. 建立指定 'NEXT USED' 檔案群組的資料分割結構描述  
 下列範例會建立上述各範例的相同資料分割函數，且會建立一項資料分割結構描述來列出數量超出相關聯資料分割函數所建立之資料分割的檔案群組。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF4 (INT)  
AS RANGE LEFT FOR VALUES (1, 100, 1000);  
GO  
CREATE PARTITION SCHEME myRangePS4  
AS PARTITION myRangePF4  
TO (test1fg, test2fg, test3fg, test4fg, test5fg)  
```  
  
 執行這個陳述式會傳回下列訊息。  
  
已順利建立分割區配置 'myRangePS4'。 'test5fg' 已標示為分割區配置 'myRangePS4' 中下一個使用的檔案群組。  
  
  
 如果將資料分割函數 `myRangePF4` 改成加入資料分割，`test5fg` 檔案群組會收到新建立的資料分割。  

### <a name="e-creating-a-partition-scheme-only-on-primary"></a>E. 僅在 PRIMARY 上建立資料分割配置

 Azure [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 不支援新增檔案和檔案群組，但只有跨 PRIMARY 檔案群組進行資料分割時支援資料表分割區。 

下列範例會建立一個資料分割函數，將資料表或索引分割成四個資料分割。 然後，就會建立分割區配置，以指定在 PRIMARY 檔案群組中建立所有分割區。  
  
```sql  
CREATE PARTITION FUNCTION myRangePF1 (INT)  
AS RANGE LEFT FOR VALUES (1, 100, 1000);  
GO  
CREATE PARTITION SCHEME myRangePS1  
AS PARTITION myRangePF1  
ALL TO ( [PRIMARY] );  
```
   
## <a name="see-also"></a>另請參閱  
 [CREATE PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-function-transact-sql.md)   
 [ALTER PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/alter-partition-scheme-transact-sql.md)   
 [DROP PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/drop-partition-scheme-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [建立分割區資料表及索引](../../relational-databases/partitions/create-partitioned-tables-and-indexes.md)   
 [sys.partition_schemes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partition-schemes-transact-sql.md)   
 [sys.data_spaces &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)   
 [sys.destination_data_spaces &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-destination-data-spaces-transact-sql.md)   
 [sys.partitions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md)   
 [sys.tables &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-tables-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)  
  
  
