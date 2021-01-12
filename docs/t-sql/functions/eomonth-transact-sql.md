---
description: EOMONTH (Transact-SQL)
title: EOMONTH (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- EOMONTH
- EOMONTH_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- EOMONTH function
ms.assetid: 1d060d8e-3297-4244-afef-57df2f8f92e2
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d198f286e9ddd57fa110e41d54ad5a96210b4087
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097228"
---
# <a name="eomonth-transact-sql"></a>EOMONTH (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函式會以選擇性位移，傳回包含指定日期的當月最後一天。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
EOMONTH ( start_date [, month_to_add ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*start_date*  
日期運算式，指定要傳回當月最後一天的日期。  
  
*month_to_add*  
選擇性整數運算式，指定要新增至 *start_date* 的月數。  
  
如果 *month_to_add* 引數具有值，則 `EOMONTH` 會將指定月數新增至 *start_date*，然後傳回當月最後一天作為結果日期。 如果這個加法溢位有效日期範圍，則 `EOMONTH` 會引發錯誤。  
  
## <a name="return-type"></a>傳回類型  
 **date**  
  
## <a name="remarks"></a>備註  
`EOMONTH` 函式可以遠端處理到 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 伺服器和更新版本。 它無法遠端處理到版本低於 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 的伺服器。  
  
## <a name="examples"></a>範例  
  
### <a name="a-eomonth-with-explicit-datetime-type"></a>A. 具明確日期時間類型的 EOMONTH  
  
```sql 
DECLARE @date DATETIME = '12/1/2011';  
SELECT EOMONTH ( @date ) AS Result;  
GO  
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Result  
------------  
2011-12-31  
  
(1 row(s) affected)  
```  

### <a name="b-eomonth-with-string-parameter-and-implicit-conversion"></a>B. 具有字串參數及明確轉換的 EOMONTH  
  
```sql
DECLARE @date VARCHAR(255) = '12/1/2011';  
SELECT EOMONTH ( @date ) AS Result;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
Result  
------------  
2011-12-31  
  
(1 row(s) affected)  
```  
  
### <a name="c-eomonth-with-and-without-the-month_to_add-parameter"></a>C. 具有和不具有 month_to_add 參數的 EOMONTH  
  
這些結果集中顯示的值反映且包含 `12/01/2011` 和 `12/31/2011` 之間的執行日期。

```sql  
DECLARE @date DATETIME = GETDATE();  
SELECT EOMONTH ( @date ) AS 'This Month';  
SELECT EOMONTH ( @date, 1 ) AS 'Next Month';  
SELECT EOMONTH ( @date, -1 ) AS 'Last Month';  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
This Month  
-----------------------  
2011-12-31  
  
(1 row(s) affected)  
  
Next Month  
-----------------------  
2012-01-31  
  
(1 row(s) affected)  
  
Last Month  
-----------------------  
2011-11-30  
  
(1 row(s) affected)  
```
