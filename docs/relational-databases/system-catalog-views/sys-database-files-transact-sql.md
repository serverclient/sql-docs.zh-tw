---
description: sys.database_files (Transact-SQL)
title: sys.database_files (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 09/19/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.database_files
- sys.database_files_TSQL
- database_files
- database_files_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.database_files catalog view
ms.assetid: 0f5b0aac-c17d-4e99-b8f7-d04efc9edf44
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 603b9d6fd9a2f15a4592a251dd2b9df6ddb44bfa
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093206"
---
# <a name="sysdatabase_files-transact-sql"></a>sys.database_files (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  依照資料庫本身的儲存情況，針對資料庫的每個檔案，各包含一個資料列。 這是針對個別資料庫的檢視。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**file_id**|**int**|資料庫內的檔案識別碼。|  
|**file_guid**|**uniqueidentifier**|檔案的 GUID。<br /><br /> Null = 資料庫已從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (升級為 SQL Server 2005 及更早版本的) 有效。|  
|**type**|**tinyint**|檔案類型：<br/><br /> 0 = 資料列<br /><br/> 1 = 記錄<br/><br /> 2 = FILESTREAM<br /><br /> 3 = [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]<br /><br /> 4 = 全文檢索|  
|**type_desc**|**nvarchar(60)**|檔案類型的描述：<br /><br /> ROWS <br /><br /> 記錄<br /><br /> FILESTREAM<br /><br /> FULLTEXT|  
|**data_space_id**|**int**|此值可能是 0 或大於 0。 值為 0 代表資料庫記錄檔，而大於 0 的值則代表儲存這個資料檔之檔案群組的識別碼。|  
|**name**|**sysname**|資料庫中之檔案的邏輯名稱。|  
|**physical_name**|**nvarchar(260)**|作業系統檔案名稱。 如果資料庫是由 AlwaysOn [可讀取的次要複本所裝載](../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)， **physical_name** 會指出主要複本資料庫的檔案位置。 如需可讀取次要資料庫的正確檔案位置，請查詢 [sys.sysaltfiles](../../relational-databases/system-compatibility-views/sys-sysaltfiles-transact-sql.md)。|  
|**state**|**tinyint**|檔案狀態：<br /><br /> 0 = ONLINE <br /><br /> 1 = RESTORING<br /><br /> 2 = RECOVERING<br /><br /> 3 = RECOVERY_PENDING<br /><br /> 4 = SUSPECT<br /><br /> 5 = [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]<br /><br /> 6 = OFFLINE<br /><br /> 7 = DEFUNCT|  
|**state_desc**|**nvarchar(60)**|檔案狀態的描述：<br /><br /> ONLINE<br /><br /> RESTORING<br /><br /> RECOVERING<br /><br /> RECOVERY_PENDING<br /><br /> SUSPECT<br /><br /> OFFLINE<br /><br /> DEFUNCT<br /><br /> 如需詳細資訊，請參閱[檔案狀態](../../relational-databases/databases/file-states.md)。|  
|**size**|**int**|目前的檔案大小 (以 8 KB 頁數為單位)。<br /><br /> 0 = 不適用<br /><br /> 如果是資料庫快照集，size 會反映快照集可以使用的最大檔案空間。<br /><br /> 針對 FILESTREAM 檔案群組容器，大小會反映容器目前使用的大小。|  
|**max_size**|**int**|最大檔案大小 (以 8 KB 頁面為單位)：<br /><br /> 0 = 不允許任何成長。<br /><br /> -1 = 檔案會成長到磁碟已滿。<br /><br /> 268435456 = 記錄檔可以成長到最大 2 TB 的大小。<br /><br /> 如果是 FILESTREAM 檔案群組容器，max_size 反映容器的大小上限。<br /><br /> 請注意，以無限制的記錄檔大小升級的資料庫，會報告記錄檔大小上限-1。|  
|**增長**|**int**|0 = 檔案是固定大小，不會成長。<br /><br /> >0 = 檔案將會自動成長。<br /><br /> 如果 is_percent_growth = 0，成長遞增是以 8 KB 頁面來表示，會捨入到最接近的 64 KB。<br /><br /> 如果 is_percent_growth = 1，便會以整數百分比的方式來表現成長遞增。|  
|**is_media_read_only**|**bit**|1 = 檔案在唯讀媒體中。<br /><br /> 0 = 檔案在讀寫媒體中。|  
|**is_read_only**|**bit**|1 = 檔案標示為唯讀。<br /><br /> 0 = 檔案標示為可讀寫。|  
|**is_sparse**|**bit**|1 = 檔案是疏鬆檔案。<br /><br /> 0 = 檔案不是疏鬆檔案。<br /><br /> 如需詳細資訊，請參閱[檢視資料庫快照集的疏鬆檔案大小 &#40;Transact-SQL&#41;](../../relational-databases/databases/view-the-size-of-the-sparse-file-of-a-database-snapshot-transact-sql.md)。|  
|**is_percent_growth**|**bit**|1 = 檔案的成長是百分比。<br /><br /> 0 = 絕對成長大小 (以頁數為單位)。|  
|**is_name_reserved**|**bit**|1 = 只有在下一次記錄備份之後，卸除的檔案名稱 (name 或 physical_name) 才可以重複使用。 當檔案從資料庫卸除時，邏輯名稱會保持在保留狀態，直到下一次記錄備份。 這個資料行只有在完整復原模式和大量記錄復原模式下才會顯出其重要性。|  
|**create_lsn**|**numeric(25,0)**|建立檔案的記錄序號 (LSN)。|  
|**drop_lsn**|**numeric(25,0)**|卸除檔案的 LSN。<br /><br /> 0 = 無法重複使用的檔案名稱。|  
|**read_only_lsn**|**numeric(25,0)**|從讀寫改成唯讀 (最近的變更) 的檔案所在之檔案群組的 LSN。|  
|**read_write_lsn**|**numeric(25,0)**|從唯讀改成讀寫 (最近的變更) 的檔案所在之檔案群組的 LSN。|  
|**differential_base_lsn**|**numeric(25,0)**|差異備份的基底。 在這個 LSN 之後變更的資料範圍會併入差異備份中。|  
|**differential_base_guid**|**uniqueidentifier**|差異備份基礎所在之基底備份的唯一識別碼。|  
|**differential_base_time**|**datetime**|對應於 differential_base_lsn 的時間。|  
|**redo_start_lsn**|**numeric(25,0)**|必須啟動下一次向前復原的 LSN。<br /><br /> 除非 state = RESTORING 或 state = RECOVERY_PENDING，否則，便是 NULL。|  
|**redo_start_fork_guid**|**uniqueidentifier**|復原分岔的唯一識別碼。 下一個還原的記錄備份之 first_fork_guid 必須符合這個值。 這代表檔案目前的狀態。|  
|**redo_target_lsn**|**numeric(25,0)**|能夠停止這個檔案的線上向前復原的 LSN。<br /><br /> 除非 state = RESTORING 或 state = RECOVERY_PENDING，否則，便是 NULL。|  
|**redo_target_fork_guid**|**uniqueidentifier**|能夠復原檔案的復原分岔。 與 redo_target_lsn 形成一組。|  
|**backup_lsn**|**numeric(25,0)**|檔案最近的資料或差異備份的 LSN。|  
  
> [!NOTE]  
>  當您卸除或重建大型索引時，或卸除或截斷大型資料表時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會延遲取消配置實際的頁面及其相關聯鎖定，直到認可交易之後。 延遲的卸除作業並不會立即釋出已配置的空間。 因此，在卸除或截斷大型物件之後，sys.database_files 傳回的值不一定能反映實際可用的磁碟空間。  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  

## <a name="examples"></a>範例  
下列語句會傳回每個資料庫檔案的名稱、檔案大小和空白空間數量。

```
SELECT name, size/128.0 FileSizeInMB,
size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0 
   AS EmptySpaceInMB
FROM sys.database_files;
```
如需使用時的詳細資訊 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] ，請參閱 SQL 客戶諮詢小組 blog 上的 [判斷 Azure SQL Database V12 的資料庫大小](/archive/blogs/sqlcat/determining-database-size-in-azure-sql-database-v12) 。
  
## <a name="see-also"></a>另請參閱  
 [資料庫和檔案目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/databases-and-files-catalog-views-transact-sql.md)   
 [檔案狀態](../../relational-databases/databases/file-states.md)   
 [sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)   
 [sys.master_files &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)   
 [Database Files and Filegroups](../../relational-databases/databases/database-files-and-filegroups.md)   
 [sys.data_spaces &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)  
  
