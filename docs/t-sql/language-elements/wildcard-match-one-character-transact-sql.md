---
description: _ (萬用字元 - 符合單一字元) (Transact-SQL)
title: _ (萬用字元 - 比對單一字元) (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/06/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- Match
- wildcard
- _TSQL
- Match One
- _
dev_langs:
- TSQL
helpviewer_keywords:
- wildcard characters [SQL Server]
- _ (wildcard - match one character)
ms.assetid: 11a2ed36-9e21-4bdf-ae20-a31db1434b97
author: cawrites
ms.author: chadam
ms.openlocfilehash: 38398adeb20a6a9f70ab225d4ead8b8504635323
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095858"
---
# <a name="_-wildcard---match-one-character-transact-sql"></a>_ (萬用字元 - 符合單一字元) (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

使用底線字元 _ 在涉及模式比對 (如 `LIKE` 和 `PATINDEX`) 的字串比較運算式中，比對任何單一字元。  
  
## <a name="examples"></a>範例  

## <a name="a-simple-example"></a>A：簡單範例   

以下範例傳回開頭為字母 `m` 且第三個字母具有 `d` 的所有資料庫名稱。 底線字元指定名稱的第二個字元可以是任何字母。 `model` 和 `msdb` 資料庫符合此原則。 `master` 資料庫則不符合。

```sql
SELECT name FROM sys.databases
WHERE name LIKE 'm_d%';
```   
[!INCLUDE[ssResult_md](../../includes/ssresult-md.md)]   
```
name
-----
model
msdb
```   
您可能具有符合此原則的其他資料庫。

您可以使用多個底線來代表多個字元。 將 `LIKE` 原則變更為包含兩個底線 `'m__%`，就會在結果中包含 master 資料庫。

### <a name="b-more-complex-example"></a>B：更複雜的範例
 下列範例會使用 _ 運算子來尋找在 `Person` 資料表中，所有名字都是三個字母且結尾是 `an` 的人員。  
  
```sql  
-- USE AdventureWorks2012
  
SELECT FirstName, LastName  
FROM Person.Person  
WHERE FirstName LIKE '_an'  
ORDER BY FirstName;  
```  
## <a name="c-escaping-the-underscore-character"></a>C：逸出底線字元   
下列範例傳回固定資料庫角色的名稱，如 `db_owner` 和 `db_ddladmin`，但它也會傳回 `dbo` 使用者。 

```sql
SELECT name FROM sys.database_principals
WHERE name LIKE 'db_%';
```

第三個字元位置中的底線視為萬用字元，不會作為開頭是 `db_` 字母的唯一原則篩選。 若要逸出底線，可用中括號 `[_]` 包圍它。 

```sql
SELECT name FROM sys.database_principals
WHERE name LIKE 'db[_]%';
```   
現在已經排除 `dbo` 使用者。   
[!INCLUDE[ssResult_md](../../includes/ssresult-md.md)]   
```
name
-------------
db_owner
db_accessadmin
db_securityadmin
...
```

  
## <a name="see-also"></a>另請參閱  
 [LIKE &#40;Transact-SQL&#41;](../../t-sql/language-elements/like-transact-sql.md)   
 [PATINDEX &#40;Transact-SQL&#41;](../../t-sql/functions/patindex-transact-sql.md)   
  [% (萬用字元 - 要比對的字元)](../../t-sql/language-elements/percent-character-wildcard-character-s-to-match-transact-sql.md)   
  [&#91; &#93; (萬用字元 - 要比對的字元)](../../t-sql/language-elements/wildcard-character-s-to-match-transact-sql.md)   
 [&#91;^&#93; (萬用字元 - 不相符的字元)](../../t-sql/language-elements/wildcard-character-s-not-to-match-transact-sql.md)     
  
