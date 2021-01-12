---
description: SQL Server 公用程式陳述式 - GO
title: SQL Server 公用程式陳述式 - GO | Microsoft Docs
ms.custom: ''
ms.date: 07/27/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- GO
- GO_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- batches [SQL Server], ending
- ending batches [SQL Server]
- GO command
ms.assetid: b2ca6791-3a07-4209-ba8e-2248a92dd738
author: cawrites
ms.author: chadam
ms.openlocfilehash: f09f2dcdd59b0e0431dbdd92a4023a2e3296149f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100918"
---
# <a name="sql-server-utilities-statements---go"></a>SQL Server 公用程式陳述式 - GO
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 提供不是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，但 **sqlcmd** 和 **osql** 公用程式以及 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 程式碼編輯器都能辨識的命令。 這些命令可用來簡化批次和指令碼的可讀性與執行。  
  
  GO 會向 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 公用程式發出 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式批次結束的信號。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
GO [count]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *計數*  
 這是正整數。 在 GO 之前的批次將會執行指定的次數。  
  
## <a name="remarks"></a>備註  
 GO 不是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式；它是 **sqlcmd** 和 **osql** 公用程式以及 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 程式碼編輯器都能辨識的命令。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 公用程式會將 GO 解譯成應該將目前的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式批次傳送給 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的信號。 目前的陳述式批次由在上一個 GO 之後輸入的所有陳述式組成；如果是第一個 GO，便是從特定工作階段或指令碼開始之後輸入的所有陳述式組成。  
  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式不能和 GO 命令佔用同一行。 不過，這一行可包含註解。  
  
 使用者必須遵照批次的規則。 例如，在批次內第一個陳述式之後，執行預存程序都必須包括 EXECUTE 關鍵字。 本機 (使用者自訂) 變數的範圍只限於批次，在 GO 命令之後，便不能參考它。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @MyMsg VARCHAR(50)  
SELECT @MyMsg = 'Hello, World.'  
GO -- @MyMsg is not valid after this GO ends the batch.  
  
-- Yields an error because @MyMsg not declared in this batch.  
PRINT @MyMsg  
GO  
  
SELECT @@VERSION;  
-- Yields an error: Must be EXEC sp_who if not first statement in   
-- batch.  
sp_who  
GO  
```  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 應用程式可以將多個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式傳給 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，以利用批次方式來執行它們。 之後，會將批次中的這些陳述式編譯成單一執行計畫。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 公用程式內執行特定陳述式的程式設計人員，或建立要在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 公用程式中執行的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 陳述式指令碼之程式設計人員，都利用 GO 來作為批次結束的信號。  
  
 如果以 ODBC 或 OLE DB API 為基礎的應用程式試圖執行 GO 命令，就會收到語法錯誤。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 公用程式永遠不會將 GO 命令傳給伺服器。  
  
 請勿在 GO 之後以分號做為陳述式結束字元。
 
```sql
-- Yields an error because ; is not permitted after GO  
SELECT @@VERSION;  
GO;  
```
  
## <a name="permissions"></a>權限  
 GO 是不需要任何權限的公用程式命令。 任何使用者都可以執行它。    
  
## <a name="examples"></a>範例  
 下列範例會建立兩個批次。 第一個批次只包含用來設定資料庫內容的 `USE AdventureWorks2012` 陳述式。 其餘陳述式使用本機變數。 因此，所有本機變數宣告都必須分組在單一批次中。 方式是將 `GO` 命令放在參考變數的最後一個陳述式之後。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @NmbrPeople INT  
SELECT @NmbrPeople = COUNT(*)  
FROM Person.Person;  
PRINT 'The number of people as of ' +  
      CAST(GETDATE() AS CHAR(20)) + ' is ' +  
      CAST(@NmbrPeople AS CHAR(10));  
GO  
```  
  
 下列範例會以批次執行陳述式兩次。  
  
```sql  
SELECT DB_NAME();  
SELECT USER_NAME();  
GO 2  
```  
  
  
