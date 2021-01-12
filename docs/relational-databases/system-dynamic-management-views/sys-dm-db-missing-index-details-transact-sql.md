---
description: sys.dm_db_missing_index_details (Transact-SQL)
title: sys.dm_db_missing_index_details (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/20/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_db_missing_index_details
- dm_db_missing_index_details
- sys.dm_db_missing_index_details_TSQL
- dm_db_missing_index_details_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- missing indexes feature [SQL Server], sys.dm_db_missing_index_details dynamic management view
- sys.dm_db_missing_index_details dynamic management view
ms.assetid: ced484ae-7c17-4613-a3f9-6d8aba65a110
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: fdce3b3269d0b60c20d82064955c83416c4254eb
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095162"
---
# <a name="sysdm_db_missing_index_details-transact-sql"></a>sys.dm_db_missing_index_details (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回有關遺漏索引 (不包含空間索引) 的詳細資訊。  
  
 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，動態管理檢視不可以公開可能會影響資料庫內含項目的資訊或公開有關使用者可存取之其他資料庫的資訊。 為了避免公開此資訊，每個包含不屬於已連線租使用者之資料的資料列都會被篩選掉。  

  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**index_handle**|**int**|識別特定的遺漏索引。 此識別碼在伺服器中是唯一的。 **index_handle** 是此資料表的索引鍵。|  
|**database_id**|**smallint**|識別具有遺漏索引之資料表的資料庫。|  
|object_id|**int**|識別遺漏索引所在的資料表。|  
|**equality_columns**|**nvarchar(4000)**|作為相等述詞之資料行的逗號分隔清單，述詞格式如下：<br /><br /> *資料表。資料行*  =*constant_value*|  
|**inequality_columns**|**nvarchar(4000)**|作為不相等述詞之資料行的逗號分隔清單，例如，格式如下的述詞：<br /><br /> *資料表。資料行*  > *constant_value*<br /><br /> "=" 以外的其他任何比較運算子都可表示不相等。|  
|**included_columns**|**nvarchar(4000)**|可作為查詢所需涵蓋資料行之資料行的逗號分隔清單。 如需涵蓋或內含資料行的詳細資訊，請參閱 [使用內含資料行建立索引](../../relational-databases/indexes/create-indexes-with-included-columns.md)。<br /><br /> 針對記憶體優化的索引 (雜湊和記憶體優化非叢集) ，請忽略 **included_columns**。 每個記憶體最佳化的索引都包含資料表的所有資料行。|  
|**陳述式**|**nvarchar(4000)**|遺漏索引所在之資料表的名稱。|  
  
## <a name="remarks"></a>備註  
 **sys.dm_db_missing_index_details** 傳回的資訊會在查詢最佳化工具進行最佳化查詢時更新，而不會一直保存。 遺漏索引資訊只會保留到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 重新啟動為止。 如果資料庫管理員想要在伺服器回收之後保留遺漏索引資訊，應該定期製作該項資訊的備份副本。  
  
 若要判斷特定遺漏索引屬於哪些遺漏索引群組，您可以依據 **index_handle** 資料行，將該遺漏索引與 **sys.dm_db_missing_index_details** 等聯結，以便查詢 **sys.dm_db_missing_index_groups** 動態管理檢視。  

  >[!NOTE]
  >此 DMV 的結果集限制為600個數據列。 每個資料列都包含一個遺漏的索引。 如果您有超過600的遺漏索引，您應該解決現有的遺漏索引，以便您可以查看較新的索引。 
  
## <a name="using-missing-index-information-in-create-index-statements"></a>在 CREATE INDEX 陳述式中使用遺漏索引資訊  
 若要將 **sys.dm_db_missing_index_details** 傳回的資訊，轉換成記憶體優化和磁片型索引的 CREATE index 語句，應該將相等資料行放在不相等資料行之前，而且它們應該要建立索引的索引鍵。 您應該使用 INCLUDE 子句，將內含資料行加入 CREATE INDEX 陳述式中。 若要決定相等資料行的有效次序，請依據其選擇性排列這些資料行：將選擇性最高的資料行列在最前面 (資料行清單的最左邊)。  
  
 如需記憶體優化索引的詳細資訊，請參閱 [Memory-Optimized 資料表的索引](../../relational-databases/in-memory-oltp/indexes-for-memory-optimized-tables.md)。  
  
## <a name="transaction-consistency"></a>交易一致性  
 如果交易建立或卸除資料表，便會從這個動態管理物件中移除包含有關已卸除物件之遺漏索引資訊的資料列，以維持交易的一致性。  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="see-also"></a>另請參閱  
 [sys.dm_db_missing_index_columns &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-columns-transact-sql.md)   
 [sys.dm_db_missing_index_groups &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql.md)   
 [sys.dm_db_missing_index_group_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-transact-sql.md)  
  
  
