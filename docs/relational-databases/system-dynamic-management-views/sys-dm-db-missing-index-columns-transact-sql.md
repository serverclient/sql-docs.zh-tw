---
description: sys.dm_db_missing_index_columns (Transact-SQL)
title: sys.dm_db_missing_index_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_db_missing_index_columns_TSQL
- sys.dm_db_missing_index_columns_TSQL
- sys.dm_db_missing_index_columns
- dm_db_missing_index_columns
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_missing_index_columns dynamic management function
- missing indexes feature [SQL Server], sys.dm_db_missing_index_columns dynamic management function
ms.assetid: 3b24e5ed-0c79-47e5-805c-a0902d0aeb86
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 3cbebd83641c3cbffdc273f2b09c4037ff0f0795
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099975"
---
# <a name="sysdm_db_missing_index_columns-transact-sql"></a>sys.dm_db_missing_index_columns (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回有關遺漏索引 (不包括空間索引) 之資料庫表格資料行的資訊。 **sys.dm_db_missing_index_columns** 是動態管理函數。  

## <a name="syntax"></a>語法  
  
```  
  
sys.dm_db_missing_index_columns(index_handle)  
```  
  
## <a name="arguments"></a>引數  
 *index_handle*  
 唯一識別遺漏索引的整數。 您可以從下列動態管理物件中取得：  
  
 [sys.dm_db_missing_index_details &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md)  
  
 [sys.dm_db_missing_index_groups &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql.md)  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**column_id**|**int**|資料行的識別碼。|  
|**column_name**|**sysname**|資料表資料行的名稱。|  
|**column_usage**|**Varchar (20)**|查詢使用資料行的方式。 可能的值和其描述如下：<br /><br /> 相等：資料行提供給表示相等的述詞，其格式如下： <br />                        *資料表。資料行*  = *constant_value*<br /><br /> 不等：資料行提供給表示不相等的述詞，例如，下列形式的述詞：*資料表. 資料行*  >  *constant_value*。 "=" 以外的其他任何比較運算子都可表示不相等。<br /><br /> INCLUDE：資料行不是用來評估述詞，而是用於其他原因，例如，用來涵蓋查詢。|  
  
## <a name="remarks"></a>備註  
 **sys.dm_db_missing_index_columns** 傳回的資訊會在查詢最佳化工具最佳化查詢時更新，而不會一直保存。 遺漏索引資訊只會保留到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 重新啟動為止。 如果資料庫管理員想要在伺服器回收之後保留遺漏索引資訊，應該定期製作該項資訊的備份副本。  
  
## <a name="transaction-consistency"></a>交易一致性  
 如果交易建立或卸除資料表，便會從這個動態管理物件中移除包含有關已卸除物件之遺漏索引資訊的資料列，以維持交易的一致性。  
  
## <a name="permissions"></a>權限  
 使用者必須取得 VIEW SERVER STATE 權限或隱含 VIEW SERVER STATE 權限的任何權限，才能查詢此動態管理函數。  
  
## <a name="examples"></a>範例  
 下列範例會對 `Address` 資料表執行查詢，然後使用 `sys.dm_db_missing_index_columns` 動態管理檢視執行查詢，傳回遺漏索引的資料表資料行。  
  
```  
USE AdventureWorks2012;  
GO  
SELECT City, StateProvinceID, PostalCode  
FROM Person.Address  
WHERE StateProvinceID = 9;  
GO  
SELECT mig.*, statement AS table_name,  
    column_id, column_name, column_usage  
FROM sys.dm_db_missing_index_details AS mid  
CROSS APPLY sys.dm_db_missing_index_columns (mid.index_handle)  
INNER JOIN sys.dm_db_missing_index_groups AS mig ON mig.index_handle = mid.index_handle  
ORDER BY mig.index_group_handle, mig.index_handle, column_id;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_db_missing_index_details &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md)   
 [sys.dm_db_missing_index_groups &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql.md)   
 [sys.dm_db_missing_index_group_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-transact-sql.md)  
  
  
