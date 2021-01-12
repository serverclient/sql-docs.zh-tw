---
title: BULK INSERT (Transact-SQL) | Microsoft Docs
description: BULK INSERT 陳述式的 Transact-SQL 參考。
ms.date: 02/21/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- BULK_TSQL
- BULK_INSERT
- BULK_INSERT_TSQL
- BULK INSERT
dev_langs:
- TSQL
helpviewer_keywords:
- tables [SQL Server], importing data into
- inserting files
- views [SQL Server], importing data into
- BULK INSERT statement
- views [SQL Server], exporting data from
- importing data, bulk import
- bulk importing [SQL Server], BULK INSERT statement
- file importing [SQL Server]
ms.assetid: be3984e1-5ab3-4226-a539-a9f58e1e01e2
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 9e0d729d951de184aa16717e784f14ae244639d8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098484"
---
# <a name="bulk-insert-transact-sql"></a>BULK INSERT (Transact-SQL)

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

依照 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中使用者指定的格式，將資料檔案匯入資料庫資料表或檢視表中

 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
BULK INSERT
   { database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }
      FROM 'data_file'
     [ WITH
    (
   [ [ , ] BATCHSIZE = batch_size ]
   [ [ , ] CHECK_CONSTRAINTS ]
   [ [ , ] CODEPAGE = { 'ACP' | 'OEM' | 'RAW' | 'code_page' } ]
   [ [ , ] DATAFILETYPE =
      { 'char' | 'native'| 'widechar' | 'widenative' } ]
   [ [ , ] DATA_SOURCE = 'data_source_name' ]
   [ [ , ] ERRORFILE = 'file_name' ]
   [ [ , ] ERRORFILE_DATA_SOURCE = 'data_source_name' ]
   [ [ , ] FIRSTROW = first_row ]
   [ [ , ] FIRE_TRIGGERS ]
   [ [ , ] FORMATFILE_DATA_SOURCE = 'data_source_name' ]
   [ [ , ] KEEPIDENTITY ]
   [ [ , ] KEEPNULLS ]
   [ [ , ] KILOBYTES_PER_BATCH = kilobytes_per_batch ]
   [ [ , ] LASTROW = last_row ]
   [ [ , ] MAXERRORS = max_errors ]
   [ [ , ] ORDER ( { column [ ASC | DESC ] } [ ,...n ] ) ]
   [ [ , ] ROWS_PER_BATCH = rows_per_batch ]
   [ [ , ] ROWTERMINATOR = 'row_terminator' ]
   [ [ , ] TABLOCK ]

   -- input file format options
   [ [ , ] FORMAT = 'CSV' ]
   [ [ , ] FIELDQUOTE = 'quote_characters']
   [ [ , ] FORMATFILE = 'format_file_path' ]
   [ [ , ] FIELDTERMINATOR = 'field_terminator' ]
   [ [ , ] ROWTERMINATOR = 'row_terminator' ]
    )]
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*database_name* 這是指定的資料表或檢視表所在資料庫名稱。 如果未指定，這就是目前的資料庫。

*schema_name* 這是資料表或檢視表結構描述的名稱。 如果執行大量匯入作業之使用者的預設結構描述，是指定之資料表或檢視表的結構描述，則 *schema_name* 為選擇性。 如果未指定 *schema*，且執行大量匯入作業之使用者的預設結構描述與指定的資料表或檢視表不同，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 就會傳回錯誤訊息，且會取消大量匯入作業。

*table_name* 這是要大量匯入資料到其中之資料表或檢視表的名稱。 您只能使用所有資料行都參考相同基底資料表的檢視表。 如需有關將資料載入至檢視表中之限制的詳細資訊，請參閱 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)。

**'** _data_file_ **'** 這是含有要匯入至指定的資料表或檢視表中資料的資料檔案完整路徑。 BULK INSERT 可以從磁碟或 Azure Blob 儲存體中匯入資料 (其中包括網路、磁碟片、硬碟等)。

*data_file* 必須指定執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之伺服器中的有效路徑。 如果 *data_file* 是一個遠端檔案，請指定「通用命名慣例」(UNC) 名稱。 UNC 名稱的格式為 \\\\*Systemname*\\*ShareName*\\*Path*\\*FileName*。 例如：

```sql
BULK INSERT Sales.Orders
FROM '\\SystemX\DiskZ\Sales\data\orders.dat';
```

**適用範圍：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 和 Azure SQL Database。
從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 開始，data_file 可位於 Azure Blob 儲存體中。 在此情況下，您必須指定 **data_source_name** 選項。 如需範例，請參閱[從 Azure Blob 儲存體中的檔案匯入資料](#f-importing-data-from-a-file-in-azure-blob-storage)。

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

**'** _data_source_name_ **'** 
**適用於：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 和 Azure SQL Database。
這是具名的外部資料來源，指向將匯入之檔案的 Azure Blob 儲存體位置。 必須使用 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 中新增的 `TYPE = BLOB_STORAGE` 選項來建立外部資料來源。 如需詳細資訊，請參閱 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)。 如需範例，請參閱[從 Azure Blob 儲存體中的檔案匯入資料](#f-importing-data-from-a-file-in-azure-blob-storage)。

BATCHSIZE **=** _batch_size_ 指定批次中的資料列數目。 每個批次都會當做一筆交易複製到伺服器中。 如果失敗，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會認可或回復每個批次的交易。 依預設，指定之資料檔中的所有資料都是單一批次。 如需有關效能考量的詳細資訊，請參閱本主題稍後的「備註」。

CHECK_CONSTRAINTS 指定在大量匯入作業期間，必須檢查目標資料表或檢視表的所有條件約束。 當沒有 CHECK_CONSTRAINTS 選項時，會忽略所有 CHECK 和 FOREIGN KEY 條件約束，而且在作業之後，會將資料表的條件約束標記為不受信任。

> [!NOTE]
> 一律強制實施 UNIQUE 和 PRIMARY KEY 條件約束。 當匯入到使用 NOT NULL 條件約束所定義的字元資料行中時，BULK INSERT 會在文字檔中沒有任何值時插入空白字串。

在某個點上，您必須檢查整份資料表的條件約束。 如果在大量匯入作業之前，資料表不是空的，重新驗證條件約束的成本可能會超出在累加資料上套用 CHECK 條件約束的成本。

如果輸入資料包含違反條件約束的資料列，您可能會想停用條件約束 (預設行為)。 當停用 CHECK 條件約束時，您可以先匯入資料，再利用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式來移除無效的資料。

> [!NOTE]
> MAXERRORS 選項不適用於條件約束檢查。

CODEPAGE **=** { **'** ACP **'** \| **'** OEM **'** \| **'** RAW **'** \| **'** _code_page_ **'** } 指定資料檔中的資料字碼頁。 只有當資料包含字元值大於 **127** 或小於 **32** 的 **char**、**varchar** 或 **text** 資料行時，CODEPAGE 才會相關。 如需範例，請參閱[指定字碼頁](#d-specifying-a-code-page)。

> [!IMPORTANT]
> 針對 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)]，Linux 上不支援 CODEPAGE 選項。 針對 [!INCLUDE[ssSQLv15_md](../../includes/sssqlv15-md.md)]，CODEPAGE 只允許使用 **'RAW'** 選項。

> [!NOTE]
> [!INCLUDE[msCoName](../../includes/msconame-md.md)] 建議您在[格式檔案](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)中，針對每一個資料行各指定一個定序名稱。

|CODEPAGE 值|描述|
|--------------------|-----------------|
|ACP|將 **char**、**varchar** 或 **text** 資料類型的資料行，從 [!INCLUDE[vcpransi](../../includes/vcpransi-md.md)]/[!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 字碼頁 (ISO 1252) 轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 字碼頁。|
|OEM (預設值)|將 **char**、**varchar** 或 **text** 資料類型的資料行，從系統 OEM 字碼頁轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 字碼頁。|
|RAW|不進行字碼頁之間的轉換；這是最快的選項。|
|*code_page*|特定字碼頁編號，如 850。<br /><br /> **&#42;&#42; 重要 &#42;&#42;** [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 版之前的版本不支援字碼頁 65001 (UTF-8 編碼)。|
| &nbsp; | &nbsp; |

DATAFILETYPE **=** { **'char'** \| **'native'** \| **'widechar'** \| **'widenative'** } 指定 BULK INSERT 使用指定的資料檔案類型值來執行匯入作業。

&nbsp;

|DATAFILETYPE 值|所有資料的表示方式如下：|
|------------------------|------------------------------|
|**char** (預設值)|字元格式。<br /><br /> 如需詳細資訊，請參閱[使用 Unicode 字元格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)。|
|**native**|原生 (資料庫) 資料類型。 請利用 **bcp** 公用程式，從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 大量匯入資料來建立原生資料檔案。<br /><br /> 原生值提供了效能比 char 值更高的替代項。 在多個 SQL Server 執行個體之間，使用不包含任何擴充/雙位元組字集 (DBCS) 字元的資料檔大量傳送資料時，建議使用原生格式。<br /><br /> 如需詳細資訊，請參閱[使用原生格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)。|
|**widechar**|Unicode 字元。<br /><br /> 如需詳細資訊，請參閱 [使用 Unicode 字元格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)。|
|**widenative**|原生 (資料庫) 資料類型，但在 **char**、**varchar** 及 **text** 資料行中除外，其中資料會儲存成 Unicode。 請利用 **bcp** 公用程式，從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 大量匯入資料來建立 **widenative** 資料檔案。<br /><br /> **widenative** 值是效能比 **widechar** 更高的替代方案。 如果資料檔案包含 [!INCLUDE[vcpransi](../../includes/vcpransi-md.md)] 擴充字元，請指定 **widenative**。<br /><br /> 如需詳細資訊，請參閱 [使用 Unicode 原生格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-native-format-to-import-or-export-data-sql-server.md)。|
| &nbsp; | &nbsp; |

ERRORFILE **='** _file_name_ **'** 指定用來收集格式錯誤且無法轉換成 OLE DB 資料列集之資料列的檔案。 這些資料列會「依照原狀」，從資料檔複製到這個錯誤檔中。

當執行命令時，便會建立這個錯誤檔。 如果檔案已經存在，會發生一則錯誤。 另外，還會建立一個副檔名為 .ERROR.txt 的控制檔。 這會參考錯誤檔中的每個資料列，且會提供錯誤診斷。 錯誤更正之後，就能夠載入資料。
**適用範圍：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。
從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 開始，`error_file_path` 可以位於 Azure Blob 儲存體中。

'errorfile_data_source_name' **適用於：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。
這是具名的外部資料來源，指向錯誤檔案的 Azure Blob 儲存體位置，該檔案將包含在匯入期間發現的錯誤。 必須使用 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 中新增的 `TYPE = BLOB_STORAGE` 選項來建立外部資料來源。 如需詳細資訊，請參閱 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)。

FIRSTROW **=** _first_row_ 指定所要載入第一個資料列的號碼。 預設值是指定之資料檔案中的第一個資料列。 FIRSTROW 是以 1 為基底。

> [!NOTE]
> FIRSTROW 屬性不是用來略過資料行標頭。 BULK INSERT 陳述式不支援略過標頭。 如果略過資料列，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 就只會查看欄位結束字元，而且不會驗證已略過之資料列中欄位的資料。

FIRE_TRIGGERS 指定在大量匯入作業期間，執行目的地資料表上所定義的任何插入觸發程序。 如果在目標資料表上定義了 INSERT 作業的觸發程序，便會針對每個已完成的批次引發觸發程序。

如果未指定 FIRE_TRIGGERS，就不會執行任何插入觸發程序。

FORMATFILE_DATA_SOURCE **=** 'data_source_name' **適用於：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 1.1。
這是具名的外部資料來源，指向格式檔案的 Azure Blob 儲存體位置，該檔案將定義所匯入資料的結構描述。 必須使用 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 中新增的 `TYPE = BLOB_STORAGE` 選項來建立外部資料來源。 如需詳細資訊，請參閱 [CREATE EXTERNAL DATA SOURCE](../../t-sql/statements/create-external-data-source-transact-sql.md)。

KEEPIDENTITY 指定識別欄位所要使用匯入資料檔案中的一或多個識別值。 如果未指定 KEEPIDENTITY，就會驗證這個資料行的識別值但不會匯入它，而且 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會根據建立資料表期間所指定的種子值和遞增值來自動指派唯一值。 如果資料檔案中沒有資料表或檢視表中之識別欄位的值，請利用格式檔來指定，在匯入資料時略過資料表或檢視表中的識別欄位；[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會自動指派資料行的唯一值。 如需詳細資訊，請參閱 [DBCC CHECKIDENT &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-checkident-transact-sql.md)。

如需有關保留識別值的詳細資訊，請參閱[大量匯入資料時保留識別值 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-identity-values-when-bulk-importing-data-sql-server.md)。

KEEPNULLS 指定在大量匯入作業期間，空白資料行應該保留 Null 值，而不是插入資料行的任何預設值。 如需詳細資訊，請參閱[大量匯入期間保留 Null 或使用預設值 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)。

KILOBYTES_PER_BATCH **=** _kilobytes_per_batch_ 以 *kilobytes_per_batch* 指定每一批資料的大約 KB 數。 依預設，KILOBYTES_PER_BATCH 是未知的。 如需有關效能考量的詳細資訊，請參閱本主題稍後的「備註」。

LASTROW **=** _last_row_ 指定所要載入最後一個資料列的號碼。 預設值是 0，表示指定之資料檔案中的最後一個資料列。

MAXERRORS **=** _max_errors_ 指定取消大量匯入作業之前所允許的資料語法錯誤數目上限。 大量匯入作業所無法匯入的每個資料列都會被忽略，且會當做一項錯誤來計算。 如果未指定 *max_errors*，則預設值為 10。

> [!NOTE]
> MAX_ERRORS 選項不適用於條件約束檢查，或是轉換 **money** 和 **bigint** 資料類型。

ORDER ( { *column* [ ASC | DESC ] } [ **,** ... *n* ] ) 指定如何排序資料檔案中的資料。 如果匯入資料時是依照資料表的叢集索引來排序，將可提升大量匯入的效能。 如果資料檔案是依據不同於叢集索引鍵順序的其他順序來進行排序，或是資料表上沒有任何叢集索引，便會略過 ORDER 子句。 提供的資料行名稱必須是目的地資料表中的有效資料行名稱。 依預設，大量插入作業會假設資料檔案沒有排序。 為了達到最佳的大量匯入效果， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也會驗證匯入的資料是否已排序。

*n* 這是一個預留位置，表示可以指定多個資料行。

ROWS_PER_BATCH **=** _rows_per_batch_ 指出資料檔案中大約有多少資料列。

依預設，資料檔案中的所有資料都會當做單一交易來傳給伺服器，而且查詢最佳化工具並不知道批次中的資料列數。 如果您指定 ROWS_PER_BATCH (值 > 0)，伺服器會使用這個值將大量匯入作業最佳化。 ROWS_PER_BATCH 指定的值應該與實際的資料列數大約相同。 如需有關效能考量的詳細資訊，請參閱本主題稍後的「備註」。

TABLOCK 指定在大量匯入作業期間，取得資料表層級鎖定。 如果資料表沒有索引，且指定了 TABLOCK，多個用戶端便可以同時載入這份資料表。 根據預設，鎖定行為是由資料表選項 **table lock on bulk load** 所決定。 在大量匯入作業期間保留鎖定，會減少競爭資料表鎖定的情況，在某些情況下，可以大幅提升效能。 如需有關效能考量的詳細資訊，請參閱本主題稍後的「備註」。

就資料行存放區索引而言， 鎖定行為不同之處在於其內部分割為多個資料列集，每個執行緒都會藉由在允許以並行資料載入工作階段進行平行資料載入的資料列集上採取 X 鎖定，以獨佔方式將資料載入到每個資料列集。 使用 TABLOCK 選項將導致執行緒在資料表上採取 X 鎖定 (不同於傳統資料列集的 BU 鎖定)，這會防止其他並行執行緒同時載入資料。

### <a name="input-file-format-options"></a>輸入檔案格式選項

FORMAT **=** 'CSV' **適用於：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。
指定符合 [RFC 4180](https://tools.ietf.org/html/rfc4180) 規範的逗點分隔值檔案。

```sql
BULK INSERT Sales.Orders
FROM '\\SystemX\DiskZ\Sales\data\orders.csv'
WITH ( FORMAT='CSV');
```

FIELDQUOTE **=** 'field_quote' **適用於：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。
指定將用來當作 CSV 檔案中引號字元的字元。 如果未指定，則會使用引號字元 (") 當作引號字元，如 [RFC 4180](https://tools.ietf.org/html/rfc4180) 標準中所定義的。

FORMATFILE **=** '_format_file_path_' 指定格式檔案的完整路徑。 格式檔描述包含預存回應的資料檔案，這些預存回應是利用 **bcp** 公用程式在相同資料表或檢視表上建立的。 在下列情況下，應該使用格式檔：

- 資料檔案包含比資料表或檢視表更多或更少的資料行。
- 資料行的順序不同。
- 資料行分隔符號不同。
- 資料格式有其他變更。 格式檔通常是利用 **bcp** 公用程式來建立的，您可以視需要利用文字編輯器來修改它。 如需詳細資訊，請參閱 [bcp 公用程式](../../tools/bcp-utility.md)和[建立格式檔案](../../relational-databases/import-export/create-a-format-file-sql-server.md)。

**適用範圍：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 和 Azure SQL Database。
從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 開始，format_file_path 可位於 Azure Blob 儲存體中。

FIELDTERMINATOR **='** _field_terminator_ **'** 指定要用於 **char** 和 **widechar** 資料檔案的欄位結束字元。 預設欄位結束字元是 \t (定位字元)。 如需詳細資訊，請參閱 [指定欄位與資料列結束字元 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)。

ROWTERMINATOR **='** _row_terminator_ **'** 指定要用於 **char** 和 **widechar** 資料檔案的資料列結束字元。 預設的資料列結束字元是 **\r\n** (新行字元)。 如需詳細資訊，請參閱 [指定欄位與資料列結束字元 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)。

## <a name="compatibility"></a>相容性

對於從檔案中讀取的資料，BULK INSERT 會強制進行嚴格的資料驗證和資料檢查，而當現有的指令碼針對無效資料執行時，這些作業可能會造成指令碼失敗。 例如，BULK INSERT 會驗證：

- **float** 或 **real** 資料類型的原生表示法是否有效。
- Unicode 資料的長度是否為偶數位元組。

## <a name="data-types"></a>資料類型

### <a name="string-to-decimal-data-type-conversions"></a>字串到十進位資料類型轉換

BULK INSERT 中使用的字串到十進位資料類型轉換遵守與 [!INCLUDE[tsql](../../includes/tsql-md.md)][CONVERT](../../t-sql/functions/cast-and-convert-transact-sql.md) 函式相同的規則，會拒絕代表使用科學記號標記法之數值的字串。 因此，BULK INSERT 會將這類字串視為無效的值，並報告轉換錯誤。

若要因應這種行為，請使用格式檔案，將科學記號標記法 **float** 資料大量匯入至十進位資料行。 在格式檔案中，請將此資料行明確描述為 **real** 或 **float** 資料。 如需有關這些資料類型的詳細資訊，請參閱 [float 和 real &#40;Transact-SQL&#41;](../../t-sql/data-types/float-and-real-transact-sql.md)。

> [!NOTE]
> 格式檔案會以 **SQLFLT4** 資料類型來表示 **real** 資料，並以 **SQLFLT8** 資料類型來表示 **float** 資料。 如需有關非 XML 格式檔案的詳細資訊，請參閱 [使用 bcp 指定檔案儲存類型 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-file-storage-type-by-using-bcp-sql-server.md)。

#### <a name="example-of-importing-a-numeric-value-that-uses-scientific-notation"></a>匯入使用科學記號標記法之數值的範例

這個範例使用下列資料表：

```sql
CREATE TABLE t_float(c1 FLOAT, c2 DECIMAL (5,4));
```

 使用者想要將大量資料匯入 `t_float` 資料表中。 資料檔案 C:\t_float-c.dat 包含科學記號標記法 **float** 資料；例如：

```input
8.0000000000000002E-28.0000000000000002E-2
```

不過，BULK INSERT 無法直接將此資料匯入 `t_float` 中，因為它的第二個資料行 `c2` 使用 `decimal` 資料類型。 因此，格式檔案是必要的。 格式檔案必須將科學記號標記法 **float** 資料對應到資料行 `c2` 的十進位格式。

下列格式檔案使用 `SQLFLT8` 資料類型，將第二個資料欄位對應到第二個資料行：

```xml
<?xml version="1.0"?>
<BCPFORMAT xmlns="https://schemas.microsoft.com/sqlserver/2004/bulkload/format" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
<RECORD>
<FIELD ID="1" xsi:type="CharTerm" TERMINATOR="\t" MAX_LENGTH="30"/>
<FIELD ID="2" xsi:type="CharTerm" TERMINATOR="\r\n" MAX_LENGTH="30"/> </RECORD> <ROW>
<COLUMN SOURCE="1" NAME="c1" xsi:type="SQLFLT8"/>
<COLUMN SOURCE="2" NAME="c2" xsi:type="SQLFLT8"/> </ROW> </BCPFORMAT>
```

若要使用此格式檔案 (使用檔案名稱 `C:\t_floatformat-c-xml.xml`) 將測試資料匯入測試資料表，請發出下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式：

```sql
BULK INSERT bulktest..t_float
FROM 'C:\t_float-c.dat' WITH (FORMATFILE='C:\t_floatformat-c-xml.xml');
```

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

### <a name="data-types-for-bulk-exporting-or-importing-sqlxml-documents"></a>大量匯出或匯入 SQLXML 文件的資料類型

若要大量匯出或匯入 SQLXML 資料，請在格式檔案中使用下列其中一種資料類型：

|資料類型|效果|
|---------------|------------|
|SQLCHAR 或 SQLVARCHAR|資料是使用用戶端字碼頁或定序所隱含的字碼頁所傳送。 這與指定 DATAFILETYPE **='char'** 而不指定格式檔案的效果一樣。|
|SQLNCHAR 或 SQLNVARCHAR|以 Unicode 格式傳送這份資料。 這與指定 DATAFILETYPE **= 'widechar'** 而不指定格式檔案的效果一樣。|
|SQLBINARY 或 SQLVARBIN|未經任何轉換即傳送這份資料。|
| &nbsp; | &nbsp; |

## <a name="general-remarks"></a>一般備註

如需 BULK INSERT 陳述式、INSERT ...SELECT \* FROM OPENROWSET(BULK...) 陳述式及 **bcp** 命令的比較，請參閱 [資料的大量匯入及匯出 &#40;SQL Server&#41;](../../relational-databases/import-export/bulk-import-and-export-of-data-sql-server.md)。

如需有關準備資料以進行大量匯入的資訊，請參閱[準備大量匯出或匯入的資料 &#40;SQL Server&#41;](../../relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server.md)。

您可以在使用者定義交易內部執行 BULK INSERT 陳述式，以便將資料匯入資料表或檢視表。 (選擇性) 若要針對大量匯入資料使用多重比對，交易可以在 BULK INSERT 陳述式中指定 BATCHSIZE 子句。 如果多重批次交易已回復，交易已經傳送至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的每個批次都會回復。

## <a name="interoperability"></a>互通性

### <a name="importing-data-from-a-csv-file"></a>從 CSV 檔案匯入資料

從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 開始，BULK INSERT 可支援 CSV 格式，如同 Azure SQL Database。
在 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 之前，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 大量匯入作業不支援逗號分隔值 (CSV) 檔案。 不過，在某些情況下，CSV 檔案可用來當做資料檔案，以便將資料大量匯入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 如需有關從 CSV 資料檔案匯入資料的需求資訊，請參閱[準備大量匯出或匯入的資料 &#40;SQL Server&#41;](../../relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server.md)。

## <a name="logging-behavior"></a>記錄行為

 如需大量匯入 SQL Server 所執行的資料列插入作業於何時記錄到交易記錄的資訊，請參閱[大量匯入採用最低限度記錄的必要條件](../../relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import.md)。 Azure SQL Database 不支援最低限度記錄。

## <a name="restrictions"></a><a name="Limitations"></a> 限制

使用格式檔案搭配 BULK INSERT 時，最多只能指定 1024 個欄位。 這與資料表中允許的資料行數目上限相同。 如果您使用 BULK INSERT 的格式檔案搭配包含超過 1024 個欄位的資料檔案，則 BULK INSERT 會產生 4822 錯誤。 [bcp 公用程式](../../tools/bcp-utility.md)沒有此限制；因此，針對包含超過 1024 個欄位的資料檔案，請使用沒有格式檔案的 BULK INSERT 或使用 **bcp** 命令。

## <a name="performance-considerations"></a>效能考量

如果要在單一批次中排清的頁數超出內部臨界值，可能會發生緩衝集區的完整掃描，以識別批次認可時要排清的頁面。 這個完整掃描可能會損及大量匯入效能。 當大型緩衝集區與緩慢的 I/O 子系統結合時，可能會超出內部臨界值。 為避免大型電腦發生緩衝區溢位，請不要使用 TABLOCK 提示 (將會移除大量最佳化) 或使用較小的批次大小 (可保留大量最佳化)。

電腦會不斷變化，因此，我們建議您利用您的資料負荷量測試各種批次大小來找出最適合您的狀況。

使用 Azure SQL Database 時，如果您要匯入大量資料，請考慮在匯入之前暫時增加資料庫或執行個體的效能層級。

## <a name="security"></a>安全性

### <a name="security-account-delegation-impersonation"></a>委派安全性帳戶 (模擬)

如果使用者是使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入，則會使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序帳戶的安全性設定檔。 使用 SQL Server 驗證的登入無法於 Database Engine 外部進行驗證。 因此，一旦使用 SQL Server 驗證的登入起始 BULK INSERT 命令，將會使用 SQL Server 處理序帳戶 (即 SQL Server Database Engine 服務所使用的帳戶) 的安全性內容建立與資料的連接。 為了能夠成功讀取來源資料，您必須授與 SQL Server Database Engine 所使用的帳戶對來源資料的存取權。相反地，如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用者是使用 Windows 驗證登入，則該使用者只能讀取其使用者帳戶可存取的檔案，而與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序的安全性設定檔無關。

使用 **sqlcmd** 或 **osql** 來執行 BULK INSERT 陳述式時，如果將一部電腦的資料插入到第二部電腦上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，然後使用 UNC 路徑指定第三部電腦上的 *data_file* 時，您可能會收到 4861 錯誤。

若要解決這個錯誤，請使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證，並指定一個使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理帳戶之安全性設定檔的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入，或設定 Windows 來啟用安全性帳戶委派。 如需有關如何使某個使用者帳戶受到信任而委派的詳細資訊，請參閱 Windows 說明。

如需有關此安全性考量及其他使用 BULK INSERT 之安全性考量的詳細資訊，請參閱[使用 BULK INSERT 或 OPENROWSET&#40;BULK...&#41; 匯入大量資料 &#40;SQL Server&#41;](../../relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server.md)。

從 Azure Blob 儲存體匯入且資料不是公用 (匿名存取) 時，請根據使用 [MASTER KEY](create-master-key-transact-sql.md) 加密的 SAS 金鑰來建立 [DATABASE SCOPED CREDENTIAL](../../t-sql/statements/create-database-scoped-credential-transact-sql.md)，然後建立要用於 BULK INSERT 命令中的[外部資料來源](../../t-sql/statements/create-external-data-source-transact-sql.md)。 如需範例，請參閱[從 Azure Blob 儲存體中的檔案匯入資料](#f-importing-data-from-a-file-in-azure-blob-storage)。

### <a name="permissions"></a>權限

需要 INSERT 和 ADMINISTER BULK OPERATIONS 權限。 在 Azure SQL Database 中，需要 INSERT 和 ADMINISTER DATABASE BULK OPERATIONS 權限。 Linux 上的 SQL Server 不支援 ADMINISTER BULK OPERATIONS 權限或 bulkadmin 角色。 只有 `sysadmin` 可以針對 Linux 上的 SQL Server 執行大量插入。 

另外，如果以下一個或多個狀況成立，則需要 ALTER TABLE 權限：

- 有條件約束存在而且未指定 CHECK_CONSTRAINTS 選項。

   > [!NOTE]
   > 停用條件約束是預設行為。 若要明確檢查條件約束，請使用 CHECK_CONSTRAINTS 選項。

- 有觸發程序存在而且未指定 FIRE_TRIGGER 選項。

   > [!NOTE]
   > 依預設不會引發觸發程序。 若要明確引發觸發程序，請使用 FIRE_TRIGGER 選項。

- 您利用 KEEPIDENTITY 選項，從資料檔案中匯入識別值。

## <a name="examples"></a>範例

### <a name="a-using-pipes-to-import-data-from-a-file"></a>A. 利用垂直線來匯入檔案資料

下列範例利用垂直線 (`AdventureWorks2012.Sales.SalesOrderDetail`) 做為欄位結束字元，並利用 `|` 做為資料列結束字元，從指定的資料檔中，將訂單詳細資訊匯入 `|\n` 資料表中。

```sql
BULK INSERT AdventureWorks2012.Sales.SalesOrderDetail
   FROM 'f:\orders\lineitem.tbl'
   WITH
      (  
         FIELDTERMINATOR =' |'
         , ROWTERMINATOR =' |\n'
      );
```

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

### <a name="b-using-the-fire_triggers-argument"></a>B. 使用 FIRE_TRIGGERS 觸發程序

下列範例指定 `FIRE_TRIGGERS` 引數。

```sql
BULK INSERT AdventureWorks2012.Sales.SalesOrderDetail
   FROM 'f:\orders\lineitem.tbl'
   WITH
     (
         FIELDTERMINATOR =' |'
         , ROWTERMINATOR = ':\n'
         , FIRE_TRIGGERS
      );
```

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

### <a name="c-using-line-feed-as-a-row-terminator"></a>C. 利用換行字元做為資料列結束字元

 下列範例利用換行字元做為資料列結束字元來匯入檔案，如 UNIX 輸出：

```sql
DECLARE @bulk_cmd VARCHAR(1000);
SET @bulk_cmd = 'BULK INSERT AdventureWorks2012.Sales.SalesOrderDetail
FROM ''<drive>:\<path>\<filename>''
WITH (ROWTERMINATOR = '''+CHAR(10)+''')';
EXEC(@bulk_cmd);
```

> [!NOTE]
> 由於 Microsoft Windows 處理文字檔的方式， **(\n** 會自動被取代為 **\r\n)** 。

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

### <a name="d-specifying-a-code-page"></a>D. 指定字碼頁

下列範例說明如何指定字碼頁。

```sql
BULK INSERT MyTable
FROM 'D:\data.csv'
WITH
( CODEPAGE = '65001'
   , DATAFILETYPE = 'char'
   , FIELDTERMINATOR = ','
);
```

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

### <a name="e-importing-data-from-a-csv-file"></a>E. 從 CSV 檔案匯入資料

下列範例示範如何指定 CSV 檔案，跳過標頭 (第一個資料列)，使用 `;` 作為欄位結束字元，以及使用 `0x0a` 作為行結束字元：

```sql
BULK INSERT Sales.Invoices
FROM '\\share\invoices\inv-2016-07-25.csv'
WITH (FORMAT = 'CSV'
      , FIRSTROW=2
      , FIELDQUOTE = '\'
      , FIELDTERMINATOR = ';'
      , ROWTERMINATOR = '0x0a');
```

> [!IMPORTANT]
> Azure SQL Database 只支援從 Azure Blob 儲存體讀取。

### <a name="f-importing-data-from-a-file-in-azure-blob-storage"></a>F. 從 Azure Blob 儲存體中的檔案匯入資料

下列範例說明如何從建立 SAS 金鑰之 Azure Blob 儲存體位置的 CSV 檔案載入資料。 Azure Blob 儲存體位置已設定為外部資料來源。 這需要使用在使用者資料庫中以主要金鑰加密的共用存取簽章來進行資料庫範圍認證。

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
          LOCATION = 'https://****************.blob.core.windows.net/invoices'
          , CREDENTIAL= MyAzureBlobStorageCredential --> CREDENTIAL is not required if a blob is configured for public (anonymous) access!
);

BULK INSERT Sales.Invoices
FROM 'inv-2017-12-08.csv'
WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

> [!IMPORTANT]
> Azure SQL 僅支援從 Azure Blob 儲存體讀取。

### <a name="g-importing-data-from-a-file-in-azure-blob-storage-and-specifying-an-error-file"></a>G. 從 Azure Blob 儲存體中的檔案匯入資料並指定錯誤檔

下列範例說明如何在已設定為外部資料來源並指定錯誤檔的 Azure Blob 儲存體位置中，從 CSV 檔案載入資料。 這需要一個使用共用存取簽章的資料庫範圍認證。 請注意，如果在 Azure SQL Database 上執行，ERRORFILE 選項應伴隨 ERRORFILE_DATA_SOURCE，否則匯入可能會因權限錯誤而導致失敗。 ERRORFILE 中指定的檔案不應該存在容器中。

```sql
BULK INSERT Sales.Invoices
FROM 'inv-2017-12-08.csv'
WITH (
         DATA_SOURCE = 'MyAzureInvoices'
         , FORMAT = 'CSV'
         , ERRORFILE = 'MyErrorFile'
         , ERRORFILE_DATA_SOURCE = 'MyAzureInvoices');
```

如需包含設定認證和外部資料來源的完整 `BULK INSERT` 範例，請參閱[大量存取 Azure Blob 儲存體資料的範例](../../relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage.md)。

### <a name="additional-examples"></a>其他範例

 下列主題提供了其他 `BULK INSERT` 範例：

- [大量匯入與匯出 XML 文件的範例 &#40;SQL Server&#41;](../../relational-databases/import-export/examples-of-bulk-import-and-export-of-xml-documents-sql-server.md)
- [大量匯入資料時保留識別值 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-identity-values-when-bulk-importing-data-sql-server.md)
- [大量匯入期間保留 Null 或使用預設值 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)
- [指定欄位與資料列結束字元 &#40;SQL Server&#41;](../../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)
- [使用格式檔案大量匯入資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)
- [使用字元格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)
- [使用原生格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)
- [使用 Unicode 字元格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)
- [使用 Unicode 原生格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-native-format-to-import-or-export-data-sql-server.md)
- [使用格式檔案略過資料表資料行 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)
- [使用格式檔案將資料表資料行對應至資料檔案欄位 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)

## <a name="see-also"></a>另請參閱

- [資料的大量匯入及匯出 &#40;SQL Server&#41;](../../relational-databases/import-export/bulk-import-and-export-of-data-sql-server.md)
- [bcp 公用程式](../../tools/bcp-utility.md)
- [匯入或匯出資料的格式檔案 &#40;SQL Server&#41;](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)
- [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)
- [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)
- [準備大量匯出或匯入的資料 &#40;SQL Server&#41;](../../relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server.md)
- [sp_tableoption &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-tableoption-transact-sql.md)