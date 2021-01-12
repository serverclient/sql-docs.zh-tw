---
description: sys.dm_tran_current_snapshot (Transact-SQL)
title: sys.dm_tran_current_snapshot (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_tran_current_snapshot_TSQL
- dm_tran_current_snapshot
- dm_tran_current_snapshot_TSQL
- sys.dm_tran_current_snapshot
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_current_snapshot dynamic management view
ms.assetid: 7509d595-c0e1-4237-a5ac-b41ad934544c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: c0488d01e23a69019734f41587b4aa6bc5e8af6c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095098"
---
# <a name="sysdm_tran_current_snapshot-transact-sql"></a>sys.dm_tran_current_snapshot (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回在目前快照集交易啟動時，顯示所有使用中交易的虛擬資料表。 如果目前的交易不是快照集交易，此函數不會傳回任何資料列。 **sys.dm_tran_current_snapshot** 類似 **sys.dm_tran_transactions_snapshot**，不過 **sys.dm_tran_current_snapshot** 只會傳回目前快照集交易的使用中交易。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_tran_current_snapshot** 名稱。  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.dm_tran_current_snapshot  
```  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**transaction_sequence_num**|**bigint**|使用中交易的交易序號|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="examples"></a>範例  
 下列範例使用的測試案例中有四筆並行交易正在 ALLOW_SNAPSHOT_ISOLATION 和 READ_COMMITTED_SNAPSHOT 選項都設為 ON 的資料庫中執行，而每一筆交易都由一個交易序號 (XSN) 識別。 正在執行的交易包括：  
  
-   XSN-57 是在可序列化隔離下執行的更新作業。  
  
-   XSN-58 與 XSN-57 相同。  
  
-   XSN-59 是在快照集隔離下執行的選取作業。  
  
-   XSN-60 與 XSN-59 相同。  
  
 下列查詢是在 XSN-59 的範圍內執行。  
  
```  
SELECT   
    transaction_sequence_num  
  FROM sys.dm_tran_current_snapshot;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
transaction_sequence_num  
------------------------  
57  
58  
```  
  
 上述結果顯示當快照集交易 XSN-59 啟動時，XSN-57 和 XSN-58 都在使用中。 即使在 XSN-57 和 XSN-58 認可或回復之後，仍會產生這個相同的結果，直到快照集交易完成為止。  
  
 相同的查詢也會在 XSN-60 的範圍內執行。  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
transaction_sequence_num  
------------------------  
57  
58  
59  
```  
  
 XSN-60 的輸出包含 XSN-59 顯示的相同交易，但也包括 XSN-60 啟動時使用中的 XSN-59。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [交易相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
  
  


