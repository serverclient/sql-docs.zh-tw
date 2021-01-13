---
description: sys.dm_io_virtual_file_stats (Transact-SQL)
title: sys.dm_io_virtual_file_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_io_virtual_file_stats
- sys.dm_io_virtual_file_stats_TSQL
- sys.dm_io_virtual_file_stats
- dm_io_virtual_file_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_io_virtual_file_stats dynamic management function
ms.assetid: fa3e321f-6fe5-45ff-b397-02a0dd3d6b7d
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 48292ef07266a6dff28479a4f08e09d0be824d93
ms.sourcegitcommit: e40e75055c1435c5e3f9b6e3246be55526807b4c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98151290"
---
# <a name="sysdm_io_virtual_file_stats-transact-sql"></a>sys.dm_io_virtual_file_stats (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  傳回資料和記錄檔的 I/O 統計資料。 這個動態管理函數會取代 [fn_virtualfilestats](../../relational-databases/system-functions/sys-fn-virtualfilestats-transact-sql.md) 函式。  
  
> [!NOTE]  
>  若要從呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] ，請使用名稱 **sys.dm_pdw_nodes_io_virtual_file_stats**。 

## <a name="syntax"></a>語法  
  
```  
-- Syntax for SQL Server and Azure SQL Database

sys.dm_io_virtual_file_stats (   
    { database_id | NULL },  
    { file_id | NULL }  
)  
```  

```  
-- Syntax for Azure Synapse Analytics

sys.dm_pdw_nodes_io_virtual_file_stats
```
  
## <a name="arguments"></a>引數  


 *database_id* |空

 **適用於：** SQL Server (從 2008 開始)、Azure SQL Database

 資料庫的識別碼。 *database_id* 是 int，沒有預設值。 資料庫的識別碼或 NULL 都是有效的輸入。 如果指定 NULL，則會傳回 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中所有的資料庫。  
  
 可以指定內建函數 [DB_ID](../../t-sql/functions/db-id-transact-sql.md)。  
  
*file_id* |空

**適用於：** SQL Server (從 2008 開始)、Azure SQL Database
 
檔案識別碼。 *file_id* 是 int，沒有預設值。 有效輸入是檔案的識別碼或 NULL。 如果指定 NULL，則會傳回資料庫中所有的檔案。  
  
 您可以指定內建函數 [FILE_IDEX](../../t-sql/functions/file-idex-transact-sql.md) ，並參考目前資料庫中的檔案。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**database_name**|**sysname**|不適 **用於：**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。<br /><br /> 資料庫名稱。</br></br>針對 Azure Synapse Analytics，這是 pdw_node_id 所識別之節點上儲存的資料庫名稱。 每個節點都有一個具有13個檔案的 tempdb 資料庫。 每個節點在每個散發中也都有一個資料庫，而且每個散發資料庫都有5個檔案。 例如，如果每個節點都包含4個散發，結果會顯示每個 pdw_node_id 20 個散發資料庫檔案。 
|**database_id**|**smallint**|資料庫的識別碼。|  
|**file_id**|**smallint**|檔案的識別碼。|  
|**sample_ms**|**bigint**|自電腦啟動之後的毫秒數。 這個資料行可用來比較這個函數的不同輸出。</br></br>資料類型為 **int** [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)][!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|  
|**num_of_reads**|**bigint**|對檔案發出的讀取數。|  
|**num_of_bytes_read**|**bigint**|這個檔案讀取的總位元組數。|  
|**io_stall_read_ms**|**bigint**|使用者等候在檔案發出讀取的總時間 (以毫秒為單位)。|  
|**num_of_writes**|**bigint**|這個檔案所進行的寫入數。|  
|**num_of_bytes_written**|**bigint**|寫入檔案的總位元組數。|  
|**io_stall_write_ms**|**bigint**|使用者等候檔案完成寫入的總時間 (以毫秒為單位)。|  
|**io_stall**|**bigint**|使用者等候檔案完成 I/O 的總時間 (以毫秒為單位)。|  
|**size_on_disk_bytes**|**bigint**|該檔案在磁碟上所用的位元組數。 如果是疏鬆檔案，這個數字就是資料庫快照集在磁碟上所用的實際位元組數。|  
|**file_handle**|**varbinary**|這個檔案的 Windows 檔案控制代碼。|  
|**io_stall_queued_read_ms**|**bigint**|不適 **用於：**： [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 至 [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 。<br /><br /> IO 資源管理針對讀取導入的總 IO 延遲。 不可為 Null。 如需詳細資訊，請參閱 [sys.dm_resource_governor_resource_pools &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql.md)。|  
|**io_stall_queued_write_ms**|**bigint**|不適 **用於：**： [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 至 [!INCLUDE[ssSQL12](../../includes/sssql11-md.md)] 。<br /><br />  IO 資源管理針對寫入導入的總 IO 延遲。 不可為 Null。|
|**pdw_node_id**|**int**|**適用對象：** [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]</br></br>分佈的節點識別碼。
 
## <a name="remarks"></a>備註
只要啟動 SQL Server (MSSQLSERVER) 服務，計數器就會初始化為空的。
  
## <a name="permissions"></a>權限  
 需要 VIEW SERVER STATE 權限。 如需詳細資訊，請參閱 [&#40;transact-sql&#41;的動態管理檢視和函數 ](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)。  
  
## <a name="examples"></a>範例  

### <a name="a-return-statistics-for-a-log-file"></a>A. 傳回記錄檔的統計資料

**適用于：** 從 2008) 開始 SQL Server (Azure SQL Database

 下列範例會傳回 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中記錄檔的統計資料。  
  
```sql  
SELECT * FROM sys.dm_io_virtual_file_stats(DB_ID(N'AdventureWorks2012'), 2);  
GO  
```  
  
### <a name="b-return-statistics-for-file-in-tempdb"></a>B. 傳回 tempdb 中檔案的統計資料

**適用于：** Azure Synapse Analytics

```sql
SELECT * FROM sys.dm_pdw_nodes_io_virtual_file_stats 
WHERE database_name = 'tempdb' AND file_id = 2;

```

## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [I/o 相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/i-o-related-dynamic-management-views-and-functions-transact-sql.md)   
 [sys.database_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)   
 [sys.master_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)  
  
  

