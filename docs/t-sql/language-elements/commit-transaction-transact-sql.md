---
description: COMMIT TRANSACTION (Transact-SQL)
title: COMMIT TRANSACTION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/09/2016
ms.prod: sql
ms.prod_service: sql-data-warehouse, database-engine, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- COMMIT
- COMMIT TRANSACTION
- COMMIT_TSQL
- COMMIT_TRANSACTION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- ending transactions [SQL Server]
- user-defined transactions [SQL Server]
- committed transactions
- transactions [SQL Server], ending
- marking end of transactions [SQL Server]
- implicit transactions
- distributed transactions [SQL Server], committed
- transactions [SQL Server], committed
- COMMIT TRANSACTION statement
- rolling back transactions, COMMIT TRANSACTION
ms.assetid: f8fe26a9-7911-497e-b348-4e69c7435dc1
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: a322d2c707c819bb1d209965fef1aa5f747d0fac
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092201"
---
# <a name="commit-transaction-transact-sql"></a>COMMIT TRANSACTION (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  標示順利完成的隱含或明確的交易結束。 如果 @@TRANCOUNT 是 1，COMMIT TRANSACTION 會將自交易開始後所有資料修改變成資料庫的永久部分，釋出交易的資源後將 @@TRANCOUNT 減量為 0。 當 @@TRANCOUNT 大於 1 時，COMMIT TRANSACTION 只會將 @@TRANCOUNT 減量 1，且交易維持使用中的狀態。  
  
 ![文章連結圖示](../../database-engine/configure-windows/media/topic-link.gif "文章連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Applies to SQL Server (starting with 2008) and Azure SQL Database
  
COMMIT [ { TRAN | TRANSACTION }  [ transaction_name | @tran_name_variable ] ] [ WITH ( DELAYED_DURABILITY = { OFF | ON } ) ]  
[ ; ]  
```  
 
```syntaxsql
-- Applies to Azure Synapse Analytics and Parallel Data Warehouse Database
  
COMMIT [ TRAN | TRANSACTION ] 
[ ; ]  
``` 
 
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *transaction_name*  
 **適用於：** SQL Server 和 Azure SQL Database
 
 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會忽略這個項目。 *transaction_name* 會指定先前的 BEGIN TRANSACTION 所指派的交易名稱。 *transaction_name* 必須符合識別碼規則，但不能超過 32 個字元。 *transaction_name* 向程式設計人員指出與 COMMIT TRANSACTION 建立關聯的巢狀 BEGIN TRANSACTION。  
  
 *\@tran_name_variable*  
 **適用於：** SQL Server 和 Azure SQL Database  
 
這是包含有效交易名稱之使用者定義變數的名稱。 此變數必須以 char、varchar、nchar 或 nvarchar 資料類型來宣告。 如果超出 32 個字元傳給變數，只會使用 32 個字元；其餘字元會截斷。  
  
 DELAYED_DURABILITY  
 **適用於：** SQL Server 和 Azure SQL Database   

 要求這筆交易應延遲持久性認可的選項。 如果資料庫已經使用 `DELAYED_DURABILITY = DISABLED` 或 `DELAYED_DURABILITY = FORCED` 更改，則會忽略要求。 如需詳細資訊，請參閱[控制交易持久性](../../relational-databases/logs/control-transaction-durability.md)。  
  
## <a name="remarks"></a>備註  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] 程式設計人員只負責在交易所參考的全部資料都邏輯正確時，才發出 COMMIT TRANSACTION。  
  
 如果認可的交易是一項 [!INCLUDE[tsql](../../includes/tsql-md.md)] 分散式交易，COMMIT TRANSACTION 會觸發 MS DTC 利用兩階段認可通訊協定來認可交易所涉及的所有伺服器。 當本機交易跨越相同 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體的兩個或更多資料庫時，執行個體會利用內部的兩階段認可，認可與交易有關的所有資料庫。  
  
 用於巢狀交易時，認可內部交易並不會釋出資源，或永久修改它們。 只有在認可外部交易時，才會使資料修改永久化及釋出資源。 當 @@TRANCOUNT 大於 1 時，每個發出的 COMMIT TRANSACTION 都只使 @@TRANCOUNT 減量 1。 當最後 @@TRANCOUNT 減量到 0 時，便會認可整個外部交易。 由於[!INCLUDE[ssDE](../../includes/ssde-md.md)]會忽略 *transaction_name*，因此，當有未完成的內部交易時，發出參考外部交易名稱的 COMMIT TRANSACTION 只會使 @@TRANCOUNT 減量 1。  
  
 當 @@TRANCOUNT 是 0 時，發出 COMMIT TRANSACTION 會產生錯誤；沒有對應的 BEGIN TRANSACTION。  
  
 您不能在發出 COMMIT TRANSACTION 陳述式之後回復交易，因為資料修改已成為資料庫的永久部分。  
  
 只有在陳述式開始時交易計數是 0 時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 才會在單一陳述式內遞增交易。  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。  
  
## <a name="examples"></a>範例  
  
### <a name="a-committing-a-transaction"></a>A. 認可交易  
**適用於：** SQL Server、Azure SQL Database、[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]   

下列範例會刪除作業候選項。 此範例使用 AdventureWorks。 
  
```sql   
BEGIN TRANSACTION;   
DELETE FROM HumanResources.JobCandidate  
    WHERE JobCandidateID = 13;   
COMMIT TRANSACTION;   
```  
  
### <a name="b-committing-a-nested-transaction"></a>B. 認可巢狀交易  
**適用於：** SQL Server 和 Azure SQL Database    

此範例會建立一份資料表，並產生三個層級的巢狀交易，再認可巢狀交易。 雖然每個 `COMMIT TRANSACTION` 陳述式都有 *transaction_name* 參數，但是 `COMMIT TRANSACTION` 與 `BEGIN TRANSACTION` 陳述式之間並沒有關聯性。 *transaction_name* 參數協助程式設計人員確認已撰寫正確認可數目的程式碼，將 `@@TRANCOUNT` 減量到 0，以便認可外部交易。 
  
```sql   
IF OBJECT_ID(N'TestTran',N'U') IS NOT NULL  
    DROP TABLE TestTran;  
GO  
CREATE TABLE TestTran (Cola INT PRIMARY KEY, Colb CHAR(3));  
GO  
-- This statement sets @@TRANCOUNT to 1.  
BEGIN TRANSACTION OuterTran;  
  
PRINT N'Transaction count after BEGIN OuterTran = '  
    + CAST(@@TRANCOUNT AS NVARCHAR(10));  
 
INSERT INTO TestTran VALUES (1, 'aaa');  
 
-- This statement sets @@TRANCOUNT to 2.  
BEGIN TRANSACTION Inner1;  
 
PRINT N'Transaction count after BEGIN Inner1 = '  
    + CAST(@@TRANCOUNT AS NVARCHAR(10));  
  
INSERT INTO TestTran VALUES (2, 'bbb');  
  
-- This statement sets @@TRANCOUNT to 3.  
BEGIN TRANSACTION Inner2;  
  
PRINT N'Transaction count after BEGIN Inner2 = '  
    + CAST(@@TRANCOUNT AS NVARCHAR(10));  
  
INSERT INTO TestTran VALUES (3, 'ccc');  
  
-- This statement decrements @@TRANCOUNT to 2.  
-- Nothing is committed.  
COMMIT TRANSACTION Inner2;  
 
PRINT N'Transaction count after COMMIT Inner2 = '  
    + CAST(@@TRANCOUNT AS NVARCHAR(10));  
 
-- This statement decrements @@TRANCOUNT to 1.  
-- Nothing is committed.  
COMMIT TRANSACTION Inner1;  
 
PRINT N'Transaction count after COMMIT Inner1 = '  
    + CAST(@@TRANCOUNT AS NVARCHAR(10));  
  
-- This statement decrements @@TRANCOUNT to 0 and  
-- commits outer transaction OuterTran.  
COMMIT TRANSACTION OuterTran;  
  
PRINT N'Transaction count after COMMIT OuterTran = '  
    + CAST(@@TRANCOUNT AS NVARCHAR(10));  
```  
  
## <a name="see-also"></a>另請參閱  
 [BEGIN DISTRIBUTED TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-distributed-transaction-transact-sql.md)   
 [BEGIN TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/begin-transaction-transact-sql.md)   
 [COMMIT WORK &#40;Transact-SQL&#41;](../../t-sql/language-elements/commit-work-transact-sql.md)   
 [ROLLBACK TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/rollback-transaction-transact-sql.md)   
 [ROLLBACK WORK &#40;Transact-SQL&#41;](../../t-sql/language-elements/rollback-work-transact-sql.md)   
 [SAVE TRANSACTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/save-transaction-transact-sql.md)   
 [@@TRANCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/trancount-transact-sql.md)  
  
