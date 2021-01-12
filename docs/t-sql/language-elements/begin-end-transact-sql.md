---
description: BEGIN...END (Transact-SQL)
title: BEGIN...END (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- BEGIN
- BEGIN_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- enclosing statements [SQL Server]
- BEGIN statement
- control-of-flow language [SQL Server], BEGIN...END statement
- BEGIN...END keyword
- grouping statements, BEGIN...END statement
- executing Transact-SQL statements together [SQL Server]
- statements [SQL Server], grouping
ms.assetid: fc2c7f76-f1f9-4f91-beef-bc8ef0da2feb
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1dc4990bc19a23dc0759adf64bfa3c22cae324d4
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100426"
---
# <a name="beginend-transact-sql"></a>BEGIN...END (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  含括一系列的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，以便執行一組 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 BEGIN 和 END 是流程控制語言關鍵字。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
BEGIN  
    { sql_statement | statement_block }   
END  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 { *sql_statement* | *statement_block* }  
 這是藉由使用陳述式區塊所定義的任何有效 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式或陳述式分組。  
  
## <a name="remarks"></a>備註  
 BEGIN...END 區塊可以有巢狀結構。  
  
 雖然 BEGIN...END 區塊中所有的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式都是有效的陳述式，但某些 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式不應在同一批次或陳述式區塊中分成一組。  
  
## <a name="examples"></a>範例  
 在下列範例中，`BEGIN` 和 `END` 會定義一系列同時執行的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 如果沒有 `BEGIN...END` 區塊，這兩個 `ROLLBACK TRANSACTION` 陳述式都要執行，而且都會傳回這兩個 `PRINT` 訊息。  
  
```sql
USE AdventureWorks2012
GO  
BEGIN TRANSACTION
GO  
IF @@TRANCOUNT = 0  
BEGIN  
    SELECT FirstName, MiddleName   
    FROM Person.Person WHERE LastName = 'Adams'
    ROLLBACK TRANSACTION
    PRINT N'Rolling back the transaction two times would cause an error.'
END
ROLLBACK TRANSACTION
PRINT N'Rolled back the transaction.'
GO  
/*  
Rolled back the transaction.  
*/  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 在下列範例中，`BEGIN` 和 `END` 會定義一系列同時執行的 [!INCLUDE[DWsql](../../includes/dwsql-md.md)] 陳述式。 如果未包含 `BEGIN...END` 區塊，則下列範例將處於持續不斷的迴圈狀態。  
  
```sql
-- Uses AdventureWorks  

DECLARE @Iteration Integer = 0  
WHILE @Iteration <10  
BEGIN  
    SELECT FirstName, MiddleName   
    FROM dbo.DimCustomer WHERE LastName = 'Adams'
    SET @Iteration += 1  
END
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/alter-trigger-transact-sql.md)   
 [流程控制語言 &#40;Transact-SQL&#41;](~/t-sql/language-elements/control-of-flow.md)   
 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)   
 [END &#40;BEGIN...END&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/end-begin-end-transact-sql.md)
