---
title: FORMAT (Transact-SQL) | Microsoft Docs
description: FORMAT 函式的 Transact-SQL 參考。
ms.date: 08/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FORMAT_TSQL
- FORMAT
dev_langs:
- TSQL
helpviewer_keywords:
- FORMAT function
ms.assetid: dad6f24c-b8d9-4dbe-a561-9b167b8f20c8
author: cawrites
ms.author: chadam
monikerRange: = azuresqldb-current||>= sql-server-2016||>= sql-server-linux-2017||=azure-sqldw-latest
ms.openlocfilehash: 9c247a8316e99373add9d34614d3f6ff4af85c07
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98597347"
---
# <a name="format-transact-sql"></a>FORMAT (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

傳回以指定格式與選擇性文化特性所格式化的值。 將 FORMAT 函數用於將日期/時間與數值視為字串的地區設定感知格式化作業。 針對一般資料類型轉換，請使用 CAST 或 CONVERT。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
FORMAT( value, format [, culture ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

 *value*  
 要格式化之受支援資料類型的運算式。 如需有效類型的清單，請參閱下面＜備註＞一節中的表格。  
  
 *format*  
 **nvarchar** 格式模式。  
  
 *format* 引數必須包含有效的 .NET Framework 格式字串，其可以是標準格式字串 (例如 "C" 或 "D")，也可以是日期與數值的自訂字元模式 (如 "MMMM DD, yyyy (dddd)")。 不支援複合格式。 如需這些格式模式的完整說明，請參閱 .NET Framework 文件中有關一般字串格式、自訂日期與時間格式，以及自訂數字格式的資訊。 您不妨從「[格式類型](/dotnet/standard/base-types/formatting-types)」主題開始著手。  
  
 *culture*  
 指定文化特性的選用 **nvarchar** 引數。  
  
 如未提供 *culture* 引數，將會使用目前工作階段的語言。 此語言是以 SET LANGUAGE 陳述式隱含或明確加以設定。 *culture* 的引數可以是 .NET Framework 所支援的任何文化特性，而不限於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 明確支援的語言。 如果 *culture* 引數無效，FORMAT 會引發錯誤。  
  
## <a name="return-types"></a>傳回型別

 **nvarchar** 或 null  
  
 傳回值的長度取決於 *format*。  
  
## <a name="remarks"></a>備註

 FORMAT 會針對不是 *valid* 的 *culture* 以外的錯誤傳回 NULL。 例如，如果 *format* 中指定的值無效，則會傳回 NULL。  

 FORMAT 函數不具決定性。
  
 FORMAT 必須仰賴既存的 .NET Framework Common Language Runtime (CLR)。  
  
 因為必須要有 CLR 才可以執行此函數，所以無法從遠端進行。 從遠端處理需要 CLR 的函數可能會導致遠端伺服器發生錯誤。  
  
 FORMAT 會依賴 CLR 格式規則，這些規則規定必須逸出冒號和句號。 因此，當格式字串 (第二個參數) 包含冒號或句號時，如果輸入值 (第一個參數) 屬於 **time** 資料類型，則必須以反斜線逸出冒號或句號。 請參閱 [D. 具有 time 資料類型的 FORMAT](#ExampleD)。  
  
 下表列出 *value* 引數可接受的資料類型，以及其 .NET Framework 對應的對等類型。  
  
|類別|類型|.NET 類型|  
|--------------|----------|---------------|  
|數值|BIGINT|Int64|  
|數值|int|Int32|  
|數值|SMALLINT|Int16|  
|數值|TINYINT|Byte|  
|數值|decimal|SqlDecimal|  
|數值|NUMERIC|SqlDecimal|  
|數值|FLOAT|Double|  
|數值|real|Single|  
|數值|SMALLMONEY|Decimal|  
|數值|money|Decimal|  
|日期及時間|date|Datetime|  
|日期及時間|time|TimeSpan|  
|日期及時間|Datetime|Datetime|  
|日期及時間|smalldatetime|Datetime|  
|日期及時間|datetime2|Datetime|  
|日期及時間|datetimeoffset|DateTimeOffset|  
  
## <a name="examples"></a>範例  
  
### <a name="a-simple-format-example"></a>A. 簡單的 FORMAT 範例

 下列範例會傳回針對不同文化特性格式化的簡單日期。  
  
```sql  
DECLARE @d DATE = '11/22/2020';
SELECT FORMAT( @d, 'd', 'en-US' ) 'US English'  
      ,FORMAT( @d, 'd', 'en-gb' ) 'Great Britain English'  
      ,FORMAT( @d, 'd', 'de-de' ) 'German'  
      ,FORMAT( @d, 'd', 'zh-cn' ) 'Simplified Chinese (PRC)';  
  
SELECT FORMAT( @d, 'D', 'en-US' ) 'US English'  
      ,FORMAT( @d, 'D', 'en-gb' ) 'Great Britain English'  
      ,FORMAT( @d, 'D', 'de-de' ) 'German'  
      ,FORMAT( @d, 'D', 'zh-cn' ) 'Chinese (Simplified PRC)';  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
US English  Great Britain English German     Simplified Chinese (PRC)  
----------  --------------------- ---------- ------------------------  
11/22/2020  22/11/2020            22.11.2020 2020/11/22 
  
US English                  Great Britain English  German                      Chinese (Simplified PRC)  
--------------------------- ---------------------- --------------------------  ---------------------------------------  
Sunday, November 22, 2020   22 November 2020       Sonntag, 22. November 2020  2020年11月22日  
  
```  
  
### <a name="b-format-with-custom-formatting-strings"></a>B. 包含自訂格式字串的 FORMAT

 下列範例會藉由指定自訂格式顯示格式數值。 此範例假設目前的日期為 2012 年 9 月 27 日。 如需有關這些自訂格式和其他自訂格式的詳細資訊，請參閱[自訂數值格式字串](/dotnet/standard/base-types/custom-numeric-format-strings)。  
  
```sql  
DECLARE @d DATE = GETDATE();  
SELECT FORMAT( @d, 'dd/MM/yyyy', 'en-US' ) AS 'Date'  
       ,FORMAT(123456789,'###-##-####') AS 'Custom Number';  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
Date        Custom Number  
----------  -------------  
22/11/2020  123-45-6789  
  
```  
  
### <a name="c-format-with-numeric-types"></a>C. 數值類型的 FORMAT

 下列範例會從 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 **Sales.CurrencyRate** 資料表傳回 5 個資料列。 **EndOfDateRate** 資料行會以 **money** 類型，儲存在資料表中。 在此範例中，資料行會以未格式化的狀態傳回，然後藉由指定 .NET Number 格式、General 格式和 Currency 格式類型進行格式化。 如需有關這些數值格式和其他數值格式的詳細資訊，請參閱[標準數值格式字串](/dotnet/standard/base-types/standard-numeric-format-strings)。  
  
```sql  
SELECT TOP(5) CurrencyRateID, EndOfDayRate  
            ,FORMAT(EndOfDayRate, 'N', 'en-us') AS 'Number Format'  
            ,FORMAT(EndOfDayRate, 'G', 'en-us') AS 'General Format'  
            ,FORMAT(EndOfDayRate, 'C', 'en-us') AS 'Currency Format'  
FROM Sales.CurrencyRate  
ORDER BY CurrencyRateID;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
CurrencyRateID EndOfDayRate  Numeric Format  General Format  Currency Format  
-------------- ------------  --------------  --------------  ---------------  
1              1.0002        1.00            1.0002          $1.00  
2              1.55          1.55            1.5500          $1.55  
3              1.9419        1.94            1.9419          $1.94  
4              1.4683        1.47            1.4683          $1.47  
5              8.2784        8.28            8.2784          $8.28  
  
```  
  
 此範例會指定德文文化特性 (de-de)。  
  
```sql  
SELECT TOP(5) CurrencyRateID, EndOfDayRate  
      ,FORMAT(EndOfDayRate, 'N', 'de-de') AS 'Numeric Format'  
      ,FORMAT(EndOfDayRate, 'G', 'de-de') AS 'General Format'  
      ,FORMAT(EndOfDayRate, 'C', 'de-de') AS 'Currency Format'  
FROM Sales.CurrencyRate  
ORDER BY CurrencyRateID;  
```  
  
```
CurrencyRateID EndOfDayRate  Numeric Format  General Format  Currency Format  
-------------- ------------  --------------  --------------  ---------------  
1              1.0002        1,00            1,0002          1,00 &euro;  
2              1.55          1,55            1,5500          1,55 &euro;  
3              1.9419        1,94            1,9419          1,94 &euro;  
4              1.4683        1,47            1,4683          1,47 &euro;  
5              8.2784        8,28            8,2784          8,28 &euro;  
  
```  
  
### <a name="d-format-with-time-data-types"></a><a name="ExampleD"></a> D. 具有 time 資料類型的 FORMAT

 FORMAT 會在這些情況下傳回 NULL，因為未逸出 `.` 和 `:`。  
  
```sql  
SELECT FORMAT(cast('07:35' as time), N'hh.mm');   --> returns NULL  
SELECT FORMAT(cast('07:35' as time), N'hh:mm');   --> returns NULL  
```  
  
 Format 會傳回格式化的字串，因為會逸出 `.` 和 `:`。  
  
```sql  
SELECT FORMAT(cast('07:35' as time), N'hh\.mm');  --> returns 07.35  
SELECT FORMAT(cast('07:35' as time), N'hh\:mm');  --> returns 07:35  
```  

Format 會傳回格式化的目前時間，並含指定的 AM 或 PM

```sql
SELECT FORMAT(SYSDATETIME(), N'hh:mm tt'); -- returns 03:46 PM
SELECT FORMAT(SYSDATETIME(), N'hh:mm t'); -- returns 03:46 P
```

Format 會傳回指定的時間，並顯示 AM

```sql
select FORMAT(CAST('2018-01-01 01:00' AS datetime2), N'hh:mm tt') -- returns 01:00 AM
select FORMAT(CAST('2018-01-01 01:00' AS datetime2), N'hh:mm t')  -- returns 01:00 A
```

Format 會傳回指定的時間，並顯示 PM

```sql
select FORMAT(CAST('2018-01-01 14:00' AS datetime2), N'hh:mm tt') -- returns 02:00 PM
select FORMAT(CAST('2018-01-01 14:00' AS datetime2), N'hh:mm t') -- returns 02:00 P
```
  
Format 會以 24 小時制格式傳回指定的時間

```sql
select FORMAT(CAST('2018-01-01 14:00' AS datetime2), N'HH:mm') -- returns 14:00
```
  
## <a name="see-also"></a>另請參閱

- [CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
- [STR &#40;Transact-SQL&#41;](../../t-sql/functions/str-transact-sql.md)  
- [字串函數 &#40;Transact-SQL&#41;](../../t-sql/functions/string-functions-transact-sql.md)