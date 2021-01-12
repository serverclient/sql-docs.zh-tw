---
description: sys.dm_db_missing_index_groups (Transact-SQL)
title: sys.dm_db_missing_index_groups (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_db_missing_index_groups
- sys.dm_db_missing_index_groups_TSQL
- dm_db_missing_index_groups
- dm_db_missing_index_groups_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_missing_index_groups dynamic management view
- missing indexes feature [SQL Server], sys.dm_db_missing_index_groups dynamic management view
ms.assetid: 9cc00acd-d83d-49f8-be72-5b2aebed246b
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: e5b131a14ace8281fc7201a3b01ac5f12c3c6baf
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097726"
---
# <a name="sysdm_db_missing_index_groups-transact-sql"></a>sys.dm_db_missing_index_groups (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  此 DMV 會傳回特定索引群組中遺漏之索引的相關資訊，但空間索引除外。 
  
 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，動態管理檢視不可以公開可能會影響資料庫內含項目的資訊或公開有關使用者可存取之其他資料庫的資訊。 為了避免公開此資訊，每個包含不屬於已連線租使用者之資料的資料列都會被篩選掉。  
   
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**index_group_handle**|**int**|識別遺漏索引群組。|  
|**index_handle**|**int**|識別屬於 **index_group_handle** 所指定之群組的遺漏索引。<br /><br /> 一個索引群組僅能包含一個索引。|  
  
## <a name="remarks"></a>備註  
 **sys.dm_db_missing_index_groups** 傳回的資訊會在查詢最佳化工具進行最佳化查詢時更新，而不會一直保存。 遺漏索引資訊只會保留到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 重新啟動為止。 如果資料庫管理員想要在伺服器回收之後保留遺漏索引資訊，應該定期製作該項資訊的備份副本。  
  
 輸出結果集的任何一個資料行都不是索引鍵，但這些資料行結合在一起便形成一個索引鍵。  

  >[!NOTE]
  >此 DMV 的結果集限制為600個數據列。 每個資料列都包含一個遺漏的索引。 如果您有超過600的遺漏索引，您應該解決現有的遺漏索引，以便您可以查看較新的索引。
  
## <a name="permissions"></a>權限  
 若要查詢此動態管理檢視，使用者必須取得 VIEW SERVER STATE 權限或隱含 VIEW SERVER STATE 權限的任何權限。  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_db_missing_index_columns &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-columns-transact-sql.md)   
 [sys.dm_db_missing_index_details &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md)   
 [sys.dm_db_missing_index_group_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-transact-sql.md)  
  
  
