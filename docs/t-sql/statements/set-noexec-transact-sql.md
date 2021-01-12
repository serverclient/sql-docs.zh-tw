---
description: SET NOEXEC (Transact-SQL)
title: SET NOEXEC (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- NOEXEC_TSQL
- SET_NOEXEC_TSQL
- SET NOEXEC
- NOEXEC
dev_langs:
- TSQL
helpviewer_keywords:
- queries [SQL Server], compiling
- SET NOEXEC statement
- compiling queries [SQL Server]
- NOEXEC option
ms.assetid: ba56fba1-af9b-4459-b6e4-5d7e71a7630b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 411450b229fe8cb9e899fc49dd88b8cc27baa1f3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093314"
---
# <a name="set-noexec-transact-sql"></a>SET NOEXEC (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  編譯每項查詢，但不執行它。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
SET NOEXEC { ON | OFF }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註  
 當 SET NOEXEC 是 ON 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會剖析並編譯 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的每個批次，但不會加以執行。 當 SET NOEXEC 是 OFF 時，就會在編譯之後執行所有批次。  NOEXEC 支援延遲的名稱解析；如果批次中有一或多個參考物件不存在，則不會擲回任何錯誤。
  
 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中執行陳述式有兩個階段：編譯和執行。 在執行時，這項設定可用來使 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證 [!INCLUDE[tsql](../../includes/tsql-md.md)] 程式碼中的語法和物件名稱。 它也可用來偵錯通常是在較大的陳述式批次中的陳述式。  
  
 SET NOEXEC 的設定是在執行階段進行設定，而不是在剖析階段進行設定。  
  
## <a name="permissions"></a>權限  
 需要 public 角色中的成員資格。  
  
## <a name="examples"></a>範例  
 下列範例搭配有效的查詢、含無效物件名稱的查詢，以及語法不正確的查詢來使用 `NOEXEC`。  
  
```sql
USE AdventureWorks2012;  
GO  
PRINT 'Valid query';  
GO  
-- SET NOEXEC to ON.  
SET NOEXEC ON;  
GO  
-- Inner join.  
SELECT e.BusinessEntityID, e.JobTitle, v.Name  
FROM HumanResources.Employee AS e   
   INNER JOIN Purchasing.PurchaseOrderHeader AS poh  
   ON e.BusinessEntityID = poh.EmployeeID  
   INNER JOIN Purchasing.Vendor AS v  
   ON poh.VendorID = v.BusinessEntityID;  
GO  
-- SET NOEXEC to OFF.  
SET NOEXEC OFF;  
GO  
  
PRINT 'Invalid object name';  
GO  
-- SET NOEXEC to ON.  
SET NOEXEC ON;  
GO  
-- Function name uses is a reserved keyword.  
USE AdventureWorks2012;  
GO  
CREATE FUNCTION dbo.Values(@BusinessEntityID int)  
RETURNS TABLE  
AS  
RETURN (SELECT PurchaseOrderID, TotalDue  
   FROM dbo.PurchaseOrderHeader  
   WHERE VendorID = @BusinessEntityID);  
  
-- SET NOEXEC to OFF.  
SET NOEXEC OFF;  
GO  
  
PRINT 'Invalid syntax';  
GO  
-- SET NOEXEC to ON.  
SET NOEXEC ON;  
GO  
-- Built-in function incorrectly invoked.  
SELECT *  
FROM fn_helpcollations;  
-- Reset SET NOEXEC to OFF.  
SET NOEXEC OFF;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET SHOWPLAN_ALL &#40;Transact-SQL&#41;](../../t-sql/statements/set-showplan-all-transact-sql.md)   
 [SET SHOWPLAN_TEXT &#40;Transact-SQL&#41;](../../t-sql/statements/set-showplan-text-transact-sql.md)  
  
  
