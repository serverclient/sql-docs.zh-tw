---
description: sys.dm_tran_active_transactions (Transact-SQL)
title: sys.dm_tran_active_transactions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/30/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_tran_active_transactions
- sys.dm_tran_active_transactions_TSQL
- dm_tran_active_transactions_TSQL
- dm_tran_active_transactions
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_active_transactions dynamic management view
ms.assetid: 154ad6ae-5455-4ed2-b014-e443abe2c6ee
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 91d4f683782b4b309d21b0604f19ba10ea34a416
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101436"
---
# <a name="sysdm_tran_active_transactions-transact-sql"></a>sys.dm_tran_active_transactions (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回有關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之交易的資訊。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_tran_active_transactions** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|transaction_id|**bigint**|執行個體層級 (而非資料庫層級) 的交易識別碼。 它只有在一個執行個體的所有資料庫才是唯一的，在所有的伺服器執行個體則不是。|  
|name|**nvarchar(32)**|交易名稱。 如果交易被標示出來，而且標示的名稱取代了交易名稱，這個值便會被覆寫。|  
|transaction_begin_time|**datetime**|交易啟動的時間。|  
|transaction_type|**int**|交易的類型。<br /><br /> 1 = 讀取/寫入交易<br /><br /> 2 = 唯讀交易<br /><br /> 3 = 系統交易<br /><br /> 4 = 分散式交易|  
|transaction_uow|**uniqueidentifier**|分散式交易的交易工作單位 (UOW) 識別碼。 MS DTC 是以 UOW 識別碼來使用分散式交易。|  
|transaction_state|**int**|0 = 交易尚未完全初始化。<br /><br /> 1 = 交易已經初始化，但尚未啟動。<br /><br /> 2 = 交易在作用中。<br /><br /> 3 = 交易已經結束。 它只用於唯讀交易。<br /><br /> 4 = 認可處理序已經在分散式交易上起始。 它只用於分散式交易。 分散式交易仍在作用中，但無法再做進一步的處理。<br /><br /> 5 = 交易是在已準備的狀態，正在等候解析。<br /><br /> 6 = 已認可交易。<br /><br /> 7 = 正在回復交易。<br /><br /> 8 = 已回復交易。|  
|transaction_status|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|transaction_status2|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|dtc_state|**int**|**適用** 于： [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 透過 [目前版本](/previous-versions/azure/ee336279(v=azure.100)))  (初始版本。<br /><br /> 1 = ACTIVE<br /><br /> 2 = PREPARED<br /><br /> 3 = COMMITTED<br /><br /> 4 = ABORTED<br /><br /> 5 = RECOVERED|  
|dtc_status|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|dtc_isolation_level|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|filestream_transaction_id|**varbinary(128)**|**適用** 于： [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 透過 [目前版本](/previous-versions/azure/ee336279(v=azure.100)))  (初始版本。<br /><br /> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_tran_session_transactions &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql.md)   
 [sys.dm_tran_database_transactions &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql.md)   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [交易相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
  
