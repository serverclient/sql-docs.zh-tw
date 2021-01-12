---
description: 斜線星形 (區塊註解) (Transact-SQL)
title: 斜線星形 (區塊註解) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/27/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- /*...*/_TSQL
- Comment
- /*...*/
dev_langs:
- TSQL
helpviewer_keywords:
- nonexecuting text strings [SQL Server]
- /*...*/ (comment)
- remarks [SQL Server]
- comments [SQL Server]
ms.assetid: 4d9ab1b2-4bbb-4c16-beb1-cafc1af7417c
author: cawrites
ms.author: chadam
ms.openlocfilehash: 00dbdd9a6fb7641c00b57eff996bf94b9b683aa3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096968"
---
# <a name="slash-star-block-comment-transact-sql"></a>斜線星形 (區塊註解) (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-asdb-xxxx-xxx_md](../../includes/tsql-appliesto-ss2008-asdb-xxxx-xxx-md.md)]


  指出使用者提供的文字。 伺服器不會評估 /* 和 \*/ 之間的文字。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
/*  
text_of_comment  
*/  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *text_of_comment*  
 這是註解的文字。 這是一或多個字元字串。  
  
## <a name="remarks"></a>備註  
 註解可以插入個別行中，也可以插入 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 多行註解必須用 /* 和 \*/ 來表示。 多行註解的常用樣式慣例是第一行開頭用 /\*，後續行用 \*\*，結尾則是 \*/。  
  
 註解沒有長度上限。  
  
 支援巢狀註解。 如果現有註解內的任何位置出現 /* 字元模式，就會將它視為巢狀註解的開頭，因此，需要一個結束的 \*/ 註解標記。 如果結束的註解標示不存在，就會產生錯誤。  
  
 例如，下列程式碼會產生錯誤。  
  
```sql  
DECLARE @comment AS VARCHAR(20);  
GO  
/*  
SELECT @comment = '/*';  
*/   
SELECT @@VERSION;  
GO   
```  
  
 若要暫時解決這個錯誤，請進行下列變更。  
  
```sql  
DECLARE @comment AS VARCHAR(20);  
GO  
/*  
SELECT @comment = '/*';  
*/ */  
SELECT @@VERSION;  
GO  
```  
  
## <a name="examples"></a>範例  
 下列範例會利用註解來說明程式碼區段應該執行的動作。  
  
```sql  
USE AdventureWorks2012;  
GO  
/*  
This section of the code joins the Person table with the Address table,   
by using the Employee and BusinessEntityAddress tables in the middle to   
get a list of all the employees in the AdventureWorks2012 database   
and their contact information.  
*/  
SELECT p.FirstName, p.LastName, a.AddressLine1, a.AddressLine2, a.City, a.PostalCode  
FROM Person.Person AS p  
JOIN HumanResources.Employee AS e ON p.BusinessEntityID = e.BusinessEntityID   
JOIN Person.BusinessEntityAddress AS ea ON e.BusinessEntityID = ea.BusinessEntityID  
JOIN Person.Address AS a ON ea.AddressID = a.AddressID;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [-- &#40;Comment&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/comment-transact-sql.md)   
 [流程控制語言 &#40;Transact-SQL&#41;](~/t-sql/language-elements/control-of-flow.md)  
  
  

