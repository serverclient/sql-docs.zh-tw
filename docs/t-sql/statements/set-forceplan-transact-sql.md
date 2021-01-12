---
description: SET FORCEPLAN (Transact-SQL)
title: SET FORCEPLAN (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SET_FORCEPLAN_TSQL
- SET FORCEPLAN
- FORCEPLAN
- FORCEPLAN_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- joins [SQL Server], overriding query optimizer process
- FORCEPLAN option
- SET FORCEPLAN statement
- query optimizer [SQL Server], optimizing process
- overriding query optimizer process
ms.assetid: b6c0b08f-2060-4696-9e12-50cb7e674321
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 35fee4ee42f2b401b584e752e1a834c991b03483
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093367"
---
# <a name="set-forceplan-transact-sql"></a>SET FORCEPLAN (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  當 FORCEPLAN 設定為 ON 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢最佳化工具會依照查詢之 FORM 子句中的資料表順序來處理聯結。 此外，除非需要其他類型的聯結來建構查詢計畫，或者聯結提示或查詢提示要求這些聯結類型，否則將 FORCEPLAN 設定為 ON 便可強制使用巢狀迴圈聯結。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
SET FORCEPLAN { ON | OFF }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註
 基本上，SET FORCEPLAN 會覆寫查詢最佳化工具用來處理 [!INCLUDE[tsql](../../includes/tsql-md.md)] SELECT 陳述式的邏輯。 不論這項設定為何，SELECT 陳述式傳回的資料都相同。 唯一不同是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理資料表來滿足查詢的方式。  
  
 查詢也可以利用查詢最佳化工具提示來影響 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理 SELECT 陳述式的方式。  
  
 SET FORCEPLAN 是在執行階段進行套用，而不是在剖析階段進行套用。  
  
## <a name="permissions"></a>權限  
 SET FORCEPLAN 權限預設給所有使用者。  
  
## <a name="examples"></a>範例  
 下列範例執行四份資料表的聯結。 因為 `SHOWPLAN_TEXT` 設定已啟用，所以 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 就會在 `SET FORCE_PLAN` 設定啟用後，傳回以不同方式處理查詢的相關資訊。  
  
```sql
USE AdventureWorks2012;  
GO  
-- Make sure FORCEPLAN is set to OFF.  
SET SHOWPLAN_TEXT OFF;  
GO  
SET FORCEPLAN OFF;  
GO  
SET SHOWPLAN_TEXT ON;  
GO  
-- Example where the query plan is not forced.  
SELECT p.LastName, p.FirstName, v.Name  
FROM Person.Person AS p  
   INNER JOIN HumanResources.Employee AS e  
   ON e.BusinessEntityID = p.BusinessEntityID  
   INNER JOIN Purchasing.PurchaseOrderHeader AS poh  
   ON e.BusinessEntityID = poh.EmployeeID  
   INNER JOIN Purchasing.Vendor AS v  
   ON poh.VendorID = v.BusinessEntityID;  
GO  
-- SET FORCEPLAN to ON.  
SET SHOWPLAN_TEXT OFF;  
GO  
SET FORCEPLAN ON;  
GO  
SET SHOWPLAN_TEXT ON;  
GO  
-- Reexecute inner join to see the effect of SET FORCEPLAN ON.  
SELECT p.LastName, p.FirstName, v.Name  
FROM Person.Person AS p  
   INNER JOIN HumanResources.Employee AS e   
   ON e.BusinessEntityID = p.BusinessEntityID  
   INNER JOIN Purchasing.PurchaseOrderHeader AS poh  
   ON e.BusinessEntityID = poh.EmployeeID  
   INNER JOIN Purchasing.Vendor AS v  
   ON poh.VendorID = v.BusinessEntityID;  
GO  
SET SHOWPLAN_TEXT OFF;  
GO  
SET FORCEPLAN OFF;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET SHOWPLAN_ALL &#40;Transact-SQL&#41;](../../t-sql/statements/set-showplan-all-transact-sql.md)   
 [SET SHOWPLAN_TEXT &#40;Transact-SQL&#41;](../../t-sql/statements/set-showplan-text-transact-sql.md)  
  
  
