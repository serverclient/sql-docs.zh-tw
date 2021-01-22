---
description: DROP SENSITIVITY CLASSIFICATION (Transact-SQL)
title: DROP SENSITIVITY CLASSIFICATION (Transact-SQL) | Microsoft Docs
ms.date: 03/25/2019
ms.reviewer: ''
ms.prod: sql
ms.technology: t-sql
ms.topic: language-reference
ms.custom: ''
ms.author: giladm
author: giladmit
f1_keywords:
- DROP SENSITIVITY CLASSIFICATION
- DROP_SENSITIVITY_CLASSIFICATION
dev_langs:
- TSQL
helpviewer_keywords:
- DROP SENSITIVITY CLASSIFICATION statement
- dropping labels
- drop labels
- removing labels
- remove labels
- classification [SQL]
- labels [SQL]
- information types
- data classification
monikerRange: " >= sql-server-ver15 || = azuresqldb-current"
ms.openlocfilehash: 8b5dce1e51adfd52c89a7cc6fbba2d9f91497933
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98688915"
---
# <a name="drop-sensitivity-classification-transact-sql"></a>DROP SENSITIVITY CLASSIFICATION (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

卸除來自至一或多個資料庫資料行的敏感度分類中繼資料。

## <a name="syntax"></a>語法

```syntaxsql
DROP SENSITIVITY CLASSIFICATION FROM
    <object_name> [, ...n ]

<object_name> ::=
{
    [schema_name.]table_name.column_name
}
```  

## <a name="arguments"></a>引數  

*object_name* ([schema_name.]table_name.column_name)

是要移除其分類的資料庫資料行名稱。 目前只支援資料行分類。
    - *schema_name* (選用) - 是分類的資料行所屬的結構描述名稱。
    - *table_name* - 是分類的資料行所屬的資料表名稱。
    - *column_name* - 是要卸除分類的資料行名稱。

## <a name="remarks"></a>備註  

- 多個物件分類可使用單一 'DROP SENSITIVITY CLASSIFICATION' 陳述式來卸除。

## <a name="permissions"></a>權限  

需要 ALTER ANY SENSITIVITY CLASSIFICATION 權限。 ALTER ANY SENSITIVITY CLASSIFICATION 是由資料庫權限 ALTER，或由伺服器權限 CONTROL SERVER 所隱含。


## <a name="examples"></a>範例  


### <a name="a-dropping-classification-from-a-single-column"></a>A. 卸除單一資料行的分類

下列範例會從資料欄 `dbo.sales.price` 中移除分類。  

```sql
DROP SENSITIVITY CLASSIFICATION FROM
    dbo.sales.price
```

### <a name="b-dropping-classification-from-multiple-columns"></a>B. 卸除多個資料行的分類

下列範例會從資料欄 `dbo.sales.price`、`dbo.sales.discount` 及 `SalesLT.Customer.Phone` 中移除分類。  

```sql
DROP SENSITIVITY CLASSIFICATION FROM
    dbo.sales.price, dbo.sales.discount, SalesLT.Customer.Phone  
```

## <a name="see-also"></a>另請參閱  

[ADD SENSITIVITY CLASSIFICATION (Transact-SQL)](../../t-sql/statements/add-sensitivity-classification-transact-sql.md)

[sys.sensitivity_classifications (Transact-SQL)](../../relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql.md)

[開始使用 SQL 資訊保護](/azure/azure-sql/database/data-discovery-and-classification-overview)