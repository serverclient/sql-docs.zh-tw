---
title: ALTER DATABASE 檔案與檔案群組
description: 使用 Transact-SQL 更新資料庫的檔案與檔案群組。
titleSuffix: SQL Server (Transact-SQL)
ms.custom: seo-lt-2019
ms.date: 02/21/2019
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ADD FILE
- ADD_FILE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- deleting files
- removing files
- deleting filegroups
- file modifications [SQL Server]
- databases [SQL Server], size
- relocating files
- databases [SQL Server], modifying
- file additions [SQL Server], ALTER DATABASE
- file moving [SQL Server]
- default filegroups
- ALTER DATABASE statement, files and filegroups
- initializing files [SQL Server]
- database files [SQL Server]
- moving files
- filegroups [SQL Server], deleting
- adding filegroups
- adding files
- database filegroups [SQL Server]
- adding log files
- modifying files
- filegroups [SQL Server], adding
- file initialization [SQL Server]
- files [SQL Server], deleting
- files [SQL Server], adding
- databases [SQL Server], moving
ms.assetid: 1f635762-f7aa-4241-9b7a-b51b22292b07
author: markingmyname
ms.author: maghan
monikerRange: =azuresqldb-mi-current||>=sql-server-2016||>=sql-server-linux-2017
ms.openlocfilehash: 271c2362f4b4313fe50a1de680937111e56f6711
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643898"
---
# <a name="alter-database-transact-sql-file-and-filegroup-options"></a>ALTER DATABASE (Transact-SQL) 檔案及檔案群組選項

修改與資料庫相關聯的檔案和檔案群組。 在資料庫中新增或移除檔案和檔案群組，以及變更資料庫或其檔案和檔案群組的屬性。 如需其他 ALTER DATABASE 選項，請參閱 [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md)。

如需語法慣例的詳細資訊，請參閱 [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-database-transact-sql-file-and-filegroup-options.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="syntax"></a>語法

```syntaxsql
ALTER DATABASE database_name
{
    <add_or_modify_files>
  | <add_or_modify_filegroups>
}

<add_or_modify_files>::=
{
    ADD FILE <filespec> [ ,...n ]
        [ TO FILEGROUP { filegroup_name } ]
  | ADD LOG FILE <filespec> [ ,...n ]
  | REMOVE FILE logical_file_name
  | MODIFY FILE <filespec>
}

<filespec>::=
(
    NAME = logical_file_name
    [ , NEWNAME = new_logical_name ]
    [ , FILENAME = {'os_file_name' | 'filestream_path' | 'memory_optimized_data_path' } ]
    [ , SIZE = size [ KB | MB | GB | TB ] ]
    [ , MAXSIZE = { max_size [ KB | MB | GB | TB ] | UNLIMITED } ]
    [ , FILEGROWTH = growth_increment [ KB | MB | GB | TB| % ] ]
    [ , OFFLINE ]
)

<add_or_modify_filegroups>::=
{
    | ADD FILEGROUP filegroup_name
        [ CONTAINS FILESTREAM | CONTAINS MEMORY_OPTIMIZED_DATA ]
    | REMOVE FILEGROUP filegroup_name
    | MODIFY FILEGROUP filegroup_name
        { <filegroup_updatability_option>
        | DEFAULT
        | NAME = new_filegroup_name
        | { AUTOGROW_SINGLE_FILE | AUTOGROW_ALL_FILES }
        }
}
<filegroup_updatability_option>::=
{
    { READONLY | READWRITE }
    | { READ_ONLY | READ_WRITE }
}
```

## <a name="arguments"></a>引數

**\<add_or_modify_files>::=**

指定要新增、移除或修改的檔案。

*database_name*：這是要修改的資料庫名稱。

ADD FILE 將檔案新增至資料庫。

TO FILEGROUP { *filegroup_name* } 指定要新增指定檔案的目標檔案群組。 若要顯示目前檔案群組及目前是預設值的檔案群組，請使用 [sys.filegroups](../../relational-databases/system-catalog-views/sys-filegroups-transact-sql.md) 目錄檢視。

ADD LOG FILE 將記錄檔新增至指定的資料庫。

REMOVE FILE *logical_file_name* 從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中移除邏輯檔案描述並刪除實體檔案。 除非檔案是空的，否則無法移除檔案。

*logical_file_name* 這是在參考檔案時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所用的邏輯名稱。

> [!WARNING]
> 移除具有與其建立關聯之 `FILE_SNAPSHOT` 備份的資料庫會成功，但將不會刪除任何相關聯的快照集，以避免使備份參考資料庫檔案不正確。 檔案將會被截斷，但實體不會被刪除，以保存完整的 FILE_SNAPSHOT 備份。 如需詳細資訊，請參閱 [SQL Server 備份及還原與 Microsoft Azure Blob 儲存體服務](../../relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service.md)。 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本)。

MODIFY FILE 指定應該修改的檔案。 每次只能變更一個 \<filespec> 屬性。 您必須在 \<filespec> 中指定 NAME，以識別要修改的檔案。 如果指定了 SIZE，新的大小必須大於目前檔案大小。

若要修改資料檔或記錄檔的邏輯名稱，請在 `NAME` 子句中指定要重新命名的邏輯檔案名稱，並在 `NEWNAME` 子句中指定檔案的新邏輯名稱。 例如：

```sql
MODIFY FILE ( NAME = logical_file_name, NEWNAME = new_logical_name )
```

若要將資料檔或記錄檔移至新的位置，請在 `NAME` 子句中指定目前的邏輯檔案名稱，並在 `FILENAME` 子句中指定新的路徑和作業系統檔案名稱。 例如：

```sql
MODIFY FILE ( NAME = logical_file_name, FILENAME = ' new_path/os_file_name ' )
```

當您移動全文檢索目錄時，只能在 FILENAME 子句中指定新的路徑。 請勿指定作業系統檔案名稱。

如需詳細資訊，請參閱[移動資料庫檔案](../../relational-databases/databases/move-database-files.md)。

如果是 FILESTREAM 檔案群組，可以在線上修改 NAME。 可以在線上修改 FILENAME；但是，要等到實際重新放置容器且伺服器關閉並重新啟動之後，變更才會生效。

您可以將 FILESTREAM 檔案設定為 OFFLINE。 當 FILESTREAM 檔案離線時，它的父檔案群組會在內部標示為離線；因此，該檔案群組內 FILESTREAM 資料的所有存取都將失敗。

> [!NOTE]
> 自主資料庫中無法使用 \<add_or_modify_files> 選項。

**\<filespec>::=**

控制檔案屬性。

NAME *logical_file_name* 指定檔案的邏輯名稱。

*logical_file_name* 這是在參考檔案時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體所用的邏輯名稱。

NEWNAME *new_logical_file_name* 指定檔案的新邏輯名稱。

*new_logical_file_name* 這是要取代現有邏輯檔案名稱的名稱。 這個名稱在資料庫內必須是唯一的，且必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。 這個名稱可以是字元或 Unicode 常數、正規識別碼，或分隔的識別碼。

FILENAME { **'** _os\_file\_name_ **'** | **'** _filestream\_path_ **'** | **'** _memory\_optimized\_data\_path_ **'** } 指定作業系統 (實體) 檔案名稱。

' *os_file_name* ' 如果是標準 (ROWS) 檔案群組，這就是當您建立檔案時，作業系統所用的路徑和檔案名稱。 這個檔案必須在安裝了 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的伺服器中。 在執行 ALTER DATABASE 陳述式之前，指定的路徑必須已經存在。

> [!NOTE]
> 當指定檔案的 UNC 路徑時，無法設定 `SIZE`、`MAXSIZE` 和 `FILEGROWTH` 參數。
>
> 系統資料庫無法位於 UNC 共用目錄。

除非檔案是唯讀的次要檔案，或資料庫是唯讀的，否則，不應將資料檔放在壓縮的檔案系統中。 記錄檔永遠不應放在壓縮的檔案系統中。

如果檔案在原始磁碟分割中，*os_file_name* 只能指定現有原始磁碟分割的磁碟機代號。 每個原始分割區只能放置一個檔案。

**'** *filestream_path* **'** 針對 FILESTREAM 檔案群組，FILENAME 會參考將儲存 FILESTREAM 資料的路徑。 到最後一個資料夾為止的路徑必須存在，而最後一個資料夾則不得存在。 例如，如果您指定 `C:\MyFiles\MyFilestreamData` 路徑，則在您執行 ALTER DATABASE 之前，`C:\MyFiles` 必須存在；但是 `MyFilestreamData` 資料夾不得存在。

> [!NOTE]
> SIZE 和 FILEGROWTH 屬性不會套用到 FILESTREAM 檔案群組。

**'** *memory_optimized_data_path* **'** 針對記憶體最佳化的檔案群組，FILENAME 會參考將儲存記憶體最佳化資料的路徑。 到最後一個資料夾為止的路徑必須存在，而最後一個資料夾則不得存在。 例如，如果您指定 `C:\MyFiles\MyData` 路徑，則在您執行 ALTER DATABASE 之前，`C:\MyFiles` 必須存在；但是 `MyData` 資料夾不得存在。

檔案群組和檔案 (`<filespec>`) 必須在相同的陳述式中建立。

> [!NOTE]
> SIZE 和 FILEGROWTH 屬性不適用 MEMORY_OPTIMIZED_DATA 檔案群組。

如需記憶體最佳化檔案群組的詳細資訊，請參閱[記憶體最佳化檔案群組](../../relational-databases/in-memory-oltp/the-memory-optimized-filegroup.md)。

SIZE *size* 指定檔案大小。 SIZE 不會套用到 FILESTREAM 檔案群組。

*size* 這是檔案的大小。

當使用 ADD FILE 來指定時，*size* 是檔案的起始大小。 當使用 MODIFY FILE 來指定時，*size* 是檔案的新大小，且必須大於目前的檔案大小。

當 *size* 未提供主要檔案的大小時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會使用 **model** 資料庫中的主要檔案大小。 當已指定次要資料檔或記錄檔，但未指定檔案的 *size* 時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會讓檔案的大小變成 1 MB。

您可以利用 KB、MB、GB 和 TB 後置詞來指定千位元組、百萬位元組、十億位元組或兆位元組。 預設值是 MB。 請指定不包括小數的整數。 若要指定 MB 的小數，請將數字乘以 1024，以便將值轉換成 KB。 例如，請指定 1536 KB，而不要指定 1.5 MB (1.5 x 1024 = 1536)。

> [!NOTE]
> 無法設定 `SIZE`：
>
> - 為檔案指定 UNC 路徑時
> - 若為 `FILESTREAM` 與 `MEMORY_OPTIMIZED_DATA` 檔案群組

MAXSIZE { *max_size*| UNLIMITED } 指定檔案所能成長的檔案大小上限。

*max_size* 這是檔案大小上限。 您可以利用 KB、MB、GB 和 TB 後置詞來指定千位元組、百萬位元組、十億位元組或兆位元組。 預設值是 MB。 請指定不包括小數的整數。 如果未指定 *max_size*，檔案大小會增加到磁碟已滿。

UNLIMITED 指定檔案可成長直到磁碟已滿。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，指定為無限成長的記錄檔，大小上限是 2 TB，資料檔案的大小上限是 16 TB。 為 FILESTREAM 容器指定這個選項時沒有最大大小。 它會繼續成長，直到磁碟已滿。

> [!NOTE]
> 為檔案指定 UNC 路徑時，無法設定 `MAXSIZE`。

FILEGROWTH *growth_increment* 指定檔案的自動成長遞增。 檔案的 FILEGROWTH 設定不能超過 MAXSIZE 設定。 FILEGROWTH 不會套用到 FILESTREAM 檔案群組。

*growth_increment* 這是每次需要新空間時，檔案所增加的空間量。

您可以利用 MB、KB、GB、TB 或百分比 (%) 來指定這個值。 如果指定的數字不含 MB、KB 或 % 後置詞，預設值是 MB。 當指定 % 時，成長遞增大小便是遞增發生時，檔案大小的指定百分比。 指定的大小會捨入到最接近 64 KB。

0 值表示將自動成長設為關閉，且不允許任何其他空間。

如果未指定 FILEGROWTH，預設值為：

|版本|預設值|
|-------------|--------------------|
|從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始|資料 64 MB。 記錄檔 64 MB。|
|從 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 開始|資料 1 MB。 記錄檔 10%。|
|[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 之前|資料 10%。 記錄檔 10%。|

> [!NOTE]
> 無法設定 `FILEGROWTH`：
>
> - 為檔案指定 UNC 路徑時
> - 若為 `FILESTREAM` 與 `MEMORY_OPTIMIZED_DATA` 檔案群組

OFFLINE 將檔案設成離線，使檔案群組中的所有物件都無法存取。

> [!CAUTION]
> 請只在檔案損毀且可以還原時，才使用這個選項。 設為 OFFLINE 的檔案，只能藉由從備份中還原檔案來設成線上狀態。 如需還原單一檔案的詳細資訊，請參閱 [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)。
>
> 自主資料庫中無法使用 \<filespec> 選項。

**\<add_or_modify_filegroups>::=**

在資料庫中新增、修改或移除檔案群組。

ADD FILEGROUP *filegroup_name* 將檔案群組新增至資料庫。

CONTAINS FILESTREAM 指定檔案群組會將 FILESTREAM 二進位大型物件 (BLOB) 儲存在檔案系統中。

CONTAINS MEMORY_OPTIMIZED_DATA

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本)

指定檔案群組將記憶體最佳化的資料儲存在檔案系統中。 如需詳細資訊，請參閱[記憶體內部 OLTP - 記憶體內部最佳化](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)。 每個資料庫只允許一個 `MEMORY_OPTIMIZED_DATA` 檔案群組。 若要建立記憶體最佳化的資料表，檔案群組不能空白。 至少必須有一個檔案。 *filegroup_name* 參考至路徑。 到最後一個資料夾為止的路徑必須存在，而最後一個資料夾則不得存在。

REMOVE FILEGROUP *filegroup_name* 從資料庫中移除檔案群組。 除非檔案群組是空的，否則無法移除檔案群組。 請先移除檔案群組中的所有檔案。 如需詳細資訊，請參閱本主題中稍早的 "REMOVE FILE *logical_file_name*"。

> [!NOTE]
> 除非 FILESTREAM 記憶體回收行程已移除 FILESTREAM 容器中的所有檔案，否則移除 FILESTREAM 容器的 `ALTER DATABASE REMOVE FILE` 作業會失敗並傳回錯誤訊息。 請參閱本主題稍後的[移除 FILESTREAM 容器](#removing-a-filestream-container)一節。

MODIFY FILEGROUP *filegroup_name* { \<filegroup_updatability_option> | DEFAULT | NAME **=** _new\_filegroup\_name_ } 透過將狀態設定為 READ_ONLY 或 READ_WRITE 來修改檔案群組，讓檔案群組成為資料庫的預設檔案群組，或變更檔案群組名稱。

\<filegroup_updatability_option> 會將檔案群組的屬性設成唯讀或讀取/寫入。

DEFAULT 將預設的資料庫檔案群組變更為 *filegroup_name*。 資料庫中只能有一個檔案群組是預設檔案群組。 如需相關資訊，請參閱 [Database Files and Filegroups](../../relational-databases/databases/database-files-and-filegroups.md)。

NAME = *new_filegroup_name* 將檔案群組名稱變更為 *new_filegroup_name*。

AUTOGROW_SINGLE_FILE **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本)

當檔案群組中的某個檔案達到自動成長閾值時，只有該檔案會成長。 這是預設值。

AUTOGROW_ALL_FILES

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本)

當檔案群組中的某個檔案達到自動成長閾值時，檔案群組中的所有檔案都會成長。

> [!NOTE]
> 這是 TempDB 的預設值。

**\<filegroup_updatability_option>::=**

將檔案群組的屬性設成唯讀或讀取/寫入。

READ_ONLY | READONLY 將檔案群組指定成唯讀狀態。 不允許更新其中的物件。 主要檔案群組不能設為唯讀。 若要變更這個狀態，您必須具有資料庫的獨佔存取權。 如需詳細資訊，請參閱 SINGLE_USER 子句。

由於唯讀資料庫不允許修改資料，因此，會出現下列情況：

- 在系統開機時跳過自動復原。
- 不可能壓縮資料庫。
- 唯讀資料庫不會出現鎖定。 因此，查詢效能會比較快。

> [!NOTE]
> 在未來的 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，將移除 `READONLY` 關鍵字。 請避免在新的開發工作中使用 `READONLY`，並規劃修改目前使用 `READONLY` 的應用程式。 請改用 `READ_ONLY`。

READ_WRITE | READWRITE 將群組指定成 READ_WRITE 狀態。 檔案群組中的物件可以更新。 若要變更這個狀態，您必須具有資料庫的獨佔存取權。 如需詳細資訊，請參閱 SINGLE_USER 子句。

> [!NOTE]
> 在未來的 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，將移除 `READWRITE` 關鍵字。 請避免在新的開發工作中使用 `READWRITE`，並規劃修改目前在使用 `READWRITE` 的應用程式，改為使用 `READ_WRITE`。
> [!TIP]
> 您可以檢查 **sys.databases** 目錄檢視中的 **is_read_only** 資料行或 `DATABASEPROPERTYEX` 函數的 **Updateability** 屬性來判斷這些選項的狀態。

## <a name="remarks"></a>備註

若要縮小資料庫大小，請使用 [DBCC SHRINKDATABASE](../../t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql.md)。

當 `BACKUP` 陳述式正在執行時，您不能新增或移除檔案。

每個資料庫最多可以指定 32,767 個檔案和 32,767 個檔案群組。

自 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 起，資料庫檔案狀態 (如線上或離線) 的維護與資料庫狀態無關。 如需詳細資訊，請參閱[檔案狀態](../../relational-databases/databases/file-states.md)。

- 檔案群組內的檔案狀態決定了整個檔案群組的可用性。 若要使某個檔案群組為可用的，則在檔案群組中的所有檔案必須都在線上。
- 如果檔案群組離線，SQL 陳述式存取檔案群組的任何嘗試都會失敗，且會出現錯誤。 當您建置 `SELECT` 陳述式的查詢計劃時，查詢最佳化工具會避開在離線檔案群組中的非叢集索引和索引檢視表。 這樣會讓這些陳述式能夠執行成功。 不過，如果離線檔案群組包含目標資料表的堆積或叢集索引，`SELECT` 陳述式將會失敗。 此外，在離線檔案群組中，以 `INSERT`、`UPDATE` 或 `DELETE` 陳述式修改含有索引的資料表將會失敗。

當指定檔案的 UNC 路徑時，無法設定 SIZE、MAXSIZE 和 FILEGROWTH 參數。

無法為記憶體最佳化檔案群組設定 SIZE 和 FILEGROWTH 參數。

在未來的 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，將移除 `READONLY` 關鍵字。 請避免在新的開發工作中使用 `READONLY`，並規劃修改目前使用 READONLY 的應用程式。 請改用 `READ_ONLY`。

在未來的 `READWRITE`[!INCLUDE[msCoName](../../includes/msconame-md.md)] 版本中，將移除 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 關鍵字。 請避免在新的開發工作中使用 `READWRITE`，並規劃修改目前在使用 `READWRITE` 的應用程式，改為使用 `READ_WRITE`。

## <a name="moving-files"></a>移動檔案

您可以在 FILENAME 中指定新位置來移動系統或使用者定義資料檔和記錄檔。 在下列狀況中，這非常有用：

- 失敗復原。 例如，資料庫處於質疑模式或硬體故障造成關閉。
- 計畫的重新放置。
- 排程的磁碟維護重新放置。

如需詳細資訊，請參閱[移動資料庫檔案](../../relational-databases/databases/move-database-files.md)。

## <a name="initializing-files"></a>初始化檔案

依預設，資料檔和記錄檔初始化的方式是在您執行下列作業之一時，在檔案中填入 0：

- 建立資料庫。
- 將檔案加入現有的資料庫中。
- 增加現有檔案的大小。
- 還原資料庫或檔案群組。

資料檔可以立即初始化。 這可以加快這些檔案作業的執行速度。 如需詳細資訊，請參閱 [資料庫檔案初始化](../../relational-databases/databases/database-instant-file-initialization.md)。

## <a name="removing-a-filestream-container"></a><a name="removing-a-filestream-container"></a> 移除 FILESTREAM 容器

即使 FILESTREAM 容器已使用 "DBCC SHRINKFILE" 作業來清空，基於各種系統維護原因，資料庫可能仍然需要維護已刪除檔案的參考。 [sp_filestream_force_garbage_collection](../../relational-databases/system-stored-procedures/filestream-and-filetable-sp-filestream-force-garbage-collection.md) 會在可安全執行時，執行 FILESTREAM 記憶體回收行程來移除這些檔案。 除非 FILESTREAM 記憶體回收行程已移除 FILESTREAM 容器中的所有檔案，否則 ALTER DATABASE REMOVE FILE 作業會無法移除 FILESTREAM 容器並傳回錯誤。 以下是移除 FILESTREAM 容器的建議處理序。

1. 搭配 EMPTYFILE 選項執行 [DBCC SHRINKFILE](../../t-sql/database-console-commands/dbcc-shrinkfile-transact-sql.md)，以將此容器內的作用中內容移至其他容器。
2. 確定已透過 FULL 或 BULK_LOGGED 復原模式建立記錄備份。
3. 確定已執行複寫記錄讀取器作業 (如果相關)。
4. 執行 [sp_filestream_force_garbage_collection](../../relational-databases/system-stored-procedures/filestream-and-filetable-sp-filestream-force-garbage-collection.md) 強制記憶體回收行程，以刪除此容器中不再需要的任何檔案。
5. 執行 ALTER DATABASE 搭配 REMOVE FILE 選項，移除這個容器。
6. 再次重複步驟 2 到 4，以完成記憶體回收。
7. 使用 ALTER Database...REMOVE FILE 移除此容器。

## <a name="examples"></a>範例

### <a name="a-adding-a-file-to-a-database"></a>A. 將檔案加入資料庫中

下列範例會將 5 MB 的資料檔加入 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中。

```sql
USE master;
GO
ALTER DATABASE AdventureWorks2012
ADD FILE
(
    NAME = Test1dat2,
    FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\t1dat2.ndf',
    SIZE = 5MB,
    MAXSIZE = 100MB,
    FILEGROWTH = 5MB
);
GO
```

### <a name="b-adding-a-filegroup-with-two-files-to-a-database"></a>B. 將含有兩個檔案的檔案群組加入資料庫中

下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中建立 `Test1FG1` 檔案群組，且會將兩個 5 MB 的檔案加入檔案群組中。

```sql
USE master
GO
ALTER DATABASE AdventureWorks2012
ADD FILEGROUP Test1FG1;
GO
ALTER DATABASE AdventureWorks2012
ADD FILE
(
    NAME = test1dat3,
    FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\t1dat3.ndf',
    SIZE = 5MB,
    MAXSIZE = 100MB,
    FILEGROWTH = 5MB
),  
(  
    NAME = test1dat4,
    FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\t1dat4.ndf',
    SIZE = 5MB,
    MAXSIZE = 100MB,
    FILEGROWTH = 5MB
)  
TO FILEGROUP Test1FG1;
GO
```

### <a name="c-adding-two-log-files-to-a-database"></a>C. 將兩個記錄檔加入資料庫中

下列範例會將兩個 5 MB 的記錄檔加入 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中。

```sql
USE master;
GO
ALTER DATABASE AdventureWorks2012
ADD LOG FILE
(
    NAME = test1log2,
    FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\test2log.ldf',
    SIZE = 5MB,
    MAXSIZE = 100MB,
    FILEGROWTH = 5MB
),
(
    NAME = test1log3,
    FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL10_50.MSSQLSERVER\MSSQL\DATA\test3log.ldf',
    SIZE = 5MB,
    MAXSIZE = 100MB,
    FILEGROWTH = 5MB
);
GO
```

### <a name="d-removing-a-file-from-a-database"></a>D. 從資料庫中移除檔案

下列範例會移除 B 範例中所加入的其中一個檔案。

```sql
USE master;
GO
ALTER DATABASE AdventureWorks2012
REMOVE FILE test1dat4;
GO
```

### <a name="e-modifying-a-file"></a>E. 修改檔案

下列範例會增加 B 範例中所新增的其中一個檔案大小。ALTER DATABASE 搭配 MODIFY FILE 命令只能讓檔案大小更大，若您需要讓檔案大小更小，則必須使用 DBCC SHRINKFILE。

```sql
USE master;
GO

ALTER DATABASE AdventureWorks2012
MODIFY FILE
(NAME = test1dat3,
SIZE = 200MB);
GO
```

此範例將資料檔案的大小縮小至 100 MB，並將大小指定為該數量。

```sql
USE AdventureWorks2012;
GO

DBCC SHRINKFILE (AdventureWorks2012_data, 100);
GO

USE master;
GO
ALTER DATABASE AdventureWorks2012
MODIFY FILE
(NAME = test1dat3,
SIZE = 200MB);
GO
```

### <a name="f-moving-a-file-to-a-new-location"></a>F. 將檔案移到新位置

下列範例會將 A 範例中所建立的 `Test1dat2` 檔移至新目錄中。

> [!NOTE]
> 您必須實際上將檔案移到新目錄之後，才能執行這個範例。 之後，請停止再啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體，或使 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫離線 (OFFLINE) 再連接 (ONLINE) 來實作變更。

```sql
USE master;
GO
ALTER DATABASE AdventureWorks2012
MODIFY FILE
(
    NAME = Test1dat2,
    FILENAME = N'c:\t1dat2.ndf'
);
GO
```

### <a name="g-moving-tempdb-to-a-new-location"></a>G. 將 tempdb 移到新位置

下列範例會將 `tempdb` 從磁碟中目前的位置移到另一個磁碟位置。 由於在每次啟動 MSSQLSERVER 服務時都會重新建立 `tempdb`，因此您不需要實際移動資料和記錄檔。 在步驟 3 重新啟動此服務時，將會建立檔案。 在重新啟動此服務之前，`tempdb` 會繼續在現有的位置運作。

1. 決定 `tempdb` 資料庫的邏輯檔案名稱，及它們目前在磁碟中的位置。

    ```sql
    SELECT name, physical_name
    FROM sys.master_files
    WHERE database_id = DB_ID('tempdb');
    GO
    ```

2. 請利用 `ALTER DATABASE`來變更每個檔案的位置。

    ```sql
    USE master;
    GO
    ALTER DATABASE tempdb
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\SQLData\tempdb.mdf');
    GO
    ALTER DATABASE tempdb
    MODIFY FILE (NAME = templog, FILENAME = 'E:\SQLData\templog.ldf');
    GO
    ```

3. 停止和重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體。

4. 確認檔案變更。

    ```sql
    SELECT name, physical_name
    FROM sys.master_files
    WHERE database_id = DB_ID('tempdb');
    ```

5. 將 tempdb.mdf 和 templog.ldf 檔案從它們的原始位置刪除。

### <a name="h-making-a-filegroup-the-default"></a>H. 使檔案群組成為預設值

下列範例將在範例 B 中建立的 `Test1FG1` 檔案群組設定為預設檔案群組。 之後，預設檔案群組會重設為 `PRIMARY` 檔案群組。 請注意，您必須用方括號或引號來分隔 `PRIMARY`。

```sql
USE master;
GO
ALTER DATABASE AdventureWorks2012
MODIFY FILEGROUP Test1FG1 DEFAULT;
GO
ALTER DATABASE AdventureWorks2012
MODIFY FILEGROUP [PRIMARY] DEFAULT;
GO
```

### <a name="i-adding-a-filegroup-using-alter-database"></a>I. 使用 ALTER DATABASE 加入檔案群組

下列範例會將包含 `FILEGROUP` 子句的 `FILESTREAM` 加入 `FileStreamPhotoDB` 資料庫。

```sql
--Create and add a FILEGROUP that CONTAINS the FILESTREAM clause.
ALTER DATABASE FileStreamPhotoDB
ADD FILEGROUP TodaysPhotoShoot
CONTAINS FILESTREAM;
GO

--Add a file for storing database photos to FILEGROUP
ALTER DATABASE FileStreamPhotoDB
ADD FILE
(
  NAME= 'PhotoShoot1',
  FILENAME = 'C:\Users\Administrator\Pictures\TodaysPhotoShoot.ndf'
)
TO FILEGROUP TodaysPhotoShoot;
GO
```

下列範例會將包含 `FILEGROUP` 子句的 `MEMORY_OPTIMIZED_DATA` 加入 `xtp_db` 資料庫。 檔案群組儲存記憶體最佳化的資料。

```sql
--Create and add a FILEGROUP that CONTAINS the MEMORY_OPTIMIZED_DATA clause.
ALTER DATABASE xtp_db
ADD FILEGROUP xtp_fg
CONTAINS MEMORY_OPTIMIZED_DATA;
GO

--Add a file for storing memory optimized data to FILEGROUP
ALTER DATABASE xtp_db
ADD FILE
(
  NAME='xtp_mod',
  FILENAME='d:\data\xtp_mod'
)
TO FILEGROUP xtp_fg;
GO
```

### <a name="j-change-filegroup-so-that-when-a-file-in-the-filegroup-meets-the-autogrow-threshold-all-files-in-the-filegroup-grow"></a>J. 變更檔案群組，以在當檔案群組中的某個檔案達到自動成長閾值時，檔案群組中的所有檔案都會成長

 下列範例會產生 `ALTER DATABASE` 陳述式，以使用 `AUTOGROW_ALL_FILES` 設定來修改讀寫檔案群組。

```sql
--Generate ALTER DATABASE ... MODIFY FILEGROUP statements
--so that all read-write filegroups grow at the same time.
SET NOCOUNT ON;

DROP TABLE IF EXISTS #tmpdbs
CREATE TABLE #tmpdbs (id INT IDENTITY(1,1), [dbid] INT, [dbname] sysname, isdone BIT);

DROP TABLE IF EXISTS #tmpfgs
CREATE TABLE #tmpfgs (id INT IDENTITY(1,1), [dbid] INT, [dbname] sysname, fgname sysname, isdone BIT);

INSERT INTO #tmpdbs ([dbid], [dbname], [isdone])
SELECT database_id, name, 0 FROM master.sys.databases (NOLOCK) WHERE is_read_only = 0 AND state = 0;

DECLARE @dbid INT, @query VARCHAR(1000), @dbname sysname, @fgname sysname

WHILE (SELECT COUNT(id) FROM #tmpdbs WHERE isdone = 0) > 0
BEGIN
  SELECT TOP 1 @dbname = [dbname], @dbid = [dbid] FROM #tmpdbs WHERE isdone = 0

  SET @query = 'SELECT ' + CAST(@dbid AS NVARCHAR) + ', ''' + @dbname + ''', [name], 0 FROM [' + @dbname + '].sys.filegroups WHERE [type] = ''FG'' AND is_read_only = 0;'
  INSERT INTO #tmpfgs
  EXEC (@query)

  UPDATE #tmpdbs
  SET isdone = 1
  WHERE [dbid] = @dbid
END;

IF (SELECT COUNT(ID) FROM #tmpfgs) > 0
BEGIN
  WHILE (SELECT COUNT(id) FROM #tmpfgs WHERE isdone = 0) > 0
  BEGIN
    SELECT TOP 1 @dbname = [dbname], @dbid = [dbid], @fgname = fgname FROM #tmpfgs WHERE isdone = 0

    SET @query = 'ALTER DATABASE [' + @dbname + '] MODIFY FILEGROUP [' + @fgname + '] AUTOGROW_ALL_FILES;'

    PRINT @query

    UPDATE #tmpfgs
    SET isdone = 1
    WHERE [dbid] = @dbid AND fgname = @fgname
  END
END;
GO
```

## <a name="see-also"></a>另請參閱

- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)
- [sp_spaceused](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md)
- [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)
- [sys.database_files](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)
- [sys.data_spaces](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)
- [sys.filegroups](../../relational-databases/system-catalog-views/sys-filegroups-transact-sql.md)
- [sys.master_files](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)
- [二進位大型物件](../../relational-databases/blob/binary-large-object-blob-data-sql-server.md)
- [DBCC SHRINKFIL](../../t-sql/database-console-commands/dbcc-shrinkfile-transact-sql.md)
- [sp_filestream_force_garbage_collection](../../relational-databases/system-stored-procedures/filestream-and-filetable-sp-filestream-force-garbage-collection.md)
- [資料庫檔案初始化](../../relational-databases/databases/database-instant-file-initialization.md)

::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](alter-database-transact-sql-file-and-filegroup-options.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL 受控執行個體\*_**<br />&nbsp;
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-sql-managed-instance"></a>Azure SQL 受控執行個體

將此陳述式與 Azure SQL 受控執行個體中的資料庫搭配使用。

## <a name="syntax-for-azure-sql-managed-instance"></a>Azure SQL 受控執行個體的語法

```syntaxsql
ALTER DATABASE database_name
{
    <add_or_modify_files>
  | <add_or_modify_filegroups>
}
[;]

<add_or_modify_files>::=
{
    ADD FILE <filespec> [ ,...n ]
        [ TO FILEGROUP { filegroup_name } ]
  | REMOVE FILE logical_file_name
  | MODIFY FILE <filespec>
}
  
<filespec>::=
(
    NAME = logical_file_name
    [ , SIZE = size [ KB | MB | GB | TB ] ]
    [ , MAXSIZE = { max_size [ KB | MB | GB | TB ] | UNLIMITED } ]
    [ , FILEGROWTH = growth_increment [ KB | MB | GB | TB| % ] ]
)

<add_or_modify_filegroups>::=
{
    | ADD FILEGROUP filegroup_name
    | REMOVE FILEGROUP filegroup_name
    | MODIFY FILEGROUP filegroup_name
        { <filegroup_updatability_option>
        | DEFAULT
        | NAME = new_filegroup_name
        | { AUTOGROW_SINGLE_FILE | AUTOGROW_ALL_FILES }
        }
}  
<filegroup_updatability_option>::=
{
    { READONLY | READWRITE }
    | { READ_ONLY | READ_WRITE }
}
```

## <a name="arguments"></a>引數

**\<add_or_modify_files>::=**

指定要新增、移除或修改的檔案。

*database_name*：這是要修改的資料庫名稱。

ADD FILE 將檔案新增至資料庫。

TO FILEGROUP { *filegroup_name* } 指定要新增指定檔案的目標檔案群組。 若要顯示目前檔案群組及目前是預設值的檔案群組，請使用 [sys.filegroups](../../relational-databases/system-catalog-views/sys-filegroups-transact-sql.md) 目錄檢視。

REMOVE FILE *logical_file_name* 從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中移除邏輯檔案描述並刪除實體檔案。 除非檔案是空的，否則無法移除檔案。

*logical_file_name* 這是在參考檔案時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所用的邏輯名稱。

MODIFY FILE 指定應該修改的檔案。 每次只能變更一個 \<filespec> 屬性。 您必須在 \<filespec> 中指定 NAME，以識別要修改的檔案。 如果指定了 SIZE，新的大小必須大於目前檔案大小。

**\<filespec>::=**

控制檔案屬性。

NAME *logical_file_name* 指定檔案的邏輯名稱。

*logical_file_name* 這是在參考檔案時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體所用的邏輯名稱。

NEWNAME *new_logical_file_name* 指定檔案的新邏輯名稱。

*new_logical_file_name* 這是要取代現有邏輯檔案名稱的名稱。 這個名稱在資料庫內必須是唯一的，且必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。 這個名稱可以是字元或 Unicode 常數、正規識別碼，或分隔的識別碼。

SIZE *size* 指定檔案大小。

*size* 這是檔案的大小。

當使用 ADD FILE 來指定時，*size* 是檔案的起始大小。 當使用 MODIFY FILE 來指定時，*size* 是檔案的新大小，且必須大於目前的檔案大小。

當 *size* 未提供主要檔案的大小時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會使用 **model** 資料庫中的主要檔案大小。 當已指定次要資料檔或記錄檔，但未指定檔案的 *size* 時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會讓檔案的大小變成 1 MB。

您可以利用 KB、MB、GB 和 TB 後置詞來指定千位元組、百萬位元組、十億位元組或兆位元組。 預設值是 MB。 請指定不包括小數的整數。 若要指定 MB 的小數，請將數字乘以 1024，以便將值轉換成 KB。 例如，請指定 1536 KB，而不要指定 1.5 MB (1.5 x 1024 = 1536)。

MAXSIZE { *max_size*| UNLIMITED } 指定檔案所能成長的檔案大小上限。

*max_size* 這是檔案大小上限。 您可以利用 KB、MB、GB 和 TB 後置詞來指定千位元組、百萬位元組、十億位元組或兆位元組。 預設值是 MB。 請指定不包括小數的整數。 如果未指定 *max_size*，檔案大小會增加到磁碟已滿。

UNLIMITED 指定檔案可成長直到磁碟已滿。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，指定為無限成長的記錄檔，大小上限是 2 TB，資料檔案的大小上限是 16 TB。

FILEGROWTH *growth_increment* 指定檔案的自動成長遞增。 檔案的 FILEGROWTH 設定不能超過 MAXSIZE 設定。

*growth_increment* 這是每次需要新空間時，檔案所增加的空間量。

您可以利用 MB、KB、GB、TB 或百分比 (%) 來指定這個值。 如果指定的數字不含 MB、KB 或 % 後置詞，預設值是 MB。 當指定 % 時，成長遞增大小便是遞增發生時，檔案大小的指定百分比。 指定的大小會捨入到最接近 64 KB。

0 值表示將自動成長設為關閉，且不允許任何其他空間。

如果未指定 FILEGROWTH，預設值為：

- 資料 64 MB
- 記錄檔 64 MB

**\<add_or_modify_filegroups>::=**

在資料庫中新增、修改或移除檔案群組。

ADD FILEGROUP *filegroup_name* 將檔案群組新增至資料庫。

下列範例會建立檔案群組，並將其加入至名為 sql_db_mi 的資料庫中，然後將檔案加入檔案群組中。

```sql
ALTER DATABASE sql_db_mi ADD FILEGROUP sql_db_mi_fg;
GO
ALTER DATABASE sql_db_mi ADD FILE (NAME='sql_db_mi_mod') TO FILEGROUP sql_db_mi_fg;
```

REMOVE FILEGROUP *filegroup_name* 從資料庫中移除檔案群組。 除非檔案群組是空的，否則無法移除檔案群組。 請先移除檔案群組中的所有檔案。 如需詳細資訊，請參閱本主題中稍早的 "REMOVE FILE *logical_file_name*"。

MODIFY FILEGROUP _filegroup\_name_ { \<filegroup_updatability_option> | DEFAULT | NAME **=** _new\_filegroup\_name_ } 透過將狀態設定為 READ_ONLY 或 READ_WRITE 來修改檔案群組，讓檔案群組成為資料庫的預設檔案群組，或變更檔案群組名稱。

\<filegroup_updatability_option> 會將檔案群組的屬性設成唯讀或讀取/寫入。

DEFAULT 將預設的資料庫檔案群組變更為 *filegroup_name*。 資料庫中只能有一個檔案群組是預設檔案群組。 如需相關資訊，請參閱 [Database Files and Filegroups](../../relational-databases/databases/database-files-and-filegroups.md)。

NAME = *new_filegroup_name* 將檔案群組名稱變更為 *new_filegroup_name*。

AUTOGROW_SINGLE_FILE

當檔案群組中的某個檔案達到自動成長閾值時，只有該檔案會成長。 這是預設值。

AUTOGROW_ALL_FILES

當檔案群組中的某個檔案達到自動成長閾值時，檔案群組中的所有檔案都會成長。

**\<filegroup_updatability_option>::=**

將檔案群組的屬性設成唯讀或讀取/寫入。

READ_ONLY | READONLY 將檔案群組指定成唯讀狀態。 不允許更新其中的物件。 主要檔案群組不能設為唯讀。 若要變更這個狀態，您必須具有資料庫的獨佔存取權。 如需詳細資訊，請參閱 SINGLE_USER 子句。

由於唯讀資料庫不允許修改資料，因此，會出現下列情況：

- 在系統開機時跳過自動復原。
- 不可能壓縮資料庫。
- 唯讀資料庫不會出現鎖定。 因此，查詢效能會比較快。

> [!NOTE]
> 在未來的 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，將移除 READONLY 關鍵字。 請避免在新的開發工作中使用 READONLY，並規劃修改目前在使用 READONLY 的應用程式。 請改用 READ_ONLY。

READ_WRITE | READWRITE 將群組指定成 READ_WRITE 狀態。 檔案群組中的物件可以更新。 若要變更這個狀態，您必須具有資料庫的獨佔存取權。 如需詳細資訊，請參閱 SINGLE_USER 子句。

> [!NOTE]
> 在未來的 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，將移除 `READWRITE` 關鍵字。 請避免在新的開發工作中使用 `READWRITE`，並規劃修改目前在使用 `READWRITE` 的應用程式，改為使用 `READ_WRITE`。

您可以檢查 **sys.databases** 目錄檢視中的 **is_read_only** 資料行或 `DATABASEPROPERTYEX` 函數的 **Updateability** 屬性來判斷這些選項的狀態。

## <a name="remarks"></a>備註

若要縮小資料庫大小，請使用 [DBCC SHRINKDATABASE](../../t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql.md)。

當 `BACKUP` 陳述式正在執行時，您不能新增或移除檔案。

每個資料庫最多可以指定 32,767 個檔案和 32,767 個檔案群組。

## <a name="examples"></a>範例

### <a name="a-adding-a-file-to-a-database"></a>A. 將檔案加入資料庫中

下列範例會將 5 MB 的資料檔加入 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中。

```sql
USE master;
GO
ALTER DATABASE AdventureWorks2012
ADD FILE
(
  NAME = Test1dat2,
    SIZE = 5MB,
    MAXSIZE = 100MB,
    FILEGROWTH = 5MB
);
GO
```

### <a name="b-adding-a-filegroup-with-two-files-to-a-database"></a>B. 將含有兩個檔案的檔案群組加入資料庫中

下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中建立 `Test1FG1` 檔案群組，且會將兩個 5 MB 的檔案加入檔案群組中。

```sql
USE master
GO
ALTER DATABASE AdventureWorks2012
ADD FILEGROUP Test1FG1;
GO
ALTER DATABASE AdventureWorks2012
ADD FILE
(
    NAME = test1dat3,
    SIZE = 5MB,
    MAXSIZE = 100MB,
    FILEGROWTH = 5MB
),
(
    NAME = test1dat4,
    SIZE = 5MB,
    MAXSIZE = 100MB,
    FILEGROWTH = 5MB
)  
TO FILEGROUP Test1FG1;
GO
```

### <a name="c-removing-a-file-from-a-database"></a>C. 從資料庫中移除檔案

下列範例會移除 B 範例中所加入的其中一個檔案。

```sql
USE master;
GO
ALTER DATABASE AdventureWorks2012
REMOVE FILE test1dat4;
GO
```

### <a name="d-modifying-a-file"></a>D. 修改檔案

下列範例會增加 B 範例中所新增的其中一個檔案大小。ALTER DATABASE 搭配 MODIFY FILE 命令只能讓檔案大小更大，若您需要讓檔案大小更小，則必須使用 DBCC SHRINKFILE。

```sql
USE master;
GO

ALTER DATABASE AdventureWorks2012
MODIFY FILE
(NAME = test1dat3,
SIZE = 200MB);
GO
```

此範例將資料檔案的大小縮小至 100 MB，並將大小指定為該數量。

```sql
USE AdventureWorks2012;
GO

DBCC SHRINKFILE (AdventureWorks2012_data, 100);
GO

USE master;
GO

ALTER DATABASE AdventureWorks2012
MODIFY FILE
(NAME = test1dat3,
SIZE = 200MB);
GO
```

### <a name="e-making-a-filegroup-the-default"></a>E. 使檔案群組成為預設值

下列範例將在範例 B 中建立的 `Test1FG1` 檔案群組設定為預設檔案群組。 之後，預設檔案群組會重設為 `PRIMARY` 檔案群組。 請注意，您必須用方括號或引號來分隔 `PRIMARY`。

```sql
USE master;
GO
ALTER DATABASE AdventureWorks2012
MODIFY FILEGROUP Test1FG1 DEFAULT;
GO
ALTER DATABASE AdventureWorks2012
MODIFY FILEGROUP [PRIMARY] DEFAULT;
GO
```

### <a name="f-adding-a-filegroup-using-alter-database"></a>F. 使用 ALTER DATABASE 加入檔案群組

下列範例會將 `FILEGROUP` 加入至 `MyDB` 資料庫中。

```sql
--Create and add a FILEGROUP
ALTER DATABASE MyDB
ADD FILEGROUP NewFG;
GO

--Add a file to FILEGROUP
ALTER DATABASE MyDB
ADD FILE
(
    NAME= 'MyFile',
)
TO FILEGROUP NewFG;
GO
```

### <a name="g-change-filegroup-so-that-when-a-file-in-the-filegroup-meets-the-autogrow-threshold-all-files-in-the-filegroup-grow"></a>G. 變更檔案群組，以在當檔案群組中的某個檔案達到自動成長閾值時，檔案群組中的所有檔案都會成長

下列範例會產生 `ALTER DATABASE` 陳述式，以使用 `AUTOGROW_ALL_FILES` 設定來修改讀寫檔案群組。

```sql
--Generate ALTER DATABASE ... MODIFY FILEGROUP statements
--so that all read-write filegroups grow at the same time.
SET NOCOUNT ON;

DROP TABLE IF EXISTS #tmpdbs
CREATE TABLE #tmpdbs (id INT IDENTITY(1,1), [dbid] INT, [dbname] sysname, isdone BIT);

DROP TABLE IF EXISTS #tmpfgs
CREATE TABLE #tmpfgs (id INT IDENTITY(1,1), [dbid] INT, [dbname] sysname, fgname sysname, isdone BIT);

INSERT INTO #tmpdbs ([dbid], [dbname], [isdone])
SELECT database_id, name, 0 FROM master.sys.databases (NOLOCK) WHERE is_read_only = 0 AND state = 0;

DECLARE @dbid INT, @query VARCHAR(1000), @dbname sysname, @fgname sysname

WHILE (SELECT COUNT(id) FROM #tmpdbs WHERE isdone = 0) > 0
BEGIN
    SELECT TOP 1 @dbname = [dbname], @dbid = [dbid] FROM #tmpdbs WHERE isdone = 0

    SET @query = 'SELECT ' + CAST(@dbid AS NVARCHAR) + ', ''' + @dbname + ''', [name], 0 FROM [' + @dbname + '].sys.filegroups WHERE [type] = ''FG'' AND is_read_only = 0;'
    INSERT INTO #tmpfgs
    EXEC (@query)

    UPDATE #tmpdbs
    SET isdone = 1
    WHERE [dbid] = @dbid
END;

IF (SELECT COUNT(ID) FROM #tmpfgs) > 0
BEGIN
    WHILE (SELECT COUNT(id) FROM #tmpfgs WHERE isdone = 0) > 0
    BEGIN
        SELECT TOP 1 @dbname = [dbname], @dbid = [dbid], @fgname = fgname FROM #tmpfgs WHERE isdone = 0

        SET @query = 'ALTER DATABASE [' + @dbname + '] MODIFY FILEGROUP [' + @fgname + '] AUTOGROW_ALL_FILES;'

        PRINT @query

        UPDATE #tmpfgs
        SET isdone = 1
        WHERE [dbid] = @dbid AND fgname = @fgname
    END
END;
GO
```

## <a name="see-also"></a>另請參閱

- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
- [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)
- [sp_spaceused](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md)
- [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)
- [sys.database_files](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)
- [sys.data_spaces](../../relational-databases/system-catalog-views/sys-data-spaces-transact-sql.md)
- [sys.filegroups](../../relational-databases/system-catalog-views/sys-filegroups-transact-sql.md)
- [sys.master_files](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)
- [DBCC SHRINKFILE](../../t-sql/database-console-commands/dbcc-shrinkfile-transact-sql.md)
- [記憶體最佳化的檔案群組](../../relational-databases/in-memory-oltp/the-memory-optimized-filegroup.md)

::: moniker-end
