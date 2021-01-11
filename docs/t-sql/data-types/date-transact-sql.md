---
description: 日期 (Transact-SQL)
title: date (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/23/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- date_TSQL
- date
dev_langs:
- TSQL
helpviewer_keywords:
- data types [SQL Server], date
- date and time [SQL Server], date
- dates [SQL Server], data types
- date data type [SQL Server]
- data types [SQL Server], date and time
ms.assetid: c963e8b4-5a85-4bd0-9d48-3f8da8f6516b
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7970166e9e9f0fa6b3c584d7c7d0ea82d81c5958
ms.sourcegitcommit: f2a28b761022e46da850595553dfbc3e98df83a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97721092"
---
# <a name="date-transact-sql"></a>日期 (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

定義 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的日期。

## <a name="date-description"></a>日期描述
  
|屬性|值|  
|--------------|-----------|  
|語法|**date**|  
|使用量|DECLARE \@MyDate **date**<br /><br /> CREATE TABLE Table1 ( Column1 **date** )|  
|預設的字串常值格式<br /><br /> (用於下層用戶端)|YYYY-MM-DD<br /><br /> 如需詳細資訊，請參閱下列的＜下層用戶端的回溯相容性＞一節。|  
|範圍|0001-01-01 到 9999-12-31 (Informatica 則為 1582-10-15 到 9999-12-31)<br /><br /> 西元 1 年 1 月 1 日到西元 9999 年 12 月 31 日 (在計算機科學則為西元 1582 年 10 月 15 日到西元 9999 年 12 月 31 日)|  
|元素範圍|YYYY 是代表年份的四位數，範圍介於 0001 至 9999 之間。 若是 Informatica，YYYY 限於 1582 至 9999 的範圍之間。<br /><br /> MM 是代表指定年份中某個月份的兩位數，範圍介於 01 至 12 之間。<br /><br /> DD 是代表指定月份中某個日期的兩位數，範圍介於 01 至 31 之間 (視月份而定)。|  
|字元長度|10 個位置|  
|有效位數，小數位數|10, 0|  
|儲存體大小|3 個位元組 (固定)|  
|儲存體結構|1 個 3 位元組的整數會儲存日期。|  
|精確度|一天|  
|預設值|1900-01-01<br /><br /> 這個值會用於從 **time** 隱含轉換成 **datetime2** 或 **datetimeoffset** 的附加日期部分。|  
|Calendar|西曆|  
|使用者自訂的小數秒數有效位數|否|  
|時區位移感知和保留|否|  
|日光節約感知|否|  
  
## <a name="supported-string-literal-formats-for-date"></a>date 支援的字串常值格式
下表顯示 **date** 資料類型的有效字串常值格式。
  
|數值|描述|  
|-------------|-----------------|  
|mdy<br /><br /> [m]m/dd/[yy]yy<br /><br /> [m]m-dd-[yy]yy<br /><br /> [m]m.dd.[yy]yy<br /><br /> myd<br /><br /> mm/[yy]yy/dd<br /><br /> mm-[yy]yy/dd<br /><br /> [m]m.[yy]yy.dd<br /><br /> dmy<br /><br /> dd/[m]m/[yy]yy<br /><br /> dd-[m]m-[yy]yy<br /><br /> dd.[m]m.[yy]yy<br /><br /> dym<br /><br /> dd/[yy]yy/[m]m<br /><br /> dd-[yy]yy-[m]m<br /><br /> dd.[yy]yy.[m]m<br /><br /> ymd<br /><br /> [yy]yy/[m]m/dd<br /><br /> [yy]yy-[m]m-dd<br /><br /> [yy]yy-[m]m-dd|[m]m、dd 和 [yy]yy 在字串中代表月、日和年，以斜線 (/)、連字號 (-) 或句號 (.) 為分隔符號。<br /><br /> 僅支援四或兩位數年份。 請盡可能使用四位數年份。 若要指定介於 0001 到 9999 之間的整數來代表截止年份，用於將兩位數年份解譯為四位數年份，請使用[設定 two digit year cutoff 伺服器組態選項](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)。<br /><br /> **注意！** 若是 Informatica，YYYY 限於 1582 至 9999 的範圍之間。<br /><br /> 兩位數年份若小於或等於截止年份的後兩位數，表示它與截止年份同一世紀。 兩位數年份若大於截止年份的後兩位數，表示它在截止年份的前一個世紀。 例如，如果兩位數年份的截止是預設值 2049，則兩位數年份 49 就會被解譯為 2049，而兩位數年份 50 則解譯為 1950。<br /><br /> 預設的日期格式由目前的語言設定決定。 您可以使用 [SET LANGUAGE](../../t-sql/statements/set-language-transact-sql.md) 和 [SET DATEFORMAT](../../t-sql/statements/set-dateformat-transact-sql.md) 陳述式來變更日期格式。<br /><br /> **date** 不支援 **ydm** 格式。|  
  
|字母順序|描述|  
|------------------|-----------------|  
|mon [dd][,] yyyy<br /><br /> mon dd[,] [yy]<br /><br /> mon yyyy [dd]<br /><br /> [dd] mon[,] yyyy<br /><br /> dd mon[,][yy]yy<br /><br /> dd [yy]yy mon<br /><br /> [dd] yyyy mon<br /><br /> yyyy mon [dd]<br /><br /> yyyy [dd] mon|**mon** 代表目前語言中指定的完整月份名稱或月份縮寫。 逗號是選擇性且會忽略大小寫。<br /><br /> 若要避免模糊不清，請使用四位數年份。<br /><br /> 如果漏了日的部分，就用當月第一天。|  
  
|ISO 8601|描述|  
|--------------|----------------|  
|YYYY-MM-DD<br /><br /> YYYYMMDD|與 SQL 標準相同。 這是唯一定義為國際標準的格式。|  
  
|未分隔|描述|  
|-----------------|-----------------|  
|[yy]yymmdd<br /><br /> yyyy[mm][dd]|您可以使用四位數、六位數或八位數指定 **date** 資料。 六位數或八位數字串一律會解譯成 **ymd**。 月和日一定是兩位數。 四位數字串則會解譯為年份。|  
  
|ODBC|描述|  
|----------|-----------------|  
|{ d 'yyyy-mm-dd' }|ODBC API 專用。|  
  
|W3C XML 格式|描述|  
|--------------------|-----------------|  
|yyyy-mm-ddTZD|支援 XML/SOAP 使用方式。<br /><br /> TZD 是時區指示項 (Z 或 + hh: mm 或 -hh:mm)：<br /><br /> -   hh:mm 表示時區時差。 hh 表示時區時差中的兩位數時數，範圍介於 0 至 14 之間。<br />-   MM 是代表時區時差中額外分鐘數的兩位數，範圍介於 0 至 59 之間。<br />-   + (加號) 或 - (減號) 是時區時差的必要符號。 這個符號指出，若要取得當地時間，則必須在國際標準時間 (UTC) 中新增或扣除時區位移。 時區位移的有效範圍介於 -14:00 至 +14:00 之間。|  
  
## <a name="ansi-and-iso-8601-compliance"></a>ANSI 和 ISO 8601 合規性  
**date** 符合西曆的 ANSI SQL 標準定義：「附註 85 - Datetime 資料類型會允許採用西曆格式的日期以 0001-01-01 CE 到 9999-12-31 CE 的日期範圍儲存。」
  
下層用戶端所使用的預設字串常值格式，遵循定義為 YYYY-MM-DD 的 SQL 標準格式。 此格式與 ISO 8601 所定義的 DATE 相同。
  
> [!NOTE]  
>  Informatica 的範圍限於 1582-10-15 (1582 年 10 月 15 日 CE) 至 9999-12-31 (9999 年 12 月 31 日 CE)。  
  
## <a name="backward-compatibility-for-down-level-clients"></a>下層用戶端的回溯相容性
有些下層用戶端不支援 **time**、**date**、**datetime2** 及 **datetimeoffset** 資料類型。 下表顯示在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的上層執行個體與下層用戶端之間的類型對應。
  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料類型|傳遞至下層用戶端的預設字串常值格式|下層 ODBC|下層 OLEDB|下層 JDBC|下層 SQLCLIENT|  
| --- | --- | --- | --- | --- | --- |
|**time**|hh:mm:ss[.nnnnnnn]|SQL_WVARCHAR 或 SQL_VARCHAR|DBTYPE_WSTR 或 DBTYPE_STR|Java.sql.String|字串或 SqString|  
|**date**|YYYY-MM-DD|SQL_WVARCHAR 或 SQL_VARCHAR|DBTYPE_WSTR 或 DBTYPE_STR|Java.sql.String|字串或 SqString|  
|**datetime2**|YYYY-MM-DD hh:mm:ss[.nnnnnnn]|SQL_WVARCHAR 或 SQL_VARCHAR|DBTYPE_WSTR 或 DBTYPE_STR|Java.sql.String|字串或 SqString|  
|**datetimeoffset**|YYYY-MM-DD hh:mm:ss[.nnnnnnn] [+&#124;-]hh:mm|SQL_WVARCHAR 或 SQL_VARCHAR|DBTYPE_WSTR 或 DBTYPE_STR|Java.sql.String|字串或 SqString|  
  
## <a name="converting-date-and-time-data"></a>轉換日期和時間資料
當您轉換成日期和時間資料類型時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會拒絕所有無法辨識為日期或時間的值。 如需搭配日期和時間資料使用 CAST 及 CONVERT 函數的詳細資訊，請參閱 [CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)。  
  
### <a name="converting-date-to-other-date-and-time-types"></a>將日期轉換成其他日期與時間類型

本節描述當 **date** 資料類型轉換成其他日期和時間資料類型時，可能發生的狀況。
  
當轉換成 **time(n)** 時，轉換會失敗，並引發錯誤訊息 206：「運算元類型衝突：date 與 time 不相容」。
  
如轉換成 **datetime**，會複製日期。 下列程式碼顯示將 `date` 值轉換成 `datetime` 值的結果。
  
```sql
DECLARE @date date= '12-10-25';  
DECLARE @datetime datetime= @date;  
  
SELECT @date AS '@date', @datetime AS '@datetime';  
  
--Result  
--@date      @datetime  
------------ -----------------------  
--2025-12-10 2025-12-10 00:00:00.000  
--  
--(1 row(s) affected)  
```  
  
當轉換成 **smalldatetime** 時，**date** 值在 [smalldatetime](../../t-sql/data-types/smalldatetime-transact-sql.md) 範圍內，會複製日期元件，並將時間元件設定為 00:00:00.000。 當 **date** 值在 **smalldatetime** 值的範圍以外時，將會引發錯誤訊息 242：「date 資料類型轉換成 smalldatetime 資料類型會產生超出範圍的值。」；而 **smalldatetime** 值會設為 NULL。 下列程式碼顯示將 `date` 值轉換成 `smalldatetime` 值的結果。
  
```sql
DECLARE @date date= '1912-10-25';  
DECLARE @smalldatetime smalldatetime = @date;  
  
SELECT @date AS '@date', @smalldatetime AS '@smalldatetime';  
  
--Result  
--@date      @smalldatetime  
------------ -----------------------  
--1912-10-25 1912-10-25 00:00:00  
--  
--(1 row(s) affected)  
```  
  
若轉換成 **datetimeoffset(n)** ，會複製日期，而時間會設定為 00:00.0000000 +00:00。 下列程式碼顯示將 `date` 值轉換成 `datetimeoffset(3)` 值的結果。
  
```sql
DECLARE @date date = '1912-10-25';  
DECLARE @datetimeoffset datetimeoffset(3) = @date;  
  
SELECT @date AS '@date', @datetimeoffset AS '@datetimeoffset';  
  
--Result  
--@date      @datetimeoffset  
------------ ------------------------------  
--1912-10-25 1912-10-25 00:00:00.000 +00:00  
--  
--(1 row(s) affected)  
```  
  
當轉換成 **datetime2(n)** 時，會複製日期元件，而時間元件會設定為 00:00.000000。 下列程式碼顯示將 `date` 值轉換成 `datetime2(3)` 值的結果。
  
```sql
DECLARE @date date = '1912-10-25'  
DECLARE @datetime2 datetime2(3) = @date;  
  
SELECT @date AS '@date', @datetime2 AS '@datetime2(3)';  
  
--Result  
--@date      @datetime2(3)  
------------ -----------------------  
--1912-10-25 1912-10-25 00:00:00.000  
--  
--(1 row(s) affected)  
```  
  
### <a name="converting-string-literals-to-date"></a>將字串常值轉換為日期
如果整個字串皆是有效的格式，即可從字串常值轉換為日期與時間類型。 否則，就會引發執行階段錯誤。 將日期與時間類型隱含或明確轉換為字串常值，在不指定樣式的情況下，一律採用目前工作階段的預設格式。 下表是字串常值轉換為 **date** 資料類型的規則。
  
|輸入字串常值|**date**|  
|---|---|
|ODBC DATE|ODBC 字串常值會對應到 **datetime** 資料類型。 任何將 ODBC DATETIME 常值指派成 **date** 類型的作業，皆會隱含轉換 **datetime** 與轉換規則定義的類型。|  
|ODBC TIME|請參閱先前的 ODBC DATE 規則。|  
|ODBC DATETIME|請參閱先前的 ODBC DATE 規則。|  
|僅限 DATE|一般|  
|僅限 TIME|提供預設值。|  
|僅限 TIMEZONE|提供預設值。|  
|DATE + TIME|使用輸入字串的 DATE 部分。|  
|DATE + TIMEZONE|不允許。|  
|TIME + TIMEZONE|提供預設值。|  
|DATE + TIME + TIMEZONE|使用本機 DATETIME 的 DATE 部分。|  
  
## <a name="examples"></a>範例  
下列範例會比較將字串轉換成各種 date 與 time 資料類型的結果。
  
```sql
SELECT   
     CAST('2007-05-08 12:35:29. 1234567 +12:15' AS time(7)) AS 'time'   
    ,CAST('2007-05-08 12:35:29. 1234567 +12:15' AS date) AS 'date'   
    ,CAST('2007-05-08 12:35:29.123' AS smalldatetime) AS   
        'smalldatetime'   
    ,CAST('2007-05-08 12:35:29.123' AS datetime) AS 'datetime'   
    ,CAST('2007-05-08 12:35:29. 1234567 +12:15' AS datetime2(7)) AS   
        'datetime2'  
    ,CAST('2007-05-08 12:35:29.1234567 +12:15' AS datetimeoffset(7)) AS   
        'datetimeoffset';  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
|資料類型|輸出|  
|---------------|------------|  
|**time**|12:35:29. 1234567|  
|**date**|2007-05-08|  
|**smalldatetime**|2007-05-08 12:35:00|  
|**datetime**|2007-05-08 12:35:29.123|  
|**datetime2**|2007-05-08 12:35:29.1234567|  
|**datetimeoffset**|2007-05-08 12:35:29.1234567 +12:15|  

首次在 SQL Server 2008 中引進。

## <a name="see-also"></a>另請參閱
[CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
  
