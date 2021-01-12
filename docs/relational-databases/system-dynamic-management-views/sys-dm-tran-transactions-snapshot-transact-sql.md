---
description: sys.dm_tran_transactions_snapshot (Transact-SQL)
title: sys.dm_tran_transactions_snapshot (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/30/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_tran_transactions_snapshot
- dm_tran_transactions_snapshot
- sys.dm_tran_transactions_snapshot_TSQL
- dm_tran_transactions_snapshot_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_transactions_snapshot dynamic management view
ms.assetid: 03f64883-07ad-4092-8be0-31973348c647
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 8c6596b92ecd000b0742886ab6565caf2521f701
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101416"
---
# <a name="sysdm_tran_transactions_snapshot-transact-sql"></a>sys.dm_tran_transactions_snapshot (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  針對每個快照集交易啟動時作用中的交易，傳回 **sequence_number** 的虛擬資料表。 此檢視傳回的資訊可以協助您執行下列工作：  
  
-   尋找目前作用中快照集交易的數目。  
  
-   識別特定快照集交易所忽略的資料修改。 快照集交易啟動時作用中交易進行的所有資料修改 (包括該交易認可之後所做的修改) 都會被快照集交易忽略。  
  
 例如，請考慮 **sys.dm_tran_transactions_snapshot** 的下列輸出：  
  
```  
transaction_sequence_num snapshot_id snapshot_sequence_num  
------------------------ ----------- ---------------------  
59                       0           57  
59                       0           58  
60                       0           57  
60                       0           58  
60                       0           59  
60                       3           57  
60                       3           58  
60                       3           59  
60                       3           60  
```  
  
  資料行識別目前快照集交易的交易序號 (XSN)。 輸出中顯示兩個序號：`59` 和 `60`。 `snapshot_sequence_num` 資料行識別每個快照集交易啟動時作用中交易的交易序號。  
  
 輸出中顯示快照集交易 XSN-59 啟動時，有兩個作用中交易 XSN-57 和 XSN-58 正在執行。 如果 XSN-57 或 XSN-58 進行資料修改，XSN-59 會忽略這些變更，並使用資料列版本設定來維護資料庫的交易一致檢視。  
  
 快照集交易 XSN-60 會忽略 XSN-57 和 XSN-58 以及 XSN 59 所做的資料修改。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**transaction_sequence_num**|**bigint**|快照集交易的交易序號 (XSN)。|  
|**snapshot_id**|**int**|在使用資料列版本設定之讀取認可之下啟動的每一個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的快照集識別碼。 這個值可用來產生資料庫的交易一致檢視，該資料庫支援在使用資料列版本設定之讀取認可快照集下面執行的每一項查詢。|  
|**snapshot_sequence_num**|**bigint**|當快照集交易啟動時作用中交易的交易序號。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
  
## <a name="remarks"></a>備註  
 當快照集交易啟動時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會記錄當時作用中的所有交易。 **sys.dm_tran_transactions_snapshot** 報告目前所有作用中快照集交易的這種資訊。  
  
 每一項交易都由交易開始時指派的交易序號所識別。 交易是在 BEGIN TRANSACTION 或 BEGIN WORK 陳述式執行時啟動。 不過，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會隨著在 BEGIN TRANSACTION 或 BEGIN WORK 陳述式之後存取資料的第一個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式執行時指派交易序號。 交易序號以 1 遞增。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [交易相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
  
  

