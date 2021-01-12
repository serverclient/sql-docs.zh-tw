---
description: sys.dm_os_volume_stats (Transact-SQL)
title: sys.dm_os_volume_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 09/03/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_os_volume_stats_TSQL
- dm_os_volume_stats
- sys.dm_os_volume_stats
- sys.dm_os_volume_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_volume_stats dynamic management function
ms.assetid: fa1c58ad-8487-42ad-956c-983f2229025f
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 32deca4f06709806d90ee7d25e25105f191ad23e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098816"
---
# <a name="sysdm_os_volume_stats-transact-sql"></a>sys.dm_os_volume_stats (Transact-SQL)
[!INCLUDE[tsql-appliesto-2008R2SP1-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-2008R2sp1-xxxx-xxxx-xxx-md.md)]

  傳回儲存在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中之指定資料庫和檔案所在之作業系統磁碟區 (目錄) 的相關資訊。 使用此動態管理函數可檢查實體磁碟機的屬性，或傳回目錄可用空間的資訊。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
sys.dm_os_volume_stats (database_id, file_id)  
```  
  
##  <a name="arguments"></a><a name="Arguments"></a> 引數  
 *database_id*  
 資料庫的識別碼。 *database_id* 為沒有預設值的 **int**。 不能是 NULL。  
  
 *file_id*  
 檔案識別碼。 *file_id* 是 **int**，沒有預設值。 不能是 NULL。  
  
## <a name="table-returned"></a>傳回的資料表  
  
||||  
|-|-|-|  
|**資料行**|**Data type**|**描述**|  
|**database_id**|**int**|資料庫的識別碼。 不可以是 null。|  
|**file_id**|**int**|檔案識別碼。 不可以是 null。|  
|**volume_mount_point**|**nvarchar(512)**|磁碟區根目錄所在的掛接點。 可以傳回空字串。 在 Linux 作業系統上傳回 null。|  
|**volume_id**|**nvarchar(512)**|作業系統磁碟區識別碼。 可以傳回空字串。 在 Linux 作業系統上傳回 null。|  
|**logical_volume_name**|**nvarchar(512)**|邏輯磁碟區名稱。 可以傳回空字串。 在 Linux 作業系統上傳回 null。|  
|**file_system_type**|**nvarchar(512)**|作業系統磁碟區的類型 (例如，NTFS、FAT、RAW)。 可以傳回空字串。 在 Linux 作業系統上傳回 null。|  
|**total_bytes**|**bigint**|磁碟區的總大小 (以位元組為單位)。 不可以是 null。|  
|**available_bytes**|**bigint**|磁碟區上的可用空間。 不可以是 null。|  
|**supports_compression**|**tinyint**|表示磁碟區是否支援作業系統壓縮。 在 Windows 上不可以是 null，並且在 Linux 作業系統上傳回 null。|  
|**supports_alternate_streams**|**tinyint**|表示磁碟區是否支援替代資料流。 在 Windows 上不可以是 null，並且在 Linux 作業系統上傳回 null。|  
|**supports_sparse_files**|**tinyint**|表示磁碟區是否支援疏鬆檔案。  在 Windows 上不可以是 null，並且在 Linux 作業系統上傳回 null。|  
|**is_read_only**|**tinyint**|表示磁碟區目前是否標示成唯讀。 不可以是 null。|  
|**is_compressed**|**tinyint**|表示這個磁碟區目前是否已經壓縮。 在 Windows 上不可以是 null，並且在 Linux 作業系統上傳回 null。|  
|**incurs_seek_penalty**|**tinyint**|指出支援此磁片區的儲存體類型。 可能的值包括：<br /><br />0：在此磁片區上沒有搜尋損失，通常是在存放裝置是 PMM 或 SSD 時<br /><br />1：在此磁片區上搜尋損失，通常是在存放裝置為 HDD 時<br /><br />2：當磁片區位於 UNC 路徑或掛接的共用上時，無法判斷存放裝置類型<br /><br />Null：無法在 Linux 作業系統上判斷儲存體類型<br /><br />**適用于：** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]從) 開始 ([!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)]|  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要 `VIEW SERVER STATE` 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-return-total-space-and-available-space-for-all-database-files"></a>A. 傳回所有資料庫檔案的總空間和可用空間  
 下列範例會傳回 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中所有資料庫檔案的總空間和可用空間 (以位元組為單位)。  
  
```sql  
SELECT f.database_id, f.file_id, volume_mount_point, total_bytes, available_bytes  
FROM sys.master_files AS f  
CROSS APPLY sys.dm_os_volume_stats(f.database_id, f.file_id);  
```  
  
### <a name="b-return-total-space-and-available-space-for-the-current-database"></a>B. 傳回目前資料庫的總空間和可用空間  
 下列範例會傳回目前資料庫中資料庫檔案的總空間和可用空間 (以位元組為單位)。  
  
```sql  
SELECT database_id, f.file_id, volume_mount_point, total_bytes, available_bytes  
FROM sys.database_files AS f  
CROSS APPLY sys.dm_os_volume_stats(DB_ID(f.name), f.file_id);  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.master_files &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)   
 [sys.database_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)  
  
  
