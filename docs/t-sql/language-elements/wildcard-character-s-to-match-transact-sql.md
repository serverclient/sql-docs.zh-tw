---
title: '[ ] 用於比對字元的萬用字元'
description: 使用萬用字元來比對一或多個字元。
titleSuffix: SQL Server (Transact-SQL)
ms.custom: seo-lt-2019
ms.date: 12/06/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- Match
- wildcard
- '[ ]'
- '[_]_TSQL'
dev_langs:
- TSQL
helpviewer_keywords:
- wildcard characters [SQL Server]
- '[ ] (wildcard - character(s) to match)'
ms.assetid: 57817576-0bf1-49ed-b05d-fac27e8fed7a
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b0cc55631431246f87a64c9a17fe953e34c454f3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095875"
---
# <a name="--wildcard---characters-to-match-transact-sql"></a>\[ \] (萬用字元 - 要比對的字元) (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

符合方括號 `[ ]` 之間所指定之指定範圍或集合內的任何單一字元。 這些萬用字元可用於包含模式比對 (如 `LIKE` 和 `PATINDEX`) 的字串比較中。  

 
## <a name="examples"></a>範例  
### <a name="a-simple-example"></a>A：簡單範例   
下列範例會傳回開頭為字母 `m` 的名稱。 `[n-z]` 會指定第二個字母必須是範圍從 `n` 到 `z` 中的某一個字母。 百分比萬用字元 `%` 允許開頭為字元 3 的任何字元或沒有字元。 `model` 和 `msdb` 資料庫符合此原則。 `master` 資料庫不符合準則，並會從結果集中加以排除。
 
```sql
SELECT name FROM sys.databases
WHERE name LIKE 'm[n-z]%';
```
[!INCLUDE[ssResult_md](../../includes/ssresult-md.md)]  

```
name
-----
model
msdb
```   
 您可能需要安裝其他符合資格的資料庫。


### <a name="b-more-complex-example"></a>B：更複雜的範例   
 下列範例會利用 [] 運算子，來尋找地址有四位數郵遞區號之所有 [!INCLUDE[ssSampleDBCoShort](../../includes/sssampledbcoshort-md.md)] 員工的識別碼和名稱。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT e.BusinessEntityID, p.FirstName, p.LastName, a.PostalCode  
FROM HumanResources.Employee AS e  
INNER JOIN Person.Person AS p ON e.BusinessEntityID = p.BusinessEntityID  
INNER JOIN Person.BusinessEntityAddress AS ea ON e.BusinessEntityID = ea.BusinessEntityID  
INNER JOIN Person.Address AS a ON a.AddressID = ea.AddressID  
WHERE a.PostalCode LIKE '[0-9][0-9][0-9][0-9]';  
```  
  
[!INCLUDE[ssResult_md](../../includes/ssresult-md.md)]  
  
```  
EmployeeID      FirstName      LastName      PostalCode  
----------      ---------      ---------     ----------  
290             Lynn           Tsoflias      3000  
```  

### <a name="c-using-a-set-that-combines-ranges-and-single-characters"></a>C.使用結合範圍和單一字元的集合

萬用字元集合可同時包含單一字元和範圍。 下列範例會使用 [] 運算子來尋找開頭為數字或一系列特殊字元的字串。

```sql
SELECT [object_id], OBJECT_NAME(object_id) AS [object_name], name, column_id 
FROM sys.columns 
WHERE name LIKE '[0-9!@#$.,;_]%';
```

[!INCLUDE[ssResult_md](../../includes/ssresult-md.md)]  

```
object_id     object_name                         name  column_id
---------     -----------                         ----  ---------
615673241     vSalesPersonSalesByFiscalYears      2002  5
615673241     vSalesPersonSalesByFiscalYears      2003  6
615673241     vSalesPersonSalesByFiscalYears      2004  7
1591676718    JunkTable                           _xyz  1
```
  
## <a name="see-also"></a>另請參閱  
 [LIKE &#40;Transact-SQL&#41;](../../t-sql/language-elements/like-transact-sql.md)   
 [PATINDEX &#40;Transact-SQL&#41;](../../t-sql/functions/patindex-transact-sql.md)   
  [% &#40;萬用字元 - 相符的字元&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/percent-character-wildcard-character-s-to-match-transact-sql.md)   
 [&#91;^&#93; &#40;萬用字元 - 不相符的字元&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/wildcard-character-s-not-to-match-transact-sql.md)     
 [\_ &#40;萬用字元 - 符合單一字元&#41; &#40;Transact-SQL&#41;](../../t-sql/language-elements/wildcard-match-one-character-transact-sql.md)  
    
  
