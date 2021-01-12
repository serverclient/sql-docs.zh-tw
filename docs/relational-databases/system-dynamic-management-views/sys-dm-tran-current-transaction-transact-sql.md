---
description: sys.dm_tran_current_transaction (Transact-SQL)
title: sys.dm_tran_current_transaction (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_tran_current_transaction
- sys.dm_tran_current_transaction_TSQL
- dm_tran_current_transaction_TSQL
- dm_tran_current_transaction
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_current_transaction dynamic management view
ms.assetid: 75d5697d-b390-4963-99b8-fa0b4244a40c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0b4dbad2e87915eec9295b2374b8f162709f22d7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099728"
---
# <a name="sysdm_tran_current_transaction-transact-sql"></a>sys.dm_tran_current_transaction (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回一個顯示目前工作階段交易之狀態資訊的資料列。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_tran_current_transaction** 名稱。  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.dm_tran_current_transaction  
```  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**transaction_id**|**bigint**|目前快照集的交易識別碼。|  
|**transaction_sequence_num**|**bigint**|產生記錄版本之交易的序號。|  
|**transaction_is_snapshot**|**bit**|快照集隔離狀態。 如果交易是在快照集隔離下啟動，這個值就是 1。 否則，這個值便為 0。|  
|**first_snapshot_sequence_num**|**bigint**|產生快照集時作用中交易的最低交易序號。 執行時，快照集交易會產生當時所有作用中交易的快照集。 如果是非快照集交易，此資料行會顯示 0。|  
|**last_transaction_sequence_num**|**bigint**|全域序號。 這個值代表系統所產生的最後一個交易序號。|  
|**first_useful_sequence_num**|**bigint**|全域序號。 這個值代表最舊的交易序號，而這種交易具有必須保留在版本存放區的資料列版本。 您可以移除先前交易建立的資料列版本。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
  
## <a name="examples"></a>範例  
 下列範例使用的測試案例中有四筆並行交易正在 ALLOW_SNAPSHOT_ISOLATION 和 READ_COMMITTED_SNAPSHOT 選項都設為 ON 的資料庫中執行，而每一筆交易都由一個交易序號 (XSN) 識別。 正在執行的交易包括：  
  
-   XSN-57 是在可序列化隔離下執行的更新作業。  
  
-   XSN-58 與 XSN-57 相同。  
  
-   XSN-59 是在快照集隔離下執行的選取作業。  
  
-   XSN-60 與 XSN-59 相同。  
  
 下列查詢是在每項交易的範圍內執行。  
  
```  
SELECT   
    transaction_id  
   ,transaction_sequence_num  
   ,transaction_is_snapshot  
   ,first_snapshot_sequence_num  
   ,last_transaction_sequence_num  
   ,first_useful_sequence_num  
  FROM sys.dm_tran_current_transaction;  
```  
  
 以下為 XSN-59 的結果。  
  
```  
transaction_id       transaction_sequence_num transaction_is_snapshot  
-------------------- ------------------------ -----------------------  
9387                 59                       1                         
  
first_snapshot_sequence_num last_transaction_sequence_num  
--------------------------- -----------------------------  
57                               61                        
  
first_useful_sequence_num  
-------------------------  
57  
```  
  
 此輸出顯示 XSN-59 是快照集交易，其使用 XSN-57 作為 XSN-59 啟動時使用中的第一項交易。 這表示 XSN-59 會讀取交易序號低於 XSN-57 之交易所認可的資料。  
  
 以下為 XSN-57 的結果。  
  
```  
transaction_id       transaction_sequence_num transaction_is_snapshot  
-------------------- ------------------------ -----------------------  
9295                 57                       0  
  
first_snapshot_sequence_num last_transaction_sequence_num  
--------------------------- -----------------------------  
NULL                        61  
  
first_useful_sequence_num  
-------------------------  
57  
```  
  
 由於 XSN-57 並不是快照集交易，因此 `first_snapshot_sequence_num` 是 `NULL`。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [交易相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
  
  


