---
description: sys.sysdatabases (Transact-SQL)
title: sys.sys資料庫 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysdatabases_TSQL
- sys.sysdatabases_TSQL
- sys.sysdatabases
- sysdatabases
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sysdatabases compatibility view
- sysdatabases system table
ms.assetid: 60a93880-62f1-4eda-a886-f046706ba90c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2a5ed7033605db917f972e83bcf6c014c49f52bd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099137"
---
# <a name="syssysdatabases-transact-sql"></a>sys.sysdatabases (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  針對實例中的每個資料庫，各包含一個資料列 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]第一次安裝時， **sysdatabases** 會包含 **master**、 **model**、 **msdb** 和 **tempdb** 資料庫的專案。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|資料庫名稱|  
|**dbid**|**smallint**|資料庫識別碼|  
|**希**|**varbinary(85)**|資料庫建立者的系統識別碼。|  
|**mode**|**smallint**|用於內部，在建立資料庫時鎖定它。|  
|**status**|**int**|狀態位，其中一些可以使用 [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md) 來設定，如下所述：<br /><br /> 1 = **autoclose** (ALTER DATABASE) <br /><br /> 4 = **select into/bulkcopy** (使用 SET RECOVERY) 的 ALTER database<br /><br /> 8 = **trunc 在 chkpt** (使用 SET RECOVERY) 的 ALTER DATABASE<br /><br /> 16 = 損毀 **頁偵測** (ALTER DATABASE) <br /><br /> 32 = **正在載入**<br /><br /> 64 =**預先** 復原<br /><br /> 128 = 正在 **復原**<br /><br /> 256 = **未復原**<br /><br /> 512 = **離線** (更改資料庫) <br /><br /> 1024 = **唯讀** (ALTER DATABASE) <br /><br /> 2048 = **dbo 僅使用** (使用 SET RESTRICTED_USER 的 ALTER DATABASE) <br /><br /> 4096 = **單一使用者** (更改資料庫) <br /><br /> 32768 = **緊急模式**<br /><br /> 65536 = **CHECKSUM** (ALTER DATABASE) <br /><br /> 4194304 = 自動 **壓縮** (ALTER DATABASE) <br /><br /> 1073741824 = **完全關機**<br /><br /> 多位元可以同時設為 ON。|  
|**status2**|**int**|16384 = **ANSI null 預設** (ALTER DATABASE) <br /><br /> 65536 = **concat null 產生 null** (ALTER DATABASE) <br /><br /> 131072 = **遞迴觸發** 程式 (ALTER DATABASE) <br /><br /> 1048576 = **預設為本機資料指標** (ALTER DATABASE) <br /><br /> 8388608 = (ALTER DATABASE) 的 **引號識別碼**<br /><br /> 33554432 = commit (ALTER DATABASE) **上的資料指標關閉**<br /><br /> 67108864 = **ANSI nulls** (ALTER DATABASE) <br /><br /> 268435456 = **ANSI 警告** (ALTER DATABASE) <br /><br /> 536870912 = **啟用全文檢索** (使用 **sp_fulltext_database** 設定) |  
|**crdate**|**datetime**|建立日期。|  
|**保留**|**datetime**|保留供未來使用。|  
|**類別**|**int**|包含複寫所用資訊的點陣圖：<br /><br /> 1 = 針對快照集或異動複寫而發行。<br /><br /> 2 = 訂閱快照式或交易式發行集。<br /><br /> 4 = 針對合併式複寫而發行。<br /><br /> 8 = 訂閱合併式發行集。<br /><br /> 16 = 散發資料庫。|  
|**cmptlevel**|**tinyint**|資料庫的相容性層級。 如需詳細資訊，請參閱 [ALTER DATABASE 相容性層級 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。|  
|**filename**|**nvarchar(260)**|資料庫主要檔案的作業系統路徑和名稱。<br /><br /> **dbcreator**、 **SYSADMIN**、具有 CREATE ANY database 許可權的資料庫擁有者，或是具有下列任何許可權的授與，都可以看到 **檔案名**： ALTER any DATABASE、CREATE any database、VIEW any DEFINITION。 若要傳回路徑和檔案名，請查詢 [sys.sys](../../relational-databases/system-compatibility-views/sys-sysfiles-transact-sql.md) 檔相容性檢視，或 [sys.database_files](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md) 視圖。|  
|**version**|**smallint**|建立資料庫所用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 程式碼的內部版本號碼。 [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
  
## <a name="see-also"></a>另請參閱  
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
