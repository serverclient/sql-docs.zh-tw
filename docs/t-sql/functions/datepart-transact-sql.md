---
title: DATEPART (Transact-SQL) | Microsoft Docs
description: DATEPART 函式的 Transact-SQL 參考。 此函式會傳回對應指定日期的 datepart 整數。
ms.date: 07/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DATEPART_TSQL
- DATEPART
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- date and time [SQL Server], DATEPART
- dateparts [SQL Server]
- functions [SQL Server], time
- functions [SQL Server], date and time
- time [SQL Server], functions
- dateparts [SQL Server], datepart
- comparing dates times [SQL Server]
- DATEPART function [SQL Server]
- dates [SQL Server], dateparts
ms.assetid: 15f1a5bc-4c0c-4c48-848d-8ec03473e6c1
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: adae6be66b149f930bee51c636bf86d7614e3bf2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093658"
---
# <a name="datepart-transact-sql"></a>DATEPART (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]


此函式會傳回整數，代表指定 *date* 的指定 *datepart*。
  
如需所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間資料類型與函式的概觀，請參閱[日期和時間資料類型與函式 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DATEPART ( datepart , date )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*datepart*  
*date* 引數的特定部分，其 `DATEPART` 會傳回 **整數**。 此表格會列出所有有效的 *datepart* 引數。

> [!NOTE]
> `DATEPART` 不會接受 *datepart* 引數的使用者定義變數對等項目。
  
|*datepart*|縮寫|  
|---|---|
|**year**|**yy**, **yyyy**|  
|**quarter**|**qq**, **q**|  
|**month**|**mm**, **m**|  
|**dayofyear**|**dy**, **y**|  
|**day**|**dd**, **d**|  
|**week**|**wk**, **ww**|  
|**weekday**|**dw**|  
|**hour**|**hh**|  
|**minute**|**mi, n**|  
|**second**|**ss**, **s**|  
|**millisecond**|**ms**|  
|**microsecond**|**mcs**|  
|**nanosecond**|**ns**|  
|**tzoffset**|**tz**|  
|**iso_week**|**isowk**、**isoww**|  
  
*date*  
可解析成下列其中一個資料類型的運算式： 

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2** 
+ **smalldatetime**
+ **time**

針對 *date*，`DATEPART` 會接受資料行運算式、運算式、字串常值或使用者定義變數。 請使用四位數年份以避免模糊不清的問題。 如需兩位數年份的資訊，請參閱[設定兩位數年份的截止伺服器設定選項](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)。
  
## <a name="return-type"></a>傳回類型  
 **int**  
  
## <a name="return-value"></a>傳回值  
每個 *datepart* 及其縮寫都會傳回相同的值。
  
傳回值會取決於使用 [SET LANGUAGE](../../t-sql/statements/set-language-transact-sql.md) 及登入的[設定 default language 伺服器設定選項](../../database-engine/configure-windows/configure-the-default-language-server-configuration-option.md)而設定的語言環境，而有所不同。 如果 *date* 是某些格式的字串常值，傳回值就會取決於 [SET DATEFORMAT](../../t-sql/statements/set-dateformat-transact-sql.md)。 當 date 是日期或時間資料類型的資料行運算式時，SET DATEFORMAT 並不會變更傳回值。
  
此表格針對陳述式 `SELECT DATEPART(datepart,'2007-10-30 12:15:32.1234567 +05:10')` 列出所有 *datepart* 引數和對應的傳回值。 *date* 引數具有 **datetimeoffset(7)** 資料類型。 **nanosecond** *datepart* 傳回值的最後兩個位數一定是 `00`，而且此值具有 9 位數的小數位數：

**.123456700**
  
|*datepart*|傳回值|  
|---|---|
|**year、yyyy、yy**|2007|  
|**quarter、qq、q**|4|  
|**month、mm、m**|10|  
|**dayofyear、dy、y**|303|  
|**day、dd、d**|30|  
|**week、wk、ww**|44|  
|**weekday、dw**|3|  
|**hour、hh**|12|  
|**minute、n**|15|  
|**second、ss、s**|32|  
|**millisecond、ms**|123|  
|**microsecond、mcs**|123456|  
|**nanosecond、ns**|123456700|  
|**tzoffset、tz**|310|  
|**iso_week、isowk、isoww**|44|  
  
## <a name="week-and-weekday-datepart-arguments"></a>Week 和 weekday datepart 引數
針對 **week** (**wk**、**ww**) 或 **weekday** (**dw**) *datepart*，`DATEPART` 傳回值會取決於 [SET DATEFIRST](../../t-sql/statements/set-datefirst-transact-sql.md) 所設定的值。
  
任何一年的 1 月 1 日皆定義 **week** _datepart_ 的起始數字。 例如：

DATEPART (**wk**, 'Jan 1, *xxx* x') = 1

其中 *xxxx* 是任何一年。
  
這個表格會針對每個 SET DATEFIRST 引數，顯示 '2007-04-21' 的 **week** 和 **weekday** *datepart* 傳回值。 2007 年 1 月 1 日是星期一。 2007 年 4 月 21 日是星期六。 針對美國英文，

`SET DATEFIRST 7 -- ( Sunday )`

可作為預設值。 設定 DATEFIRST 之後，請使用針對 datepart 資料表值建議的這個 SQL 陳述式：

`SELECT DATEPART(week, '2007-04-21 '), DATEPART(weekday, '2007-04-21 ')`
  
|SET DATEFIRST<br /><br /> 引數|week<br /><br /> 傳回|weekday<br /><br /> 傳回|  
|---|---|---|
|1|16|6|  
|2|17|5|  
|3|17|4|  
|4|17|3|  
|5|17|2|  
|6|17|1|  
|7|16|7|  
  
## <a name="year-month-and-day-datepart-arguments"></a>year、month 和 day datepart 引數  
針對 DATEPART (**year**、*date*)、DATEPART (**month**、*date*) 和 DATEPART (**day**、*date*) 所傳回的值分別與 [YEAR](../../t-sql/functions/year-transact-sql.md)、[MONTH](../../t-sql/functions/month-transact-sql.md) 和 [DAY](../../t-sql/functions/day-transact-sql.md) 函式傳回的值相同。
  
## <a name="iso_week-datepart"></a>iso_week datepart  
ISO 8601 包含 ISO 週-日期系統 (週數的編號系統)。 每一週都與星期四所在的年份相關聯。 例如，2004 年第 1 週 (2004W01) 從 2003 年 12 月 29 日星期一到 2004 年 1 月 4 日星期日結束。 歐洲國家/地區通常會使用這種編號樣式。 非歐洲國家/地區通常不會使用。

注意：一年的最高週數可能是 52 或 53。
  
不同國家/地區的編號系統可能不符合 ISO 標準。 此表格顯示六種可能性：
  
|每週的第一天|一年第一週包含|週指派兩次|使用於|  
|---|---|---|---|
|星期日|1 月 1 日，<br /><br /> 第一個星期六，<br /><br /> 一年的第 1-7 天|是|美國|  
|星期一|1 月 1 日，<br /><br /> 第一個星期日，<br /><br /> 一年的第 1-7 天|是|大部分歐洲國家 (地區) 和英國|  
|星期一|1 月 4 日，<br /><br /> 第一個星期四，<br /><br /> 一年的第 4-7 天|否|ISO 8601、挪威和瑞典|  
|星期一|1 月 7 日，<br /><br /> 第一個星期一，<br /><br /> 一年的第 7 天|否||  
|星期三|1 月 1 日，<br /><br /> 第一個星期二，<br /><br /> 一年的第 1-7 天|是||  
|星期六|1 月 1 日，<br /><br /> 第一個星期五，<br /><br /> 一年的第 1-7 天|是||  
  
## <a name="tzoffset"></a>tzoffset  
`DATEPART` 會以分鐘數 (帶正負號) 傳回 **tzoffset** (**tz**) 值。 此陳述式會傳回 310 分鐘的時區位移：
  
```sql
SELECT DATEPART (tzoffset, '2007-05-10  00:00:01.1234567 +05:10');  
```  
`DATEPART` 轉譯 tzoffset 值的方式如下：
- 針對 datetimeoffset 和 datetime2，tzoffset 傳回的時間位移會以分鐘為單位，而 datetime2 的位移一律是 0 分鐘。
- 針對可以隱含轉換成 **datetimeoffset** 或 **datetime2** 的資料類型，`DATEPART` 會以分鐘數傳回時間位移。 例外：其他日期/時間資料類型。
- 所有其他類型的參數都會導致錯誤。
  
  
## <a name="smalldatetime-date-argument"></a>smalldatetime date 引數  
針對 [smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md) *date* 值，`DATEPART` 會以 00 形式傳回秒數。
  
## <a name="default-returned-for-a-datepart-that-is-not-in-a-date-argument"></a>針對不在 date 引數中的 datepart 所傳回的預設值  
如果 *date* 引數的資料類型沒有指定的 *datepart*，只有在為 *date* 指定常值時，`DATEPART` 才會傳回該 *datepart* 的預設值。
  
例如，任何 **date** 資料類型的預設年-月-日都是 1900-01-01。 此陳述式具有 *datepart* 的日期部分引數、*date* 的時間引數，而且會傳回 `1900, 1, 1, 1, 2`。
  
```sql
SELECT DATEPART(year, '12:10:30.123')  
    ,DATEPART(month, '12:10:30.123')  
    ,DATEPART(day, '12:10:30.123')  
    ,DATEPART(dayofyear, '12:10:30.123')  
    ,DATEPART(weekday, '12:10:30.123');  
```  
  
如果 *date* 指定為變數或資料表資料行，而該變數或資料行的資料類型沒有指定的 *datepart*，`DATEPART` 會傳回錯誤 9810。 在此範例中，變數 *\@t* 具有 **time** 資料類型。 此範例會失敗，因為 DATEPART year 對 **time** 資料類型無效：
  
```sql
DECLARE @t time = '12:10:30.123';   
SELECT DATEPART(year, @t);  
```  
  
## <a name="fractional-seconds"></a>小數秒數
這些陳述式顯示 `DATEPART` 會傳回小數秒數：
  
```sql
SELECT DATEPART(millisecond, '00:00:01.1234567'); -- Returns 123  
SELECT DATEPART(microsecond, '00:00:01.1234567'); -- Returns 123456  
SELECT DATEPART(nanosecond,  '00:00:01.1234567'); -- Returns 123456700  
```  
  
## <a name="remarks"></a>備註  
`DATEPART` 可用於 SELECT 清單、WHERE、HAVING、GROUP BY 和 ORDER BY 子句中。
  
DATEPART 會隱含地將字串常值轉換為 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中的 **datetime2** 類型。 這表示，將日期當做字串傳遞時，DATENAME 不支援 YDM 格式。 您必須明確地將字串轉換為 **datetime** 或 **smalldatetime** 類型，才能使用 YDM 格式。
  
## <a name="examples"></a>範例  
此範例會傳回基底年份。 基底年份可協助日期計算。 在此範例中，日期是由數字指定。 請注意，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將 0 解譯為 1900 年 1 月 1 日。
  
```sql
SELECT DATEPART(year, 0), DATEPART(month, 0), DATEPART(day, 0);  

-- Returns: 1900    1    1 
```  
  
此範例會傳回日期 `12/20/1974` 的日期部分。
  
```sql
-- Uses AdventureWorks  
  
SELECT TOP(1) DATEPART (day,'12/20/1974') FROM dbo.DimCustomer;  

-- Returns: 20
```  
  
此範例會傳回日期 `12/20/1974` 的年份部分。
  
```sql
-- Uses AdventureWorks  
  
SELECT TOP(1) DATEPART (year,'12/20/1974') FROM dbo.DimCustomer;  

-- Returns: 1974
```  
  
## <a name="see-also"></a>另請參閱
[CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
  
