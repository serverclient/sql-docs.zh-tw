---
description: sys.dm_db_xtp_table_memory_stats (Transact-SQL)
title: sys.dm_db_xtp_table_memory_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/29/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_db_xtp_table_memory_stats_TSQL
- dm_db_xtp_table_memory_stats
- sys.dm_db_xtp_table_memory_stats
- dm_db_xtp_table_memory_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_xtp_table_memory_stats
- dm_db_xtp_table_memory_stats
ms.assetid: ad0efc06-3d9c-4861-9dfa-a7a87822d0c8
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 12e1f0e140593526314ce12a38df96effadaafb7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099831"
---
# <a name="sysdm_db_xtp_table_memory_stats-transact-sql"></a>sys.dm_db_xtp_table_memory_stats (Transact-SQL)
[!INCLUDE[sql-asdb-asdbmi](../../includes/applies-to-version/sql-asdb-asdbmi.md)]

  傳回目前資料庫中每個 [!INCLUDE[hek_2](../../includes/hek-2-md.md)] 資料表 (使用者和系統) 的記憶體使用量統計資料。 系統資料表具有負數物件識別碼，並且用於儲存 [!INCLUDE[hek_2](../../includes/hek-2-md.md)] 引擎的執行階段資訊。 與使用者物件不同的是，系統資料表為內部物件且只存在於記憶體中，因而無法透過目錄檢視查看。 系統資料表是用以儲存資訊，例如儲存體中所有資料/差異檔案的中繼資料、合併要求、差異檔案用於篩選資料列的標準、已卸除的資料表，以及復原與備份的相關資訊。 假設 [!INCLUDE[hek_2](../../includes/hek-2-md.md)] 引擎最多可以有 8,192 個資料檔案和差異檔案組，若是大型的記憶體中資料庫，系統資料表佔用的記憶體可能只有數 MB 之多。  
  
 如需詳細資訊，請參閱[記憶體內部 OLTP &#40;記憶體內部最佳化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|資料表的物件識別碼。 NULL 表示記憶體中 OLTP 系統資料表。|  
|memory_allocated_for_table_kb|**bigint**|配置給這個資料表的記憶體。|  
|memory_used_by_table_kb|**bigint**|資料表使用的記憶體，包括資料列版本。|  
|memory_allocated_for_indexes_kb|**bigint**|配置給這個資料表之索引的記憶體。|  
|memory_used_by_indexes_kb|**bigint**|這個資料表之索引所耗用的記憶體。|  
  
## <a name="permissions"></a>權限  
 如果您具有目前資料庫的 VIEW DATABASE STATE 權限，則會傳回所有資料列。 否則，就會傳回空白資料列集。  
  
 如果您沒有 VIEW DATABASE 權限，則會傳回您具有 SELECT 權限之資料表資料列的所有資料行。  
  
 只針對具有 VIEW DATABASE STATE 權限的使用者傳回系統資料表。  
  
## <a name="examples"></a>範例  
 您可以查詢下列 DMV，取得配置給資料庫內之資料表和索引的記憶體：  
  
```  
-- finding memory for objects  
SELECT OBJECT_NAME(object_id), *   
FROM sys.dm_db_xtp_table_memory_stats;  
```  
  
 若要尋找資料庫內所有物件的記憶體：  
  
```  
SELECT SUM( memory_allocated_for_indexes_kb + memory_allocated_for_table_kb) AS  
 memoryallocated_objects_in_kb   
FROM sys.dm_db_xtp_table_memory_stats;  
```  
  
## <a name="user-scenario"></a>使用者案例  
 先在名為 HkDb1 的資料庫中建立下列資料表。  
  
```  
-- set max server memory to 4 GB  
EXEC sp_configure 'max server memory (MB)', 4048  
go  
  
RECONFIGURE  
go  
  
-- create a resource pool for database with memory-optimized objects  
CREATE RESOURCE POOL PoolHkDb1 WITH (MAX_MEMORY_PERCENT = 50);  
ALTER RESOURCE GOVERNOR RECONFIGURE;  
go  
  
--bind the pool to the database  
EXEC sp_xtp_bind_db_resource_pool 'HkDb1', 'PoolHkdb1'  
go  
  
-- take database offline/online to associate the pool  
use master  
go  
  
alter database HkDb1 set offline  
go  
alter database HkDb1 set online  
go  
  
USE HkDb1  
go  
  
CREATE TABLE dbo.t1 (  
       c1 int NOT NULL,  
       c2 char(40) NOT NULL,  
       c3 char(8000) NOT NULL,  
  
       CONSTRAINT [pk_t1_c1] PRIMARY KEY NONCLUSTERED HASH (c1) WITH (BUCKET_COUNT = 100000)  
) WITH (MEMORY_OPTIMIZED = ON, DURABILITY = SCHEMA_AND_DATA)  
go  
  
CREATE TABLE dbo.t2 (  
       c1 int NOT NULL,  
       c2 char(40) NOT NULL,  
       c3 char(8000) NOT NULL,  
  
       CONSTRAINT [pk_t2_c1] PRIMARY KEY NONCLUSTERED HASH (c1) WITH (BUCKET_COUNT = 100000)  
) WITH (MEMORY_OPTIMIZED = ON, DURABILITY = SCHEMA_AND_DATA)  
go  
  
CREATE TABLE dbo.t3 (  
       c1 int NOT NULL,  
       c2 char(40) NOT NULL,  
       c3 char(8000) NOT NULL,  
  
       CONSTRAINT [pk_t3_c1] PRIMARY KEY NONCLUSTERED HASH (c1) WITH (BUCKET_COUNT = 1000000)  
) WITH (MEMORY_OPTIMIZED = ON, DURABILITY = SCHEMA_AND_DATA)  
go  
  
-- load 150K rows  
declare @i int = 0  
while (@i <= 150000)  
begin  
       insert t1 values (@i, 'a', replicate ('b', 8000))  
       set @i += 1;  
end  
go  
```  
  
 當資料載入資料表中時，您可以看到使用者定義的資料表，以及資料所使用的儲存空間。 例如，資料表的每一個資料列大約是 8070 MB (配置大小為 8K (8192 個位元組))。 您可以看到每個資料表的索引，以及索引所使用的儲存空間。 例如，1MB 是 100K 個項目，其進位到下一個 2 乘冪 (2**17) = 131072 (各有 8 個位元組)。 資料表不一定有索引，在這種情況下，它會顯示索引的記憶體配置。 其他資料列可能代表系統資料表  
  
```  
select convert(char(10), object_name(object_id)) as Name,*   
from sys.dm_db_xtp_table_memory_stats  
```  
  
 輸出如下，分成兩個部分：  
  
```  
Name       object_id   memory_allocated_for_table_kb memory_used_by_table_kb  
---------- ----------- ----------------------------- -----------------------  
t3         629577281   0                             0  
t1         565577053   1372928                       1202351  
t2         597577167   0                             0  
NULL       -6          0                             0  
NULL       -5          0                             0  
NULL       -4          0                             0  
NULL       -3          0                             0  
NULL       -2          192                           25  
  
memory_allocated_for_indexes_kb memory_used_by_indexes_kb  
------------------------------- -------------------------  
8192                            8192  
1024                            1024  
8192                            8192  
2                               2  
24                              24  
2                               2  
2                               2  
16                              16  
```  
  
 下列程式碼的輸出  
  
```  
select  sum(allocated_bytes)/(1024*1024) as total_allocated_MB,   
       sum(used_bytes)/(1024*1024) as total_used_MB  
from sys.dm_db_xtp_memory_consumers  
```  
  
 為  
  
```  
total_allocated_MB   total_used_MB  
-------------------- --------------------  
1357                 1191  
```  
  
 接著，讓我們來看看資源集區的輸出。 請注意，集區所使用的記憶體是 1356 MB  
  
```  
select pool_id,convert(char(10), name) as Name, min_memory_percent, max_memory_percent,   
   max_memory_kb/1024 as max_memory_mb  
from sys.dm_resource_governor_resource_pools  
  
select used_memory_kb/1024 as used_memory_mb ,target_memory_kb/1024 as target_memory_mb  
from sys.dm_resource_governor_resource_pools  
```  
  
 輸出如下：  
  
```  
pool_id     Name       min_memory_percent max_memory_percent max_memory_mb  
----------- ---------- ------------------ ------------------ --------------------  
1           internal   0                  100                3845  
2           default    0                  100                3845  
259         PoolHkDb1  0                  100                3845  
  
used_memory_mb       target_memory_mb  
-------------------- --------------------  
125                  3845  
32                   3845  
1356                 3845  
```  
  
## <a name="see-also"></a>另請參閱  
 [記憶體最佳化的資料表動態管理檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/memory-optimized-table-dynamic-management-views-transact-sql.md)  
  
  
