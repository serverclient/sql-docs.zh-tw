---
description: DATEDIFF_BIG (Transact-SQL)
title: DATEDIFF_BIG (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/18/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DATEDIFF_BIG
- DATEDIFF_BIG_TSQL
helpviewer_keywords:
- DATEDIFF_BIG function [SQL Server]
- dates [SQL Server]. functions
- date and time [SQL Server], DATEDIFF_BIG
- functions [SQL Server], time
- functions [SQL Server], date and time
- time [SQL Server], functions
ms.assetid: 19ac1693-3cfa-400d-bf83-20a9cb46599a
author: cawrites
ms.author: chadam
ms.openlocfilehash: ea087da6532c43493fd10f647788297d98f35f72
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093648"
---
# <a name="datediff_big-transact-sql"></a>DATEDIFF_BIG (Transact-SQL)

[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

此函式會傳回跨越指定之 *startdate* 和 *enddate* 的指定之 *datepart* 界限的計數 (作為帶正負號的大整數值)。
  
如需所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間資料類型與函式的概觀，請參閱[日期和時間資料類型與函式 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DATEDIFF_BIG ( datepart , startdate , enddate )  
```  

## <a name="arguments"></a>引數
*datepart*  
指定所跨越界限類型之 *startdate* 和 *enddate* 的一部分。

> [!NOTE]
> `DATEDIFF_BIG` 不會接受使用者自訂變數中的 *datepart* 值，也不會接受以引號括住的字串。

此表格列出所有有效的 *datepart* 引數名稱與縮寫。
  
|*datepart* 名稱| *datepart* 縮寫|  
|---|---|
|**year**|**yy, yyyy**|  
|**quarter**|**qq, q**|  
|**month**|**mm, m**|  
|**dayofyear**|**dy, y**|  
|**day**|**dd, d**|  
|**week**|**wk, ww**|  
|**hour**|**hh**|  
|**minute**|**mi, n**|  
|**second**|**ss, s**|  
|**millisecond**|**ms**|  
|**microsecond**|**mcs**|  
|**nanosecond**|**ns**|  

> [!NOTE]
> 每個特定 *datepart* 名稱和該 *datepart* 名稱的縮寫都會傳回相同值。

*startdate*  
可解析成下列其中一個值的運算式：

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2** 
+ **smalldatetime**
+ **time**

針對 *date*，`DATEDIFF_BIG` 會接受資料行運算式、運算式、字串常值或使用者定義變數。 字串常值必須解析成 **datetime**。 請使用四位數年份以避免模糊不清的問題。 `DATEDIFF_BIG` 會從 *enddate* 減去 *startdate*。 若要避免模糊不清，請使用四位數年份。 如需兩位數年份的資訊，請參閱[設定兩位數年份的截止伺服器設定選項](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)。
  
*enddate*  
請參閱＜*startdate*＞。
  
## <a name="return-type"></a>傳回類型  
帶正負號的 **bigint**  
  
## <a name="return-value"></a>傳回值  
會傳回 *startdate* 和 *enddate* 之間的 **bigint** 差異，以 *datepart* 所設定的 coundary 表示。
  
針對超出 **bigint** 範圍 (-9,223,372,036,854,775,808 到 9,223,372,036,854,775,807) 的傳回值，`DATEDIFF_BIG` 會傳回錯誤。 不同於 `DATEDIFF` 會傳回 **int**，因此可使用 **minute** 或更高的精確度進行溢位，`DATEDIFF_BIG` 只能在使用 **nanosecond** 精確度 (其中 *enddate* 和 *startdate* 之間的差距超出 292 年 3 個月 10 天 23 小時 47 分鐘又 16.8547758 秒) 時進行溢位。
  
如果 *startdate* 和 *enddate* 都只獲指派時間值，且 *datepart* 不是時間 *datepart*，`DATEDIFF_BIG` 會傳回 0。
  
`DATEDIFF_BIG` 不會使用 *startdate* 或 *enddate* 的時區時差元件來計算傳回值。
  
針對用於 *startdate* 或 *enddate* 的 **smalldatetime** 值，`DATEDIFF_BIG` 一律會在傳回值中將秒和毫秒設定為 0，因為 [smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md) 的精確度只有到分鐘。
  
如果您只有將時間值指派給日期資料類型變數，`DATEDIFF_BIG` 會將遺漏日期部分的值設定為預設值：`1900-01-01`。 如果您只有將日期值指派給時間或日期資料類型的變數，`DATEDIFF_BIG` 會將遺漏時間部分的值設定為預設值：`00:00:00`。 如果 *startdate* 或 *enddate* 其中之一只有時間部分，而另一個只有日期部分，`DATEDIFF_BIG` 會將遺漏的時間和日期部分設定為預設值。
  
如果 *startdate* 和 *enddate* 具有不同的日期資料類型，而且其中一個項目的時間部分或小數秒數有效位數超過另一個項目，`DATEDIFF_BIG` 會將另一個項目的遺漏部分設定為 0。
  
## <a name="datepart-boundaries"></a>datepart 界限
下列陳述式具有相同的 *startdate* 和相同的 *enddate* 值。 這些日期都很接近且時間差距為一微秒 (.0000001 秒)。 每個陳述式中 *startdate* 與 *enddate* 之間的差異會跨越其 *datepart* 的日曆或時間界限。 每個陳述式都會傳回 1。 如果 *startdate* 和 *enddate* 具有不同年份值但具有相同日曆週值，則 `DATEDIFF_BIG` 會針對 *datepart* **week** 傳回 0。

```sql
SELECT DATEDIFF_BIG(year,        '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF_BIG(quarter,     '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF_BIG(month,       '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF_BIG(dayofyear,   '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF_BIG(day,         '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF_BIG(week,        '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF_BIG(hour,        '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF_BIG(minute,      '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF_BIG(second,      '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF_BIG(millisecond, '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
```
  
## <a name="remarks"></a>備註  
您可以在 `SELECT <list>`、`WHERE`、`HAVING`、`GROUP BY` 和 `ORDER BY` 子句中使用 `DATEDIFF_BIG`。
  
`DATEDIFF_BIG` 會以隱含的方式，將字串常值轉換為 **datetime2** 類型。 這表示，將日期當作字串傳遞時，`DATEDIFF_BIG` 不支援 YDM 格式。 您必須明確地將字串轉換為 **datetime** 或 **smalldatetime** 類型，才能使用 YDM 格式。
  
指定 `SET DATEFIRST` 對 `DATEDIFF_BIG` 沒有任何作用。 `DATEDIFF_BIG` 一律會使用星期天當作一週的第一天，以確保此函式以具決定性的方式運作。

如果 *enddate* 與 *startdate* 的差距傳回超出 **bigint** 範圍的值，則 `DATEDIFF_BIG` 可使用 **nanosecond** 的精確度進行溢位。
  
## <a name="examples"></a>範例 
  
### <a name="specifying-columns-for-startdate-and-enddate"></a>指定 startdate 和 enddate 的資料行  
此範例會使用不同的運算式類型，當作 *startdate* 和 *enddate* 參數的引數。 它會計算資料表的兩個資料行之間的日期已跨越的天數界限。
  
```sql
CREATE TABLE dbo.Duration  
    (startDate datetime2, endDate datetime2);  
    
INSERT INTO dbo.Duration(startDate,endDate)  
    VALUES('2007-05-06 12:10:09', '2007-05-07 12:10:09');  
    
SELECT DATEDIFF_BIG(day, startDate, endDate) AS 'Duration'  
    FROM dbo.Duration;  
-- Returns: 1  
```  

### <a name="finding-difference-between-startdate-and-enddate-as-date-parts-strings"></a>求得 startdate 與 enddate 的差距，並以日期部分字串表示

```sql
DECLARE @date1 DATETIME2, @date2 DATETIME2, @result VARCHAR(100)
DECLARE @years BIGINT, @months BIGINT, @days BIGINT, @hours BIGINT, @minutes BIGINT, @seconds BIGINT, @milliseconds BIGINT

SET @date1 = '0001-01-01 00:00:00.00000000'
SET @date2 = '2018-12-12 07:08:01.12345678'

SELECT @years = DATEDIFF(yy, @date1, @date2)
IF DATEADD(yy, -@years, @date2) < @date1 
SELECT @years = @years-1
SET @date2 = DATEADD(yy, -@years, @date2)

SELECT @months = DATEDIFF(mm, @date1, @date2)
IF DATEADD(mm, -@months, @date2) < @date1 
SELECT @months=@months-1
SET @date2= DATEADD(mm, -@months, @date2)

SELECT @days=DATEDIFF(dd, @date1, @date2)
IF DATEADD(dd, -@days, @date2) < @date1 
SELECT @days=@days-1
SET @date2= DATEADD(dd, -@days, @date2)

SELECT @hours=DATEDIFF(hh, @date1, @date2)
IF DATEADD(hh, -@hours, @date2) < @date1 
SELECT @hours=@hours-1
SET @date2= DATEADD(hh, -@hours, @date2)

SELECT @minutes=DATEDIFF(mi, @date1, @date2)
IF DATEADD(mi, -@minutes, @date2) < @date1 
SELECT @minutes=@minutes-1
SET @date2= DATEADD(mi, -@minutes, @date2)

SELECT @seconds=DATEDIFF(s, @date1, @date2)
IF DATEADD(s, -@seconds, @date2) < @date1 
SELECT @seconds=@seconds-1
SET @date2= DATEADD(s, -@seconds, @date2)

SELECT @milliseconds=DATEDIFF(ms, @date1, @date2)

SELECT @result= ISNULL(CAST(NULLIF(@years,0) AS VARCHAR(10)) + ' years,','')
     + ISNULL(' ' + CAST(NULLIF(@months,0) AS VARCHAR(10)) + ' months,','')    
     + ISNULL(' ' + CAST(NULLIF(@days,0) AS VARCHAR(10)) + ' days,','')
     + ISNULL(' ' + CAST(NULLIF(@hours,0) AS VARCHAR(10)) + ' hours,','')
     + ISNULL(' ' + CAST(@minutes AS VARCHAR(10)) + ' minutes and','')
     + ISNULL(' ' + CAST(@seconds AS VARCHAR(10)) 
          + CASE WHEN @milliseconds > 0 THEN '.' + CAST(@milliseconds AS VARCHAR(10)) 
               ELSE '' END 
          + ' seconds','')

SELECT @result
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)] 

```
2017 years, 11 months, 11 days, 7 hours, 8 minutes and 1.123 seconds
```   

請參閱 [DATEDIFF &#40;Transact-SQL&#41;](../../t-sql/functions/datediff-transact-sql.md) 中更緊密相關的範例。
  
## <a name="see-also"></a>另請參閱
[CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
[DATEDIFF &#40;Transact-SQL&#41;](../../t-sql/functions/datediff-transact-sql.md)
  
  
