---
title: 日期與時間資料類型與函數
description: 日期與時間資料類型與函數文章的連結。
titleSuffix: SQL Server (Transact-SQL)
ms.custom: seo-lt-2019
ms.date: 09/01/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- dates [SQL Server]
- date and time [SQL Server], all data types and functions
- date and time [SQL Server]
- functions [SQL Server], time
- functions [SQL Server], date and time
- time [SQL Server], functions
ms.assetid: 83e378a2-6e89-4c80-bc4f-644958d9e0a9
author: cawrites
ms.author: chadam
monikerRange: = azure-sqldw-latest||= azuresqldb-current || >= sql-server-2016 || >= sql-server-linux-2017
ms.openlocfilehash: 461b4db55574fcad2fb5ff7250ce88f508999fa6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102608"
---
# <a name="date-and-time-data-types-and-functions-transact-sql"></a>日期和時間資料類型與函數 (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

本主題中的各節涵蓋所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間資料類型與函式。

- [日期和時間資料類型](#DateandTimeDataTypes)
- [日期和時間函式](#DateandTimeFunctions)
  - [傳回系統日期和時間值的函式](#GetSystemDateandTimeValues)
  - [傳回日期和時間部分的函式](#GetDateandTimeParts)
  - [從各自部分取得日期和時間值的函式](#fromParts)
  - [傳回日期和時間差異值的函式](#GetDateandTimeDifference)
  - [修改日期和時間值的函式](#ModifyDateandTimeValues)
  - [設定或傳回工作階段格式函式的函式](#SetorGetSessionFormatFunctions)
  - [驗證日期和時間值的函式](#ValidateDateandTimeValues)
- [與日期和時間相關的主題](#DateandTimeRelatedTopics)

## <a name="date-and-time-data-types"></a><a name="DateandTimeDataTypes"></a> 日期和時間資料類型

[!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間資料類型會列在下表中：

|資料類型|[格式]|範圍|精確度|儲存體大小 (位元組)|使用者自訂的小數秒數有效位數|時區位移|
|---|---|---|---|---|---|---|
|[time](../../t-sql/data-types/time-transact-sql.md)|hh:mm:ss[.nnnnnnn]|00:00:00.0000000 到 23:59:59.9999999|100 奈秒|3 到 5|是|否|
|[date](../../t-sql/data-types/date-transact-sql.md)|YYYY-MM-DD|0001-01-01 到 31.12.99|1 日|3|否|否|
|[smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md)|YYYY-MM-DD hh:mm:ss|1900-01-01 到 2079-06-06|1 分鐘|4|否|否|
|[datetime](../../t-sql/data-types/datetime-transact-sql.md)|YYYY-MM-DD hh:mm:ss[.nnn]|1753-01-01 到 9999-12-31|0.00333 秒鐘|8|否|否|
|[datetime2](../../t-sql/data-types/datetime2-transact-sql.md)|YYYY-MM-DD hh:mm:ss[.nnnnnnn]|0001-01-01 00:00:00.0000000 到 9999-12-31 23:59:59.9999999|100 奈秒|6 到 8|是|否|
|[datetimeoffset](../../t-sql/data-types/datetimeoffset-transact-sql.md)|YYYY-MM-DD hh:mm:ss[.nnnnnnn] [+&#124;-]hh:mm|0001-01-01 00:00:00.0000000 到 9999-12-31 23:59:59.9999999 (以 UTC 為單位)|100 奈秒|8 到 10|是|是|

> [!NOTE]
> [!INCLUDE[tsql](../../includes/tsql-md.md)] [rowversion](../../t-sql/data-types/rowversion-transact-sql.md) 資料類型不是日期或時間資料類型。 **timestamp** 是 **rowversion** 的已取代同義字。

## <a name="date-and-time-functions"></a><a name="DateandTimeFunctions"></a> 日期和時間函式

下表列出 [!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間函式。 如需決定性的詳細資訊，請參閱[決定性與非決定性函式](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)。

### <a name="function-that-return-system-date-and-time-values"></a><a name="GetSystemDateandTimeValues"></a> 傳回系統日期和時間值的函式

[!INCLUDE[tsql](../../includes/tsql-md.md)] 會從執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之電腦的作業系統衍生所有系統日期和時間值。

#### <a name="higher-precision-system-date-and-time-functions"></a>較高精確度的系統日期和時間函數

[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 會透過使用 GetSystemTimeAsFileTime() Windows API 來衍生日期和時間值。 精確度取決於執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的電腦硬體和 Windows 版本。 此 API 的精確度是固定於 100 奈秒。 請使用 GetSystemTimeAdjustment() Windows API 來判斷正確性。

|函式|語法|傳回值|傳回資料類型|決定性|
|---|---|---|---|---|
|[SYSDATETIME](../../t-sql/functions/sysdatetime-transact-sql.md)|SYSDATETIME ()|傳回 **datetime2(7)** 值，此值包含執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之電腦的日期和時間。 傳回的值不包含時區時差。|**datetime2(7)**|不具決定性|
|[SYSDATETIMEOFFSET](../../t-sql/functions/sysdatetimeoffset-transact-sql.md)|SYSDATETIMEOFFSET ( )|傳回 **datetimeoffset(7)** 值，此值包含執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之電腦的日期和時間。 傳回的值包含時區時差。|**datetimeoffset(7)**|不具決定性|
|[SYSUTCDATETIME](../../t-sql/functions/sysutcdatetime-transact-sql.md)|SYSUTCDATETIME ( )|傳回 **datetime2(7)** 值，此值包含執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之電腦的日期和時間。 此函式是以國際標準時間 (Coordinated Universal Time，UTC) 傳回日期和時間值。|**datetime2(7)**|不具決定性|

#### <a name="lower-precision-system-date-and-time-functions"></a>較低精確度的系統日期和時間函數

|函式|語法|傳回值|傳回資料類型|決定性|
|---|---|---|---|---|
|[CURRENT_TIMESTAMP](../../t-sql/functions/current-timestamp-transact-sql.md)|CURRENT_TIMESTAMP|傳回 **datetime** 值，此值包含執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之電腦的日期和時間。 傳回的值不包含時區時差。|**datetime**|不具決定性|
|[GETDATE](../../t-sql/functions/getdate-transact-sql.md)|GETDATE ( )|傳回 **datetime** 值，此值包含執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之電腦的日期和時間。 傳回的值不包含時區時差。|**datetime**|不具決定性|
|[GETUTCDATE](../../t-sql/functions/getutcdate-transact-sql.md)|GETUTCDATE ( )|傳回 **datetime** 值，此值包含執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之電腦的日期和時間。 此函式是以國際標準時間 (Coordinated Universal Time，UTC) 傳回日期和時間值。|**datetime**|不具決定性|

### <a name="functions-that-return-date-and-time-parts"></a><a name="GetDateandTimeParts"></a> 傳回日期和時間部分的函式

|函式|語法|傳回值|傳回資料類型|決定性|
|--------------|------------|------------------|----------------------|-----------------|
|[DATENAME](../../t-sql/functions/datename-transact-sql.md)|DATENAME ( *datepart* , *date* )|傳回字元字串，代表指定日期的指定 *datepart*。|**nvarchar**|不具決定性|
|[DATEPART](../../t-sql/functions/datepart-transact-sql.md)|DATEPART ( *datepart* , *date* )|傳回一個整數，代表指定 *date* 的指定 *datepart*。|**int**|不具決定性|
|[DAY](../../t-sql/functions/day-transact-sql.md)|DAY ( *date* )|傳回一個整數，代表指定 *date* 的日 (Day) 部分。|**int**|具決定性|
|[MONTH](../../t-sql/functions/month-transact-sql.md)|MONTH ( *date* )|傳回一個整數，代表指定 *date* 的月 (Month) 部分。|**int**|具決定性|
|[YEAR](../../t-sql/functions/year-transact-sql.md)|YEAR ( *date* )|傳回一個整數，代表指定 *date* 的年 (Year) 部分。|**int**|具決定性|

### <a name="functions-that-return-date-and-time-values-from-their-parts"></a><a name="fromParts"></a> 從各自部分取得日期和時間值的函式

|函式|語法|傳回值|傳回資料類型|決定性|
|---|---|---|---|---|
|[DATEFROMPARTS](../../t-sql/functions/datefromparts-transact-sql.md)|DATEFROMPARTS ( *year*, *month*, *day* )|傳回指定之年、月、日的 **date** 值。|**date**|具決定性|
|[DATETIME2FROMPARTS](../../t-sql/functions/datetime2fromparts-transact-sql.md)|DATETIME2FROMPARTS ( *year*, *month*, *day*, *hour*, *minute*, *seconds*, *fractions*, *precision*)|以指定的精確度傳回指定日期與時間的 **datetime2** 值。|**datetime2(** _precision_ **)**|具決定性|
|[DATETIMEFROMPARTS](../../t-sql/functions/datetimefromparts-transact-sql.md)|DATETIMEFROMPARTS ( *year*, *month*, *day*, *hour*, *minute*, *seconds*, *milliseconds*)|傳回指定日期和時間的 **datetime** 值。|**datetime**|具決定性|
|[DATETIMEOFFSETFROMPARTS](../../t-sql/functions/datetimeoffsetfromparts-transact-sql.md)|DATETIMEOFFSETFROMPARTS ( *year*, *month*, *day*, *hour*, *minute*, *seconds*, *fractions*, *hour_offset*, *minute_offset*, *precision*)|以指定的時差和精確度傳回指定日期和時間的 **datetimeoffset** 值。|**datetimeoffset(** _precision_ **)**|具決定性|
|[SMALLDATETIMEFROMPARTS](../../t-sql/functions/smalldatetimefromparts-transact-sql.md)|SMALLDATETIMEFROMPARTS ( *year*, *month*, *day*, *hour*, *minute* )|傳回指定日期和時間的 **smlldatetime** 值。|**smalldatetime**|具決定性|
|[TIMEFROMPARTS](../../t-sql/functions/timefromparts-transact-sql.md)|TIMEFROMPARTS ( *hour*, *minute*, *seconds*, *fractions*, *precision* )|以指定的精確度傳回指定時間的 **time** 值。|**time(** _precision_ **)**|具決定性|

### <a name="functions-that-return-date-and-time-difference-values"></a><a name="GetDateandTimeDifference"></a> 傳回日期和時間差異值的函式

|函式|語法|傳回值|傳回資料類型|決定性|
|---|---|---|---|---|
|[DATEDIFF](../../t-sql/functions/datediff-transact-sql.md)|DATEDIFF ( *datepart* , *startdate* , *enddate* )|傳回跨越兩個指定日期的日期或時間 *datepart* 界限數字。|**int**|具決定性|
|[DATEDIFF_BIG](../../t-sql/functions/datediff-big-transact-sql.md)|DATEDIFF_BIG ( *datepart* , *startdate* , *enddate* )|傳回跨越兩個指定日期的日期或時間 *datepart* 界限數字。|**bigint**|具決定性|

### <a name="functions-that-modify-date-and-time-values"></a><a name="ModifyDateandTimeValues"></a> 修改日期和時間值的函式

|函式|語法|傳回值|傳回資料類型|決定性|
|---|---|---|---|---|
|[DATEADD](../../t-sql/functions/dateadd-transact-sql.md)|DATEADD (*datepart* , *number* , *date* )|透過在指定 *date* 的指定 *datepart* 中新增間隔，傳回新的 **datetime** 值。|*date* 引數的資料類型|具決定性|
|[EOMONTH](../../t-sql/functions/eomonth-transact-sql.md)|EOMONTH ( *start_date* [, *month_to_add* ] )|以選擇性位移，傳回包含指定日期的當月最後一天。|傳回類型是 *start_date* 引數的類型，或者是 **date** 資料類型。|具決定性|
|[SWITCHOFFSET](../../t-sql/functions/switchoffset-transact-sql.md)|SWITCHOFFSET (*DATETIMEOFFSET* , *time_zone*)|SWITCHOFFSET 會變更 DATETIMEOFFSET 值的時區時差，並保留 UTC 值。|具有 *DATETIMEOFFSET* 之毫秒精確度的 **datetimeoffset**|具決定性|
|[TODATETIMEOFFSET](../../t-sql/functions/todatetimeoffset-transact-sql.md)|TODATETIMEOFFSET (*expression* , *time_zone*)|TODATETIMEOFFSET 會將 datetime2 值轉換成 datetimeoffset 值。 *TODATETIMEOFFSET* 會針對指定的 time_zone 以當地時間解譯 datetime2 值。|具有 *datetime* 引數之毫秒精確度的 **datetimeoffset**|具決定性|

### <a name="functions-that-set-or-return-session-format-functions"></a><a name="SetorGetSessionFormatFunctions"></a> 設定或傳回工作階段格式函式的函式

|函式|語法|傳回值|傳回資料類型|決定性|
|---|---|---|---|---|
|[@@DATEFIRST](../../t-sql/functions/datefirst-transact-sql.md)|@@DATEFIRST|傳回 SET DATEFIRST 之工作階段的目前值。|**tinyint**|不具決定性|
|[SET DATEFIRST](../../t-sql/statements/set-datefirst-transact-sql.md)|SET DATEFIRST { *number* &#124; **\@** _number_var_ }|將一週的第一天設為 1-7 其中一個數字。|不適用|不適用|
|[SET DATEFORMAT](../../t-sql/statements/set-dateformat-transact-sql.md)|SET DATEFORMAT { *format* &#124; **@** _format_var_ }|設定輸入 **datetime** 或 **smalldatetime** 資料時，日期部分 (月/日/年) 的順序。|不適用|不適用|
|[@@LANGUAGE](../../t-sql/functions/language-transact-sql.md)|@@LANGUAGE|傳回目前使用中的語言名稱。 @@LANGUAGE 不是日期或時間函式。 不過，語言設定可能會影響日期函數的輸出。|不適用|不適用|
|[SET LANGUAGE](../../t-sql/statements/set-language-transact-sql.md)|SET LANGUAGE { [ N ] **'** _language_ **'** &#124; **\@** _language_var_ }|設定工作階段和系統訊息的語言環境。 SET LANGUAGE 不是日期或時間函數。 不過，語言設定會影響日期函數的輸出。|不適用|不適用|
|[sp_helplanguage](../../relational-databases/system-stored-procedures/sp-helplanguage-transact-sql.md)|**sp_helplanguage** [ [ **@language =** ] **'** _language_ **'** ]|傳回所有支援語言之日期格式的詳細資訊。 **sp_helplanguage** 不是日期或時間預存程序。 不過，語言設定會影響日期函數的輸出。|不適用|不適用|

### <a name="functions-that-validate-date-and-time-values"></a><a name="ValidateDateandTimeValues"></a> 驗證日期和時間值的函式

|函式|語法|傳回值|傳回資料類型|決定性|
|---|---|---|---|---|
|[ISDATE](../../t-sql/functions/isdate-transact-sql.md)|ISDATE ( *expression* )|判斷 **datetime** 或 **smalldatetime** 輸入運算式是否具有有效的日期或時間值。|**int**|只有在搭配 CONVERT 函數使用、已指定 CONVERT 樣式參數，而且樣式不等於 0、100、9 或 109 時，ISDATE 才具有決定性。|

## <a name="date-and-time-related-topics"></a><a name="DateandTimeRelatedTopics"></a> 與日期和時間相關的主題

|主題|描述|
|-----------|-----------------|
|[FORMAT](../../t-sql/functions/format-transact-sql.md)|傳回以指定格式與選擇性文化特性所格式化的值。 將 FORMAT 函數用於將日期/時間與數值視為字串的地區設定感知格式化作業。|
|[CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)|提供將日期和時間值在字串常值與其他日期和時間格式之間來回轉換的相關資訊。|
|[撰寫國際通用的 Transact-SQL 陳述式](../../relational-databases/collations/write-international-transact-sql-statements.md)|提供一些指導方針，讓使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的資料庫與資料庫應用程式能從某種語言移植至另一種語言，或可支援多種語言。|
|[ODBC 純量函式 &#40;Transact-SQL&#41;](../../t-sql/functions/odbc-scalar-functions-transact-sql.md)|提供可用於 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式之 ODBC 純量函式的相關資訊。 這包括 ODBC 日期和時間函式。|
|[AT TIME ZONE &#40;Transact-SQL&#41;](../../t-sql/queries/at-time-zone-transact-sql.md)|提供時區轉換。|

## <a name="see-also"></a>另請參閱

- [函數](../../t-sql/functions/functions.md)
- [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)
