---
description: sys.dm_tran_active_snapshot_database_transactions (Transact-SQL)
title: sys.dm_tran_active_snapshot_database_transactions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_tran_active_snapshot_database_transactions_TSQL
- dm_tran_active_snapshot_database_transactions
- sys.dm_tran_active_snapshot_database_transactions
- dm_tran_active_snapshot_database_transactions_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_active_snapshot_database_transactions dynamic management view
ms.assetid: 55b83f9c-da10-4e65-9846-f4ef3c0c0f36
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 24259c4e5842243545b9fc02d07c6b7a33387289
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093979"
---
# <a name="sysdm_tran_active_snapshot_database_transactions-transact-sql"></a>sys.dm_tran_active_snapshot_database_transactions (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中，此動態管理檢視會為產生或可能存取資料列版本的所有使用中交易，傳回一個虛擬資料表。 在下列一或多個情況下，將會包含交易：  
  
-   ALLOW_SNAPSHOT_ISOLATION 及 READ_COMMITTED_SNAPSHOT 其中一個或兩個資料庫選項都設為 ON：  
  
    -   在快照隔離等級或使用資料列版本設定的讀取認可隔離等級下執行的每一筆交易都會有一個資料列。  
  
    -   導致目前資料庫建立資料行版本的每一筆交易都會有一個資料列。 例如，交易會在目前的資料庫中更新或刪除資料列，以產生資料列版本。  
  
-   引發觸發程序時，用來執行觸發程序的交易都會有一個資料列。  
  
-   當線上索引程序正在執行時，建立索引的交易都會有一個資料列。  
  
-   當 Multiple Active Results Set (MARS) 工作階段啟用之後，存取資料列版本的每一筆交易都會有一個資料列。  
  
 此動態管理檢視不包含任何系統交易。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_tran_active_snapshot_database_transactions** 名稱。  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.dm_tran_active_snapshot_database_transactions  
```  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**transaction_id**|**bigint**|指派給交易的唯一識別碼。 交易識別碼主要是用來識別鎖定作業中的交易。|  
|**transaction_sequence_num**|**bigint**|交易序號。 這是交易啟動時指派給交易的唯一序號。 不產生版本記錄且不使用快照集掃描的交易不會接收到交易序號。|  
|**commit_sequence_num**|**bigint**|指出交易完成 (確定或停止) 的序號。 如果是作用中的交易，這個值就是 NULL。|  
|**is_snapshot**|**int**|0 = 不是快照集隔離交易。<br /><br /> 1 = 是快照集隔離交易。|  
|**session_id**|**int**|啟動交易的工作階段識別碼。|  
|**first_snapshot_sequence_num**|**bigint**|產生快照集時作用中交易的最低交易序號。 執行時，快照集交易會產生當時所有作用中交易的快照集。 如果是非快照集交易，此資料行會顯示 0。|  
|**max_version_chain_traversed**|**int**|為了尋找交易一致版本而往返之版本鏈結的最大長度。|  
|**average_version_chain_traversed**|**real**|在往返的版本鏈結中資料列版本的平均數目。|  
|**elapsed_time_seconds**|**bigint**|自從交易取得其交易序號以來的經歷時間。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="remarks"></a>備註  
 **sys.dm_tran_active_snapshot_database_transactions** 報告 (XSN) 指派的交易序號的交易。 交易會在第一次存取版本存放區時指派 XSN。 在已啟用快照集隔離或使用資料列版本設定之讀取認可隔離的資料庫中，這些範例顯示將 XSN 指派給交易的時間：  
  
-   如果交易在可序列化的隔離等級下執行，將會在交易第一次執行可以建立資料列版本的陳述式 (例如 UPDATE 作業) 時指派 XSN。  
  
-   如果交易在快照集隔離下執行，將會在執行任何資料操作語言 (DML) 陳述式 (包括 SELECT 作業) 時指派 XSN。  
  
 對於在 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的執行個體中啟動的每一筆交易，會連續增加交易序號。  
  
## <a name="examples"></a>範例  
 下列範例使用的測試案例中有四筆並行交易正在 ALLOW_SNAPSHOT_ISOLATION 和 READ_COMMITTED_SNAPSHOT 選項都設為 ON 的資料庫中執行，而每一筆交易都由一個交易序號 (XSN) 識別。 正在執行的交易包括：  
  
-   XSN-57 是在可序列化隔離下執行的更新作業。  
  
-   XSN-58 與 XSN-57 相同。  
  
-   XSN-59 是在快照集隔離下執行的選取作業。  
  
-   XSN-60 與 XSN-59 相同。  
  
 執行以下查詢：  
  
```  
SELECT   
    transaction_id,  
    transaction_sequence_num,  
    commit_sequence_num,  
    is_snapshot session_id,  
    first_snapshot_sequence_num,  
    max_version_chain_traversed,  
    average_version_chain_traversed,  
    elapsed_time_seconds  
  FROM sys.dm_tran_active_snapshot_database_transactions;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
transaction_id  transaction_sequence_num  commit_sequence_num  
--------------  ------------------------  -------------------  
9295            57                        NULL  
9324            58                        NULL  
9387            59                        NULL  
9400            60                        NULL  
  
is_snapshot  session_id   first_snapshot_sequence_num  
-----------  -----------  ---------------------------  
0            54           0  
0            53           0  
1            52           57  
1            51           57  
  
max_version_chain_traversed  average_version_chain_traversed  
---------------------------  -------------------------------  
0                            0  
0                            0  
1                            1  
1                            1  
  
elapsed_time_seconds  
--------------------  
419  
397  
359  
333  
```  
  
 下列資訊會評估來自 **sys.dm_tran_active_snapshot_database_transactions** 的結果：  
  
-   XSN-57：因為此交易不是在快照集隔離下執行，所以 `is_snapshot` 值和 `first_snapshot_sequence_num` 為 `0` 。 `transaction_sequence_num` 顯示此筆交易已經指派了交易序號，因為 ALLOW_SNAPSHOT_ISOLATION 或 READ_COMMITTED_SNAPSHOT 其中一個或兩個資料庫選項為 ON。  
  
-   XSN-58：這筆交易不是在快照集隔離下執行，而且 XSN-57 的相同資訊也適用。  
  
-   XSN-59：這是在快照集隔離之下執行的第一筆使用中交易。 這筆交易將依照 `first_snapshot_sequence_num` 的指示，讀取已在 XSN-57 之前認可的資料。 此筆交易的輸出也顯示對資料列往返的最大版本鏈結是 `1`，每一個存取的資料列平均往返 `1` 個版本。 這表示 XSN-57、XSN-58 和 XSN-60 等交易並未修改資料列且已認可。  
  
-   XSN-60：這是在快照集隔離下執行的第二筆交易。 其輸出顯示的資訊與 XSN-59 相同。  
  
## <a name="see-also"></a>另請參閱  
 [SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [交易相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
  
  


