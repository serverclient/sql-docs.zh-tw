---
title: sys.dm_db_xtp_checkpoint_files (Transact-sql) |Microsoft Docs
description: 顯示檢查點檔案的相關資訊，包括檔案大小、實體位置和交易識別碼。 瞭解此視圖與 SQL Server 版本的差異。
ms.date: 03/20/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.custom: ''
ms.technology: in-memory-oltp
ms.topic: language-reference
f1_keywords:
- dm_db_xtp_checkpoint_files
- sys.dm_db_xtp_checkpoint_files_TSQL
- dm_db_xtp_checkpoint_files_TSQL
- sys.dm_db_xtp_checkpoint_files
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_xtp_checkpoint_files dynamic management view
ms.assetid: ac8e6333-7a9f-478a-b446-5602283e81c9
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: bed4fa18b01a295815c07f6a0f37d4bc0b942c16
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092899"
---
# <a name="sysdm_db_xtp_checkpoint_files-transact-sql"></a>sys.dm_db_xtp_checkpoint_files (Transact-SQL)
[!INCLUDE[sql-asdb-asdbmi](../../includes/applies-to-version/sql-asdb-asdbmi.md)]

  顯示有關檢查點檔案的資訊，包括檔案大小、實體位置及交易識別碼。  
  
> **注意：** 針對尚未關閉的目前檢查點，的 [狀態] 資料行 `ys.dm_db_xtp_checkpoint_files` 將會在 [新檔案的結構] 下進行。 當最後一個檢查點之後，當交易記錄檔成長已足夠時，或您發出 `CHECKPOINT` 命令 ([檢查點 &#40;transact-sql&#41;](../../t-sql/language-elements/checkpoint-transact-sql.md)) 時，檢查點會自動關閉。  
  
 記憶體優化的檔案群組會在內部使用僅限附加的檔案來儲存記憶體中資料表的插入和刪除資料列。 有兩種檔案類型： 當差異檔案包含已刪除之資料列的參考時，資料檔案會包含插入的資料列。 
  
 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 與較新版本的差異很大，在 [SQL Server 2014](#bkmk_2014)的主題中將會討論較低的部分。  
  
 如需詳細資訊，請參閱 [建立和管理 Memory-Optimized 物件的儲存體](../../relational-databases/in-memory-oltp/creating-and-managing-storage-for-memory-optimized-objects.md)。  
  
##  <a name="sssql15-and-later"></a><a name="bkmk_2016"></a> [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本  
 下表描述從開始的資料行 `sys.dm_db_xtp_checkpoint_files` **[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]** 。  
  
|資料行名稱|類型|描述|  
|-----------------|----------|-----------------|  
|container_id|**int**|資料或差異檔案所屬之容器的識別碼 (以 sys.database_files 中的 FILESTREAM 類型檔案來表示)。 使用 [sys.database_files &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)中的 file_id 聯結。|  
|container_guid|**uniqueidentifier**|容器的 GUID，其為根、資料或差異檔案的一部分。 與 sys.database_files 資料表中的 file_guid 聯結。|  
|checkpoint_file_id|**uniqueidentifier**|檢查點檔案的 GUID。|  
|relative_file_path|**nvarchar(256)**|相對於對應容器的檔案路徑。|  
|file_type|**smallint**|-1 代表免費<br /><br /> 0適用于資料檔案。<br /><br /> 1代表差異檔案。<br /><br /> 2為根檔案<br /><br /> 3適用于大型資料檔案|  
|file_type_desc|**nvarchar(60)**|免費：維持為可用的所有檔案都可以進行配置。 視系統預期的需求而定，可用檔案的大小可能會有所不同。 大小上限為1GB。<br /><br /> 資料檔案包含已插入記憶體優化資料表中的資料列。<br /><br /> 差異差異檔案包含已刪除之資料檔案中資料列的參考。<br /><br /> 根根檔案包含記憶體優化和原生編譯物件的系統中繼資料。<br /><br /> 大型資料：大型資料檔案包含插入 (n) Varchar (max) 和 Varbinary (max) 資料行，以及屬於記憶體優化資料表上資料行存放區索引一部分的資料行區段。|  
|internal_storage_slot|**int**|內部儲存體陣列中之檔案的索引。 如果是根或1以外的狀態，則為 Null。|  
|checkpoint_pair_file_id|**uniqueidentifier**|對應的資料或差異檔案。 根的 Null。|  
|file_size_in_bytes|**bigint**|磁片上的檔案大小。|  
|file_size_used_in_bytes|**bigint**|針對仍在擴展的檢查點檔案組，這個資料行會在下一個檢查點之後更新。|  
|logical_row_count|**bigint**|針對資料，插入的資料列數目。<br /><br /> 若為 Delta，則為捨棄資料表之後刪除的資料列數目。<br /><br /> 若為 Root，則為 Null。|  
|state|**smallint**|0-預先建立<br /><br /> 1-下建設<br /><br /> 2 - 使用中<br /><br /> 3-合併目標<br /><br /> 8-等候記錄截斷|  
|state_desc|**nvarchar(60)**|預先建立-預先配置的一些檢查點檔案可將任何等候配置新檔案的等候降至最低或刪除，因為正在執行交易。 這些預先建立檔的大小可能會有所不同，視工作負載的預估需求而定，但不包含任何資料。 這是具有 MEMORY_OPTIMIZED_DATA 檔案群組之資料庫的儲存空間額外負荷。<br /><br /> 在 [結構] 下，這些檢查點檔案是在結構中，這表示它們會根據資料庫所產生的記錄檔記錄進行填入，而且還不是檢查點的一部分。<br /><br /> 使用中-這些包含先前關閉的檢查點所插入/刪除的資料列。 在資料庫重新開機時套用交易記錄的使用中部分之前，這些資料表會包含區域讀入記憶體中的資料表內容。 我們預期這些檢查點檔案的大小大約是記憶體優化資料表的記憶體中大小的2倍，假設合併作業跟上交易工作負載。<br /><br /> 合併目標-合併作業的目標-這些檢查點檔案會儲存合併原則所識別的來源檔案中的合併資料列。 一旦安裝合併之後，「合併目標」就會轉換為「使用中」狀態。<br /><br /> 等候記錄截斷-在安裝合併且合併目標 CFP 是永久性檢查點的一部分之後，合併來源檢查點檔案會轉換為此狀態。 具有記憶體優化資料表之資料庫的操作正確性需要此狀態的檔案。  例如，若要從持久性檢查點復原來回到過去。|  
|lower_bound_tsn|**bigint**|檔案中交易的下限：如果狀態不在 (1，3) ，則為 null。|  
|upper_bound_tsn|**bigint**|檔案中交易的上限;如果狀態不在 (1，3) ，則為 null。|  
|begin_checkpoint_id|**bigint**|開始檢查點的識別碼。|  
|end_checkpoint_id|**bigint**|結束檢查點的識別碼。|  
|last_updated_checkpoint_id|**bigint**|更新此檔案之最後一個檢查點的識別碼。|  
|encryption_status|**smallint**|0、1、2|  
|encryption_status_desc|**nvarchar(60)**|0 = 未加密><br /><br /> 1 => 使用金鑰1加密<br /><br /> 2 = 使用金鑰2加密>。 只對作用中的檔案有效。|  
  
##  <a name="sssql14"></a><a name="bkmk_2014"></a> [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]  
 下表描述的資料行 `sys.dm_db_xtp_checkpoint_files` **[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]** 。  
  
|資料行名稱|類型|描述|  
|-----------------|----------|-----------------|  
|container_id|**int**|資料或差異檔案所屬之容器的識別碼 (以 sys.database_files 中的 FILESTREAM 類型檔案來表示)。 使用 [sys.database_files &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)中的 file_id 聯結。|  
|container_guid|**uniqueidentifier**|資料或差異檔案所屬之容器的 GUID。|  
|checkpoint_file_id|**GUID**|資料或差異檔案的識別碼。|  
|relative_file_path|**nvarchar(256)**|資料或差異檔案的路徑 (相對於容器的位置)。|  
|file_type|**tinyint**|0 代表資料檔案。<br /><br /> 1 代表差異檔案。<br /><br /> 如果 state 資料行設定為 7 則為 NULL。|  
|file_type_desc|**nvarchar(60)**|如果 state 資料行設定為7，則檔案類型： DATA_FILE、DELTA_FILE 或 Null。|  
|internal_storage_slot|**int**|內部儲存體陣列中之檔案的索引。 如果 state 資料行不是 2 或 3 則為 NULL。|  
|checkpoint_pair_file_id|**uniqueidentifier**|對應的資料或差異檔案。|  
|file_size_in_bytes|**bigint**|使用的檔案大小。 如果 state 資料行設定為 5、6 或 7 則為 NULL。|  
|file_size_used_in_bytes|**bigint**|所使用之檔案的使用大小。 如果 state 資料行設定為 5、6 或 7 則為 NULL。<br /><br /> 針對仍在擴展的檢查點檔案組，這個資料行會在下一個檢查點之後更新。|  
|inserted_row_count|**bigint**|資料檔案中的資料列數。|  
|deleted_row_count|**bigint**|差異檔案中已刪除的資料列數。|  
|drop_table_deleted_row_count|**bigint**|資料檔案中受到卸除資料表影響的資料列數目。 當 state 資料行等於 1 時，適用於資料檔案。<br /><br /> 顯示已卸除的資料表中刪除的資料列計數。 在完成已卸除之資料表中資料列的記憶體回收及取得檢查點之後，就會編譯 drop_table_deleted_row_count 統計資料。 如果您在卸除資料表統計資料反映於這個資料行之前重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，統計資料將會在復原期間更新。 復原程序不會從已卸除的資料表中載入資料列。 載入階段會編譯已卸除之資料表的統計資料，並在復原完成時於這個資料行中報告。|  
|state|**int**|0-預先建立<br /><br /> 1-下建設<br /><br /> 2 - 使用中<br /><br /> 3-合併目標<br /><br /> 4-合併來源<br /><br /> 5-備份/HA 需要<br /><br /> 6-轉換成標記<br /><br /> 7-標記|  
|state_desc|**nvarchar(60)**|預先建立-一組較小的資料和差異檔案組（也稱為成對的檢查點檔案組） (Cfp) 會保留預先配置，以最小化或消除在執行交易時，配置新檔案的任何等候。 其為配置全額，即資料檔案大小為 128MB 且差異檔案大小為 8 MB 但不含資料。 CFP 數目是依邏輯處理器或排程器的數目來計算 (每個核心一個，無上限)，最少為 8 個。 這是具有記憶體最佳化資料表的資料庫固定的儲存負擔。<br /><br /> 在 Cfp 中，會在最後一個檢查點之後儲存剛插入且可能已刪除的資料列集。<br /><br /> 使用中 - 包含來自先前已關閉之檢查點的已插入和刪除的資料列。 這些 CFP 包含了在資料庫重新啟動時套用交易記錄的使用中部分之前所需的所有已插入和刪除的資料列。 這些 CFP 的大小大約是記憶體最佳化資料表的記憶體中大小的兩倍，前提是合併作業透過交易式工作負載維持在最新狀態。<br /><br /> 合併目標-CFP 會儲存合併原則所識別之 CFP (s) 的合併資料列。 一旦安裝合併之後，「合併目標」就會轉換為「使用中」狀態。<br /><br /> 合併來源-安裝合併作業之後，來源 Cfp 會標示為合併來源。 請注意，合併原則評估工具可識別多個合併，但是 CFP 只能參與一個合併作業。<br /><br /> 備份/HA 的必要項-在安裝合併且合併目標 CFP 是永久性檢查點的一部分時，合併來源 Cfp 會轉換為此狀態。 包含記憶體最佳化資料表的資料庫需要這個狀態下的 CFP 來確定其作業的正確性。  例如，若要從持久性檢查點復原來回到過去。 CFP 要等到記錄截斷點移出其交易範圍後才可標示為記憶體回收。<br /><br /> 在轉換成標記時，In-Memory OLTP 引擎不需要這些 Cfp，而且可以進行垃圾收集。 此狀態表示這些 CFP 正在等候背景執行緒將其轉換到下一個狀態，也就是「標記」。<br /><br /> 標記-這些 Cfp 正在等候 filestream 垃圾收集行程進行垃圾收集。  ([sp_filestream_force_garbage_collection &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/filestream-and-filetable-sp-filestream-force-garbage-collection.md)) |  
|lower_bound_tsn|**bigint**|檔案中包含之交易的下限。 如果 state 資料行不是 2、3 或 4 則為 Null。|  
|upper_bound_tsn|**bigint**|檔案中包含之交易的上限。 如果 state 資料行不是 2、3 或 4 則為 Null。|  
|last_backup_page_count|**int**|上一次備份所決定的邏輯頁計數。 當 state 資料行設定為 2、3、4 或 5 時適用。 如果頁面計數未知則為 NULL。|  
|delta_watermark_tsn|**int**|寫入至這個差異檔案之上一次檢查點的交易。 這是差異檔案的標準。|  
|last_checkpoint_recovery_lsn|**Nvarchar (23)**|仍然需要此檔案之上一次檢查點的復原記錄序號 (LSN)。|  
|tombstone_operation_lsn|**Nvarchar (23)**|一旦 tombstone_operation_lsn 落後記錄截斷記錄檔序號，這個檔案就會遭到刪除。|  
|logical_deletion_log_block_id|**bigint**|僅適用於狀態 5。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 `VIEW DATABASE STATE` 權限。  
  
## <a name="use-cases"></a>使用案例  
 您可以估計 In-Memory OLTP 使用的儲存體，如下所示：  
  
```  
-- total storage used by In-Memory OLTP  
SELECT SUM (file_size_in_bytes)/(1024*1024) as file_size_in_MB  
FROM sys.dm_db_xtp_checkpoint_files  
```  
  
  
若要查看依狀態和檔案類型的儲存體使用量細目，請執行下列查詢：
  
```  
SELECT state_desc  
 , file_type_desc  
 , COUNT(*) AS [count]  
 , SUM(file_size_in_bytes) / 1024 / 1024 AS [on-disk size MB]   
FROM sys.dm_db_xtp_checkpoint_files  
GROUP BY state, state_desc, file_type, file_type_desc  
ORDER BY state, file_type  
```  
  

  
## <a name="see-also"></a>另請參閱  
 [記憶體最佳化的資料表動態管理檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/memory-optimized-table-dynamic-management-views-transact-sql.md)  
  
  
