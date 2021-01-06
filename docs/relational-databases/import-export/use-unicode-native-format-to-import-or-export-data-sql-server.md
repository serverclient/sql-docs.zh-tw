---
title: 使用 Unicode 原生格式匯入或匯出資料 (SQL Server) | Microsoft 文件
description: 使用 Unicode 原生格式在 SQL Server 的執行個體之間大量傳輸資料，可免除資料類型與字元格式之間的轉換。
ms.custom: ''
ms.date: 09/30/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: data-movement
ms.topic: conceptual
helpviewer_keywords:
- Unicode [SQL Server], bulk importing and exporting
- data formats [SQL Server], Unicode native
ms.assetid: a6213308-f3d5-406e-9029-19d8bb3367f3
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 977e7c4222a200d697304be0be774214fbc4376a
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643455"
---
# <a name="use-unicode-native-format-to-import-or-export-data-sql-server"></a>使用 Unicode 原生格式匯入或匯出資料 (SQL Server)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
當必須從某個 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝將資訊複製到其他安裝時，Unicode 原生格式很有用。 對非字元的資料使用原生格式可節省時間，消除在資料類型與字元格式之間，不必要的來回轉換。 對所有字元資料使用 Unicode 字元格式，可以防止在使用不同字碼頁的伺服器之間大量傳送資料期間，失去任何擴充字元。 任何大量匯入方法都可以讀取以 Unicode 原生格式表示的資料檔。  
  
 建議使用 Unicode 原生格式，在多個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之間，使用包含擴充字元或 DBCS 字元的資料檔，大量傳送資料。 若是非字元資料，Unicode 原生格式會使用原生 (資料庫) 資料類型。 若是字元資料，如 [char](../../t-sql/data-types/char-and-varchar-transact-sql.md)、 [nchar](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)、 [varchar](../../t-sql/data-types/char-and-varchar-transact-sql.md)、 [nvarchar](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)、 [text](../../t-sql/data-types/ntext-text-and-image-transact-sql.md)、 [varchar(max)](../../t-sql/data-types/char-and-varchar-transact-sql.md)、 [nvarchar(max)](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)及 [ntext](../../t-sql/data-types/ntext-text-and-image-transact-sql.md)，Unicode 原生格式會使用 Unicode 字元資料格式。  
  
 以 SQLVARIANT 儲存在 Unicode 原生格式資料檔的 [sql_variant](../../t-sql/data-types/sql-variant-transact-sql.md) 資料，會以它在原生格式資料檔的相同方式操作，不同處是 [char](../../t-sql/data-types/char-and-varchar-transact-sql.md) 及 [varchar](../../t-sql/data-types/char-and-varchar-transact-sql.md) 值會轉換為 [nchar](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md) 及 [nvarchar](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)，這會讓受影響資料行所需的儲存空間量加倍。 原始中繼資料會加以保留，而且在大量匯入資料表資料行時，這些值會轉換回它們的原始 [char](../../t-sql/data-types/char-and-varchar-transact-sql.md) 及 [varchar](../../t-sql/data-types/char-and-varchar-transact-sql.md) 資料類型。  
 
 |本主題內容：|
|---|
|[Unicode 原生格式的命令選項](#command_options)|
|[範例測試條件](#etc)<br />&emsp;&#9679;&emsp;[範例資料表](#sample_table)<br />&emsp;&#9679;&emsp;[範例非 XML 格式檔案](#nonxml_format_file)|
|[範例](#examples)<br />&emsp;&#9679;&emsp;[使用 BCP 與 Unicode 原生格式匯出資料](#bcp_widenative_export)<br />&emsp;&#9679;&emsp;[不使用格式檔案而使用 BCP 與 Unicode 原生格式匯入資料](#bcp_widenative_import)<br />&emsp;&#9679;&emsp;[使用 BCP 與 Unicode 原生格式匯入非 XML 格式檔案的資料](#bcp_widenative_import_fmt)<br />&emsp;&#9679;&emsp;[不使用格式檔案而使用 BULK INSERT 與 Unicode 原生格式](#bulk_widenative)<br />&emsp;&#9679;&emsp;[對非 XML 格式檔案使用 BULK INSERT 與 Unicode 原生格式](#bulk_widenative_fmt)<br />&emsp;&#9679;&emsp;[對非 XML 格式檔案使用 OPENROWSET 與 Unicode 原生格式](#openrowset_widenative_fmt)|
|[相關工作](#RelatedTasks)<p>                                                                                                                                                                                                                  </p>|
  
## <a name="command-options-for-unicode-native-format"></a>Unicode 原生格式的命令選項<a name="command_options"></a>  
您可以將 Unicode 原生格式資料匯入資料表，方法是使用 [bcp](../../tools/bcp-utility.md)、[BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) 或 [INSERT ...SELECT * FROM OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md)。對於 [bcp](../../tools/bcp-utility.md) 命令或 [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) 陳述式，您可以在陳述式中指定資料格式。  對於 [INSERT...SELECT * FROM OPENROWSET(BULK...)](../../t-sql/functions/openrowset-transact-sql.md) 陳述式，您必須在格式檔案中指定資料格式。  
  
下列命令選項支援 Unicode 原生格式：  
  
|Command|選項|描述|  
|-------------|------------|-----------------|  
|bcp|**-N**|導致 **bcp** 公用程式使用 Unicode 原生格式，這個格式會對所有非字元資料使用原生 (資料庫) 資料類型，對所有字元 (**char**、 **nchar**、 **varchar**、 **nvarchar**、 **text** 及 **ntext**) 資料使用 Unicode 字元資料格式。|  
|BULK INSERT|DATAFILETYPE **='widenative'**|使用 Unicode 原生格式大量匯入資料。|  
|OPENROWSET|N/A|必須使用格式檔案|
    
> [!NOTE]
>  或者，您可以在格式檔案中按照每個欄位指定格式。 如需詳細資訊，請參閱 [匯入或匯出資料的格式檔案 &#40;SQL Server&#41;](../../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)＞。
  
## <a name="example-test-conditions"></a>範例測試條件<a name="etc"></a>  
本主題中的範例採用下列定義的資料表、資料檔案與格式檔案。

### <a name="sample-table"></a>**範例資料表**<a name="sample_table"></a>
下列指令碼會建立測試資料庫、名為 `myWidenative` 的資料表，以及在資料表中填入一些初始值。  請在 Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) 中執行下列 Transact-SQL：
```sql
CREATE DATABASE TestDatabase;
GO

USE TestDatabase;
CREATE TABLE dbo.myWidenative ( 
    PersonID smallint NOT NULL,
    FirstName nvarchar(25) NOT NULL,
    LastName nvarchar(30) NOT NULL,
    BirthDate date,
    AnnualSalary money
);

-- Populate table
INSERT TestDatabase.dbo.myWidenative
VALUES 
(1, N'ϴAnthony', N'Grosse', '02-23-1980', 65000.00),
(2, N'❤Alica', N'Fatnowna', '11-14-1963', 45000.00),
(3, N'☎Stella', N'Rossenhain', '03-02-1992', 120000.00);

-- Review Data
SELECT * FROM TestDatabase.dbo.myWidenative;
```

### <a name="sample-non-xml-format-file"></a>**範例非 XML 格式檔案**<a name="nonxml_format_file"></a>
SQL Server 支援兩種類型的格式檔案：非 XML 格式和 XML 格式。  非 XML 格式是舊版 SQL Server 所支援的原始格式。  如需詳細資訊，請參閱 [非 XML 格式檔案 (SQL Server)](../../relational-databases/import-export/non-xml-format-files-sql-server.md) 。  下列命令將使用 [bcp 公用程式](../../tools/bcp-utility.md) ，根據 `myWidenative.fmt`的結構描述產生非 XML 格式檔案 `myWidenative`。  使用 [bcp](../../tools/bcp-utility.md) 命令建立格式檔案時，請指定 **format** 引數並使用 **nul** 取代資料檔案路徑。  format 選項也需要 **-f** 選項。  此外，以此範例為例，限定詞 **c** 會用於指定字元資料， **T** 會用於指定使用整合式安全性的信任連線。  請在命令提示字元之下，輸入下列命令：

```
bcp TestDatabase.dbo.myWidenative format nul -f D:\BCP\myWidenative.fmt -T -N

REM Review file
Notepad D:\BCP\myWidenative.fmt
```

> [!IMPORTANT]
> 請確認您的非 XML 格式檔案以歸位字元\換行字元結尾。  否則您可能會收到下列錯誤訊息︰
> 
> `SQLState = S1000, NativeError = 0`  
> `Error = [Microsoft][ODBC Driver 13 for SQL Server]I/O error while reading BCP format file`

## <a name="examples"></a>範例<a name="examples"></a>
下列範例使用以上所建立的資料庫、資料檔案與格式檔案。

### <a name="using-bcp-and-unicode-native-format-to-export-data"></a>**使用 bcp 與 Unicode 原生格式匯出資料**<a name="bcp_widenative_export"></a>
**-N** 參數與 **OUT** 命令。  注意︰後續所有範例皆會使用此範例中所建立的資料檔案。  請在命令提示字元之下，輸入下列命令：
```
bcp TestDatabase.dbo.myWidenative OUT D:\BCP\myWidenative.bcp -T -N

REM Review results
NOTEPAD D:\BCP\myWidenative.bcp
```

### <a name="using-bcp-and-unicode-native-format-to-import-data-without-a-format-file"></a>**不使用格式檔案而使用 bcp 與原生格式匯入資料**<a name="bcp_widenative_import"></a>
**-N** 參數與 **IN** 命令。  請在命令提示字元之下，輸入下列命令：
```
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myWidenative;"

REM Import data
bcp TestDatabase.dbo.myWidenative IN D:\BCP\myWidenative.bcp -T -N

REM Review results is SSMS
```

### <a name="using-bcp-and-unicode-native-format-to-import-data-with-a-non-xml-format-file"></a>**使用 bcp 與原生格式匯入非 XML 格式檔案的資料**<a name="bcp_widenative_import_fmt"></a>
**-N** 與 **-f** 參數與 **IN** 命令。  請在命令提示字元之下，輸入下列命令：
```
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myWidenative;"

REM Import data
bcp TestDatabase.dbo.myWidenative IN D:\BCP\myWidenative.bcp -f D:\BCP\myWidenative.fmt -T -N

REM Review results is SSMS
```

### <a name="using-bulk-insert-and-unicode-native-format-without-a-format-file"></a>**不使用格式檔案而使用 BULK INSERT 與原生格式**<a name="bulk_widenative"></a>
**DATAFILETYPE** 引數。  請在 Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) 中執行下列 Transact-SQL：
```sql
TRUNCATE TABLE TestDatabase.dbo.myWidenative; -- for testing
BULK INSERT TestDatabase.dbo.myWidenative
    FROM 'D:\BCP\myWidenative.bcp'
    WITH (
        DATAFILETYPE = 'widenative'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myWidenative;
```

### <a name="using-bulk-insert-and-unicode-native-format-with-a-non-xml-format-file"></a>**對非 XML 格式檔案使用 BULK INSERT 與原生格式**<a name="bulk_widenative_fmt"></a>
**FORMATFILE** 引數。  請在 Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) 中執行下列 Transact-SQL：
```sql
TRUNCATE TABLE TestDatabase.dbo.myWidenative; -- for testing
BULK INSERT TestDatabase.dbo.myWidenative
   FROM 'D:\BCP\myWidenative.bcp'
   WITH (
        FORMATFILE = 'D:\BCP\myWidenative.fmt'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myWidenative;
```

### <a name="using-openrowset-and-unicode-native-format-with-a-non-xml-format-file"></a>**對非 XML 格式檔案使用 OPENROWSET 與原生格式**<a name="openrowset_widenative_fmt"></a>
**FORMATFILE** 引數。  請在 Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS) 中執行下列 Transact-SQL：
```sql
TRUNCATE TABLE TestDatabase.dbo.myWidenative;  -- for testing
INSERT INTO TestDatabase.dbo.myWidenative
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myWidenative.bcp', 
        FORMATFILE = 'D:\BCP\myWidenative.fmt'  
        ) AS t1;

-- review results
SELECT * FROM TestDatabase.dbo.myWidenative;
```

## <a name="related-tasks"></a>相關工作<a name="RelatedTasks"></a>
若要使用大量匯入或大量匯出的資料格式  
-   [從舊版 SQL Server 匯入原生與字元格式資料](../../relational-databases/import-export/import-native-and-character-format-data-from-earlier-versions-of-sql-server.md)  
  
-   [使用字元格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)  
  
-   [使用原生格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)  
  
-   [使用 Unicode 字元格式匯入或匯出資料 &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [bcp 公用程式](../../tools/bcp-utility.md)   
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)  
  
  
