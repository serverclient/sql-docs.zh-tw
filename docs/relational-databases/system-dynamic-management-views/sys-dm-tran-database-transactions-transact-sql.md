---
description: sys.dm_tran_database_transactions (Transact-SQL)
title: sys.dm_tran_database_transactions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/09/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_tran_database_transactions
- sys.dm_tran_database_transactions_TSQL
- dm_tran_database_transactions_TSQL
- sys.dm_tran_database_transactions
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_database_transactions dynamic management view
ms.assetid: 82a44295-4cbc-4a5b-891a-8ebaf307b8f5
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a9932a3f57c02307699ff75d53efaf81f25594e3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097487"
---
# <a name="sysdm_tran_database_transactions-transact-sql"></a>sys.dm_tran_database_transactions (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回有關資料庫層級之交易的資訊。  
  
> [!NOTE]  
>  若要從或呼叫此 DMV [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_tran_database_transactions** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|transaction_id|**bigint**|執行個體層級 (而非資料庫層級) 的交易識別碼。 它只有在一個執行個體的所有資料庫才是唯一的，在所有的伺服器執行個體則不是。|  
|database_id|**int**|與交易相關聯的資料庫識別碼。|  
|database_transaction_begin_time|**datetime**|資料庫變成與交易有關的時間。 尤其，它是資料庫中針對交易的第一筆記錄的時間。|  
|database_transaction_type|**int**|1 = 讀取/寫入交易<br /><br /> 2 = 唯讀交易<br /><br /> 3 = 系統交易|  
|database_transaction_state|**int**|1 = 交易未初始化。<br /><br /> 3 = 交易已初始化，但未產生任何記錄。<br /><br /> 4 = 交易已產生記錄。<br /><br /> 5 = 已準備交易。<br /><br /> 10 = 已認可交易。<br /><br /> 11 = 已回復交易。<br /><br /> 12 = 正在認可交易。  (正在產生記錄檔記錄，但尚未具體化或保存。 ) |  
|database_transaction_status|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|database_transaction_status2|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|database_transaction_log_record_count|**bigint**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 在資料庫中針對交易產生的記錄數。|  
|database_transaction_replicate_record_count|**int**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 複寫的交易在資料庫中產生的記錄檔記錄數目。|  
|database_transaction_log_bytes_used|**bigint**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 到目前為止在資料庫記錄中針對交易所使用的位元組數。|  
|database_transaction_log_bytes_reserved|**bigint**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 在資料庫記錄中針對交易所使用而保留的位元組數。|  
|database_transaction_log_bytes_used_system|**int**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 到目前為止在資料庫記錄中針對代表交易之系統交易所使用的位元組數。|  
|database_transaction_log_bytes_reserved_system|**int**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 在資料庫記錄中針對代表交易之系統交易所使用而保留的位元組數。|  
|database_transaction_begin_lsn|**numeric(25,0)**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 資料庫記錄中交易之開始記錄的記錄序號 (LSN)。|  
|database_transaction_last_lsn|**numeric(25,0)**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 資料庫記錄中交易之最近記錄的 LSN。|  
|database_transaction_most_recent_savepoint_lsn|**numeric(25,0)**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 資料庫記錄中交易之最近儲存點的 LSN。|  
|database_transaction_commit_lsn|**numeric(25,0)**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 資料庫記錄中交易之認可記錄的 LSN。|  
|database_transaction_last_rollback_lsn|**numeric(25,0)**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 最近回復的 LSN。 如果未進行復原，則此值為 MaxLSN。|  
|database_transaction_next_undo_lsn|**numeric(25,0)**|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 要恢復之下一筆記錄的 LSN。|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="see-also"></a>另請參閱  
 [sys.dm_tran_active_transactions &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql.md)   
 [sys.dm_tran_session_transactions &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql.md)   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [交易相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
  
  


