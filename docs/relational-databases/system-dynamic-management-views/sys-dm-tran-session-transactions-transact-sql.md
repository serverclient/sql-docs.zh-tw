---
description: sys.dm_tran_session_transactions (Transact-SQL)
title: sys.dm_tran_session_transactions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/30/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_tran_session_transactions
- sys.dm_tran_session_transactions
- sys.dm_tran_session_transactions_TSQL
- dm_tran_session_transactions_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_session_transactions dynamic management view
ms.assetid: c7157491-58c2-49fe-87d7-0c9723113adf
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5dcb5a90c960d616ced6852e3b42bc29f5e7e92d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101444"
---
# <a name="sysdm_tran_session_transactions-transact-sql"></a>sys.dm_tran_session_transactions (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回相關聯交易和工作階段的相互關聯資訊。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_tran_session_transactions** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|session_id|**int**|執行交易所在的工作階段識別碼。|  
|transaction_id|**bigint**|交易的識別碼。|  
|transaction_descriptor|**二元 (8)**|與用戶端驅動程式通訊時 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用的交易識別碼。|  
|enlist_count|**int**|在工作階段中處理交易的作用中要求數目。|  
|is_user_transaction|**bit**|1 = 交易由使用者要求起始。<br /><br /> 0 = 系統交易。|  
|is_local|**bit**|1 = 本機交易。<br /><br /> 0 = 分散式交易或編列的已繫結工作階段交易。|  
|is_enlisted|**bit**|1 = 編列的分散式交易。<br /><br /> 0 = 非編列的分散式交易。|  
|is_bound|**bit**|1 = 交易透過繫結工作階段而作用於工作階段中。<br /><br /> 0 = 交易透過繫結工作階段而未作用於工作階段中。|  
|open_transaction_count||每個工作階段開啟中的交易數目。|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="remarks"></a>備註  
 透過繫結工作階段和分散式交易，交易可在一個以上的工作階段中執行。 在這些情況下，sys.dm_tran_session_transactions 將針對相同的 transaction_id 顯示多個資料列，針對執行交易所在的每一個工作階段，各顯示一個資料列。  
  
 在自動認可模式中使用 Multiple Active Result Set (MARS) 來執行多項要求，就可以使單一工作階段中有一個以上的作用中交易。 在這些情況下，sys.dm_tran_session_transactions 將針對相同的 session_id 顯示多個資料列，針對在該工作階段下執行的每一項交易，各顯示一個資料列。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [交易相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
  
  


