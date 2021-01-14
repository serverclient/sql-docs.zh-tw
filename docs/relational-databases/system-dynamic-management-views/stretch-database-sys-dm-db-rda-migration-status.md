---
title: sys.dm_db_rda_migration_status (Transact-sql) |Microsoft Docs
description: 瞭解 sys.dm_db_rda_migration_status 在 SQL Server 的本機實例上，每個已啟用 Stretch 的資料表中，每個已啟用 Stretch 的資料表各有一個資料列。
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: stored-procedures
ms.topic: language-reference
f1_keywords:
- sys.dm_db_rda_migration_status
- sys.dm_db_rda_migration_status_TSQL
- dm_db_rda_migration_status
- dm_db_rda_migration_status_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_rda_migration_status dynamic management view
ms.assetid: faf3901c-a0e0-4e0c-8b1b-86d9f15f34dd
author: pmasl
ms.author: pelopes
ms.openlocfilehash: eac014075edd5663b490791f6c49fc0b865afd2a
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171630"
---
# <a name="stretch-database---sysdm_db_rda_migration_status"></a>Stretch Database-sys.dm_db_rda_migration_status
[!INCLUDE [sqlserver2016](../../includes/applies-to-version/sqlserver2016.md)]

  針對本機實例上每個已啟用 Stretch 的資料表，各包含一個資料列 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 批次是由其開始時間和結束時間來識別。  
  
 **sys.dm_db_rda_migration_status** 的範圍設定為目前的資料庫內容。 確定您是在您想要查看其遷移狀態的 Stretch enable 資料表的資料庫內容中。  
  
 在中 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] ， **sys.dm_db_rda_migration_status** 的輸出限制為200個數據列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**table_id**|**int**|從中遷移資料列的資料表識別碼。|  
|**database_id**|**int**|從中遷移資料列之資料庫的識別碼。|  
|**migrated_rows**|**bigint**|此批次中遷移的資料列數目。|  
|**start_time_utc**|**datetime**|批次開始的 UTC 時間。|  
|**end_time_utc**|**datetime**|批次完成的 UTC 時間。|  
|**error_number**|**int**|如果批次失敗，則為所發生錯誤的錯誤號碼。否則為 null。|  
|**error_severity**|**int**|如果批次失敗，則為所發生錯誤的嚴重性;否則為 null。|  
|**error_state**|**int**|如果批次失敗，則為所發生錯誤的狀態。否則為 null。<br /><br /> **Error_state** 指出發生錯誤的條件或位置。|  
  
## <a name="see-also"></a>另請參閱  
 [Stretch Database](../../sql-server/stretch-database/stretch-database.md)  
  
  
