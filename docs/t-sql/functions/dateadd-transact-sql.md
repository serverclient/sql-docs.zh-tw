---
title: DATEADD (Transact-SQL) | Microsoft Docs
description: DATEADD 函式的 Transact-SQL 參考。 此函式會傳回已由指定日期部分所修改的日期。
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DATEADD
- DATEADD_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- add interval to date or time [SQL Server]
- subtract interval from date or time [SQL Server]
- functions [SQL Server], time
- functions [SQL Server], date and time
- time [SQL Server], functions
- dates [SQL Server], intervals
- date and time [SQL Server], DATEADD
- DATEADD function [SQL Server]
ms.assetid: 89c5ae32-89c6-47e1-979e-15d97908b9f1
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4edc9ff57e9f8d1bd875df6d62814b3317c7ac45
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093675"
---
# <a name="dateadd-transact-sql"></a>DATEADD (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函式會將指定的 *number* 值(以帶正負號的整數形式) 加到輸入 *date* 值的指定 *datepart*，然後傳回該修改過的值。
  
如需所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間資料類型與函式的概觀，請參閱[日期和時間資料類型與函式 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```sql
DATEADD (datepart , number , date )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*datepart*  
`DATEADD` 要加上 **整數** *number* 的 *date* 部分。 此表格會列出所有有效的 *datepart* 引數。 

> [!NOTE]
> `DATEADD` 不會接受 *datepart* 引數的使用者定義變數對等項目。 
  
|*datepart*|縮寫|  
|---|---|
|**year**|**yy**, **yyyy**|  
|**quarter**|**qq**, **q**|  
|**month**|**mm**, **m**|  
|**dayofyear**|**dy**, **y**|  
|**day**|**dd**, **d**|  
|**week**|**wk**, **ww**|  
|**weekday**|**dw**, **w**|  
|**hour**|**hh**|  
|**minute**|**mi**, **n**|  
|**second**|**ss**, **s**|  
|**millisecond**|**ms**|  
|**microsecond**|**mcs**|  
|**nanosecond**|**ns**|  
  
*number*  
可解析成 [int](../../t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql.md) (`DATEADD` 要加到 *date* 的 *datepart*) 的運算式。 `DATEADD` 接受 *number* 的使用者定義變數值。 `DATEADD` 將會截斷具有十進位小數的指定 *number* 值。 在此情況下，不會捨入 *number* 值。
  
*date*  
可解析成下列其中一個值的運算式： 

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2** 
+ **smalldatetime**
+ **time**

針對 *date*，`DATEADD` 會接受資料行運算式、運算式、字串常值或使用者定義變數。 字串常值必須解析成 **datetime**。 請使用四位數年份以避免模糊不清的問題。 如需兩位數年份的資訊，請參閱[設定兩位數年份的截止伺服器設定選項](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)。
  
## <a name="return-types"></a>傳回類型

這個方法的傳回值資料類型為動態。 傳回型別取決於提供給 `date` 的引數而定。 如果 `date` 的值是字串常值日期，則 `DATEADD` 會傳回 **datetime** 值。 如果提供其他有效輸入資料類型給 `date`，則 `DATEADD` 會傳回相同的資料類型。 如果字串常值的秒數小數位數超過三個小數位數位置 (.nnn)，者字串常值包含時區時差部分，則 `DATEADD` 會引發錯誤。
  
## <a name="return-value"></a>傳回值  
  
## <a name="datepart-argument"></a>datepart 引數  
**dayofyear**、**day** 和 **weekday** 都會傳回相同的值。
  
每個 *datepart* 及其縮寫都會傳回相同的值。
  
如果下列條件成立：

+ *datepart* 是 **月份**
+ *date* 月的天數多於傳回月份
+ *date* 日不存在於傳回月份

`DATEADD` 則會傳回該傳回月份的最後一天。 例如，九月有 30 (三十) 天。因此，這些陳述式會傳回 2006-09-30 00:00:00.000：
  
```sql
SELECT DATEADD(month, 1, '20060830');
SELECT DATEADD(month, 1, '20060831');
```
  
## <a name="number-argument"></a>number 引數  
*number* 引數不得超過 **int** 的範圍。在下列陳述式中，*number* 引數超過 **int** 的範圍 (超過 1)。 這些陳述式都會傳回下列錯誤訊息："`Msg 8115, Level 16, State 2, Line 1. Arithmetic overflow error converting expression to data type int."`
  
```sql
SELECT DATEADD(year,2147483648, '20060731');  
SELECT DATEADD(year,-2147483649, '20060731');  
```  
  
## <a name="date-argument"></a>date 引數  
`DATEADD` 將不會接受遞增至超過其資料類型範圍值的 *date*。 在下列陳述式中，加到 *date* 值的 *number* 值超過 *date* 資料類型的範圍。 `DATEADD` 會傳回下列錯誤訊息："`Msg 517, Level 16, State 1, Line 1 Adding a value to a 'datetime' column caused overflow`"。
  
```sql
SELECT DATEADD(year,2147483647, '20060731');  
SELECT DATEADD(year,-2147483647, '20060731');  
```  
  
## <a name="return-values-for-a-smalldatetime-date-and-a-second-or-fractional-seconds-datepart"></a>smalldatetime date 和 second 或小數秒數 datepart 的傳回值  
[smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md) 值的秒數部分一律為 00。 對於 **smalldatetime** *date* 值，適用下列情況： 

-   如果是 **second** 的 *datepart*，且 *number* 介於 -30 到 +29 之間，`DATEADD` 不會變更。  
-   如果是 **second** 的 *datepart*，且 *number* 值小於 -30 或大於 +29，`DATEADD` 會從一分鐘開始執行加法。  
-   如果是 **millisecond** 的 *datepart*，且 *number* 介於 -30001 到 +29998 之間，`DATEADD` 不會變更。  
-   如果是 **millisecond** 的 *datepart*，且 *number* 值小於 -30001 或大於 +29998，`DATEADD` 會從一分鐘開始執行加法。  
  
## <a name="remarks"></a>備註  
在下列子句中使用 `DATEADD`：

+ GROUP BY
+ HAVING
+ 排序依據
+ SELECT \<list>
+ WHERE
  
## <a name="fractional-seconds-precision"></a>小數秒數有效位數
`DATEADD`不允許針對 *date* 資料類型 **smalldatetime**、**date** 和 **datetime** 執行 **microsecond** 或 **nanosecond** 的 *datepart* 加法。
  
毫秒具有小數位數 3 (.123)，微秒具有小數位數 6 (.123456)，奈秒具有小數位數 9 (.123456789)。 **time**、**datetime2** 和 **datetimeoffset** 資料類型都具有最大小數位數 7 (.1234567)。 如果是 **nanosecond** 的 *datepart*，在 *date* 的小數秒數增加之前，*number* 必須是 100。 1 與 49 之間的 *number* 會無條件捨去成 0，而 50 到 99 的 number 會無條件進位到 100。
  
這些陳述式會加上 **millisecond**、**microsecond** 或 **nanosecond** 的 *datepart*。
  
```sql
DECLARE @datetime2 datetime2 = '2007-01-01 13:10:10.1111111';  
SELECT '1 millisecond', DATEADD(millisecond,1,@datetime2)  
UNION ALL  
SELECT '2 milliseconds', DATEADD(millisecond,2,@datetime2)  
UNION ALL  
SELECT '1 microsecond', DATEADD(microsecond,1,@datetime2)  
UNION ALL  
SELECT '2 microseconds', DATEADD(microsecond,2,@datetime2)  
UNION ALL  
SELECT '49 nanoseconds', DATEADD(nanosecond,49,@datetime2)  
UNION ALL  
SELECT '50 nanoseconds', DATEADD(nanosecond,50,@datetime2)  
UNION ALL  
SELECT '150 nanoseconds', DATEADD(nanosecond,150,@datetime2);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
1 millisecond     2007-01-01 13:10:10.1121111  
2 milliseconds    2007-01-01 13:10:10.1131111  
1 microsecond     2007-01-01 13:10:10.1111121  
2 microseconds    2007-01-01 13:10:10.1111131  
49 nanoseconds    2007-01-01 13:10:10.1111111  
50 nanoseconds    2007-01-01 13:10:10.1111112  
150 nanoseconds   2007-01-01 13:10:10.1111113  
```  
  
## <a name="time-zone-offset"></a>時區位移
`DATEADD` 不允許針對時區時差執行加法。
  
## <a name="examples"></a>範例  

### <a name="a-incrementing-datepart-by-an-interval-of-1"></a>A. 以 1 為間隔遞增 datepart  
以下每個陳述式都會以 1 為間隔遞增 *datepart*：
  
```sql
DECLARE @datetime2 datetime2 = '2007-01-01 13:10:10.1111111';  
SELECT 'year', DATEADD(year,1,@datetime2)  
UNION ALL  
SELECT 'quarter',DATEADD(quarter,1,@datetime2)  
UNION ALL  
SELECT 'month',DATEADD(month,1,@datetime2)  
UNION ALL  
SELECT 'dayofyear',DATEADD(dayofyear,1,@datetime2)  
UNION ALL  
SELECT 'day',DATEADD(day,1,@datetime2)  
UNION ALL  
SELECT 'week',DATEADD(week,1,@datetime2)  
UNION ALL  
SELECT 'weekday',DATEADD(weekday,1,@datetime2)  
UNION ALL  
SELECT 'hour',DATEADD(hour,1,@datetime2)  
UNION ALL  
SELECT 'minute',DATEADD(minute,1,@datetime2)  
UNION ALL  
SELECT 'second',DATEADD(second,1,@datetime2)  
UNION ALL  
SELECT 'millisecond',DATEADD(millisecond,1,@datetime2)  
UNION ALL  
SELECT 'microsecond',DATEADD(microsecond,1,@datetime2)  
UNION ALL  
SELECT 'nanosecond',DATEADD(nanosecond,1,@datetime2);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Year         2008-01-01 13:10:10.1111111  
quarter      2007-04-01 13:10:10.1111111  
month        2007-02-01 13:10:10.1111111  
dayofyear    2007-01-02 13:10:10.1111111  
day          2007-01-02 13:10:10.1111111  
week         2007-01-08 13:10:10.1111111  
weekday      2007-01-02 13:10:10.1111111  
hour         2007-01-01 14:10:10.1111111  
minute       2007-01-01 13:11:10.1111111  
second       2007-01-01 13:10:11.1111111  
millisecond  2007-01-01 13:10:10.1121111  
microsecond  2007-01-01 13:10:10.1111121  
nanosecond   2007-01-01 13:10:10.1111111  
```  
  
### <a name="b-incrementing-more-than-one-level-of-datepart-in-one-statement"></a>B. 在單一陳述式中遞增一個以上的 datepart 層級  
以下每個陳述式都會利用足以同時遞增 *date* 中下一個較高 *datepart* 的 *number* 來遞增 *datepart*：
  
```sql
DECLARE @datetime2 datetime2;  
SET @datetime2 = '2007-01-01 01:01:01.1111111';  
--Statement                                 Result     
-------------------------------------------------------------------   
SELECT DATEADD(quarter,4,@datetime2);     --2008-01-01 01:01:01.1111111  
SELECT DATEADD(month,13,@datetime2);      --2008-02-01 01:01:01.1111111  
SELECT DATEADD(dayofyear,365,@datetime2); --2008-01-01 01:01:01.1111111  
SELECT DATEADD(day,365,@datetime2);       --2008-01-01 01:01:01.1111111  
SELECT DATEADD(week,5,@datetime2);        --2007-02-05 01:01:01.1111111  
SELECT DATEADD(weekday,31,@datetime2);    --2007-02-01 01:01:01.1111111  
SELECT DATEADD(hour,23,@datetime2);       --2007-01-02 00:01:01.1111111  
SELECT DATEADD(minute,59,@datetime2);     --2007-01-01 02:00:01.1111111  
SELECT DATEADD(second,59,@datetime2);     --2007-01-01 01:02:00.1111111  
SELECT DATEADD(millisecond,1,@datetime2); --2007-01-01 01:01:01.1121111  
```  
  
### <a name="c-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>C. 使用運算式當做 number 和 date 參數的引數  
這些範例會使用不同的運算式類型，作為 *number* 和 *date* 參數的引數。 範例使用的是 AdventureWorks 資料庫。
  
#### <a name="specifying-a-column-as-date"></a>指定資料行成為 date  
此範例會將 `2` 兩天加到 `OrderDate` 資料行中的每個值，以便衍生名為 `PromisedShipDate` 的新資料行：
  
```sql
SELECT SalesOrderID  
    ,OrderDate   
    ,DATEADD(day,2,OrderDate) AS PromisedShipDate  
FROM Sales.SalesOrderHeader;  
```  
  
部分結果集：
  
```
SalesOrderID OrderDate               PromisedShipDate  
------------ ----------------------- -----------------------  
43659        2005-07-01 00:00:00.000 2005-07-03 00:00:00.000  
43660        2005-07-01 00:00:00.000 2005-07-03 00:00:00.000  
43661        2005-07-01 00:00:00.000 2005-07-03 00:00:00.000  
...  
43702        2005-07-02 00:00:00.000 2005-07-04 00:00:00.000  
43703        2005-07-02 00:00:00.000 2005-07-04 00:00:00.000  
43704        2005-07-02 00:00:00.000 2005-07-04 00:00:00.000  
43705        2005-07-02 00:00:00.000 2005-07-04 00:00:00.000  
43706        2005-07-03 00:00:00.000 2005-07-05 00:00:00.000  
...  
43711        2005-07-04 00:00:00.000 2005-07-06 00:00:00.000  
43712        2005-07-04 00:00:00.000 2005-07-06 00:00:00.000  
...  
43740        2005-07-11 00:00:00.000 2005-07-13 00:00:00.000  
43741        2005-07-12 00:00:00.000 2005-07-14 00:00:00.000  
  
```  
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>指定使用者自訂變數成為 number 和 date  
此範例會將使用者定義變數指定為 *number* 和 *date* 的引數：
  
```sql
DECLARE @days INT = 365,   
        @datetime DATETIME = '2000-01-01 01:01:01.111'; /* 2000 was a leap year */;  
SELECT DATEADD(day, @days, @datetime);  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
-----------------------  
2000-12-31 01:01:01.110  
  
(1 row(s) affected)  
```  
  
#### <a name="specifying-scalar-system-function-as-date"></a>指定純量系統函數成為 date  
這個範例會針對 *date* 指定 `SYSDATETIME`。 傳回的精確值取決於陳述式執行的日期和時間：
  
```sql
SELECT DATEADD(month, 1, SYSDATETIME());  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
---------------------------  
2013-02-06 14:29:59.6727944  
  
(1 row(s) affected)  
```  
  
#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>指定純量子查詢和純量函數成為 number 和 date  
此範例會使用純量子查詢 `MAX(ModifiedDate)`，作為 *number* 和 *date* 的引數。 `(SELECT TOP 1 BusinessEntityID FROM Person.Person)` 會作為 number 參數的人工引數，以示範如何從值清單中選取 *number* 引數。
  
```sql
SELECT DATEADD(month,(SELECT TOP 1 BusinessEntityID FROM Person.Person),  
    (SELECT MAX(ModifiedDate) FROM Person.Person));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>指定數值運算式和純量系統函數成為 number 和 date  
此範例會使用數值運算式 (-`(10/2))`、[一元運算子](../../mdx/unary-operators.md) (`-`)、[算數運算子](../../mdx/arithmetic-operators.md) (`/`) 和純量系統函式 (`SYSDATETIME`)，作為 *number* 和 *date* 的引數。
  
```sql
SELECT DATEADD(month,-(10/2), SYSDATETIME());  
```  
  
#### <a name="specifying-ranking-functions-as-number"></a>指定排名函數成為 number  
此範例會使用次序函數，作為 *number* 的引數。
  
```sql
SELECT p.FirstName, p.LastName  
    ,DATEADD(day,ROW_NUMBER() OVER (ORDER BY  
        a.PostalCode),SYSDATETIME()) AS 'Row Number'  
FROM Sales.SalesPerson AS s   
    INNER JOIN Person.Person AS p   
        ON s.BusinessEntityID = p.BusinessEntityID  
    INNER JOIN Person.Address AS a   
        ON a.AddressID = p.BusinessEntityID  
WHERE TerritoryID IS NOT NULL   
    AND SalesYTD <> 0;  
```  
  
#### <a name="specifying-an-aggregate-window-function-as-number"></a>指定彙總視窗函數成為 number  
此範例會使用彙總視窗函式，作為 *number* 的引數。
  
```sql
SELECT SalesOrderID, ProductID, OrderQty  
    ,DATEADD(day,SUM(OrderQty)   
        OVER(PARTITION BY SalesOrderID),SYSDATETIME()) AS 'Total'  
FROM Sales.SalesOrderDetail   
WHERE SalesOrderID IN(43659,43664);  
GO  
```  
  
  
## <a name="see-also"></a>另請參閱
[CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
  

