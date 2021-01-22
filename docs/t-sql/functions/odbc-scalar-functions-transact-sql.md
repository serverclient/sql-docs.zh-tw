---
description: ODBC 純量函數 (Transact-SQL)
title: ODBC 純量函式 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- CURDATE ODBC function
- DAYOFMONTH ODBC function
- WEEK ODBC function
- functions, ODBC CONCAT
- SECOND ODBC function
- functions, ODBC CURRENT_TIME
- functions, ODBC HOUR
- functions, ODBC QUARTER
- TRUNCATE ODBC function
- functions, ODBC SECOND
- DAYOFWEEK ODBC function
- CURRENT_DATE ODBC function
- DAYNAME ODBC function
- CURTIME ODBC function
- functions, ODBC CURRENT_DATE
- MINUTE ODBC function
- functions, ODBC BIT_LENGTH
- functions, ODBC OCTET_LENGTH
- CONCAT ODBC function
- MONTHNAME ODBC function
- functions, ODBC DAYOFYEAR
- OCTET_LENGTH ODBC function
- functions [SQL Server], ODBC scalar functions
- BIT_LENGTH ODBC function
- functions, ODBC DAYOFMONTH
- CURRENT_TIME ODBC function
- functions, ODBC CURDATE
- functions, ODBC CURTIME
- functions, ODBC TRUNCATE
- functions, ODBC MONTHNAME
- functions, ODBC DAYNAME
- ODBC scalar functions
- functions, ODBC MINUTE
- functions, ODBC DAYOFWEEK
- QUARTER ODBC function
- DAYOFYEAR ODBC function
- functions, ODBC WEEK
- HOUR ODBC function
ms.assetid: a0df1ac2-6699-4ac0-8f79-f362f23496f1
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 0fbc4c09e7d00c8836f7c3efd17dbfe660cd669a
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98597125"
---
# <a name="odbc-scalar-functions-transact-sql"></a>ODBC 純量函數 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  您可以在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式中使用 [ODBC 純量函式](../../odbc/reference/appendixes/appendix-e-scalar-functions.md)。 這些陳述式會由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 進行解譯。 它們可用於預存程序和使用者自訂函數中。 這些項目包括字串、數值、時間、日期、間隔和系統函數。  
  
## <a name="usage"></a>使用方式  
 ```syntaxsql
 SELECT {fn <function_name> [ (<argument>,....n) ] }
 ```
  
## <a name="functions"></a>函數  
 下表將列出不會在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 中重複的 ODBC 純量函式。  
  
### <a name="string-functions"></a>字串函式  
  
|函式|描述|  
|--------------|-----------------|  
|BIT_LENGTH( string_exp ) (ODBC 3.0)|傳回字串運算式的長度 (以位元為單位)。<br /><br /> 傳回指定資料類型的內部大小，而不將 string_exp 轉換成字串。|  
|CONCAT( string_exp1,string_exp2) (ODBC 1.0)|傳回字元字串，表示將 string_exp2 串連至 string_exp1 的結果。 產生的字串為 DBMS 相依。 例如，如果由 string_exp1 所代表的資料行包含 NULL 值，DB2 就會傳回 NULL，但是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回非 NULL 字串。|  
|OCTET_LENGTH( string_exp ) (ODBC 3.0)|傳回字串運算式的長度 (以位元組為單位)。 結果就是不小於位元數的最小整數除以 8。<br /><br /> 傳回指定資料類型的內部大小，而不將 string_exp 轉換成字串。|  
  
### <a name="numeric-function"></a>數值函數  
  
|函式|描述|  
|--------------|-----------------|  
|TRUNCATE( numeric_exp, integer_exp) (ODBC 2.0)|傳回 numeric_exp (截斷至小數點右邊的 integer_exp 個位置)。 若 integer_exp 為負數，則 numeric_exp 會截斷至小數點左邊的 &#124;integer_exp&#124; 個位置。|  
  
### <a name="time-date-and-interval-functions"></a>時間、日期和間隔函數  
  
|函式|描述|  
|--------------|-----------------|  
|CURRENT_DATE( ) (ODBC 3.0)|傳回目前日期。|  
|CURDATE( ) (ODBC 3.0)|傳回目前日期。|  
|CURRENT_TIME`[( time-precision )]` (ODBC 3.0)|傳回目前的當地時間。 其中 time-precision 引數會決定傳回值的秒數有效位數。|  
|CURTIME() (ODBC 3.0)|傳回目前的當地時間。|  
|DAYNAME( date_exp ) (ODBC 2.0)|傳回字元字串，其中針對 date_exp 的日期部分包含資料來源專用的日期名稱。 例如，若為使用英文的資料來源，則名稱為 Sunday 到 Saturday 或 Sun. 到 Sat.； 。 若為使用德文的資料來源，則名稱為 Sonntag 到 Samstag。|
|DAYOFMONTH( date_exp ) (ODBC 1.0)|根據 date_exp 中的月份欄位，以整數值傳回月份的日期。 傳回值的範圍介於 1-31 之間。|  
|DAYOFWEEK( date_exp ) (ODBC 1.0)|根據 date_exp 中的週欄位，以整數值傳回當週的日期。 傳回值的範圍介於 1-7 之間，其中 1 代表星期日。|  
|HOUR( time_exp ) (ODBC 1.0)|根據 time_exp 中的小時欄位，以整數值 (範圍介於 0-23 之間) 傳回小時。|  
|MINUTE( time_exp ) (ODBC 1.0)|根據 time_exp 中的分鐘欄位，以整數值 (範圍介於 0-59 之間) 傳回分鐘。|  
|SECOND( time_exp ) (ODBC 1.0)|根據 time_exp 中的秒數欄位，以整數值 (範圍介於 0-59 之間) 傳回秒數。|  
|MONTHNAME( date_exp ) (ODBC 2.0)|傳回字元字串，其中針對 date_exp 的月份部分包含資料來源專用的月份名稱。 例如，若為使用英文的資料來源，則名稱為 January 到 December 或 Jan. 到 Dec.。 若為使用德文的資料來源，則名稱為 Januar 到 Dezember。|  
|QUARTER( date_exp ) (ODBC 1.0)|傳回 date_exp 中的季度成為整數值 (範圍介於 1-4 之間，其中 1 代表 1 月 1 日到 3 月 31 日)。|  
|WEEK( date_exp ) (ODBC 1.0)|根據 date_exp 中的週欄位，以整數值 (範圍介於 1-53 之間) 傳回當年的週數。|  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-an-odbc-function-in-a-stored-procedure"></a>A. 在預存程序中使用 ODBC 函數  
 下列範例會在預存程序中使用 ODBC 函數：  
  

```sql 
CREATE PROCEDURE dbo.ODBCprocedure  
(  
    @string_exp NVARCHAR(4000)  
)  
AS  
SELECT {fn OCTET_LENGTH( @string_exp )};  
```  
  
### <a name="b-using-an-odbc-function-in-a-user-defined-function"></a>B. 在使用者定義函數中使用 ODBC 函數  
 下列範例會在使用者自訂函數中使用 ODBC 函數：  
  
```sql  
CREATE FUNCTION dbo.ODBCudf  
(  
    @string_exp NVARCHAR(4000)  
)  
RETURNS INT  
AS  
BEGIN  
DECLARE @len INT  
SET @len = (SELECT {fn OCTET_LENGTH( @string_exp )})  
RETURN(@len)  
END ;  
  
SELECT dbo.ODBCudf('Returns the length.');  
--Returns 38  
```  
  
### <a name="c-using-an-odbc-functions-in-select-statements"></a>C. 在 SELECT 陳述式中使用 ODBC 函數  
 下列 SELECT 陳述式會使用 ODBC 函數：  
  
```sql 
DECLARE @string_exp NVARCHAR(4000) = 'Returns the length.';  
SELECT {fn BIT_LENGTH( @string_exp )};  
-- Returns 304  
SELECT {fn OCTET_LENGTH( @string_exp )};  
-- Returns 38  
  
SELECT {fn CONCAT( 'CONCAT ','returns a character string')};  
-- Returns CONCAT returns a character string  
SELECT {fn TRUNCATE( 100.123456, 4)};  
-- Returns 100.123400  
SELECT {fn CURRENT_DATE( )};  
-- Returns 2007-04-20  
SELECT {fn CURRENT_TIME(6)};  
-- Returns 10:27:11.973000  
  
DECLARE @date_exp NVARCHAR(30) = '2007-04-21 01:01:01.1234567';  
SELECT {fn DAYNAME( @date_exp )};  
-- Returns Saturday  
SELECT {fn DAYOFMONTH( @date_exp )};  
-- Returns 21  
SELECT {fn DAYOFWEEK( @date_exp )};  
-- Returns 7  
SELECT {fn HOUR( @date_exp)};  
-- Returns 1   
SELECT {fn MINUTE( @date_exp )};  
-- Returns 1  
SELECT {fn SECOND( @date_exp )};  
-- Returns 1  
SELECT {fn MONTHNAME( @date_exp )};  
-- Returns April  
SELECT {fn QUARTER( @date_exp )};  
-- Returns 2  
SELECT {fn WEEK( @date_exp )};  
-- Returns 16  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-using-an-odbc-function-in-a-stored-procedure"></a>D. 在預存程序中使用 ODBC 函數  
 下列範例會在預存程序中使用 ODBC 函數：  
  
```sql  
CREATE PROCEDURE dbo.ODBCprocedure  
(  
    @string_exp NVARCHAR(4000)  
)  
AS  
SELECT {fn BIT_LENGTH( @string_exp )};  
```  
  
### <a name="e-using-an-odbc-function-in-a-user-defined-function"></a>E. 在使用者定義函數中使用 ODBC 函數  
 下列範例會在使用者自訂函數中使用 ODBC 函數：  
  
```sql  
CREATE FUNCTION dbo.ODBCudf  
(  
    @string_exp NVARCHAR(4000)  
)  
RETURNS INT  
AS  
BEGIN  
DECLARE @len INT  
SET @len = (SELECT {fn BIT_LENGTH( @string_exp )})  
RETURN(@len)  
END ;  
  
SELECT dbo.ODBCudf('Returns the length in bits.');  
--Returns 432  
```  
  
### <a name="f-using-an-odbc-functions-in-select-statements"></a>F. 在 SELECT 陳述式中使用 ODBC 函數  
 下列 SELECT 陳述式會使用 ODBC 函數：  
  
```sql  
DECLARE @string_exp NVARCHAR(4000) = 'Returns the length.';  
SELECT {fn BIT_LENGTH( @string_exp )};  
-- Returns 304  
  
SELECT {fn CONCAT( 'CONCAT ','returns a character string')};  
-- Returns CONCAT returns a character string  
SELECT {fn CURRENT_DATE( )};  
-- Returns today's date  
SELECT {fn CURRENT_TIME(6)};  
-- Returns the time  
  
DECLARE @date_exp NVARCHAR(30) = '2007-04-21 01:01:01.1234567';  
SELECT {fn DAYNAME( @date_exp )};  
-- Returns Saturday  
SELECT {fn DAYOFMONTH( @date_exp )};  
-- Returns 21  
SELECT {fn DAYOFWEEK( @date_exp )};  
-- Returns 7  
SELECT {fn HOUR( @date_exp)};  
-- Returns 1   
SELECT {fn MINUTE( @date_exp )};  
-- Returns 1  
SELECT {fn SECOND( @date_exp )};  
-- Returns 1  
SELECT {fn MONTHNAME( @date_exp )};  
-- Returns April  
SELECT {fn QUARTER( @date_exp )};  
-- Returns 2  
SELECT {fn WEEK( @date_exp )};  
-- Returns 16  
```  
  
## <a name="see-also"></a>另請參閱  
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)