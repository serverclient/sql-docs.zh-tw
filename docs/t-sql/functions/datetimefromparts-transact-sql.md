---
description: DATETIMEFROMPARTS (Transact-SQL)
title: DATETIMEFROMPARTS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DATETIMEFROMPARTS_TSQL
- DATETIMEFROMPARTS
dev_langs:
- TSQL
helpviewer_keywords:
- DATETIMEFROMPARTS function
ms.assetid: 6008148b-bf75-4c98-9392-68a89fa0711c
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f86250de50575a37320684b237867c517991b0f7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101228"
---
# <a name="datetimefromparts-transact-sql"></a>DATETIMEFROMPARTS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函式會傳回指定日期和時間引數的 **datetime** 值。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DATETIMEFROMPARTS ( year, month, day, hour, minute, seconds, milliseconds )  
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
  
*milliseconds*  
指定毫秒的整數運算式。
  
## <a name="return-types"></a>傳回類型
**datetime**
  
## <a name="remarks"></a>備註  
`DATETIMEFROMPARTS` 會傳回完整初始化的 **datetime** 值。 如果至少一個必要引數具有無效的值，`DATETIMEFROMPARTS` 會引發錯誤。 如果至少一個必要引數具有 Null 值，則 `DATETIMEFROMPARTS` 會傳回 Null。
  
此函式支援遠端處理到 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 伺服器及更新版本。 它不支援遠端處理到版本低於 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 的伺服器。
  
## <a name="examples"></a>範例  
  
```sql
SELECT DATETIMEFROMPARTS ( 2010, 12, 31, 23, 59, 59, 0 ) AS Result;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Result  
---------------------------  
2010-12-31 23:59:59.000  
  
(1 row(s) affected)  
```  
  
## <a name="see-also"></a>另請參閱
[datetime &#40;Transact-SQL&#41;](../../t-sql/data-types/datetime-transact-sql.md)
  
  

