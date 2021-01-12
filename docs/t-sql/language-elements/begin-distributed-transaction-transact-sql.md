---
description: BEGIN DISTRIBUTED TRANSACTION (Transact-SQL)
title: BEGIN DISTRIBUTED TRANSACTION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/29/2016
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DISTRIBUTED
- BEGIN_DISTRIBUTED_TRANSACTION_TSQL
- DISTRIBUTED_TSQL
- BEGIN DISTRIBUTED TRANSACTION
dev_langs:
- TSQL
helpviewer_keywords:
- BEGIN DISTRIBUTED TRANSACTION statement
- distributed transactions [SQL Server], starting
- MS DTC, transaction management
- distributed transactions [SQL Server], BEGIN DISTRIBUTED TRANSACTION statement
- servers [SQL Server], distributed transactions
- starting distributed transactions
- remote servers [SQL Server], distributed transactions
- starting transactions
ms.assetid: c3bc2716-39d3-4061-8c6a-8734899231ac
author: cawrites
ms.author: chadam
ms.openlocfilehash: 15f3221d2e7848a64f3ba22d618bdc54927afeba
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095986"
---
# <a name="begin-distributed-transaction-transact-sql"></a>BEGIN DISTRIBUTED TRANSACTION (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  指定啟動 [!INCLUDE[tsql](../../includes/tsql-md.md)] 分散式交易協調器 (MS DTC) 所管理的 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 分散式交易。  
    
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
BEGIN DISTRIBUTED { TRAN | TRANSACTION }   
     [ transaction_name | @tran_name_variable ]   
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *transaction_name*  
 這是在 MS DTC 公用程式內，用來追蹤分散式交易的使用者定義交易名稱。 *transaction_name* 必須符合識別碼的規則，且必須 \<= 32 個字元。  
  
 @*tran_name_variable*  
 這是一個使用者定義變數的名稱，變數中包含在 MS DTC 公用程式內，用來追蹤分散式交易的交易名稱。 這個變數必須用 **char**、**varchar**、**nchar** 或 **nvarchar** 資料類型來宣告。  
  
## <a name="remarks"></a>備註  
 執行 BEGIN DISTRIBUTED TRANSACTION 陳述式的 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體是交易發起者，它會控制交易的完成。 當發出工作階段的後續 COMMIT TRANSACTION 或 ROLLBACK TRANSACTION 陳述式時，負責控制的執行個體會要求 MS DTC 跨越所涉及的所有執行個體來管理分散式交易的完成。  
  
 交易層級快照集隔離不支援分散式交易。  
  
 在已編列到分散式交易的工作階段執行參考連結伺服器的分散式查詢，是將 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 遠端執行個體編列到分散式交易的主要方式。  
  
 例如，如果對 ServerA 發出 BEGIN DISTRIBUTED TRANSACTION，工作階段會在 ServerB 上呼叫一個預存程序，在 ServerC 上呼叫另一個預存程序。 ServerC 的預存程序會針對 ServerD 來執行分散式查詢，然後四部電腦才會涉及分散式交易。 ServerA 的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體是交易的起始控制執行個體。  
  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] 分散式交易所涉及的工作階段不會取得它們可傳給另一個工作階段的交易物件，以便將它明確加入分散式交易。 遠端伺服器編列到交易的唯一方法，是當做分散式查詢或遠端預存程序呼叫的目標。  
  
 在本機交易中執行分散式查詢時，如果目標 OLE DB 資料來源支援 ITransactionLocal，交易就會自動升級為分散式交易。 如果目標 OLE DB 資料來源不支援 ITransactionLocal，分散式查詢中就只允許唯讀作業。  
  
 已編列到分散式交易的工作階段會執行參考遠端伺服器的遠端預存程序呼叫。  
  
 **sp_configure remote proc trans** 選項會控制在本機交易內對遠端預存程序的呼叫，是否會使本機交易自動升級為 MS DTC 所管理的分散式交易。 連線層級的 SET 選項 REMOTE_PROC_TRANSACTIONS 可用來覆寫 **sp_configure remote proc trans** 所建立的執行個體預設值。當這個選項設為 ON 時，遠端預存程序呼叫會使本機交易升級為分散式交易。 建立 MS DTC 交易的連接會成為交易的發起者。 COMMIT TRANSACTION 會起始一項 MS DTC 協調認可。 如果 **sp_configure remote proc trans** 選項是 ON，本機交易中的遠端預存程序呼叫就會成為分散式交易的一部分並自動受到保護，而您不需重新撰寫應用程式，明確發出 BEGIN DISTRIBUTED TRANSACTION 來取代 BEGIN TRANSACTION。  
  
 如需有關分散式交易環境和處理序的詳細資訊，請參閱 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 分散式交易協調器文件集。  
  
## <a name="permissions"></a>權限  
 需要 public 角色中的成員資格。  
  
## <a name="examples"></a>範例  
 這個範例會從 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 本機執行個體和遠端伺服器之執行個體的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 資料庫中，刪除一個候選項。 本機和遠端資料庫都會認可或回復交易。  
  
> [!NOTE]  
>  除非 MS DTC 目前安裝在執行 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體的電腦中，否則，這個範例會產生錯誤訊息。 如需有關安裝 MS DTC 的詳細資訊，請參閱 Microsoft 分散式交易協調器文件集。  
  
```sql  
USE AdventureWorks2012;  
GO  
BEGIN DISTRIBUTED TRANSACTION;  
-- Delete candidate from local instance.  
DELETE AdventureWorks2012.HumanResources.JobCandidate  
    WHERE JobCandidateID = 13;  
-- Delete candidate from remote instance.  
DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate  
    WHERE JobCandidateID = 13;  
COMMIT TRANSACTION;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [BEGIN TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-transaction-transact-sql.md)   
 [COMMIT TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/commit-transaction-transact-sql.md)   
 [COMMIT WORK &#40;Transact-SQL&#41;](../../t-sql/language-elements/commit-work-transact-sql.md)   
 [ROLLBACK TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/rollback-transaction-transact-sql.md)   
 [ROLLBACK WORK &#40;Transact-SQL&#41;](../../t-sql/language-elements/rollback-work-transact-sql.md)   
 [SAVE TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/save-transaction-transact-sql.md)  
  
  
