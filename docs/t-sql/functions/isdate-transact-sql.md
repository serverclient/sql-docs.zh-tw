---
description: ISDATE (Transact-SQL)
title: ISDATE (Transact-SQL)
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ISDATETIME
- ISDATE_TSQL
- ISDATE
- ISDATETIME_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- date and time [SQL Server], ISDATE
- validate dates times [SQL Server]
- formats [SQL Server], time
- dates [SQL Server], validate
- verify dates times [SQL Server]
- functions [SQL Server], time
- formats [SQL Server], dates
- functions [SQL Server], date and time
- time [SQL Server], functions
- time [SQL Server], validate
- ISDATE function [SQL Server]
ms.assetid: 8e2c9ee7-388a-432f-b2c9-7b398f26bf85
author: cawrites
ms.author: chadam
ms.reviewer: ''
ms.custom: ''
ms.date: 03/14/2017
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5345bbdc161730ba0d08c79e0d52b7e2ec901498
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097212"
---
# <a name="isdate-transact-sql"></a>ISDATE (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  若 *expression* 為有效的 **date**、**time** 或 **datetime**，則傳回 1；否則傳回 0。  
  
 ISDATE 會在 *expression* 為 **datetime2** 值時傳回 0。  
  
 如需所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間資料類型與函數的概觀，請參閱[日期和時間資料類型與函數 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)。 請注意，datetime 資料範圍為 1753-01-01 到 9999-12-31，而 date 資料範圍是 0001-01-01 到 9999-12-31。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ISDATE ( expression )
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *expression*  
 為字元字串，或可以轉換為字元字串的[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。 此運算式的長度必須少於 4,000 個字元。 日期和時間資料類型 (但不包括 datetime 和 smalldatetime) 不允許做為 ISDATE 的引數。  
  
## <a name="return-type"></a>傳回類型  
 **int**  
  
## <a name="remarks"></a>備註  
 只有在搭配 [CONVERT](../../t-sql/functions/cast-and-convert-transact-sql.md) 函式使用，且已指定 CONVERT 樣式參數，並且樣式不等於 0、100、9 或 109 時，ISDATE 才具確定性。  
  
 ISDATE 的傳回值會根據 [SET DATEFORMAT](../../t-sql/statements/set-dateformat-transact-sql.md)、[SET LANGUAGE](../../t-sql/statements/set-language-transact-sql.md) 和[設定預設語言伺服器設定選項](../../database-engine/configure-windows/configure-the-default-language-server-configuration-option.md)的設定而不同。  
  
## <a name="isdate-expression-formats"></a>ISDATE 運算式格式  
 如需 ISDATE 將傳回 1 之有效格式的範例，請參閱 [datetime](../../t-sql/data-types/datetime-transact-sql.md) 和 [smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md) 主題中的＜datetime 支援的字串常值格式＞一節。 如需其他範例，請參閱 [CAST 和 CONVERT](../../t-sql/functions/cast-and-convert-transact-sql.md) 中＜引數＞一節內的輸入/輸出資料行。  
  
 下表將摘要列出無效而且會傳回 0 或錯誤的輸入運算式格式。  
  
|ISDATE 運算式|ISDATE 傳回值|  
|-----------------------|-------------------------|  
|NULL|0|  
|在[資料類型](../../t-sql/data-types/data-types-transact-sql.md)的任何資料類型類別中所列的資料類型值，但字元字串、Unicode 字元字串或日期和時間除外。|0|  
|**text**、**ntext** 或 **image** 資料類型的值。|0|  
|秒數有效位數超過 3 的任何值 (.0000 到 .0000000...n)。 若 *expression* 為 **datetime2** 值，則 ISDATE 會傳回 0；若 *expression* 為有效的 **datetime** 值，則會傳回 1。|0|  
|混合有效日期與無效值的任何值，例如 1995-10-1a。|0|  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-isdate-to-test-for-a-valid-datetime-expression"></a>A. 使用 ISDATE 測試有效的 datetime 運算式  
 下列範例示範如何使用 `ISDATE` 來測試字元字串是否為有效的 **datetime**。  
  
```sql  
IF ISDATE('2009-05-12 10:19:41.177') = 1  
    PRINT 'VALID'  
ELSE  
    PRINT 'INVALID';  
```  
  
### <a name="b-showing-the-effects-of-the-set-dateformat-and-set-language-settings-on-return-values"></a>B. 說明 SET DATEFORMAT 和 SET LANGUAGE 設定對傳回值的影響  
 下列陳述式會說明傳回成為 `SET DATEFORMAT` 和 `SET LANGUAGE` 設定結果的值。  
  
```sql  
/* Use these sessions settings. */  
SET LANGUAGE us_english;  
SET DATEFORMAT mdy;  
/* Expression in mdy dateformat */  
SELECT ISDATE('04/15/2008'); --Returns 1.  
/* Expression in mdy dateformat */  
SELECT ISDATE('04-15-2008'); --Returns 1.   
/* Expression in mdy dateformat */  
SELECT ISDATE('04.15.2008'); --Returns 1.   
/* Expression in myd  dateformat */  
SELECT ISDATE('04/2008/15'); --Returns 1.  
  
SET DATEFORMAT mdy;  
SELECT ISDATE('15/04/2008'); --Returns 0.  
SET DATEFORMAT mdy;  
SELECT ISDATE('15/2008/04'); --Returns 0.  
SET DATEFORMAT mdy;  
SELECT ISDATE('2008/15/04'); --Returns 0.  
SET DATEFORMAT mdy;  
SELECT ISDATE('2008/04/15'); --Returns 1.  
  
SET DATEFORMAT dmy;  
SELECT ISDATE('15/04/2008'); --Returns 1.  
SET DATEFORMAT dym;  
SELECT ISDATE('15/2008/04'); --Returns 1.  
SET DATEFORMAT ydm;  
SELECT ISDATE('2008/15/04'); --Returns 1.  
SET DATEFORMAT ymd;  
SELECT ISDATE('2008/04/15'); --Returns 1.  
  
SET LANGUAGE English;  
SELECT ISDATE('15/04/2008'); --Returns 0.  
SET LANGUAGE Hungarian;  
SELECT ISDATE('15/2008/04'); --Returns 0.  
SET LANGUAGE Swedish;  
SELECT ISDATE('2008/15/04'); --Returns 0.  
SET LANGUAGE Italian;  
SELECT ISDATE('2008/04/15'); --Returns 1.  
  
/* Return to these sessions settings. */  
SET LANGUAGE us_english;  
SET DATEFORMAT mdy;  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-using-isdate-to-test-for-a-valid-datetime-expression"></a>C. 使用 ISDATE 測試有效的 datetime 運算式  
 下列範例示範如何使用 `ISDATE` 來測試字元字串是否為有效的 **datetime**。  
  
```sql  
IF ISDATE('2009-05-12 10:19:41.177') = 1  
    SELECT 'VALID';  
ELSE  
    SELECT 'INVALID';  
```  
  
## <a name="see-also"></a>另請參閱  
 [CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
