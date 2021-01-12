---
description: sys.dm_db_log_stats (Transact-SQL)
title: sys.dm_db_log_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/17/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_db_log_stats_TSQL
- sys.dm_db_log_stats
- sys.dm_db_log_stats_TSQL
- dm_db_log_stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_log_stats dynamic management function
ms.assetid: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=sql-server-2017||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ddfd608b3f2d5bb5c4c259dfb0ec822f245f72b3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096558"
---
# <a name="sysdm_db_log_stats-transact-sql"></a>sys.dm_db_log_stats (Transact-SQL)   
[!INCLUDE[tsql-appliesto-2016sp2-asdb-xxxx-xxx-md](../../includes/tsql-appliesto-2016sp2-asdb-xxxx-xxx-md.md)]

傳回資料庫之交易記錄檔的摘要層級屬性和資訊。 使用此資訊來監視和診斷交易記錄健全狀況。   
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
 sys.dm_db_log_stats ( database_id )
```  
  
## <a name="arguments"></a>引數  

*database_id* |Null | **預設值**

資料庫的識別碼。 `database_id` 是 `int` \(英文\)。 有效的輸入為資料庫、或的識別碼號碼 `NULL` `DEFAULT` 。 預設為 `NULL`。 `NULL` 和在 `DEFAULT` 目前資料庫的內容中是相等的值。  
可以指定內建函數 [DB_ID](../../t-sql/functions/db-id-transact-sql.md)。 使用 `DB_ID` 但未指定資料庫名稱時，目前資料庫的相容性層級必須為90或更高。

  
## <a name="tables-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|database_id    |**int**    |資料庫識別碼 |  
|recovery_model |**nvarchar(60)**   |   資料庫的復原模式。 可能的值包括： <br /> 簡單<br /> BULK_LOGGED <br /> FULL |  
|log_min_lsn    |**nvarchar(24)**   |   在交易記錄中， [ (LSN) ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#Logical_Arch) 的目前起始記錄序號。|  
|log_end_lsn    |**nvarchar(24)**   |   記錄序號 (交易記錄中最後一個記錄檔記錄的[LSN) ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#Logical_Arch) 。|  
|current_vlf_sequence_number    |**bigint** |   目前的 [虛擬記錄檔 (](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#physical_arch) 在執行時 VLF) 序號。|  
|current_vlf_size_mb    |**float**  |   目前的 [虛擬記錄檔 (VLF) ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#physical_arch) 大小（以 MB 為單位）。|   
|total_vlf_count    |**bigint** |   交易記錄中 [ (vlf) 的虛擬記錄 ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#physical_arch) 檔總數。 |  
|total_log_size_mb  |**float**  |   交易記錄大小總計（以 MB 為單位）。 |  
|active_vlf_count   |**bigint** |   在交易記錄中 [ (vlf) ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#physical_arch) 的使用中虛擬記錄檔總數。|  
|active_log_size_mb |**float**  |   使用中交易記錄大小總計（以 MB 為單位）。|  
|log_truncation_holdup_reason   |**nvarchar(60)**   |   記錄截斷扣留原因。 值與  `log_reuse_wait_desc` 的資料行相同 `sys.databases` 。   (如需這些值的詳細說明，請參閱 [交易記錄](../../relational-databases/logs/the-transaction-log-sql-server.md)) 。 <br />可能的值包括： <br />NOTHING<br />CHECKPOINT<br />LOG_BACKUP<br />ACTIVE_BACKUP_OR_RESTORE<br />ACTIVE_TRANSACTION<br />DATABASE_MIRRORING<br />複寫<br />DATABASE_SNAPSHOT_CREATION<br />LOG_SCAN<br />AVAILABILITY_REPLICA<br />OLDEST_PAGE<br />XTP_CHECKPOINT<br />其他暫時性 |  
|log_backup_time    |**datetime**   |   前次交易歷史記錄備份時間。|   
|log_backup_lsn |**nvarchar(24)**   |   最後一個交易記錄備份 [記錄序號 (LSN) ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#Logical_Arch)。|   
|log_since_last_log_backup_mb   |**float**  |   記錄檔大小（以 MB 為單位），自前次交易歷史記錄備份 [記錄序號 (LSN) ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#Logical_Arch)。|  
|log_checkpoint_lsn |**nvarchar(24)**   |   最後一個檢查點 [記錄序號 (LSN) ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#Logical_Arch)。|  
|log_since_last_checkpoint_mb   |**float**  |   記錄檔大小（以 MB 為單位），自上次檢查點 [記錄序號 (LSN) ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#Logical_Arch)。|  
|log_recovery_lsn   |**nvarchar(24)**   |   資料庫 [ (LSN) 的修復記錄序號 ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#Logical_Arch) 。 如果在 `log_recovery_lsn` 檢查點 lsn 之前發生， `log_recovery_lsn` 則為最舊的使用中交易 lsn，否則 `log_recovery_lsn` 為檢查點 lsn。|  
|log_recovery_size_mb   |**float**  |   記錄檔大小（以 MB 為單位），因為記錄檔修復 [記錄序號 (LSN) ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#Logical_Arch)。|  
|recovery_vlf_count |**bigint** |   如果容錯移轉或伺服器重新開機，則為要復原 [ (vlf) 的虛擬記錄 ](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#physical_arch) 檔總數。 |  


## <a name="remarks"></a>備註
`sys.dm_db_log_stats`針對參與可用性群組做為次要複本的資料庫執行時，只會傳回上面所述的欄位子集。  目前， `database_id` `recovery_model` `log_backup_time` 針對次要資料庫執行時，只會傳回、和。   

## <a name="permissions"></a>權限  
需要 `VIEW DATABASE STATE` 資料庫中的許可權。   
  
## <a name="examples"></a>範例  

### <a name="a-determining-databases-in-a-ssnoversion-instance-with-high-number-of-vlfs"></a>A. 在具有大量 Vlf 的實例中判斷資料庫 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]   
下列查詢會傳回記錄檔中具有 100 Vlf 以上的資料庫。 大量的 Vlf 可能會影響資料庫的啟動、還原和復原時間。

```sql  
SELECT name AS 'Database Name', total_vlf_count AS 'VLF count' 
FROM sys.databases AS s
CROSS APPLY sys.dm_db_log_stats(s.database_id) 
WHERE total_vlf_count  > 100;
```   

### <a name="b-determining-databases-in-a-ssnoversion-instance-with-transaction-log-backups-older-than-4-hours"></a>B. [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]使用超過4小時的交易記錄備份來判斷實例中的資料庫   
下列查詢會判斷實例中資料庫的最後一個記錄備份時間。

```sql  
SELECT name AS 'Database Name', log_backup_time AS 'last log backup time' 
FROM sys.databases AS s
CROSS APPLY sys.dm_db_log_stats(s.database_id); 
```

## <a name="see-also"></a>另請參閱  
[動態管理檢視與函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
[資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)   
[sys.dm_db_log_space_usage &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql.md)   
[sys.dm_db_log_info &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-log-info-transact-sql.md)    
  
