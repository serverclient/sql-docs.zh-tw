---
description: CREATE EXTERNAL FILE FORMAT (Transact-SQL)
title: CREATE EXTERNAL FILE FORMAT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/08/2020
ms.prod: sql
ms.prod_service: sql-data-warehouse, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE EXTERNAL FILE FORMAT
- CREATE_EXTERNAL_FILE_FORMAT
dev_langs:
- TSQL
helpviewer_keywords:
- External
- External, file format
- PolyBase, external file format
ms.assetid: abd5ec8c-1a0e-4d38-a374-8ce3401bc60c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 3e72de795b4572c010a315eb7fe02e8f10e731cd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100830"
---
# <a name="create-external-file-format-transact-sql"></a>CREATE EXTERNAL FILE FORMAT (Transact-SQL)
[!INCLUDE [sqlserver2016-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdbmi-asa-pdw.md)]

建立外部檔案格式物件來定義儲存於 Hadoop、Azure Blob 儲存體、Azure Data Lake Store 的外部資料，或用於與外部資料流建立關聯的輸入和輸出資料流。 建立外部檔案格式是建立外部資料表的先決條件。 透過建立外部檔案格式，您可以指定外部資料表所參考資料的實際配置。  
  
支援下列檔案格式：
  
- 分隔符號文字  
  
- Hive RCFile - 不適用於 Azure Synapse Analytics。
  
- Hive ORC
  
- Parquet

- JSON - 僅適用於 Azure SQL Edge。


若要建立外部資料表，請參閱 [CREATE EXTERNAL TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-table-transact-sql.md)。
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法
  
### <a name="delimited-text"></a>[分隔符號文字](#tab/delimited)
```syntaxsql
-- Create an external file format for DELIMITED (CSV/TSV) files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
        FORMAT_TYPE = DELIMITEDTEXT  
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    [ , DATA_COMPRESSION = {  
           'org.apache.hadoop.io.compress.GzipCodec'  
         | 'org.apache.hadoop.io.compress.DefaultCodec'  
        }  
     ]);

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter 
    | First_Row = integer -- ONLY AVAILABLE FOR AZURE SYNAPSE ANALYTICS
    | DATE_FORMAT = datetime_format  
    | USE_TYPE_DEFAULT = { TRUE | FALSE } 
    | Encoding = {'UTF8' | 'UTF16'} 
}
```
### <a name="rc"></a>[RC](#tab/rc)
```syntaxsql
--Create an external file format for RC files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = RCFILE,  
    SERDE_METHOD = {  
        'org.apache.hadoop.hive.serde2.columnar.LazyBinaryColumnarSerDe'  
      | 'org.apache.hadoop.hive.serde2.columnar.ColumnarSerDe'  
    }  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec' ]);
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

### <a name="orc"></a>[ORC](#tab/orc)
```syntaxsql  
--Create an external file format for ORC file.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (
         FORMAT_TYPE = ORC  
     [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.DefaultCodec'      }  
    ]);  
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

### <a name="parquet"></a>[Parquet](#tab/parquet)
```syntaxsql
--Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
         FORMAT_TYPE = PARQUET  
     [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);    
```
### <a name="json"></a>[JSON](#tab/json)
```syntaxsql
-- Create an external file format for JSON files.
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = JSON  
     [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      
      | 'org.apache.hadoop.io.compress.DefaultCodec'  }  
    ]);  
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

---
  
## <a name="arguments"></a>引數  
*file_format_name*  
指定外部檔案格式的名稱。
 
### <a name="format_type"></a>FORMAT_TYPE 
`FORMAT_TYPE = [ PARQUET | ORC | RCFILE | DELIMITEDTEXT]`

指定外部資料的格式。
  
- PARQUET 指定 Parquet 格式。
  
- ORC  
  指定 Optimized Row Columnar (ORC) 格式。 此選項要求外部 Hadoop 叢集上必須有 Hive 0.11 版或更高版本。 在 Hadoop 中，ORC 檔案格式會提供比 RCFILE 檔案格式更好的壓縮率和效能。

- RCFILE (與 SERDE_METHOD = *SERDE_method* 組合在一起) 指定 Record Columnar File (RcFile) 格式。 此選項要求您必須指定 Hive 序列化程式和還原序列化程式 (SerDe) 方法。 如果您在 Hadoop 中使用 Hive/HiveQL 來查詢 RC 檔案，則此需求是一樣的。 請注意，SerDe 方法會區分大小寫。

  使用 PolyBase 所支援的這兩種 SerDe 方法來指定 RCFile 的範例。

  - FORMAT_TYPE = RCFILE, SERDE_METHOD = 'org.apache.hadoop.hive.serde2.columnar.LazyBinaryColumnarSerDe'

  - FORMAT_TYPE = RCFILE, SERDE_METHOD = 'org.apache.hadoop.hive.serde2.columnar.ColumnarSerDe'

- DELIMITEDTEXT 指定具有欄分隔符號的文字格式，也稱為欄位結束字元。
   
- JSON 指定 JSON 格式。 僅適用於 Azure SQL Edge。 

### <a name="data_compression"></a>DATA\_COMPRESSION
 `DATA_COMPRESSION = *data_compression_method*`  
 指定外部資料的資料壓縮方法。 未指定 DATA_COMPRESSION 時，預設值為未經壓縮的資料。
若要正常運作，Gzip 壓縮檔案必須具有 ".gz" 副檔名。
 
 #### <a name="delimited-text"></a>[分隔符號文字](#tab/delimited)
 DELIMITEDTEXT 格式類型支援這些壓縮方法：
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec'
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

#### <a name="rc"></a>[RC](#tab/rc)
 RCFILE 格式類型支援這個壓縮方法：
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec'
  
#### <a name="orc"></a>[ORC](#tab/orc)
 ORC 檔案格式類型支援這些壓縮方法：
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec'
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

#### <a name="parquet"></a>[Parquet](#tab/parquet)
 PARQUET 檔案格式類型支援下列壓縮方法：
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

#### <a name="json"></a>[JSON](#tab/json)
 JSON 檔案格式類型支援下列壓縮方法：
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
-   DATA COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec'
---  
### <a name="delimited-text-format-options"></a>[分隔的文字格式] 選項

此節中所述的格式選項都是選用的，而且僅適用於分隔的文字檔。

#### <a name="field_terminator"></a>FIELD_TERMINATOR
`FIELD_TERMINATOR = *field_terminator*`  
僅適用於分隔符號文字檔。 欄位結束字元會指定一或多個字元，在文字分隔檔案中標記每個欄位 (欄) 的結尾。 預設值是管道字元 ꞌ|ꞌ。 若要保證支援，我們建議使用一或多個 ASCII 字元。
  
  
範例：  
  
-   FIELD_TERMINATOR = '|'  
  
-   FIELD_TERMINATOR = ' '  
  
-   FIELD_TERMINATOR = ꞌ\tꞌ  
  
-   FIELD_TERMINATOR = '~|~'  
  
#### <a name="string_delimiter"></a>STRING_DELIMITER
`STRING_DELIMITER = *string_delimiter*`

針對文字分隔檔案中類型字串的資料指定欄位結束字元。 字串分隔符號的長度為一或多個字元，且要以單引號括起來。 預設值為空字串 ""。 若要保證支援，我們建議使用一或多個 ASCII 字元。
 
  
 範例：  

-   STRING_DELIMITER = '"'

-   STRING_DELIMITER = '0x22' -- 雙引號十六進位
  
-   STRING_DELIMITER = '*'  
  
-   STRING_DELIMITER = ꞌ,ꞌ  
  
-   STRING_DELIMITER = '0x7E0x7E' -- 兩個波狀符號 (例如 ~~)
 
#### <a name="first_row"></a>FIRST_ROW
 `FIRST_ROW = *First_row_int*`  
指定在 PolyBase 載入期間，要在所有檔案中先讀取的資料列編號。 這個參數可以接受 1 到 15 的值。 如果將值設定為二，則在載入資料時，會略過每個檔案中的第一個資料列 (標題列)。 如果資料列具有資料列結束字元 (/r/n、/r、/n)，就會略過。 使用此選項進行匯出時，會將資料列加入至資料，以確定可讀取檔案且不會遺失任何資料。 如果將值設定為 >2，則匯出的第一列是外部資料表的資料行名稱。

#### <a name="date_format"></a>DATE\_FORMAT
 `DATE_FORMAT = *datetime_format*`  
針對所有可能出現在分隔符號文字檔中的日期和時間資料，指定自訂格式。 如果來源檔案使用預設的日期時間格式，就不需要這個選項。 每個檔案只允許一個自訂的日期時間格式。 您無法針對每個檔案指定多個自訂的日期時間格式。 不過，如果每個日期時間格式在外部資料表定義中都是其各自資料類型的預設格式，您就可以使用多個日期時間格式。

> [!IMPORTANT]
> PolyBase 只會使用自訂日期格式來匯入資料。 它不會使用自訂格式來將資料寫入至外部檔案。

 未指定 DATE_FORMAT 或其為空字串時，PolyBase 會使用下列預設格式：
  
-   DateTime：'yyyy-MM-dd HH:mm:ss'  
  
-   SmallDateTime：'yyyy-MM-dd HH:mm'  
  
-   Date：'yyyy-MM-dd'  
  
-   DateTime2：'yyyy-MM-dd HH:mm:ss'  
  
-   DateTimeOffset：'yyyy-MM-dd HH:mm:ss'  
  
-   Time：'HH:mm:ss'  

> [!IMPORTANT]
> 指定自訂 `DATE_FORMAT` 將會覆寫所有的預設類型格式。 這表示檔案的所有日期時間、日期與時間資料格，都必須有相同的日期格式。 使用覆寫的 `DATE_FORMAT` 時，您不能有格式不同的日期與時間值。

下表提供 **範例日期格式**：
  
關於該表格的注意事項：  
  
-   年、月和日均可有各種不同的格式和順序。 下表僅顯示 **ymd** 格式。 月份可以有一或兩個位數，或是三個字元。 日期可以有一或兩個位數。 年份可以有兩或四個位數。
  
-   毫秒 (fffffff) 不是必要的。
  
-   Am、pm (tt) 不是必要的。 預設值是 AM。
  
|日期類型|範例|描述|  
|---------------|-------------|-----------------|  
|Datetime|DATE_FORMAT = 'yyyy-MM-dd HH:mm:ss.fff'|除了年、月及日之外，此日期格式包含 00-24 時、00-59 分、00-59 秒，以及 3 位數的毫秒。|  
|Datetime|DATE_FORMAT = 'yyyy-MM-dd hh:mm:ss.ffftt'|除了年、月及日之外，此日期格式包含 00-12 時、00-59 分、00-59 秒、3 位數的毫秒，以及 AM、am、PM 或 pm。 |  
|SmallDateTime|DATE_FORMAT =  'yyyy-MM-dd HH:mm'|除了年、月及日之外，此日期格式包含 00-23 時、00-59 分。|  
|SmallDateTime|DATE_FORMAT =  'yyyy-MM-dd hh:mmtt'|除了年、月及日之外，此日期格式包含 00-11 時、00-59 分、沒有秒，以及 AM、am、PM 或 pm。|  
|Date|DATE_FORMAT =  'yyyy-MM-dd'|年、月及日。 未包含任何時間元素。|  
|Date|DATE_FORMAT = 'yyyy-MMM-dd'|年、月及日。 使用 3 個 M 來指定月份時，輸入值為下列其中一個字串：Jan、Feb、Mar、Apr、May、Jun、Jul、Aug、Sep、Oct、Nov 或 Dec。|  
|DateTime2|DATE_FORMAT = 'yyyy-MM-dd HH:mm:ss.fffffff'|除了年、月及日之外，此日期格式包含 00-23 時、00-59 分、00-59 秒，以及 7 位數的毫秒。|  
|DateTime2|DATE_FORMAT = 'yyyy-MM-dd hh:mm:ss.ffffffftt'|除了年、月及日之外，此日期格式包含 00-11 時、00-59 分、00-59 秒、7 位數的毫秒，以及 AM、am、PM 或 pm。|  
|DateTimeOffset|DATE_FORMAT = 'yyyy-MM-dd HH:mm:ss.fffffff zzz'|除了年、月及日之外，此日期格式包含 00-23 時、00-59 分、00-59 秒、7 位數的毫秒，以及您放入輸入檔以作為 `{+&#124;-}HH:ss` 的時區差距。 例如，由於不含日光節約時間的洛杉磯時間比 UTC 晚 8 小時，因此，輸入檔中的 -08:00 值會指定洛杉磯的時區。|  
|DateTimeOffset|DATE_FORMAT = 'yyyy-MM-dd hh:mm:ss.ffffffftt zzz'|除了年、月及日之外，此日期格式包含 00-11 時、00-59 分、00-59 秒、7 位數的毫秒、(AM、am、PM 或 pm)，以及時區差距。 請參閱上一列的描述。|  
|Time|DATE_FORMAT = 'HH:mm:ss'|沒有日期值，只有 00-23 時、00-59 分和 00-59 秒。|  
  
 ##### <a name="supported-date-and-time-formats"></a>支援的日期與時間格式
 
 外部檔案格式可以描述大量的日期與時間格式：
  
|Datetime|smalldatetime|date|datetime2|datetimeoffset|  
|--------------|-------------------|----------|---------------|--------------------|  
|[M[M]]M-[d]d-[yy]yy HH:mm:ss[.fff]|[M[M]]M-[d]d-[yy]yy HH:mm[:00]|[M[M]]M-[d]d-[yy]yy|[M[M]]M-[d]d-[yy]yy HH:mm:ss[.fffffff]|[M[M]]M-[d]d-[yy]yy HH:mm:ss[.fffffff] zzz|  
|[M[M]]M-[d]d-[yy]yy hh:mm:ss[.fff][tt]|[M[M]]M-[d]d-[yy]yy hh:mm[:00][tt]||[M[M]]M-[d]d-[yy]yy hh:mm:ss[.fffffff][tt]|[M[M]]M-[d]d-[yy]yy hh:mm:ss[.fffffff][tt] zzz|  
|[M[M]]M-[yy]yy-[d]d HH:mm:ss[.fff]|[M[M]]M-[yy]yy-[d]d HH:mm[:00]|[M[M]]M-[yy]yy-[d]d|[M[M]]M-[yy]yy-[d]d HH:mm:ss[.fffffff]|[M[M]]M-[yy]yy-[d]d HH:mm:ss[.fffffff] zzz|  
|[M[M]]M-[yy]yy-[d]d hh:mm:ss[.fff][tt]|[M[M]]M-[yy]yy-[d]d hh:mm[:00][tt]||[M[M]]M-[yy]yy-[d]d hh:mm:ss[.fffffff][tt]|[M[M]]M-[yy]yy-[d]d hh:mm:ss[.fffffff][tt] zzz|  
|[yy]yy-[M[M]]M-[d]d HH:mm:ss[.fff]|[yy]yy-[M[M]]M-[d]d HH:mm[:00]|[yy]yy-[M[M]]M-[d]d|[yy]yy-[M[M]]M-[d]d HH:mm:ss[.fffffff]|[yy]yy-[M[M]]M-[d]d HH:mm:ss[.fffffff]  zzz|  
|[yy]yy-[M[M]]M-[d]d hh:mm:ss[.fff][tt]|[yy]yy-[M[M]]M-[d]d hh:mm[:00][tt]||[yy]yy-[M[M]]M-[d]d hh:mm:ss[.fffffff][tt]|[yy]yy-[M[M]]M-[d]d hh:mm:ss[.fffffff][tt] zzz|  
|[yy]yy-[d]d-[M[M]]M HH:mm:ss[.fff]|[yy]yy-[d]d-[M[M]]M HH:mm[:00]|[yy]yy-[d]d-[M[M]]M|[yy]yy-[d]d-[M[M]]M HH:mm:ss[.fffffff]|[yy]yy-[d]d-[M[M]]M HH:mm:ss[.fffffff]  zzz|  
|[yy]yy-[d]d-[M[M]]M hh:mm:ss[.fff][tt]|[yy]yy-[d]d-[M[M]]M hh:mm[:00][tt]||[yy]yy-[d]d-[M[M]]M hh:mm:ss[.fffffff][tt]|[yy]yy-[d]d-[M[M]]M hh:mm:ss[.fffffff][tt] zzz|  
|[d]d-[M[M]]M-[yy]yy HH:mm:ss[.fff]|[d]d-[M[M]]M-[yy]yy HH:mm[:00]|[d]d-[M[M]]M-[yy]yy|[d]d-[M[M]]M-[yy]yy HH:mm:ss[.fffffff]|[d]d-[M[M]]M-[yy]yy HH:mm:ss[.fffffff] zzz|  
|[d]d-[M[M]]M-[yy]yy hh:mm:ss[.fff][tt]|[d]d-[M[M]]M-[yy]yy hh:mm[:00][tt]||[d]d-[M[M]]M-[yy]yy hh:mm:ss[.fffffff][tt]|[d]d-[M[M]]M-[yy]yy hh:mm:ss[.fffffff][tt] zzz|  
|[d]d-[yy]yy-[M[M]]M HH:mm:ss[.fff]|[d]d-[yy]yy-[M[M]]M HH:mm[:00]|[d]d-[yy]yy-[M[M]]M|[d]d-[yy]yy-[M[M]]M HH:mm:ss[.fffffff]|[d]d-[yy]yy-[M[M]]M HH:mm:ss[.fffffff]  zzz|  
|[d]d-[yy]yy-[M[M]]M hh:mm:ss[.fff][tt]|[d]d-[yy]yy-[M[M]]M hh:mm[:00][tt]||[d]d-[yy]yy-[M[M]]M hh:mm:ss[.fffffff][tt]|[d]d-[yy]yy-[M[M]]M hh:mm:ss[.fffffff][tt] zzz|  
  
 詳細資料：  
  
-   若要分隔月、日和年的值，您可以使用 '-'、'/' 或 '.'。 為了簡單起見，此表格只使用 '-' 分隔符號。
  
-   若要以文字格式指定月份，請使用三個以上的字元。 含有一或兩個字元的月份會被視為數字。
  
-   若要分隔時間值，請使用 ':' 符號。
  
-   括在方括號中的字母是選擇性的。
  
-   字母 'tt' 會指定 [AM|PM|am|pm]。 預設值是 AM。 指定 'tt' 時，小時值 (hh) 的範圍必須介於 0 到 12 之間。
  
-   字母 'zzz' 會以 {+|-}HH:ss] 格式，指定系統目前時區的時區差距。
 
#### <a name="use_type_default"></a>USE_TYPE_DEFAULT 
 `USE_TYPE_DEFAULT = { TRUE | FALSE }`
 
 指定當 PolyBase 從文字檔擷取資料時，如何處理分隔符號文字檔中遺漏的值。
  
 TRUE  
 從文字檔擷取資料時，使用外部資料表定義中對應資料行之資料類型的預設值來儲存每個遺漏值。 例如，使用下列項目來取代遺漏值：  
  
-   如果將資料行定義為數值資料行，使用 0。 不支援十進位資料行，且會出現錯誤。
  
-   如果資料行為字串資料行，使用空字串 ""。
  
-   如果資料行為日期資料行，使用 1900-01-01。
  
 **FALSE**  
 將所有遺漏值儲存為 NULL。 在分隔符號文字檔中使用 NULL 一字儲存的任何 NULL 值都會以 'NULL' 字串匯入。
 
#### <a name="encoding"></a>編碼方式
   `Encoding = {'UTF8' | 'UTF16'}`
   
 在 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] (APS CU7.4) 中，PolyBase 可讀取 UTF8 和 UTF16-LE 編碼的分隔符號文字檔。 在 SQL Server 中，PolyBase 不支援讀取 UTF16 編碼的檔案。

## <a name="permissions"></a>權限  
 需要 ALTER ANY EXTERNAL FILE FORMAT 權限。
  
## <a name="general-remarks"></a>一般備註
 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]中，外部檔案格式位於資料庫範圍內。 在[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]中，則位於伺服器範圍內。  
  
 格式選項都是選用的，僅適用於分隔符號文字檔。  
  
 以其中一種壓縮格式儲存資料時，PolyBase 會先將資料解壓縮，然後再傳回資料記錄。
  
## <a name="limitations-and-restrictions"></a>限制事項
  
 Hadoop 的 LineRecordReader 必須支援分隔符號文字檔中的資料列分隔符號。 也就是說，它必須是 '\r'、'\n' 或 '\r\n'。 使用者無法設定這些分隔符號。
  
 本文先前已列出支援的 SerDe 方法與 RCFile 的組合以及支援的資料壓縮方法。 並非所有組合都受到支援。
  
 並行 PolyBase 查詢的數目上限為 32 個。 當 32 個並行查詢正在執行時，每個查詢都能從外部檔案位置讀取最多 33,000 個檔案。 根資料夾與每個子資料夾也算是一個檔案。 如果並行程度小於 32，外部檔案位置所包含的檔案就可超過 33,000 個。
  
 由於外部資料表中檔案數目的限制，我們建議您在外部檔案位置的根和子資料夾中儲存少於 30,000 個檔案。 此外，建議您讓根目錄底下的子資料夾數目保持在一個很小的數目。 參考太多檔案時，可能會發生 Java 虛擬機器記憶體不足的例外狀況。
  
  透過 PolyBase 將檔案匯出到 Hadoop 或 Azure Blob 儲存體時，就會如 CREATE EXTERNAL TABLE 命令中所定義，只匯出資料，而不含資料行名稱 (中繼資料)。

## <a name="locking"></a>鎖定  
 在 EXTERNAL FILE FORMAT 物件上取得共用鎖定。
  
## <a name="performance"></a>效能
 使用壓縮檔案一律要在下列兩者間進行取捨：在外部資料來源和 SQL Server 之間傳送較少資料，同時要提高 CPU 使用量來壓縮和解壓縮資料。
  
 Gzip 壓縮文字檔不是可分割的。 若要提升 Gzip 壓縮文字檔的效能，我們建議產生多個檔案，並將它們全都儲存於外部資料來源內的相同目錄中。 這個檔案結構允許 PolyBase 使用多個讀取器和解壓縮程序，更快速地讀取及解壓縮資料。 壓縮檔案的理想數目是每個計算節點的資料讀取器處理序數目上限。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 中，資料讀取器程序數目上限為每個節點 8 個，但每個節點 20 個讀取器的 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] Gen2 除外。 在 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]中，每個節點的資料讀取器處理序數目上限會因 SLO 而異。 如需詳細資訊，請參閱 [[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 載入模式和策略](https://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/)。  
  
## <a name="examples"></a>範例  
  
### <a name="a-create-a-delimitedtext-external-file-format"></a>A. 建立 DELIMITEDTEXT 外部檔案格式  
 這個範例會建立名為 *textdelimited1* 的外部檔案格式，以供文字分隔檔案使用。 針對 FORMAT\_OPTIONS 列出的選項會指定應該使用管道字元 '|' 來分隔檔案中的欄位。 文字檔也會使用 Gzip 轉碼器來壓縮。 如果未指定 DATA\_COMPRESSION，就不會壓縮文字檔。
  
 針對分隔符號文字檔，資料壓縮方法可以是預設轉碼器 'org.apache.hadoop.io.compress.DefaultCodec' 或 Gzip 轉碼器 'org.apache.hadoop.io.compress.GzipCodec'。
  
```sql  
CREATE EXTERNAL FILE FORMAT textdelimited1  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,  
    FORMAT_OPTIONS (  
        FIELD_TERMINATOR = '|',  
        DATE_FORMAT = 'MM/dd/yyyy' ),  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
);  
```  
  
### <a name="b-create-an-rcfile-external-file-format"></a>B. 建立 RCFile 外部檔案格式  
 這個範例會針對使用序列化/還原序列化方法 org.apache.hadoop.hive.serde2.columnar.LazyBinaryColumnarSerDe 的 RCFile 建立外部檔案格式。 它也會指定要針對資料壓縮方法使用預設轉碼器。 如果未指定 DATA_COMPRESSION，預設為不壓縮。
  
```sql  
CREATE EXTERNAL FILE FORMAT rcfile1  
WITH (  
    FORMAT_TYPE = RCFILE,  
    SERDE_METHOD = 'org.apache.hadoop.hive.serde2.columnar.LazyBinaryColumnarSerDe',  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.DefaultCodec'  
);  
```  
  
### <a name="c-create-an-orc-external-file-format"></a>C. 建立 ORC 外部檔案格式  
 這個範例會針對使用 org.apache.io.compress.SnappyCodec 資料壓縮方法來壓縮資料的 ORC 檔案建立外部檔案格式。 如果未指定 DATA_COMPRESSION，預設為不壓縮。
  
```sql 
CREATE EXTERNAL FILE FORMAT orcfile1  
WITH (  
    FORMAT_TYPE = ORC,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);  
```  
  
### <a name="d-create-a-parquet-external-file-format"></a>D. 建立 PARQUET 外部檔案格式  
 這個範例會針對使用 org.apache.io.compress.SnappyCodec 資料壓縮方法來壓縮資料的 Parquet 檔案建立外部檔案格式。 如果未指定 DATA_COMPRESSION，預設為不壓縮。  
  
```sql  
CREATE EXTERNAL FILE FORMAT parquetfile1  
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);  
```  
### <a name="e-create-a-delimited-text-file-skipping-header-row-azure-synapse-analytics-only"></a>E. 建立略過標題列的分隔符號文字檔 (僅適用於 Azure Synapse Analytics)
 這個範例會針對具有單一標題列的 CSV 檔案建立外部檔案格式。 
  
```sql  
CREATE EXTERNAL FILE FORMAT skipHeader_CSV
WITH (FORMAT_TYPE = DELIMITEDTEXT,
      FORMAT_OPTIONS(
          FIELD_TERMINATOR = ',',
          STRING_DELIMITER = '"',
          FIRST_ROW = 2, 
          USE_TYPE_DEFAULT = True)
)
```   
### <a name="f-create-a-json-external-file-format"></a>F. 建立 JSON 外部檔案格式  
 這個範例會針對使用 org.apache.io.compress.SnappyCodec 資料壓縮方法來壓縮資料的 JSON 檔案建立外部檔案格式。 如果未指定 DATA_COMPRESSION，預設為不壓縮。 此範例適用於 Azure SQL Edge，但其他 SQL 產品目前不支援此範例。 
  
```sql  
CREATE EXTERNAL FILE FORMAT jsonFileFormat  
WITH (  
    FORMAT_TYPE = JSON,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);  
```  

## <a name="see-also"></a>另請參閱
 [CREATE EXTERNAL DATA SOURCE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-data-source-transact-sql.md)   
 [CREATE EXTERNAL TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-table-transact-sql.md)   
 [CREATE EXTERNAL TABLE AS SELECT &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-table-as-select-transact-sql.md)   
 [CREATE TABLE AS SELECT &#40;Azure Synapse Analytics&#41;](../../t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md)   
 [sys.external_file_formats &#40;TRANSACT-SQL&#41;](../../relational-databases/system-catalog-views/sys-external-file-formats-transact-sql.md)  
