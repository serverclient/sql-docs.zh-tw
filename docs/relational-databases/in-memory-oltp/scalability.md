---
title: 延展性 | Microsoft Docs
description: 了解針對 SQL Server 中經記憶體最佳化的資料表磁碟上儲存體之可擴縮性的增強功能，例如使用多個執行緒來保存資料表。
ms.custom: ''
ms.date: 08/27/2015
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: a4891c57-56bb-49f4-9bb5-f11b745279e5
author: MightyPen
ms.author: genemi
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 3e72e67bf033ecef94b318d69b31f33604572cf8
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172060"
---
# <a name="scalability"></a>延展性
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 包含記憶體最佳化資料表磁碟上儲存體的延展性增強功能。 

## <a name="multiple-threads-to-persist-memory-optimized-tables"></a>保存記憶體最佳化資料表的多個執行緒  
  
[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 中具有單一離線檢查點執行緒，可掃描交易記錄中是否有記憶體最佳化資料表變更，並將其保存在檢查點檔案 (例如資料和差異檔案) 中。 若電腦有較多的核心數，單一離線檢查點執行緒可能會落後。  
  
從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，有多個並行執行緒負責保存記憶體最佳化資料表的變更。  
  
## <a name="multi-threaded-recovery"></a>多執行緒復原
在舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]中，作為部分復原作業的記錄套用為單一執行緒。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，記錄套用為多執行緒。  
  
## <a name="merge-operation"></a>MERGE 作業  
MERGE 作業現在為多執行緒。  
   
> [!NOTE]
> 已停用手動合併，因為多執行緒合併預期會跟上負載。 

## <a name="dynamic-management-views"></a>動態管理檢視  
DMV [sys.dm_db_xtp_checkpoint_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-checkpoint-stats-transact-sql.md) 和 [sys.dm_db_xtp_checkpoint_files &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-xtp-checkpoint-files-transact-sql.md) 已大幅變更。  

## <a name="storage-management"></a>存放管理
記憶體內部 OLTP 引擎會根據 FILESTREAM 繼續使用記憶體最佳化檔案群組，但是會從 FILESTREAM 中分離檔案群組中的個別檔案。 記憶體內部 OLTP 引擎會完全管理這些檔案 (例如，用於建立、卸除和記憶體回收)。 

> [!NOTE]
> 不支援 [DBCC SHRINKFILE &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-shrinkfile-transact-sql.md)。  
  
## <a name="see-also"></a>另請參閱   
[建立及管理記憶體最佳化物件的儲存體](../../relational-databases/in-memory-oltp/creating-and-managing-storage-for-memory-optimized-objects.md)     
[Database Files and Filegroups](../../relational-databases/databases/database-files-and-filegroups.md)    
[ALTER DATABASE 檔案及檔案群組選項 (Transact-SQL)](../../t-sql/statements/alter-database-transact-sql-file-and-filegroup-options.md)    
