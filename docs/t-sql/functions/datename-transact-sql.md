---
description: DATENAME (Transact-SQL)
title: DATENAME (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DATENAME_TSQL
- DATENAME
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- DATENAME function [SQL Server
- functions [SQL Server], time
- functions [SQL Server], date and time
- dateparts [SQL Server], datename
- date and time [SQL Server], DATENAME
- comparing dates times [SQL Server]
- dates [SQL Server], dateparts
ms.assetid: 11855b56-c554-495d-aad4-ba446990153b
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 021c8e18b0767fcd844a92f598b4fbcb434740a4
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097256"
---
# <a name="datename-transact-sql"></a>DATENAME (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函式會傳回字元字串，代表指定之 *date* 的指定 *datepart*。

如需所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間資料類型與函式的概觀，請參閱[日期和時間資料類型與函式 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DATENAME ( datepart , date )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*datepart*  
`DATENAME` 會傳回之 *date* 引數的特定部分。 此表格會列出所有有效的 *datepart* 引數。

> [!NOTE]
> `DATENAME` 不會接受 *datepart* 引數的使用者定義變數對等項目。
  
|*datepart*|縮寫|  
|---|---|
|**year**|**yy, yyyy**|  
|**quarter**|**qq, q**|  
|**month**|**mm, m**|  
|**dayofyear**|**dy, y**|  
|**day**|**dd, d**|  
|**week**|**wk, ww**|  
|**weekday**|**dw, w**|  
|**hour**|**hh**|  
|**minute**|**mi, n**|  
|**second**|**ss, s**|  
|**millisecond**|**ms**|  
|**microsecond**|**mcs**|  
|**nanosecond**|**ns**|  
|**TZoffset**|**tz**|  
|**ISO_WEEK**|**ISOWK, ISOWW**|  
  
*date*  

可解析成下列其中一個資料類型的運算式： 

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2** 
+ **smalldatetime**
+ **time**

針對 *date*，`DATENAME` 會接受資料行運算式、運算式、字串常值或使用者定義變數。 請使用四位數年份以避免模糊不清的問題。 如需兩位數年份的資訊，請參閱[設定兩位數年份的截止伺服器設定選項](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)。
  
## <a name="return-type"></a>傳回類型  
**nvarchar**
  
## <a name="return-value"></a>傳回值  
  
-   每個 *datepart* 及其縮寫都會傳回相同的值。  
  
傳回值會取決於使用 [SET LANGUAGE](../../t-sql/statements/set-language-transact-sql.md) 及登入的[設定 default language 伺服器設定選項](../../database-engine/configure-windows/configure-the-default-language-server-configuration-option.md)而設定的語言環境，而有所不同。 如果 *date* 是某些格式的字串常值，傳回值就會取決於 [SET DATEFORMAT](../../t-sql/statements/set-dateformat-transact-sql.md)。 當 date 是日期或時間資料類型的資料行運算式時，SET DATEFORMAT 並不會變更傳回值。
  
當 *date* 參數具有 **date** 資料類型引數時，傳回值就會根據 [SET DATEFIRST](../../t-sql/statements/set-datefirst-transact-sql.md) 所指定的設定而不同。
  
## <a name="tzoffset-datepart-argument"></a>TZoffset datepart 引數  
如果 *datepart* 引數是 **TZoffset** (**tz**) 而且 *date* 引數沒有時區位移時，`DATEADD` 就會傳回 0。
  
## <a name="smalldatetime-date-argument"></a>smalldatetime date 引數  
當 *date* 是 [smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md) 時，`DATENAME` 會以 00 形式傳回秒數。
  
## <a name="default-returned-for-a-datepart-that-is-not-in-the-date-argument"></a>針對不在 date 引數中的 datepart 所傳回的預設值  
如果 *date* 引數的資料類型沒有指定的 *datepart*，只有在 *date* 引數具有常值時，`DATENAME` 才會傳回該 *datepart* 的預設值。
  
例如，任何 **date** 資料類型的預設年-月-日都是 1900-01-01。 此陳述式具有 *datepart* 的日期部分引數、*date* 的時間引數，而且 `DATENAME` 會傳回 `1900, January, 1, 1, Monday`。
  
```sql
SELECT DATENAME(year, '12:10:30.123')  
    ,DATENAME(month, '12:10:30.123')  
    ,DATENAME(day, '12:10:30.123')  
    ,DATENAME(dayofyear, '12:10:30.123')  
    ,DATENAME(weekday, '12:10:30.123');  
```  
  
如果 *date* 指定為變數或資料表資料行，而該變數或資料行的資料類型沒有指定的 *datepart*，`DATENAME` 會傳回錯誤 9810。 在此範例中，變數 *\@t* 具有 **time** 資料類型。 此範例會失敗，因為 DATEPART year 對 **time** 資料類型無效：
  
```sql
DECLARE @t time = '12:10:30.123';   
SELECT DATENAME(year, @t);  
```  
  
## <a name="remarks"></a>備註  

在下列子句中使用 `DATENAME`：

+ GROUP BY
+ HAVING
+ 排序依據
+ SELECT \<list>
+ WHERE
  
在 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中，DATENAME 會隱含地將字串常值轉換為 **datetime2** 類型。 換句話說，將日期當作字串傳遞時，`DATENAME` 不支援 YDM 格式。 您必須明確地將字串轉換為 **datetime** 或 **smalldatetime** 類型，才能使用 YDM 格式。
  
## <a name="examples"></a>範例  
此範例會針對指定的日期傳回日期部分。 請將資料表中的 *datepart* 值取代為 SELECT 陳述式中的 `datepart` 引數：
  
`SELECT DATENAME(datepart,'2007-10-30 12:15:32.1234567 +05:10');`
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
|*datepart*|傳回值|  
|---|---|
|**year、yyyy、yy**|2007|  
|**quarter、qq、q**|4|  
|**month、mm、m**|10 月|  
|**dayofyear、dy、y**|303|  
|**day、dd、d**|30|  
|**week、wk、ww**|44|  
|**weekday、dw**|Tuesday|  
|**hour、hh**|12|  
|**minute、n**|15|  
|**second、ss、s**|32|  
|**millisecond、ms**|123|  
|**microsecond、mcs**|123456|  
|**nanosecond、ns**|123456700|  
|**TZoffset、tz**|+05:10|  
|**ISO_WEEK、ISOWK、ISOWW**|44|  
  
[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

此範例會針對指定的日期傳回日期部分。 請將資料表中的 *datepart* 值取代為 SELECT 陳述式中的 `datepart` 引數：
  
```sql
SELECT DATENAME(datepart,'2007-10-30 12:15:32.1234567 +05:10');  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
|*datepart*|傳回值|  
|---|---|
|**year、yyyy、yy**|2007|  
|**quarter、qq、q**|4|  
|**month、mm、m**|10 月|  
|**dayofyear、dy、y**|303|  
|**day、dd、d**|30|  
|**week、wk、ww**|44|  
|**weekday、dw**|Tuesday|  
|**hour、hh**|12|  
|**minute、n**|15|  
|**second、ss、s**|32|  
|**millisecond、ms**|123|  
|**microsecond、mcs**|123456|  
|**nanosecond、ns**|123456700|  
|**TZoffset、tz**|+05:10|  
|**ISO_WEEK、ISOWK、ISOWW**|44|  
  
## <a name="see-also"></a>另請參閱
[CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
  

