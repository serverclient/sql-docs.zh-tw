---
description: SET REMOTE_PROC_TRANSACTIONS (Transact-SQL)
title: SET REMOTE_PROC_TRANSACTIONS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- REMOTE_PROC_TRANSACTIONS_TSQL
- SET REMOTE_PROC_TRANSACTIONS
- REMOTE_PROC_TRANSACTIONS
- SET_REMOTE_PROC_TRANSACTIONS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- remote stored procedures [SQL Server]
- SET REMOTE_PROC_TRANSACTIONS statement
- distributed transactions [SQL Server], starting
- REMOTE_PROC_TRANSACTIONS option
- active transactions
ms.assetid: 4d284ae9-3f5f-465a-b0dd-1328a4832a03
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: ecfcda70eb37c4b7b10db2ac49a02996140060da
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100680"
---
# <a name="set-remote_proc_transactions-transact-sql"></a>SET REMOTE_PROC_TRANSACTIONS (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  指定當本機交易在使用中，執行遠端預存程序會啟動一項 [!INCLUDE[tsql](../../includes/tsql-md.md)] 分散式交易協調器 (MS DTC) 所管理的 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 分散式交易。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepNextDontUse](../../includes/ssnotedepnextdontuse-md.md)]這個選項專用來相容於使用遠端預存程序的舊版應用程式。 請改用參考連結伺服器的分散式查詢，不再發出遠端預存程序呼叫。 這些作業是使用 [sp_addlinkedserver](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md) 來定義。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
SET REMOTE_PROC_TRANSACTIONS { ON | OFF }   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 ON | OFF  
 如果設為 ON，當從本機交易執行遠端預存程序時，會啟動 [!INCLUDE[tsql](../../includes/tsql-md.md)] 分散式交易。 如果設為 OFF，當從本機交易呼叫遠端預存程序時，並不會啟動 [!INCLUDE[tsql](../../includes/tsql-md.md)] 分散式交易。  
  
## <a name="remarks"></a>備註  
 當 REMOTE_PROC_TRANSACTIONS 是 ON 時，呼叫預存程序會啟動一項分散式交易，且會利用 MS DTC 來編列這項交易。 發出遠端預存程序呼叫的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體是交易發起者，它會控制交易的完成。 當發出連接的後續 COMMIT TRANSACTION 或 ROLLBACK TRANSACTION 陳述式時，負責控制的執行個體會要求 MS DTC 跨越所涉及的各部電腦來管理分散式交易的完成。  
  
 在啟動 [!INCLUDE[tsql](../../includes/tsql-md.md)] 分散式交易之後，會向已定義為遠端伺服器的其他 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體發出遠端預存程序呼叫。 所有遠端伺服器都會編列在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 分散式交易中，MS DTC 會確保已針對每部遠端伺服器來完成交易。  
  
 REMOTE_PROC_TRANSACTIONS 是一個連線層級的設定，可用來覆寫執行個體層級的 **sp_configure remote proc trans** 選項。  
  
 當 REMOTE_PROC_TRANSACTIONS 是 OFF 時，遠端預存程序呼叫不會成為本機交易的一部分。 當遠端預存程序完成時，會認可或回復它所進行的修正。 呼叫遠端預存程序之連接所發出的後續 COMMIT TRANSACTION 或 ROLLBACK TRANSACTION 陳述式，對於程序所完成的處理，不會有任何作用。  
  
 REMOTE_PROC_TRANSACTIONS 選項是一個相容性選項，它只會影響對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體 (藉由 **sp_addserver** 定義為遠端伺服器) 所發出的遠端預存程序呼叫。 這個選項不適合在執行個體 (藉由 **sp_addlinkedserver** 定義為連結伺服器) 上執行預存程序的分散式查詢。  
  
 SET REMOTE_PROC_TRANSACTIONS 的設定是在執行階段進行設定，而不是在剖析階段進行設定。  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。  
  
## <a name="see-also"></a>另請參閱  
 [BEGIN DISTRIBUTED TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-distributed-transaction-transact-sql.md)   
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)  
  
  
