---
description: OPENROWSET (Transact-SQL)
title: OPENROWSET (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/30/2019
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- OPENROWSET_TSQL
- OPENROWSET
dev_langs:
- TSQL
helpviewer_keywords:
- data sources [SQL Server]
- OPENROWSET function
- remote data access [SQL Server], OPENROWSET
- ad hoc distributed queries
- OPENROWSET function, Transact-SQL
- OPENROWSET statement
- OLE DB data sources [SQL Server]
- ad hoc connection information
ms.assetid: f47eda43-33aa-454d-840a-bb15a031ca17
author: julieMSFT
ms.author: jrasnick
monikerRange: =azuresqldb-mi-current||>=sql-server-2016||>=sql-server-linux-2017
ms.openlocfilehash: 43f3bbae327731f6522b71c0fdaafbc9441fe9ca
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98169747"
---
# <a name="openrowset-transact-sql"></a>OPENROWSET (Transact-SQL)

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

包含所有從 OLE DB 資料來源存取遠端資料所需的連接資訊。 這個方法是存取連結伺服器資料表的另一個方法，而且是使用 OLE DB 來連接和存取遠端資料的單次特定方法。 對於更常用到的 OLE DB 資料來源參考，請改用連結的伺服器。 如需詳細資訊，請參閱 [連結的伺服器 &#40;Database Engine&#41;](../../relational-databases/linked-servers/linked-servers-database-engine.md)。 您可以依照參考資料表名稱的相同方式，在查詢的 FROM 子句中參考 `OPENROWSET` 函數。 根據 OLE DB 提供者的能力而定，`OPENROWSET` 函數也可以被當做 `INSERT`、`UPDATE` 或 `DELETE` 陳述式的目標資料表加以參考。 雖然查詢可能傳回多個結果集，但是 `OPENROWSET` 只能傳回第一個。

`OPENROWSET` 也支援透過內建 BULK 提供者執行大量作業，可讓檔案資料被讀取，並且當做資料列集傳回。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
OPENROWSET
( { 'provider_name' 
    , { 'datasource' ; 'user_id' ; 'password' | 'provider_string' }
    , {   <table_or_view> | 'query' }
   | BULK 'data_file' ,
       { FORMATFILE = 'format_file_path' [ <bulk_options> ]
       | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB }
} )

<table_or_view> ::= [ catalog. ] [ schema. ] object

<bulk_options> ::=

   [ , DATASOURCE = 'data_source_name' ]

   [ , ERRORFILE = 'file_name' ]
   [ , ERRORFILE_DATA_SOURCE = 'data_source_name' ]
   [ , MAXERRORS = maximum_errors ]

   [ , FIRSTROW = first_row ]
   [ , LASTROW = last_row ]
   [ , ROWS_PER_BATCH = rows_per_batch ]
   [ , ORDER ( { column [ ASC | DESC ] } [ ,...n ] ) [ UNIQUE ] ]
  
   -- bulk_options related to input file format
   [ , CODEPAGE = { 'ACP' | 'OEM' | 'RAW' | 'code_page' } ]
   [ , FORMAT = 'CSV' ]
   [ , FIELDQUOTE = 'quote_characters']
   [ , FORMATFILE = 'format_file_path' ]
   [ , FORMATFILE_DATA_SOURCE = 'data_source_name' ]
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

### <a name="provider_name"></a>'*provider_name*'
這是一個字元字串，代表在登錄中指定之 OLE DB 提供者的易記名稱 (或 PROGID)。 *provider_name* 沒有預設值。 提供者名稱範例包括 `Microsoft.Jet.OLEDB.4.0`、`SQLNCLI` 或 `MSDASQL`。

### <a name="datasource"></a>'*datasource*'
這是一個對應至特定 OLE DB 資料來源的字串常數。 *datasource* 是指要傳遞到提供者的 IDBProperties 介面，將提供者初始化所用的 DBPROP_INIT_DATASOURCE 屬性。 這個字串通常都包含資料庫檔案的名稱、資料庫伺服器的名稱，或是提供者尋找資料庫所用的名稱。
資料來源可以是 `Microsoft.Jet.OLEDB.4.0` 提供者的檔案路徑 `C:\SAMPLES\Northwind.mdb'`，或 `SQLNCLI` 提供者的連接字串 `Server=Seattle1;Trusted_Connection=yes;`。

### <a name="user_id"></a>'*user_id*'
這是一個字串常數，代表傳遞到指定之 OLE DB 提供者的使用者名稱。 *user_id* 會指定連線的安全性內容，而且會當做 DBPROP_AUTH_USERID 屬性傳入來初始化提供者。 *user_id* 不可以是 Microsoft Windows 登入名稱。

### <a name="password"></a>'*password*'
這是一個字串常數，代表傳遞到 OLE DB 提供者的使用者密碼。 *password* 在將提供者初始化時，會當做 DBPROP_AUTH_PASSWORD 屬性來傳入。 *password* 不可以是 Microsoft Windows 密碼。

```sql
SELECT a.*
   FROM OPENROWSET('Microsoft.Jet.OLEDB.4.0',
                   'C:\SAMPLES\Northwind.mdb';
                   'admin';
                   'password',
                   Customers) AS a;
```

### <a name="provider_string"></a>'*provider_string*'
這是一個提供者特定的連接字串，會當做 DBPROP_INIT_PROVIDERSTRING 屬性傳入來初始化 OLE DB 提供者。 *provider_string* 通常會封裝將提供者初始化所需的所有連線資訊。 如需由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB 提供者辨識的關鍵字清單，請參閱[初始化和授權屬性](../../relational-databases/native-client-ole-db-data-source-objects/initialization-and-authorization-properties.md)。

```sql
SELECT d.*
FROM OPENROWSET('SQLNCLI', 'Server=Seattle1;Trusted_Connection=yes;',
                            Department) AS d;
```

### <a name="table_or_view"></a><table_or_view>
遠端資料表或檢視表，其中包含 `OPENROWSET` 應該讀取的資料。 可以是具有下列元件的三部分名稱物件：
- *catalog* (選擇性) - 這是所指定物件所在的目錄或資料庫名稱。
- *schema* (選擇性) - 這是所指定物件的結構描述或物件擁有者名稱。
- *object* - 這是唯一識別所處理物件的物件名稱。

```sql
SELECT d.*
FROM OPENROWSET('SQLNCLI', 'Server=Seattle1;Trusted_Connection=yes;',
                 AdventureWorks2012.HumanResources.Department) AS d;
```

### <a name="query"></a>'*query*'
這是傳給提供者，並且由提供者執行的字串常數。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的本機執行個體不會處理這項查詢，但會處理提供者傳回的查詢結果，亦即通過查詢。 如果提供者不是透過資料表名稱，而只透過命令語言使用其資料表資料，通過查詢將會很實用。 只要查詢提供者支援 OLE DB Command 物件與其必要介面，遠端伺服器就支援通過查詢。 如需詳細資訊，請參閱 [SQL Server Native Client &#40;OLE DB&#41; 參考](../../relational-databases/native-client-ole-db-interfaces/sql-server-native-client-ole-db-interfaces.md)。

```sql
SELECT a.*
FROM OPENROWSET('SQLNCLI', 'Server=Seattle1;Trusted_Connection=yes;',
     'SELECT TOP 10 GroupName, Name
     FROM AdventureWorks2012.HumanResources.Department') AS a;
```

### <a name="bulk"></a>BULK
使用 BULK 資料列集提供者，讓 OPENROWSET 讀取檔案資料。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，OPENROWSET 可以從資料檔讀取，而不必將資料載入到目標資料表中。 此舉可讓您搭配簡單的 SELECT 陳述式來使用 OPENROWSET。

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

BULK 選項的引數可讓您全力控制在哪裡開始和結束資料讀取、如何處理錯誤以及如何解譯資料。 例如，您可以指定讓資料檔當做 **varbinary**、**varchar** 或 **nvarchar** 類型的單一資料列、單一資料行資料列集加以讀取。 預設行為將在接下來的引數描述中加以描述。

 如需有關如何使用 BULK 選項的詳細資訊，請參閱本主題稍後的＜備註＞。 如需有關 BULK 選項所需權限的詳細資訊，請參閱本主題稍後的「權限」。

> [!NOTE]
> 當它以完整復原模式匯入資料時，OPENROWSET (BULK ...) 不會最佳化記錄。

如需準備資料進行大量匯入的資訊，請參閱[準備大量匯出或匯入的資料 &#40;SQL Server&#41;](../../relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server.md) 方面的知識。

#### <a name="bulk-data_file"></a>BULK '*data_file*'
這是要將資料複製到目標資料表之資料檔的完整路徑。

```sql
SELECT * FROM OPENROWSET(
   BULK 'C:\DATA\inv-2017-01-19.csv',
   SINGLE_CLOB) AS DATA;
```

**適用範圍：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。
從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 開始，data_file 就可以保留在 Azure Blob 儲存體中。 例如，請參閱[大量存取 Azure Blob 儲存體資料的範例](../../relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage.md)。

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

#### <a name="bulk-error-handling-options"></a>BULK 錯誤處理選項

##### <a name="errorfile"></a>ERRORFILE
`ERRORFILE` ='*file_name*' 指定用於收集資料列的檔案，其中資料列的格式錯誤且無法轉換成 OLE DB 資料列集。 這些資料列會「依照原狀」，從資料檔複製到這個錯誤檔中。

錯誤檔是在開始執行命令時建立。 如果檔案已經存在，就會引發錯誤。 另外，還會建立一個副檔名為 .ERROR.txt 的控制檔。 這個檔案會參考錯誤檔中的每個資料列，且會提供錯誤診斷。 錯誤更正之後，就能夠載入資料。
**適用範圍：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。
從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 開始，`error_file_path` 可以位於 Azure Blob 儲存體中。

##### <a name="errorfile_data_source_name"></a>ERRORFILE_DATA_SOURCE_NAME
**適用範圍：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。
這是具名的外部資料來源，指向錯誤檔案的 Azure Blob 儲存體位置，該檔案將包含在匯入期間發現的錯誤。 必須使用 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 中新增的 `TYPE = BLOB_STORAGE` 選項來建立外部資料來源。 如需詳細資訊，請參閱 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)。

##### <a name="maxerrors"></a>MAXERRORS
`MAXERRORS` =*maximum_errors* 指定最多出現幾個語法錯誤或不符合的資料列 (如格式檔案所定義) 之後，OPENROWSET 便會擲出例外狀況。 只要尚未到達 MAXERRORS，OPENROWSET 會忽略所有不正確的資料列，也不載入它，並將這個不正確的資料列計為一個錯誤。

*maximum_errors* 的預設值為 10。

> [!NOTE]
> `MAX_ERRORS` 不適用於 CHECK 限制式，也不能轉換 **money** 和 **bigint** 資料類型。

#### <a name="bulk-data-processing-options"></a>BULK 資料處理選項

##### <a name="firstrow"></a>FIRSTROW
`FIRSTROW` =*first_row* 指定所要載入第一個資料列的號碼。 預設值是 1。 這表示指定之資料檔中的第一個資料列。 資料列號碼是由計算資料列結束字元所決定。 FIRSTROW 是以 1 為基底。

##### <a name="lastrow"></a>LASTROW
`LASTROW` =*last_row* 指定所要載入最後一個資料列的號碼。 預設值是 0。 這表示指定的資料檔中的最後一個資料列。

##### <a name="rows_per_batch"></a>ROWS_PER_BATCH
`ROWS_PER_BATCH` =*rows_per_batch* 指定資料檔案中大約有多少資料列。 這個值應該與實際的資料列數差不多。

`OPENROWSET` 一律將資料檔案當作單一批次加以匯入。 不過，如果您為 *rows_per_batch* 指定 > 0 的值，查詢處理器會使用 *rows_per_batch* 的值當作提示，在查詢計劃中配置資源。

根據預設，ROWS_PER_BATCH 是未知的。 指定 ROWS_PER_BATCH = 0 相當於省略 ROWS_PER_BATCH。

##### <a name="order"></a>ORDER
`ORDER` ( { *column* [ ASC | DESC ] } [ ,... *n* ] [ UNIQUE ] ) 選擇性提示，指定如何排序資料檔案中的資料。 依預設，大量作業會假設資料檔沒有排序。 如果查詢最佳化工具可以利用指定的順序來產生更有效率的查詢計畫，效能就可能會提升。 指定排序可能很有用處的範例包括以下情況：

- 將資料列插入具有叢集索引的資料表中，其中的資料列集資料會根據叢集索引鍵來排序。
- 將資料列集與另一個資料表聯結，其中的排序資料行和聯結資料行會相符。
- 依據排序資料行彙總資料列集資料。
- 在查詢的 FROM 子句中使用資料列集當做來源資料表，其中的排序資料行和聯結資料行會相符。

##### <a name="unique"></a>UNIQUE
`UNIQUE` 指定沒有重複項目的資料檔案。

如果資料檔中的實際資料列並未根據指定的順序來排序，或是指定了 UNIQUE 提示而且有重複的索引鍵存在，則會傳回錯誤。

當使用 ORDER 時，需要資料行別名。 資料行別名清單必須參考由 BULK 子句所存取的衍生資料表。 ORDER 子句中所指定的資料行名稱會參考這個資料行別名清單。 無法指定大數值類型 (**varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** 與 **xml**) 和大型物件 (LOB) 類型 (**text**、**ntext** 和 **image**) 資料行。

##### <a name="single_blob"></a>SINGLE_BLOB
將 *data_file* 的內容當作 **varbinary(max)** 類型的單一資料列、單一資料行資料列集加以傳回。

> [!IMPORTANT]
> 建議您只使用 SINGLE_BLOB 選項匯入 XML 資料，而不要使用 SINGLE_CLOB 和 SINGLE_NCLOB，因為只有 SINGLE_BLOB 支援所有的 Windows 編碼轉換。

##### <a name="single_clob"></a>SINGLE_CLOB
以 ASCII 格式讀取 *data_file*，並且使用目前資料庫的定序，將內容當做 **varchar(max)** 類型的單一資料列、單一資料行資料列集加以傳回。

##### <a name="single_nclob"></a>SINGLE_NCLOB
以 UNICODE 格式讀取 *data_file*，並且使用目前資料庫的定序，將內容當做 **varchar(max)** 類型的單一資料列、單一資料行資料列集加以傳回。

```sql
SELECT *
   FROM OPENROWSET(BULK N'C:\Text1.txt', SINGLE_NCLOB) AS Document;
```

#### <a name="bulk-input-file-format-options"></a>BULK 輸入檔格式選項

##### <a name="codepage"></a>CODEPAGE
`CODEPAGE` = { 'ACP' \| 'OEM' \| 'RAW' \| '*code_page*' } 指定資料檔案中資料的字碼頁。 只有當資料包含字元值大於 127 或小於 32 的 **char** **varchar** 或 **text** 資料行時，CODEPAGE 才會相關。

> [!IMPORTANT]
> 在 Linux 上，`CODEPAGE` 不是支援的選項。

> [!NOTE]
> 除非您希望 65001 選項的優先順序高於定序/字碼頁指定值，否則建議您在格式檔案中指定每個資料行的定序名稱。

|CODEPAGE 值|描述|
|--------------------|-----------------|
|ACP|將 **char**、**varchar** 或 **text** 資料類型的資料行，從 ANSI/[!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 字碼頁 (ISO 1252) 轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 字碼頁。|
|OEM (預設值)|將 **char**、**varchar** 或 **text** 資料類型的資料行，從系統 OEM 字碼頁轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 字碼頁。|
|RAW|不進行字碼頁之間的轉換。 這是最快的選項。|
|*code_page*|指出在哪一個來源字碼頁，將資料檔中的字元資料加以編碼；例如 850。<br /><br /> **重要**[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 之前的版本不支援字碼頁 65001 (UTF-8 編碼)。|

##### <a name="format"></a>FORMAT
`FORMAT` **=** 'CSV' **適用於：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。
指定符合 [RFC 4180](https://tools.ietf.org/html/rfc4180) 規範的逗點分隔值檔案。

```sql
SELECT *
FROM OPENROWSET(BULK N'D:\XChange\test-csv.csv',
    FORMATFILE = N'D:\XChange\test-csv.fmt',
    FIRSTROW=2,
    FORMAT='CSV') AS cars;
```

##### <a name="formatfile"></a>FORMATFILE
`FORMATFILE` ='*format_file_path*' 指定格式檔案的完整路徑。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援兩種類型的格式檔案：XML 和非 XML。

您必須使用格式檔，才能定義結果集中的資料行類型。 不過，當指定 SINGLE_CLOB、SINGLE_BLOB 或 SINGLE_NCLOB 時，就不需要格式檔，這是唯一的例外狀況。

如需格式檔案的詳細資訊，請參閱[使用格式檔案大量匯入資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)。

**適用範圍：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。
從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 開始，format_file_path 可位於 Azure Blob 儲存體中。 例如，請參閱[大量存取 Azure Blob 儲存體資料的範例](../../relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage.md)。

##### <a name="fieldquote"></a>FIELDQUOTE
`FIELDQUOTE` **=** 'field_quote' **適用於：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。
指定將用來當作 CSV 檔案中引號字元的字元。 如果未指定，則會使用引號字元 (") 當作引號字元，如 [RFC 4180](https://tools.ietf.org/html/rfc4180) 標準中所定義的。

## <a name="remarks"></a>備註

唯有針對指定的提供者將 **DisallowAdhocAccess** 登錄選項明確設定為 0，且已啟用 [隨選分散式查詢] 進階設定選項時，才能使用 `OPENROWSET` 來存取 OLE DB 資料來源的遠端資料。 若未設定這些選項，預設行為便不允許特定存取。

存取遠端 OLE DB 資料來源時，用戶端連接到將進行查詢之伺服器所在的伺服器上，不會自動委派信任連接的登入識別。 此時必須設定驗證委派。

如果 OLE DB 提供者支援指定之資料來源中的多個目錄和結構描述，則需要目錄和結構描述名稱。 如果 OLE DB 提供者不支援 _catalog_ 和 _schema_ 的值，就可以將它們省略。 如果提供者只支援結構描述名稱，就必須指定 _schema_ **.** _object_ 格式的兩部分名稱。 如果提供者只支援目錄名稱，則必須指定 _catalog_ **.** _schema_ **.** _object_ 格式的三部分名稱。 您必須為使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB 提供者的通過查詢指定三部分的名稱。 如需詳細資訊，請參閱 [Transact-SQL 語法慣例 &#40;Transact-SQL&#41;](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

`OPENROWSET` 不接受變數作為其引數。

對 `FROM` 子句中 `OPENDATASOURCE`、`OPENQUERY` 或 `OPENROWSET` 的任何呼叫都會與當做更新目標使用之這些函數的任何呼叫進行個別且獨立的評估，即使完全相同的引數套用至這兩種呼叫也一樣。 尤其，針對其中一個呼叫結果所套用的篩選或聯結條件對於另一個呼叫的結果沒有作用。

## <a name="using-openrowset-with-the-bulk-option"></a>搭配 BULK 選項使用 OPENROWSET

下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 增強功能支援 OPENROWSET(BULK...) 函數：

- 搭配 `SELECT` 使用的 FROM 子句可以透過完整的 `SELECT` 功能呼叫 `OPENROWSET(BULK...)` 而不是資料表名稱。

   具有 `BULK` 選項的 `OPENROWSET` 在 `FROM` 子句中需要一個相互關聯名稱，又稱為範圍變數或別名。 您可以指定資料行別名。 如果未指定資料行別名清單，格式檔就必須有資料行名稱。 指定資料行別名會覆寫格式檔中的資料行名稱，例如：

  - `FROM OPENROWSET(BULK...) AS table_alias`
  - `FROM OPENROWSET(BULK...) AS table_alias(column_alias,...n)`

  > [!IMPORTANT]
  > 新增 `AS <table_alias>` 失敗將會導致錯誤：訊息 491，層級 16，狀態 1，行 20 必須為 FROM 子句中的大量資料列集指定相互關聯名稱。

- `SELECT...FROM OPENROWSET(BULK...)` 陳述式會直接查詢檔案中的資料，而不將資料匯入資料表中。 `SELECT...FROM OPENROWSET(BULK...)` 陳述式也可以使用格式檔案來指定資料行名稱和資料類型，以列出大量資料行別名。
- 使用 `OPENROWSET(BULK...)` 當做 `INSERT` 或 `MERGE` 陳述式中的來源資料表會將資料檔中的資料大量匯入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料表中。 如需詳細資訊，請參閱[使用 BULK INSERT 或 OPENROWSET&#40;BULK...&#41; 匯入大量資料 &#40;SQL Server&#41;](../../relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server.md)。
- 當搭配 `INSERT` 陳述式使用 `OPENROWSET BULK` 選項時，BULK 子句支援資料表提示。 除了一般的資料表提示 (例如 `TABLOCK`) 之外，`BULK` 子句也接受下列特殊化資料表提示：`IGNORE_CONSTRAINTS` (僅忽略 `CHECK` 和 `FOREIGN KEY` 限制式)、`IGNORE_TRIGGERS``KEEPDEFAULTS`和 `KEEPIDENTITY`。 如需詳細資訊，請參閱[資料表提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)。

  如需如何使用 `INSERT...SELECT * FROM OPENROWSET(BULK...)` 陳述式的資訊，請參閱[資料的大量匯入及匯出 &#40;SQL Server&#41;](../../relational-databases/import-export/bulk-import-and-export-of-data-sql-server.md)。 如需大量匯入所執行的資料列插入作業於何時記錄到交易記錄的資訊，請參閱 [大量匯入採用最低限度記錄的必要條件](../../relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import.md)。

> [!NOTE]
> 使用 `OPENROWSET` 時，一定要了解 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 如何處理模擬。 如需安全性考量的資訊，請參閱[使用 BULK INSERT 或 OPENROWSET&#40;BULK...&#41; 匯入大量資料 &#40;SQL Server&#41;](../../relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server.md)。

### <a name="bulk-importing-sqlchar-sqlnchar-or-sqlbinary-data"></a>大量匯入 SQLCHAR、SQLNCHAR 或 SQLBINARY 資料

OPENROWSET(BULK...) 會假設，如果未指定，則 SQLCHAR、SQLNCHAR 或 SQLBINARY 資料的最大長度不會超過 8000 個位元組。 如果要匯入的資料位於 LOB 資料欄位中，該欄位包含了超過 8000 個位元組的任何 **varchar(max)** 、**nvarchar(max)** 或 **varbinary(max)** 物件，您必須使用 XML 格式檔案來定義資料欄位的最大長度。 若要指定最大長度，請編輯格式檔案，並宣告 MAX_LENGTH 屬性。

> [!NOTE]
> 自動產生的格式檔案不會指定 LOB 欄位的長度或最大長度。 但是，您可以編輯格式檔案，並手動指定長度或最大長度。

### <a name="bulk-exporting-or-importing-sqlxml-documents"></a>大量匯出或匯入 SQLXML 文件

若要大量匯出或匯入 SQLXML 資料，請在格式檔案中使用下列其中一種資料類型。

|資料類型|效果|
|---------------|------------|
|SQLCHAR 或 SQLVARYCHAR|資料是使用用戶端字碼頁或定序所隱含的字碼頁所傳送。|
|SQLNCHAR 或 SQLNVARCHAR|以 Unicode 格式傳送這份資料。|
|SQLBINARY 或 SQLVARYBIN|未經任何轉換即傳送這份資料。|

## <a name="permissions"></a>權限

`OPENROWSET` 權限是由傳遞給 OLE DB 提供者之使用者名稱的權限所決定。 若要使用 `BULK` 選項，需要 `ADMINISTER BULK OPERATIONS` 或 `ADMINISTER DATABASE BULK OPERATIONS` 權限。

## <a name="examples"></a>範例

### <a name="a-using-openrowset-with-select-and-the-sql-server-native-client-ole-db-provider"></a>A. 搭配 SELECT 和 SQL Server Native Client OLE DB 提供者來使用 OPENROWSET

下列範例會使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB 提供者來存取遠端伺服器 `Seattle1` 上 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫中的 `HumanResources.Department` 資料表。 (使用 SQLNCLI 和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將會重新導向至最新版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB 提供者)。`SELECT` 陳述式是用來定義傳回的資料列集。 提供者字串包含 `Server` 和 `Trusted_Connection` 關鍵字。 這些關鍵字是由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB 提供者所辨識。

```sql
SELECT a.*
FROM OPENROWSET('SQLNCLI', 'Server=Seattle1;Trusted_Connection=yes;',
     'SELECT GroupName, Name, DepartmentID
      FROM AdventureWorks2012.HumanResources.Department
      ORDER BY GroupName, Name') AS a;
```

### <a name="b-using-the-microsoft-ole-db-provider-for-jet"></a>B. 使用 Microsoft OLE DB Provider for Jet

下列範例是透過 [!INCLUDE[msCoName](../../includes/msconame-md.md)] OLE DB Provider for Jet，存取 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Access `Customers` 資料庫中的 `Northwind` 資料表。

> [!NOTE]
> 這個範例假設您已經安裝了 Access。 若要執行這個範例，您必須安裝 Northwind 資料庫。

```sql
SELECT CustomerID, CompanyName
   FROM OPENROWSET('Microsoft.Jet.OLEDB.4.0',
      'C:\Program Files\Microsoft Office\OFFICE11\SAMPLES\Northwind.mdb';
      'admin';'',Customers);
```

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

### <a name="c-using-openrowset-and-another-table-in-an-inner-join"></a>C. 使用 OPENROWSET 和 INNER JOIN 中的另一份資料表

下列範例會從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] `Northwind` 資料庫本機執行個體的 `Customers` 資料表，以及從儲存在同一部電腦的 Access `Northwind` 資料庫之 `Orders` 資料表中，選取所有的資料。

> [!NOTE]
> 這個範例假設您已經安裝了 Access。 若要執行這個範例，您必須安裝 Northwind 資料庫。

```sql
USE Northwind;
GO
SELECT c.*, o.*
FROM Northwind.dbo.Customers AS c
   INNER JOIN OPENROWSET('Microsoft.Jet.OLEDB.4.0',
   'C:\Program Files\Microsoft Office\OFFICE11\SAMPLES\Northwind.mdb';'admin';'', Orders)
   AS o
   ON c.CustomerID = o.CustomerID ;
```

> [!IMPORTANT]
> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 僅支援從 Azure Blob 儲存體讀取。

### <a name="d-using-openrowset-to-bulk-insert-file-data-into-a-varbinarymax-column"></a>D. 使用 OPENROWSET 將檔案資料大量插入到 varbinary(max) 資料行中

 下列範例會建立一份小型資料表做為示範之用，並且從 `Text1.txt` 根目錄下的 `C:` 檔，將檔案資料插入到 `varbinary(max)` 資料行中。

```sql
CREATE TABLE myTable(FileName NVARCHAR(60),
  FileType NVARCHAR(60), Document VARBINARY(max));
GO

INSERT INTO myTable(FileName, FileType, Document)
   SELECT
      'Text1.txt' AS FileName
      , '.txt' AS FileType
      , *
   FROM OPENROWSET(BULK N'C:\Text1.txt', SINGLE_BLOB) AS Document;
GO
```

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

### <a name="e-using-the-openrowset-bulk-provider-with-a-format-file-to-retrieve-rows-from-a-text-file"></a>E. 搭配一個格式檔來使用 OPENROWSET BULK 提供者，從文字檔擷取資料列

下列範例會利用一個格式檔，從 Tab 鍵分隔的文字檔 `values.txt` 擷取資料列，該檔含有下列資料：

```
1     Data Item 1
2     Data Item 2
3     Data Item 3
```

格式檔 `values.fmt` 會描述 `values.txt` 中的資料行：

```
9.0
2  
1  SQLCHAR  0  10 "\t"        1  ID                      SQL_Latin1_General_Cp437_BIN
2  SQLCHAR  0  40 "\r\n"      2  Description        SQL_Latin1_General_Cp437_BIN
```

這是擷取該資料的查詢：

```sql
SELECT a.* FROM OPENROWSET( BULK 'c:\test\values.txt',
   FORMATFILE = 'c:\test\values.fmt') AS a;
```

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

### <a name="f-specifying-a-format-file-and-code-page"></a>F. 指定格式檔案和字碼頁

下列範例顯示如何同時使用格式檔案和字碼頁選項。

```sql
INSERT INTO MyTable SELECT a.* FROM
OPENROWSET (BULK N'D:\data.csv', FORMATFILE =
    'D:\format_no_collation.txt', CODEPAGE = '65001') AS a;
```

### <a name="g-accessing-data-from-a-csv-file-with-a-format-file"></a>G. 從具有格式檔案的 CSV 檔案存取資料

**適用範圍：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。

```sql
SELECT *
FROM OPENROWSET(BULK N'D:\XChange\test-csv.csv',
    FORMATFILE = N'D:\XChange\test-csv.fmt',
    FIRSTROW=2,
    FORMAT='CSV') AS cars;
```

> [!IMPORTANT]
> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 僅支援從 Azure Blob 儲存體讀取。

### <a name="h-accessing-data-from-a-csv-file-without-a-format-file"></a>H. 從沒有格式檔案的 CSV 檔案存取資料

```sql
SELECT * FROM OPENROWSET(
   BULK 'C:\Program Files\Microsoft SQL Server\MSSQL14.CTP1_1\MSSQL\DATA\inv-2017-01-19.csv',
   SINGLE_CLOB) AS DATA;
```

```sql
SELECT *
FROM OPENROWSET
   (  'MSDASQL'
     ,'Driver={Microsoft Access Text Driver (*.txt, *.csv)}'
     ,'select * from E:\Tlog\TerritoryData.csv')
;
```

> [!IMPORTANT]
>
> - ODBC 驅動程式應該是 64 位元。 在 Windows 中，開啟 [OBDC 資料來源](../../integration-services/import-export-data/connect-to-an-odbc-data-source-sql-server-import-and-export-wizard.md) 應用程式的 [驅動程式]  索引標籤來確認這一點。 32 位元的 `Microsoft Text Driver (*.txt, *.csv)` 將無法與 64 位元版本的 sqlservr.exe 一起運作。
> - Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

### <a name="i-accessing-data-from-a-file-stored-on-azure-blob-storage"></a>I. 從儲存在 Azure Blob 儲存體上的檔案存取資料

**適用範圍：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。
下列範例使用指向 Azure 儲存體帳戶中的容器和針對共用存取簽章而建立的資料庫範圍認證的外部資料來源。

```sql
SELECT * FROM OPENROWSET(
   BULK 'inv-2017-01-19.csv',
   DATA_SOURCE = 'MyAzureInvoices',
   SINGLE_CLOB) AS DataFile;
```

如需包含設定認證和外部資料來源的完整 `OPENROWSET` 範例，請參閱[大量存取 Azure Blob 儲存體資料的範例](../../relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage.md)。

### <a name="j-importing-into-a-table-from-a-file-stored-on-azure-blob-storage"></a>J. 從儲存在 Azure Blob 儲存體上的檔案匯入資料表

下列範例說明如何使用 OPENROWSET 命令，從已建立 SAS 金鑰的 Azure Blob 儲存體位置中 CSV 檔案載入資料。 Azure Blob 儲存體位置已設定為外部資料來源。 這需要使用在使用者資料庫中以主要金鑰加密的共用存取簽章來進行資料庫範圍認證。

```sql
--> Optional - a MASTER KEY is not required if a DATABASE SCOPED CREDENTIAL is not required because the blob is configured for public (anonymous) access!
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'YourStrongPassword1';
GO
--> Optional - a DATABASE SCOPED CREDENTIAL is not required because the blob is configured for public (anonymous) access!
CREATE DATABASE SCOPED CREDENTIAL MyAzureBlobStorageCredential
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=2016-12-29T16:55:34Z***************';

 -- NOTE: Make sure that you don't have a leading ? in SAS token, and
 -- that you have at least read permission on the object that should be loaded srt=o&sp=r, and
 -- that expiration period is valid (all dates are in UTC time)

CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
WITH ( TYPE = BLOB_STORAGE,
          LOCATION = 'https://****************.blob.core.windows.net/curriculum'
          , CREDENTIAL= MyAzureBlobStorageCredential --> CREDENTIAL is not required if a blob is configured for public (anonymous) access!
);

INSERT INTO achievements with (TABLOCK) (id, description)
SELECT * FROM OPENROWSET(
   BULK  'csv/achievements.csv',
   DATA_SOURCE = 'MyAzureBlobStorage',
   FORMAT ='CSV',
   FORMATFILE='csv/achievements-c.xml',
   FORMATFILE_DATA_SOURCE = 'MyAzureBlobStorage'
    ) AS DataFile;
```

> [!IMPORTANT]
> Azure SQL Database 只支援使用 SAS 權杖從 Azure Blob 儲存體讀取。

### <a name="additional-examples"></a>其他範例

如需示範如何使用 `INSERT...SELECT * FROM OPENROWSET(BULK...)` 的其他範例，請參閱下列主題：

- [大量匯入與匯出 XML 文件的範例 &#40;SQL Server&#41;](../../relational-databases/import-export/examples-of-bulk-import-and-export-of-xml-documents-sql-server.md)
- [大量匯入資料時保留識別值 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-identity-values-when-bulk-importing-data-sql-server.md)
- [大量匯入期間保留 Null 或使用預設值 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)
- [使用格式檔案大量匯入資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)
- [使用字元格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)
- [使用格式檔案略過資料表資料行 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)
- [使用格式檔案略過資料欄位 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)
- [使用格式檔案將資料表資料行對應至資料檔案欄位 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)

## <a name="see-also"></a>另請參閱

- [DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)
- [FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)
- [資料的大量匯入及匯出 &#40;SQL Server&#41;](../../relational-databases/import-export/bulk-import-and-export-of-data-sql-server.md)
- [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)
- [OPENDATASOURCE &#40;Transact-SQL&#41;](../../t-sql/functions/opendatasource-transact-sql.md)
- [OPENQUERY &#40;Transact-SQL&#41;](../../t-sql/functions/openquery-transact-sql.md)
- [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)
- [sp_addlinkedserver &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)
- [sp_serveroption &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-serveroption-transact-sql.md)
- [UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)
- [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)
