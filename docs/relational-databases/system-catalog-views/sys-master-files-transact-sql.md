---
description: sys.master_files (Transact-SQL)
title: sys.master_files (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/10/2016
ms.prod: sql
ms.prod_service: database-engine, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.master_files
- master_files_TSQL
- sys.master_files_TSQL
- master_files
dev_langs:
- TSQL
helpviewer_keywords:
- sys.master_files catalog view
ms.assetid: 803b22f2-0016-436b-a561-ce6f023d6b6a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 55b5465b2c43b02247a48478272393f941a4a51d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095521"
---
# <a name="sysmaster_files-transact-sql"></a>sys.master_files (Transact-SQL)
[!INCLUDE [sql-asdbmi-pdw](../../includes/applies-to-version/sql-asdbmi-pdw.md)]

  依照 master 資料庫的儲存情況，針對資料庫的每個檔案，各包含一個資料列。 這是單一全系統的檢視。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|database_id|**int**|套用這個檔案的資料庫識別碼。 Masterdatabase_id 一律為1。|  
|file_id|**int**|資料庫內的檔案識別碼。 主要 file_id 一定是 1。|  
|file_guid|**uniqueidentifier**|檔案的唯一識別碼。<br /><br /> Null = 資料庫已從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (升級為 SQL Server 2005 及更早版本的) 有效。|  
|類型|**tinyint**|檔案類型：<br /><br /> 0 = 資料列 <br /><br /> 1 = 記錄<br /><br /> 2 = FILESTREAM<br /><br /> 3 = [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]<br /><br /> 4 = 全文檢索 (早於 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 的全文檢索目錄；已升級為 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 或更高版本或是以此版本建立的全文檢索目錄將報告檔案類型 0)。|  
|type_desc|**nvarchar(60)**|檔案類型的描述：<br /><br /> ROWS<br /><br /> 記錄<br /><br /> FILESTREAM<br /><br /> FULLTEXT (早於 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 的全文檢索目錄)。|  
|data_space_id|**int**|這個檔案所屬的資料空間識別碼。 資料空間是一個檔案群組。<br /><br /> 0 = 記錄檔。|  
|NAME|**sysname**|資料庫中之檔案的邏輯名稱。|  
|physical_name|**nvarchar(260)**|作業系統檔案名稱。|  
|state|**tinyint**|檔案狀態：<br /><br /> 0 = ONLINE <br /><br /> 1 = RESTORING<br /><br /> 2 = RECOVERING<br /><br /> 3 = RECOVERY_PENDING<br /><br /> 4 = SUSPECT<br /><br /> 5 = [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]<br /><br /> 6 = OFFLINE<br /><br /> 7 = DEFUNCT|  
|state_desc|**nvarchar(60)**|檔案狀態的描述：<br /><br /> ONLINE<br /><br /> RESTORING<br /><br /> RECOVERING<br /><br /> RECOVERY_PENDING<br /><br /> SUSPECT<br /><br /> OFFLINE<br /><br /> DEFUNCT<br /><br /> 如需詳細資訊，請參閱[檔案狀態](../../relational-databases/databases/file-states.md)。|  
|大小|**int**|目前檔案大小 (以 8 KB 頁面為單位)。 如果是資料庫快照集，size 會反映快照集可以使用的最大檔案空間。<br /><br /> 注意： FILESTREAM 容器的這個欄位會填入零。 針對 FILESTREAM 容器的實際大小，查詢 *sys.database_files* 目錄檢視。|  
|max_size|**int**|最大檔案大小 (以 8 KB 頁面為單位)：<br /><br /> 0 = 不允許任何成長。<br /><br /> -1 = 檔案會成長到磁碟已滿。<br /><br /> 268435456 = 記錄檔可以成長到最大 2 TB 的大小。<br /><br /> 注意：以無限制的記錄檔大小升級的資料庫，將會報告記錄檔大小上限-1。|  
|growth|**int**|0 = 檔案是固定大小，不會成長。<br /><br /> >0 = 檔案將會自動成長。<br /><br /> 如果 is_percent_growth = 0，成長遞增是以 8 KB 頁面來表示，會捨入到最接近的 64 KB。<br /><br /> 如果 is_percent_growth = 1，便會以整數百分比的方式來表現成長遞增。|  
|is_media_read_onlyF|**bit**|1 = 檔案在唯讀媒體中。<br /><br /> 0 = 檔案在讀寫媒體中。|  
|is_read_only|**bit**|1 = 檔案標示為唯讀。<br /><br /> 0 = 檔案標示為可讀寫。|  
|is_sparse|**bit**|1 = 檔案是疏鬆檔案。<br /><br /> 0 = 檔案不是疏鬆檔案。<br /><br /> 如需詳細資訊，請參閱[檢視資料庫快照集的疏鬆檔案大小 &#40;Transact-SQL&#41;](../../relational-databases/databases/view-the-size-of-the-sparse-file-of-a-database-snapshot-transact-sql.md)。|  
|is_percent_growth|**bit**|1 = 檔案的成長是百分比。<br /><br /> 0 = 絕對成長大小 (以頁數為單位)。|  
|is_name_reserved|**bit**|1 = 卸除的檔案名稱可以重複使用。 您必須先取出記錄備份，名稱 (name 或 physical_name) 才能供新檔案名稱重複使用。<br /><br /> 0 = 檔案名稱無法重複使用。|  
|create_lsn|**numeric(25,0)**|建立檔案的記錄序號 (LSN)。|  
|drop_lsn|**numeric(25,0)**|卸除檔案的 LSN。|  
|read_only_lsn|**numeric(25,0)**|從讀寫改成唯讀 (最近的變更) 的檔案所在之檔案群組的 LSN。|  
|read_write_lsn|**numeric(25,0)**|從唯讀改成讀寫 (最近的變更) 的檔案所在之檔案群組的 LSN。|  
|differential_base_lsn|**numeric(25,0)**|差異備份的基底。 在這個 LSN 之後變更的資料範圍會併入差異備份中。|  
|differential_base_guid|**uniqueidentifier**|差異備份基礎所在之基底備份的唯一識別碼。|  
|differential_base_time|**datetime**|對應於 differential_base_lsn 的時間。|  
|redo_start_lsn|**numeric(25,0)**|必須啟動下一次向前復原的 LSN。<br /><br /> 除非 state = RESTORING 或 state = RECOVERY_PENDING，否則，便是 NULL。|  
|redo_start_fork_guid|**uniqueidentifier**|復原分岔的唯一識別碼。 下一個還原的記錄備份之 first_fork_guid 必須符合這個值。 這代表容器目前的狀態。|  
|redo_target_lsn|**numeric(25,0)**|能夠停止這個檔案的線上向前復原的 LSN。<br /><br /> 除非 state = RESTORING 或 state = RECOVERY_PENDING，否則，便是 NULL。|  
|redo_target_fork_guid|**uniqueidentifier**|能夠復原容器的復原分岔。 與 redo_target_lsn 形成一組。|  
|backup_lsn|**numeric(25,0)**|檔案最近的資料或差異備份的 LSN。|  
|credential_id|**int**|`credential_id` `sys.credentials` 用來儲存檔案的。 例如，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Azure 虛擬機器上執行時，如果資料庫檔案儲存在 azure blob 儲存體中，則會使用儲存位置的存取認證來設定認證。|  
  
> [!NOTE]  
>  當您卸除或重建大型索引時，或卸除或截斷大型資料表時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會延遲取消配置實際的頁面及其相關聯鎖定，直到認可交易之後。 延遲的卸除作業並不會立即釋出已配置的空間。 因此，在卸除或截斷大型物件之後，sys.master_files 立即傳回的值不一定能反映實際可用的磁碟空間。  
  
## <a name="permissions"></a>權限  
 查看對應資料列時所需的最低權限為 CREATE DATABASE、ALTER ANY DATABASE 或 VIEW ANY DEFINITION。  
  
## <a name="see-also"></a>另請參閱  
 [資料庫和檔案目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/databases-and-files-catalog-views-transact-sql.md)   
 [檔案狀態](../../relational-databases/databases/file-states.md)   
 [sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)   
 [sys.database_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)   
 [資料庫檔案與檔案群組](../../relational-databases/databases/database-files-and-filegroups.md)  
  
  
