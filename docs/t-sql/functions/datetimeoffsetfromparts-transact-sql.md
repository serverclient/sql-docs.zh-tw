---
description: DATETIMEOFFSETFROMPARTS (Transact-SQL)
title: DATETIMEOFFSETFROMPARTS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DATETIMEOFFSETFROMPARTS_TSQL
- DATETIMEOFFSETFROMPARTS
dev_langs:
- TSQL
helpviewer_keywords:
- DATETIMEOFFSETFROMPARTS function
ms.assetid: 463da1f4-b4b6-45a3-9a95-ea1f99575542
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ab37520d60c482afccb40bde7392de2515a79753
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101186"
---
# <a name="datetimeoffsetfromparts-transact-sql"></a>DATETIMEOFFSETFROMPARTS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

會傳回指定日期和時間引數的 **datetimeoffset** 值。 傳回值的有效位數由 precision 引數指定，時差則由 offset 引數指定。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DATETIMEOFFSETFROMPARTS ( year, month, day, hour, minute, seconds, fractions, hour_offset, minute_offset, precision )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*year*  
指定年份的整數運算式。  
  
*month*  
指定月份的整數運算式。  
  
*day*  
指定日期的整數運算式。  
  
*hour*  
指定小時的整數運算式。  
  
*minute*  
指定分鐘的整數運算式。  
  
*seconds*  
指定秒的整數運算式。  
  
*fractions*  
指定小數秒值的整數運算式。  
  
*hour_offset*  
指定時區時差之小時部分的整數運算式。  
  
*minute_offset*  
指定時區時差之分鐘部分的整數運算式。  
  
*有效位數*  
整數常值，指定 `DATETIMEOFFSETFROMPARTS` 所傳回 **datetimeoffset** 值的有效位數。  
  
## <a name="return-types"></a>傳回類型
**datetimeoffset(** *precision* **)**  
  
## <a name="remarks"></a>備註  

`DATETIMEOFFSETFROMPARTS` 會傳回完整初始化的 **datetimeoffset** 資料類型。 offset 引數代表時區時差。 針對省略的 offset 引數，`DATETIMEOFFSETFROMPARTS` 會假設時區時差為 `00:00`，也就是完全沒有時區時差。 針對指定的 offset 引數，`DATETIMEOFFSETFROMPARTS` 預期兩個引數都有值，而且兩個值同時為正數值或負數值。 如果 *minute_offset* 具有值而 *hour_offset* 沒有值，`DATETIMEOFFSETFROMPARTS` 會引發錯誤。 如果其他引數的值無效，則 `DATETIMEOFFSETFROMPARTS` 會引發錯誤。 如果至少一個必要引數具有 `NULL` 值，則 `DATETIMEOFFSETFROMPARTS` 會傳回 `NULL`。 不過，如果 *precision* 引數具有 `NULL` 值，則 `DATETIMEOFFSETFROMPARTS` 會引發錯誤。  
  
*fractions* 引數相依於 precision 引數。 例如，precision 值為 7 的每個部分表示 100 奈秒；precision 為 3 的每個部分表示 1 毫秒。 如果 precision 值為零，fractions 也必須為零，否則 `DATETIMEOFFSETFROMPARTS` 會引發錯誤。  
  
此函式支援遠端處理到 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 伺服器及更新版本。 它不支援遠端處理到版本低於 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 的伺服器。
  
## <a name="examples"></a>範例  
  
### <a name="a-an-example-without-fractions-of-a-second"></a>A. 不包含秒之小數部分的範例  
  
```sql
SELECT DATETIMEOFFSETFROMPARTS ( 2010, 12, 31, 14, 23, 23, 0, 12, 0, 7 ) AS Result;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Result  
----------------------------------
2010-12-31 14:23:23.0000000 +12:00  
  
(1 row(s) affected)  
```  
  
### <a name="b-example-with-fractions-of-a-second"></a>B. 含秒的小數部分的範例  

此範例示範 *fractions* 和 *precision* 參數的用法：  

1. 若 *fractions* 的值為 5、*precision* 的值為 1，則 *fractions* 的值表示 5/10 秒。  

2. 若 *fractions* 的值為 50、*precision* 的值為 2，則 *fractions* 的值表示 50/100 秒。  

3. 若 *fractions* 的值為 500、*precision* 的值為 3，則 *fractions* 的值表示 500/1000 秒。  
  
```sql
SELECT DATETIMEOFFSETFROMPARTS ( 2011, 8, 15, 14, 30, 00, 5, 12, 30, 1 );  
SELECT DATETIMEOFFSETFROMPARTS ( 2011, 8, 15, 14, 30, 00, 50, 12, 30, 2 );  
SELECT DATETIMEOFFSETFROMPARTS ( 2011, 8, 15, 14, 30, 00, 500, 12, 30, 3 );  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
----------------------------------  
2011-08-15 14:30:00.5 +12:30  
  
(1 row(s) affected)  
  
----------------------------------  
2011-08-15 14:30:00.50 +12:30  
  
(1 row(s) affected)  
  
----------------------------------  
2011-08-15 14:30:00.500 +12:30  
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>另請參閱
[datetimeoffset &#40;Transact-SQL&#41;](../../t-sql/data-types/datetimeoffset-transact-sql.md)  
[AT TIME ZONE &#40;Transact-SQL&#41;](../../t-sql/queries/at-time-zone-transact-sql.md)
  
  


