---
description: sys.tables (Transact-SQL)
title: sys. 資料表 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/22/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- tables_TSQL
- sys.tables_TSQL
- sys.tables
- tables
dev_langs:
- TSQL
helpviewer_keywords:
- sys.tables catalog view
ms.assetid: 8c42eba1-c19f-4045-ac82-b97a5e994090
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c20bbd88f7f65ce16029913a63c4516aa935dbb3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094459"
---
# <a name="systables-transact-sql"></a>sys.tables (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的每個使用者資料表，各傳回一個資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|\<inherited columns>||如需此視圖所繼承之資料行的清單，請參閱 [sys. objects &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)。|  
|lob_data_space_id|**int**|非零值是存放這份資料表的大型物件二進位 (LOB) 資料之資料空間 (檔案群組或分割區配置) 的識別碼。 LOB 資料類型的範例包括 **Varbinary (max)**、 **Varchar (max)**、 **geography** 或 **xml**。<br /><br /> 0 = 資料表沒有 LOB 資料。|  
|filestream_data_space_id|**int**|這是 FILESTREAM 檔案群組的資料空間識別碼，或是由 FILESTREAM 檔案群組所組成的分割區配置。<br /><br /> 若要報告 FILESTREAM 檔案群組的名稱，請執行查詢 `SELECT FILEGROUP_NAME (filestream_data_space_id) FROM sys.tables` 。<br /><br /> sys.tables 可以聯結到 filestream_data_space_id = data_space_id 上的下列檢視表。<br /><br /> -sys. 檔案群組<br /><br /> -sys.partition_schemes<br /><br /> -sys. 索引<br /><br /> -sys.allocation_units<br /><br /> -sys.fulltext_catalogs<br /><br /> -sys.data_spaces<br /><br /> -sys.destination_data_spaces<br /><br /> -sys.master_files<br /><br /> -sys.database_files<br /><br /> -backupfilegroup (加入 filegroup_id) |  
|max_column_id_used|**int**|這份資料表用過的最大資料行識別碼。|  
|lock_on_bulk_load|**bit**|資料表在大量載入時會予以鎖定。 如需詳細資訊，請參閱 [sp_tableoption &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-tableoption-transact-sql.md)。|  
|uses_ansi_nulls|**bit**|資料表是在 SET ANSI_NULLS 資料庫選項為 ON 的情況下加以建立。|  
|is_replicated|**bit**|1 = 利用快照式複寫或異動複寫來發行資料表。|  
|has_replication_filter|**bit**|1 = 資料表有一項複寫篩選。|  
|is_merge_published|**bit**|1 = 資料表是利用合併式複寫來發行。|  
|is_sync_tran_subscribed|**bit**|1 = 資料表是利用立即更新訂閱來訂閱。|  
|has_unchecked_assembly_data|**bit**|1 = 資料表包含保存資料，這些保存資料會隨著上次 ALTER ASSEMBLY 期間變更定義的組件而不同。 在下次 DBCC CHECKDB 或 DBCC CHECKTABLE 順利完成之後，將重設為 0。|  
|text_in_row_limit|**int**|"text in row" 所允許的最大位元組數目。<br /><br /> 0 = 未設定 "text in row" 選項。 如需詳細資訊，請參閱 [sp_tableoption &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-tableoption-transact-sql.md)。|  
|large_value_types_out_of_row|**bit**|1 = 大數值類型是以 out-of-row 的方式來儲存。 如需詳細資訊，請參閱 [sp_tableoption &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-tableoption-transact-sql.md)。|  
|is_tracked_by_cdc|**bit**|1 = 資料表已啟用異動資料擷取。 如需詳細資訊，請參閱 [sys.sp_cdc_enable_table &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sys-sp-cdc-enable-table-transact-sql.md)。|  
|lock_escalation|**tinyint**|資料表之 LOCK_ESCALATION 選項的值：<br /><br /> 0 = TABLE<br /><br /> 1 = DISABLE<br /><br /> 2 = AUTO|  
|lock_escalation_desc|**nvarchar(60)**|資料表之 lock_escalation 選項的文字描述。 可能的值為：TABLE、AUTO 和 DISABLE。|  
|is_filetable|**bit**|**適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本和 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> 1 = 資料表是 FileTable。<br /><br /> 如需有關 FileTable 的詳細資訊，請參閱 [FileTables &#40;SQL Server&#41;](../../relational-databases/blob/filetables-sql-server.md)。|  
|持久性|**tinyint**|**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> 以下是可能的值：<br /><br /> 0 = SCHEMA_AND_DATA<br /><br /> 1 = SCHEMA_ONLY<br /><br /> 預設值為 0 值。|  
|durability_desc|**nvarchar(60)**|**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> 以下是可能的值：<br /><br /> SCHEMA_ONLY<br /><br /> SCHEMA_AND_DATA<br /><br /> SCHEMA_AND_DATA 的值表示資料表是持久、記憶體中的資料表。 SCHEMA_AND_DATA 是記憶體最佳化資料表的預設值。 SCHEMA_ONLY 值表示，在具有記憶體最佳化物件的資料庫重新啟動時，資料表資料不會保存。|  
|is_memory_optimized|**bit**|**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> 以下是可能的值：<br /><br /> 0 = 不是記憶體最佳化的。<br /><br /> 1 = 是記憶體最佳化的。<br /><br /> 預設值是 0 值。<br /><br /> 記憶體最佳化的資料表是記憶體中的使用者資料表，其結構描述保存在磁碟上，類似於其他使用者資料表。 記憶體最佳化的資料表可以從原生編譯預存程序存取。|  
|temporal_type|**tinyint**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本和 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> 代表資料表類型的數值：<br /><br /> 0 = NON_TEMPORAL_TABLE<br /><br /> 1 = HISTORY_TABLE<br /><br /> 2 = SYSTEM_VERSIONED_TEMPORAL_TABLE|  
|temporal_type_desc|**nvarchar(60)**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本和 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> 資料表類型的文字描述：<br /><br /> NON_TEMPORAL_TABLE<br /><br /> HISTORY_TABLE<br /><br /> SYSTEM_VERSIONED_TEMPORAL_TABLE|  
|history_table_id|**int**|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本和 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。<br /><br /> 當 temporal_type 于 (2 時，4) 會傳回維護歷程記錄資料的資料表 object_id，否則會傳回 Null。|  
|is_remote_data_archive_enabled|**bit**|**適用** 于： [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和更新版本和 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]<br /><br /> 指出資料表是否已啟用 Stretch。<br /><br /> 0 = 資料表未啟用 Stretch。<br /><br /> 1 = 資料表已啟用 Stretch。<br /><br /> 如需詳細資訊，請參閱 [Stretch Database](../../sql-server/stretch-database/stretch-database.md)。|  
|is_external|**bit**|**適用** 于： [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和更新版本、 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)] 和 [!INCLUDE[sssdwfull](../../includes/sssdwfull-md.md)] 。<br /><br /> 表示資料表是外部資料表。<br /><br /> 0 = 資料表不是外部資料表。<br /><br /> 1 = 資料表是外部資料表。| 
|history_retention_period|**int**|**適用於**： [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。 <br/><br/>數值，表示時態歷程記錄保留期間的持續時間（以 history_retention_period_unit 指定的單位）。 |  
|history_retention_period_unit|**int**|**適用於**： [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。 <br/><br/>代表時態歷程記錄保留週期單位之類型的數值。 <br /><br />-1：無限 <br /><br />3：日 <br /><br />4：周 <br /><br />5：月 <br /><br />6：年 |  
|history_retention_period_unit_desc|**Nvarchar (10)**|**適用於**： [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。 <br/><br/>時態歷程記錄保留週期單位之類型的文字描述。 <br /><br />INFINITE <br /><br />DAY <br /><br />WEEK <br /><br />月 <br /><br />年 |  
|is_node|**bit**|**適用對象**：[!INCLUDE[sssql17-md.md](../../includes/sssql17-md.md)] 和 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。 <br/><br/>1 = 這是圖形節點資料表。 <br /><br />0 = 這不是圖形節點資料表。 |  
|is_edge|**bit**|**適用對象**：[!INCLUDE[sssql17-md.md](../../includes/sssql17-md.md)] 和 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。 <br/><br/>1 = 這是圖形邊緣資料表。 <br /><br />0 = 這不是圖形邊緣資料表。 |  

## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="examples"></a>範例  
 下列範例會傳回沒有主索引鍵的所有使用者資料表。  
  
```  
SELECT SCHEMA_NAME(schema_id) AS schema_name  
    ,name AS table_name   
FROM sys.tables   
WHERE OBJECTPROPERTY(object_id,'TableHasPrimaryKey') = 0  
ORDER BY schema_name, table_name;  
GO  
  
```  
  
下列範例顯示如何公開相關的時態性資料。  
   
**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本和 [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。
  
```  
SELECT T1.object_id, T1.name as TemporalTableName, SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,  
T2.name as HistoryTableName, SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,  
T1.temporal_type_desc  
FROM sys.tables T1  
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id  
ORDER BY T1.temporal_type desc  
```  

下列範例會顯示如何公開時態性記錄保留的資訊。  

**適用於**： [!INCLUDE[sssdsfull](../../includes/sssdsfull-md.md)]。  
  
```  
SELECT DB.is_temporal_history_retention_enabled, SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema, 
T1.name as TemporalTableName, SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema, T2.name as HistoryTableName,
T1.history_retention_period, T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases where name = DB_NAME()) DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2 
```  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [DBCC CHECKDB &#40;Transact-sql&#41;](../../t-sql/database-console-commands/dbcc-checkdb-transact-sql.md)   
 [DBCC CHECKTABLE &#40;Transact-sql&#41;](../../t-sql/database-console-commands/dbcc-checktable-transact-sql.md)   
 [查詢 SQL Server 系統目錄常見問題](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)   
 [記憶體內部 OLTP &#40;記憶體內部最佳化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)  
  
  
