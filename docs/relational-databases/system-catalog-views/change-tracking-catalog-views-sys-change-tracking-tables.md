---
description: 變更追蹤目錄檢視-sys.change_tracking_tables
title: sys.change_tracking_tables (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/08/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- change_tracking_tables_TSQL
- sys.change_tracking_tables
- change_tracking_tables
- sys.change_tracking_tables_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- change tracking [SQL Server], sys.change_tracking_tables
- sys.change_tracking_tables
ms.assetid: 97ec69b6-0d49-4d98-82f0-d3e77ba1ad2b
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 8a4f0a4ef53b1225efee4ed1c382a3cf012bac02
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093231"
---
# <a name="change-tracking-catalog-views---syschange_tracking_tables"></a>變更追蹤目錄檢視-sys.change_tracking_tables
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  針對已啟用變更追蹤之目前資料庫中的每一個資料表，各傳回一個資料列。  
   
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|擁有變更日誌之資料表的識別碼。 即使變更追蹤目前為關閉狀態，資料表也可以擁有變更日誌。<br /><br /> 資料表識別碼在資料庫中是唯一的。|  
|is_track_columns_updated_on|**bit**|變更追蹤在資料表上的目前狀態：<br /><br /> 0 = OFF<br /><br /> 1 = ON|  
|begin_version|**bigint**|針對資料表開始變更追蹤時的資料庫版本。 這個版本通常會指出啟用變更追蹤的時間，但如果資料表遭到截斷，就會重設這個值。|  
|cleanup_version|**bigint**|清除可能已經移除變更追蹤資訊多早的版本。|  
|min_valid_version|**bigint**|可用於資料表之變更追蹤資訊的最大有效版本。<br /><br /> 從與此資料列相關聯之資料表取得變更時，last_sync_version 的值必須大於或等於此資料行所報告的版本。 如需詳細資訊，請參閱 [CHANGE_TRACKING_MIN_VALID_VERSION &#40;transact-sql&#41;](../../relational-databases/system-functions/change-tracking-min-valid-version-transact-sql.md)。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [CHANGE_TRACKING_MIN_VALID_VERSION &#40;Transact-SQL&#41;](../../relational-databases/system-functions/change-tracking-min-valid-version-transact-sql.md)   
 [變更追蹤 &#40;Transact-sql&#41;的目錄檢視 ](./catalog-views-transact-sql.md)   
 [追蹤資料變更 &#40;SQL Server&#41;](../../relational-databases/track-changes/track-data-changes-sql-server.md)  
  
