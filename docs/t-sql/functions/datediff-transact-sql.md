---
title: DATEDIFF (Transact-SQL) | Microsoft Docs
description: DATEDIFF 函式的 Transact-SQL 參考。 以 datepart 為基礎，傳回開始與結束日期之間的數值差異。
ms.date: 07/18/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DATEDIFF_TSQL
- DATEDIFF
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- DATEDIFF function [SQL Server]
- time [SQL Server], crossed boundaries
- differences in date and time [SQL Server]
- counting crossed date time boundaries [SQL Server]
- date and time [SQL Server], DATEDIFF
- dates [SQL Server], crossed boundaries
- boundary differences date and time [SQL Server]
- functions [SQL Server], time
- functions [SQL Server], date and time
- interval dates [SQL Server]
- time [SQL Server], functions
- crossing date time boundaries [SQL Server]
- calculating dates times [SQL Server]
ms.assetid: eba979f2-1a8d-4cce-9d75-b74f9b519b37
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 744e0f900b0ff71c908cc32b7946b0b95f1061bc
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100502"
---
# <a name="datediff-transact-sql"></a>DATEDIFF (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函式會傳回跨越指定 *startdate* 和 *enddate* 之指定 datepart 界限的計數 (作為帶正負號的整數值)。
  
如需處理 *startdate* 與 *enddate* 值之間較大差異的函式，請參閱 [DATEDIFF_BIG &#40;Transact-SQL&#41;](../../t-sql/functions/datediff-big-transact-sql.md)。 如需所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間資料類型與函式的概觀，請參閱[日期和時間資料類型與函式 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DATEDIFF ( datepart , startdate , enddate )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*datepart*  
**DATEDIFF** 所報告 _startdate_ 和 _enddate_ 之間差異的單位。 常用的 _datepart_ 單位包括 `month` 或 `second`。

_Datepart_ 值不能在變數中指定，也不能是以引號括住的字串 (例如 `'month'`)。

下表列出所有有效的 _datepart_ 值。 **DATEDIFF** 接受 _datepart_ 的完整名稱，或任何所列出的完整名稱縮寫。

|*datepart* 名稱|*datepart* 縮寫|  
|-----------|------------|
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
| &nbsp; | &nbsp; |

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
  
請使用四位數年份以避免模糊不清。 如需兩位數年份值的資訊，請參閱[設定兩位數年份的截止伺服器設定選項](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)。
  
*enddate*  
請參閱＜*startdate*＞。
  
## <a name="return-type"></a>傳回類型  
 **int**  
  
## <a name="return-value"></a>傳回值  

*startdate* 和 *enddate* 之間的 **int** 差異，以 *datepart* 所設定的界限表示。
  
例如，`SELECT DATEDIFF(day, '2036-03-01', '2036-02-28');` 會傳回-2，提示 2036 必須是閏年。 這種情況表示如果我們從 _startdate_ '2036-03-01' 開始，然後計數 -2 天，則 _enddate_ 會是 '2036-02-28'。
  
針對超出 **int** 範圍 (-2,147,483,648 到 +2,147,483,647) 的傳回值，`DATEDIFF` 會傳回錯誤。  針對 **millisecond**，*startdate* 和 *enddate* 最大的差異為 24 天 20 小時 31 分鐘 23.647 秒。 針對 **second**，最大的差異為 68 年 19 天 3 小時 14 分鐘 7 秒。
  
如果 *startdate* 和 *enddate* 都只獲指派時間值，且 *datepart* 不是時間 *datepart*，`DATEDIFF` 會傳回 0。
  
`DATEDIFF` 會使用 *startdate* 或 *enddate* 的時區時差元件來計算傳回值。
  
由於 [smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md) 的精確度只有到分鐘，因此當 *startdate* 或 *enddate* 具有 **smalldatetime** 值時，秒和毫秒就一律會在傳回值中設定為 0。
  
如果您只有將時間值指派給日期資料類型變數，`DATEDIFF` 會將遺漏日期部分的值設定為預設值：`1900-01-01`。 如果您只有將日期值指派給時間或日期資料類型的變數，`DATEDIFF` 會將遺漏時間部分的值設定為預設值：`00:00:00`。 如果 *startdate* 或 *enddate* 其中之一只有時間部分，而另一個只有日期部分，`DATEDIFF` 會將遺漏的時間和日期部分設定為預設值。
  
如果 *startdate* 和 *enddate* 具有不同的日期資料類型，而且其中一個項目的時間部分或小數秒數有效位數超過另一個項目，`DATEDIFF` 會將另一個項目的遺漏部分設定為 0。
  
## <a name="_datepart_-boundaries"></a>_datepart_ 界限

下列陳述式具有相同的 *startdate* 和相同的 *enddate* 值。 這些日期都很接近且時間差距為一百奈秒 (.0000001 秒)。 每個陳述式中 *startdate* 與 *enddate* 之間的差異會跨越其 *datepart* 的日曆或時間界限。 每個陳述式都會傳回 1。
  
```sql
SELECT DATEDIFF(year,        '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(quarter,     '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(month,       '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(dayofyear,   '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(day,         '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(week,        '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(hour,        '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(minute,      '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(second,      '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(millisecond, '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
SELECT DATEDIFF(microsecond, '2005-12-31 23:59:59.9999999', '2006-01-01 00:00:00.0000000');
```

如果 *startdate* 和 *enddate* 具有不同年份值，但具有相同日曆週值，則 `DATEDIFF` 會針對 *datepart* **week** 傳回 0。

## <a name="remarks"></a>備註  
您可以在 `SELECT <list>`、`WHERE`、`HAVING`、`GROUP BY` 和 `ORDER BY` 子句中使用 `DATEDIFF`。
  
`DATEDIFF` 會以隱含的方式，將字串常值轉換為 **datetime2** 類型。 這表示，將日期當作字串傳遞時，`DATEDIFF` 不支援 YDM 格式。 您必須明確地將字串轉換為 **datetime** 或 **smalldatetime** 類型，才能使用 YDM 格式。
  
指定 `SET DATEFIRST` 對 `DATEDIFF` 沒有任何作用。 `DATEDIFF` 一律會使用星期天當作一週的第一天，以確保此函式以具決定性的方式運作。

如果 *enddate* 與 *startdate* 的差距傳回超出 **int** 範圍的值，則 `DATEDIFF` 可使用 **minute** 或更高的精確度進行溢位。
  
## <a name="examples"></a>範例  
這些範例會使用不同的運算式類型，當作 *startdate* 和 *enddate* 參數的引數。
  
### <a name="a-specifying-columns-for-startdate-and-enddate"></a>A. 指定 startdate 和 enddate 的資料行  
此範例會計算資料表的兩個資料行日期之間跨越界限的天數。
  
```sql
CREATE TABLE dbo.Duration  
    (startDate datetime2, endDate datetime2);  
    
INSERT INTO dbo.Duration(startDate, endDate)  
    VALUES ('2007-05-06 12:10:09', '2007-05-07 12:10:09');  
    
SELECT DATEDIFF(day, startDate, endDate) AS 'Duration'  
    FROM dbo.Duration;  
-- Returns: 1  
```  
  
### <a name="b-specifying-user-defined-variables-for-startdate-and-enddate"></a>B. 指定 startdate 和 enddate 的使用者自訂變數  
在此範例中，會以使用者定義的變數作為 *startdate* 和 *enddate* 的引數。
  
```sql
DECLARE @startdate DATETIME2 = '2007-05-05 12:10:09.3312722';  
DECLARE @enddate   DATETIME2 = '2007-05-04 12:10:09.3312722';   
SELECT DATEDIFF(day, @startdate, @enddate);  
```  
  
### <a name="c-specifying-scalar-system-functions-for-startdate-and-enddate"></a>C. 指定 startdate 和 enddate 的純量系統函數  
此範例會使用純量系統函數，當作 *startdate* 和 *enddate* 的引數。
  
```sql
SELECT DATEDIFF(millisecond, GETDATE(), SYSDATETIME());  
```  
  
### <a name="d-specifying-scalar-subqueries-and-scalar-functions-for-startdate-and-enddate"></a>D. 指定 startdate 和 enddate 的純量子查詢和純量函數  
此範例會使用純量子查詢和純量函數，當作 *startdate* 和 *enddate* 的引數。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT DATEDIFF(day,
    (SELECT MIN(OrderDate) FROM Sales.SalesOrderHeader),  
    (SELECT MAX(OrderDate) FROM Sales.SalesOrderHeader));  
```  
  
### <a name="e-specifying-constants-for-startdate-and-enddate"></a>E. 指定 startdate 和 enddate 的常數  
此範例會使用字元常數，當作 *startdate* 和 *enddate* 的引數。
  
```sql
SELECT DATEDIFF(day,
   '2007-05-07 09:53:01.0376635',
   '2007-05-08 09:53:01.0376635');  
```  
  
### <a name="f-specifying-numeric-expressions-and-scalar-system-functions-for-enddate"></a>F. 指定 enddate 的數值運算式和純量系統函數  
此範例會使用數值運算式 `(GETDATE() + 1)` 和純量系統函數 `GETDATE` 與 `SYSDATETIME`，當作 *enddate* 的引數。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT DATEDIFF(day, '2007-05-07 09:53:01.0376635', GETDATE() + 1)
    AS NumberOfDays  
    FROM Sales.SalesOrderHeader;  
GO  
USE AdventureWorks2012;  
GO  
SELECT
    DATEDIFF(
            day,
            '2007-05-07 09:53:01.0376635',
            DATEADD(day, 1, SYSDATETIME())
        ) AS NumberOfDays  
    FROM Sales.SalesOrderHeader;  
GO  
```  
  
### <a name="g-specifying-ranking-functions-for-startdate"></a>G. 指定 startdate 的排名函數  
此範例會使用次序函數，當作 *startdate* 的引數。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT p.FirstName, p.LastName  
    ,DATEDIFF(day, ROW_NUMBER() OVER (ORDER BY   
        a.PostalCode), SYSDATETIME()) AS 'Row Number'  
FROM Sales.SalesPerson s   
    INNER JOIN Person.Person p   
        ON s.BusinessEntityID = p.BusinessEntityID  
    INNER JOIN Person.Address a   
        ON a.AddressID = p.BusinessEntityID  
WHERE TerritoryID IS NOT NULL   
    AND SalesYTD <> 0;  
```  
  
### <a name="h-specifying-an-aggregate-window-function-for-startdate"></a>H. 指定 startdate 的彙總視窗函數  
此範例會使用彙總視窗函式，當作 *startdate* 的引數。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT soh.SalesOrderID, sod.ProductID, sod.OrderQty, soh.OrderDate,
    DATEDIFF(day, MIN(soh.OrderDate)   
        OVER(PARTITION BY soh.SalesOrderID), SYSDATETIME()) AS 'Total'  
FROM Sales.SalesOrderDetail sod  
    INNER JOIN Sales.SalesOrderHeader soh  
        ON sod.SalesOrderID = soh.SalesOrderID  
WHERE soh.SalesOrderID IN(43659, 58918);  
GO  
```  

### <a name="i-finding-difference-between-startdate-and-enddate-as-date-parts-strings"></a>I. 求得 startdate 與 enddate 的差距，並以日期部分字串表示

```sql
-- DOES NOT ACCOUNT FOR LEAP YEARS
DECLARE @date1 DATETIME, @date2 DATETIME, @result VARCHAR(100);
DECLARE @years INT, @months INT, @days INT,
    @hours INT, @minutes INT, @seconds INT, @milliseconds INT;

SET @date1 = '1900-01-01 00:00:00.000'
SET @date2 = '2018-12-12 07:08:01.123'

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
     + CASE
            WHEN @milliseconds > 0
                THEN '.' + CAST(@milliseconds AS VARCHAR(10)) 
            ELSE ''
       END 
     + ' seconds','')

SELECT @result
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)] 

```
118 years, 11 months, 11 days, 7 hours, 8 minutes and 1.123 seconds
```
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
這些範例會使用不同的運算式類型，當作 *startdate* 和 *enddate* 參數的引數。
  
### <a name="j-specifying-columns-for-startdate-and-enddate"></a>J. 指定 startdate 和 enddate 的資料行  
此範例會計算資料表的兩個資料行日期之間跨越界限的天數。
  
```sql
CREATE TABLE dbo.Duration 
    (startDate datetime2, endDate datetime2);
    
INSERT INTO dbo.Duration (startDate, endDate)  
    VALUES ('2007-05-06 12:10:09', '2007-05-07 12:10:09');  
    
SELECT TOP(1) DATEDIFF(day, startDate, endDate) AS Duration  
    FROM dbo.Duration;  
-- Returns: 1  
```  
  
### <a name="k-specifying-scalar-subqueries-and-scalar-functions-for-startdate-and-enddate"></a>K. 指定 startdate 和 enddate 的純量子查詢和純量函數  
此範例會使用純量子查詢和純量函數，當作 *startdate* 和 *enddate* 的引數。
  
```sql
-- Uses AdventureWorks  
  
SELECT TOP(1) DATEDIFF(day, (SELECT MIN(HireDate) FROM dbo.DimEmployee),  
    (SELECT MAX(HireDate) FROM dbo.DimEmployee))   
FROM dbo.DimEmployee;  
  
```  
  
### <a name="l-specifying-constants-for-startdate-and-enddate"></a>L. 指定 startdate 和 enddate 的常數  
此範例會使用字元常數，當作 *startdate* 和 *enddate* 的引數。
  
```sql
-- Uses AdventureWorks  
  
SELECT TOP(1) DATEDIFF(day,
    '2007-05-07 09:53:01.0376635',
    '2007-05-08 09:53:01.0376635') FROM DimCustomer;  
```  
  
### <a name="m-specifying-ranking-functions-for-startdate"></a>M. 指定 startdate 的排名函數  
此範例會使用次序函數，當作 *startdate* 的引數。
  
```sql
-- Uses AdventureWorks  
  
SELECT FirstName, LastName,
    DATEDIFF(day, ROW_NUMBER() OVER (ORDER BY   
        DepartmentName), SYSDATETIME()) AS RowNumber  
FROM dbo.DimEmployee;  
```  
  
### <a name="n-specifying-an-aggregate-window-function-for-startdate"></a>N. 指定 startdate 的彙總視窗函數  
此範例會使用彙總視窗函式，當作 *startdate* 的引數。
  
```sql
-- Uses AdventureWorks  
  
SELECT FirstName, LastName, DepartmentName,
    DATEDIFF(year, MAX(HireDate)  
        OVER (PARTITION BY DepartmentName), SYSDATETIME()) AS SomeValue  
FROM dbo.DimEmployee  
```  
  
## <a name="see-also"></a>另請參閱
[DATEDIFF_BIG &#40;Transact-SQL&#41;](../../t-sql/functions/datediff-big-transact-sql.md)  
[CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
