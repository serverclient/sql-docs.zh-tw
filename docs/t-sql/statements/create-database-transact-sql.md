---
title: CREATE DATABASE (Transact-SQL) | Microsoft Docs
description: 建立適用於 SQL Server、Azure SQL Database、Azure Synapse Analytics，以及 Analytics Platform System 的資料庫語法
ms.custom: references_regions
ms.date: 12/11/2020
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DATABASE_TSQL
- DATABASE
- CONTAINMENT_TSQL
- CREATE DATABASE
- CREATE_DATABASE_TSQL
- CONTAINS_FILESTREAM_TSQL
- CONTAINS FILESTREAM
- CONTAINMENT
dev_langs:
- TSQL
helpviewer_keywords:
- snapshots [SQL Server database snapshots], creating
- databases [SQL Server], creating
- model database [SQL Server], database creation
- mounted drives [SQL Server]
- CREATE DATABASE
- CREATE DATABASE statement
- file creation [SQL Server]
- creating databases
- containment
- filegroups [SQL Server], database creation
- database creation [SQL Server], CREATE DATABASE statement
- moving databases
- attaching databases [SQL Server], CREATE DATABASE...FOR ATTACH
ms.assetid: 29ddac46-7a0f-4151-bd94-75c1908c89f8
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-current||=azuresqldb-mi-current||=azure-sqldw-latest||>=aps-pdw-2016'
ms.openlocfilehash: 5321af1026b82d2ed49f077d7cf5604bce3a8e69
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689015"
---
# <a name="create-database"></a>CREATE DATABASE

建立新資料庫。

按一下下列其中一個索引標籤，以查看您所使用特定 SQL 版本的語法、引數、備註、權限和範例。

如需語法慣例的詳細資訊，請參閱 [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL Database](create-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](create-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-database-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server"></a>SQL Server

## <a name="overview"></a>概觀

在 SQL Server 中，此陳述式會建立新的資料庫與使用的檔案及其檔案群組。 它也可以用來建立資料庫快照集，或附加資料庫檔案，以從其他資料庫中斷連結的檔案建立資料庫。

## <a name="syntax"></a>語法

建立資料庫

```syntaxsql
CREATE DATABASE database_name
[ CONTAINMENT = { NONE | PARTIAL } ]
[ ON
      [ PRIMARY ] <filespec> [ ,...n ]
      [ , <filegroup> [ ,...n ] ]
      [ LOG ON <filespec> [ ,...n ] ]
]
[ COLLATE collation_name ]
[ WITH <option> [,...n ] ]
[;]

<option> ::=
{
      FILESTREAM ( <filestream_option> [,...n ] )
    | DEFAULT_FULLTEXT_LANGUAGE = { lcid | language_name | language_alias }
    | DEFAULT_LANGUAGE = { lcid | language_name | language_alias }
    | NESTED_TRIGGERS = { OFF | ON }
    | TRANSFORM_NOISE_WORDS = { OFF | ON}
    | TWO_DIGIT_YEAR_CUTOFF = <two_digit_year_cutoff>
    | DB_CHAINING { OFF | ON }
    | TRUSTWORTHY { OFF | ON }
    | PERSISTENT_LOG_BUFFER=ON ( DIRECTORY_NAME='<Filepath to folder on DAX formatted volume>' )
}

<filestream_option> ::=
{
      NON_TRANSACTED_ACCESS = { OFF | READ_ONLY | FULL }
    | DIRECTORY_NAME = 'directory_name'
}

<filespec> ::=
{
(
    NAME = logical_file_name ,
    FILENAME = { 'os_file_name' | 'filestream_path' }
    [ , SIZE = size [ KB | MB | GB | TB ] ]
    [ , MAXSIZE = { max_size [ KB | MB | GB | TB ] | UNLIMITED } ]
    [ , FILEGROWTH = growth_increment [ KB | MB | GB | TB | % ] ]
)
}

<filegroup> ::=
{
FILEGROUP filegroup name [ [ CONTAINS FILESTREAM ] [ DEFAULT ] | CONTAINS MEMORY_OPTIMIZED_DATA ]
    <filespec> [ ,...n ]
}
```

附加資料庫

```syntaxsql
CREATE DATABASE database_name
    ON <filespec> [ ,...n ]
    FOR { { ATTACH [ WITH <attach_database_option> [ , ...n ] ] }
        | ATTACH_REBUILD_LOG }
[;]

<attach_database_option> ::=
{
      <service_broker_option>
    | RESTRICTED_USER
    | FILESTREAM ( DIRECTORY_NAME = { 'directory_name' | NULL } )
}

<service_broker_option> ::=
{
    ENABLE_BROKER
  | NEW_BROKER
  | ERROR_BROKER_CONVERSATIONS
}
```

建立資料庫快照集

```syntaxsql
CREATE DATABASE database_snapshot_name
    ON
    (
        NAME = logical_file_name,
        FILENAME = 'os_file_name'
    ) [ ,...n ]
    AS SNAPSHOT OF
[;]
```

## <a name="arguments"></a>引數

*database_name* 這是新資料庫的名稱。 資料庫名稱在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體內必須是唯一的，且必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。

除非沒有指定記錄檔的邏輯名稱，否則 *database_name* 最多可有 128 個字元。 如果未指定邏輯記錄檔名稱，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會就藉由在 *database_name* 附加後置詞，來產生記錄檔的 *logical_file_name* 和 *os_file_name*。 這會將 *database_name* 限制為 123 個字元，使所產生的邏輯檔案名稱不超過 128 個字元。

如果未指定資料檔案名稱，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 就會使用 *database_name* 同時作為 *logical_file_name* 和 *os_file_name*。 預設路徑是從登錄取得。 您可以使用 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 中的 [伺服器屬性] ([資料庫設定] 頁面)來變更預設路徑。 變更預設路徑需要重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。

CONTAINMENT = { NONE | PARTIAL }

**適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本

指定資料庫的內含項目狀態。 NONE = 非自主資料庫 PARTIAL = 部分自主資料庫

ON 指定必須明確定義用來儲存資料庫之資料區段 (資料檔案) 的磁碟檔案。 當後面接著一份定義主要檔案群組資料檔案的 \<filespec> 項目清單 (以逗號分隔) 時，必須使用 ON。 主要檔案群組中的檔案清單後面可以接著一份選擇性的 \<filegroup> 項目清單 (以逗號分隔)，其中定義使用者檔案群組及其檔案。

PRIMARY 指定相關聯的 \<filespec> 清單必須定義主要檔案。 主要檔案群組的 \<filespec> 項目中所指定第一個檔案會成為主要檔案。 資料庫只能有一個主要檔案。 如需相關資訊，請參閱 [Database Files and Filegroups](../../relational-databases/databases/database-files-and-filegroups.md)。

如果未指定 PRIMARY，CREATE DATABASE 陳述式中列出的第一個檔案會成為主要檔案。

LOG ON 指定必須明確定義用來儲存資料庫記錄 (記錄檔) 的磁碟檔案。 LOG ON 後面會接著定義記錄檔的 \<filespec> 項目清單 (以逗號分隔)。 如果未指定 LOG ON，系統會自動建立一個記錄檔，該檔案的大小是資料庫之所有資料檔案的大小總和的 25% 或 512 KB 其中較大者。 這個檔案會放置在預設的記錄檔位置中。 如需此位置的資訊，請參閱[檢視或變更資料及記錄檔的預設位置 - SSMS](../../database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files.md)。

資料庫快照集中無法指定 LOG ON。

COLLATE *collation_name* 指定資料庫的預設定序。 定序名稱可以是 Windows 定序名稱或 SQL 定序名稱。 若未指定，會將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的預設定序指派給資料庫。 資料庫快照集中無法指定定序名稱。

定序名稱無法利用 FOR ATTACH 或 FOR ATTACH_REBUILD_LOG 子句來指定。 如需有關如何變更所附加資料庫之定序的資訊，請瀏覽此 [Microsoft 網站](https://go.microsoft.com/fwlink/?linkid=16419&kbid=325335)。

如需有關 Windows 和 SQL 定序名稱的詳細資訊，請參閱 [COLLATE](~/t-sql/statements/collations.md)。

> [!NOTE]
> 自主資料庫的定序方式不同於非自主資料庫。 如需詳細資訊，請參閱[自主資料庫定序](../../relational-databases/databases/contained-database-collations.md)。

WITH \<option>
 **\<filestream_option>**

NON_TRANSACTED_ACCESS = { **OFF** | READ_ONLY | FULL } **適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。

指定資料庫層級的非交易式 FILESTREAM 存取層級。

|值|描述|
|-----------|-----------------|
|OFF|已停用非交易式存取|
|READONLY|非交易式處理序可以讀取此資料庫中的 FILESTREAM 資料。|
|FULL|已啟用 FILESTREAM FileTables 的完整非交易式存取。|

DIRECTORY_NAME = \<directory_name>
**適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本

Windows 相容的目錄名稱。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的所有 Database_Directory 名稱之間，此名稱必須是唯一的。 不論 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 定序設定為何，唯一性比較不區分大小寫。 在此資料庫中建立 FileTable 之前，應該先設定這個選項。

只有當 CONTAINMENT 已經設為 PARTIAL 時，才允許下列選項。 如果 CONTAINMENT 設定為 NONE，便會發生錯誤。

- **DEFAULT_FULLTEXT_LANGUAGE = \<lcid> | \<language name> | \<language alias>**

  **適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本

  如需此選項的完整描述，請參閱[設定 default full-text language 伺服器設定選項](../../database-engine/configure-windows/configure-the-default-full-text-language-server-configuration-option.md)。

- **DEFAULT_LANGUAGE = \<lcid> | \<language name> | \<language alias>**

  **適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本

  如需此選項的完整描述，請參閱[設定 default language 伺服器設定選項](../../database-engine/configure-windows/configure-the-default-language-server-configuration-option.md)。

- **NESTED_TRIGGERS = { OFF | ON}**

  **適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本

  如需此選項的完整描述，請參閱[設定 nested triggers 伺服器設定選項](../../database-engine/configure-windows/configure-the-nested-triggers-server-configuration-option.md)。

- **TRANSFORM_NOISE_WORDS = { OFF | ON}**

  **適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本

  如需此選項的完整描述，請參閱 [transform noise words 伺服器設定選項](../../database-engine/configure-windows/transform-noise-words-server-configuration-option.md)。

- **TWO_DIGIT_YEAR_CUTOFF = { 2049 | \<any year between 1753 and 9999> }**

  表示一年的四位數。 2049 是預設值。 如需此選項的完整說明，請參閱[設定兩位數年份的截止伺服器組態選項](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)。

- **DB_CHAINING { OFF | ON }**

  當指定 ON 時，資料庫可以是跨資料庫擁有權鏈結的來源或目標。

  當指定 OFF 時，資料庫不能參與跨資料庫擁有權鏈結。 預設值為 OFF。

  > [!IMPORTANT]
  > 當 cross db ownership chaining 伺服器選項為 0 (OFF) 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體可以辨識這項設定。 當 cross db ownership chaining 為 1 (ON) 時，不論這個選項的值為何，所有使用者資料庫都可以參與跨資料庫擁有權鏈結。 您可以使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) 來設定這個選項。

  若要設定這個選項，則需要有系統管理員 (sysadmin) 固定伺服器角色的成員資格。 您不能在下列系統資料庫上設定 DB_CHAINING 選項：master、model、tempdb。

- **TRUSTWORTHY { OFF | ON }**

  當指定 ON 時，使用模擬內容的資料庫模組 (例如，檢視表、使用者定義函數或預存程序) 可以存取資料庫外部的資源。

  當指定 OFF 時，模擬內容中的資料庫模組不能存取資料庫外部的資源。 預設值為 OFF。

  每當附加資料庫時，TRUSTWORTHY 都設為 OFF。

  依預設，除了 msdb 資料庫以外，所有的系統資料庫都會將 TRUSTWORTHY 設為 OFF。 model 和 tempdb 資料庫的這個值不可變更。 建議您絕對不要將 master 資料庫的 TRUSTWORTHY 選項設為 ON。

- **PERSISTENT_LOG_BUFFER=ON ( DIRECTORY_NAME='' )**

  指定此選項時，會在位於存放裝置類別記憶體 (NVDIMM-N 非揮發性儲存體) 所支援磁碟裝置上的磁碟區，建立交易記錄緩衝區，也就是持續記錄緩衝區。 如需詳細資訊，請參閱[使用存放裝置類別記憶體加速交易認可延遲](/archive/blogs/sqlserverstorageengine/transaction-commit-latency-acceleration-using-storage-class-memory-in-windows-server-2016sql-server-2016-sp1) \(英文\)。 **適用於**：[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 和更新版本。

FOR ATTACH [ WITH \< attach_database_option > ] 指定資料庫是藉由[附加](../../relational-databases/databases/database-detach-and-attach-sql-server.md)一組現有的作業系統檔案所建立。 必須有一個指定主要檔案的 \<filespec> 項目。 任何檔案如果其路徑與第一次建立資料庫或最後一次附加資料庫時的路徑不同，則其 \<filespec> 項目是唯一所需的其他 <filespec> 項目。 您必須針對這些檔案指定 \<filespec> 項目。

FOR ATTACH 需要下列項目：

- 所有資料檔案 (MDF 和 NDF) 都必須是可用的。
- 如果存在多個記錄檔，它們必須全部都是可用的。

如果讀取/寫入資料庫有目前無法使用的單一記錄檔，且在進行附加作業之前，資料庫因為沒有使用者或開啟的交易而關閉，則 FOR ATTACH 會自動重建記錄檔並更新主要檔案。 反之，如果是唯讀資料庫，則會因為無法更新主要檔案而無法重建記錄。 因此，當您所附加的唯讀資料庫之記錄無法使用時，您必須在 FOR ATTACH 子句中提供記錄檔或檔案。

> [!NOTE]
> 由較新版本 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所建立的資料庫無法附加在舊版本中。

在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，所附加之資料庫中的任何全文檢索檔案，都會隨著資料庫而一起附加。 若要指定全文檢索目錄的新路徑，請指定一個不含全文檢索作業系統檔案名稱的新位置。 如需詳細資訊，請參閱＜範例＞一節。

若將包含 FILESTREAM "Directory name" 選項的資料庫附加至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，將會提示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證 Database_Directory 名稱是否為唯一。 如果不是唯一，附加作業就會失敗，並顯示 [FILESTREAM Database_Directory name \<name> 在這個 SQL Server 執行個體中不是唯一的] 錯誤。 若要避免這個錯誤，應該將選擇性參數 *directory_name* 傳遞給此作業。

資料庫快照集中無法指定 FOR ATTACH。

FOR ATTACH 可以指定 RESTRICTED_USER 選項。 RESTRICTED_USER 只允許 db_owner 固定資料庫角色以及資料庫建立者 (dbcreator) 和系統管理員 (sysadmin) 固定伺服器角色的成員連接到資料庫，但並不限制他們的數目。 不合格的使用者嘗試連接遭到拒絕。

如果資料庫使用 [!INCLUDE[ssSB](../../includes/sssb-md.md)]，請在 FOR ATTACH 子句中使用 WITH \<service_broker_option>：

\<service_broker_option> 可控制 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 訊息傳遞以及資料庫的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼。 只有在使用 FOR ATTACH 子句的情況下才能指定 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 選項。

ENABLE_BROKER 指定啟用指定資料庫的 [!INCLUDE[ssSB](../../includes/sssb-md.md)]。 也就是說，會啟動訊息傳遞，且 is_broker_enabled 在 sys.databases 目錄檢視中會設定為 true。 資料庫會保留現有的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼。

NEW_BROKER 在 sys.databases 和還原的資料庫中，建立新的 service_broker_guid 值，並以清除結束所有交談端點。 它會啟用 Broker，但不會傳送任何訊息到遠端交談端點。 您必須使用新的識別碼來重新建立參考舊 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼的任何路由。

ERROR_BROKER_CONVERSATIONS 結束所有交談，並顯示一則指出已附加或還原資料庫的錯誤。 Broker 將保持停用，直到這項作業完成之後才會啟用。 資料庫會保留現有的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼。

當您附加的複寫資料庫是複製而非卸離時，請考慮下列各項：

- 如果您要將資料庫附加至與原始資料庫相同的伺服器執行個體和版本，則不需要其他步驟。
- 如果您將資料庫附加至相同但版本已升級的伺服器執行個體，則必須在附加作業完成後，執行 [sp_vupgrade_replication](../../relational-databases/system-stored-procedures/sp-vupgrade-replication-transact-sql.md) 來升級複寫。
- 如果您將資料庫附加至不同的伺服器執行個體，則不論版本為何，都必須在附加作業完成後，執行 [sp_removedbreplication](../../relational-databases/system-stored-procedures/sp-removedbreplication-transact-sql.md) 來移除複寫。

> [!NOTE]
> 附加作業可搭配 **Vardecimal** 儲存格式運作，但 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 必須至少升級至 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] SP2。 您不能將使用 Vardecimal 儲存格式的資料庫附加到舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 如需有關 **Vardecimal** 儲存格式的詳細資訊，請參閱 [資料壓縮](../../relational-databases/data-compression/data-compression.md)。

當資料庫第一次連接或還原到新的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體時，資料庫主要金鑰複本 (由服務主要金鑰加密) 尚未儲存在伺服器中。 您必須利用 **OPEN MASTER KEY** 陳述式來解密資料庫主要金鑰 (DMK)。 DMK 解密之後，您便可以選擇利用 **ALTER MASTER KEY REGENERATE** 陳述式來提供服務主要金鑰 (SMK) 所加密的 DMK 複本給伺服器，以在未來啟用自動解密。 當資料庫從舊版升級時，應該會重新產生 DMK 以使用較新的 AES 演算法。 如需重新產生 DMK 的詳細資訊，請參閱 [ALTER MASTER KEY](../../t-sql/statements/alter-master-key-transact-sql.md)。 重新產生 DMK 金鑰以升級至 AES 所需的時間是取決於 DMK 所保護的物件數目而定。 重新產生 DMK 金鑰以升級至 AES 只需執行一次，且不會影響金鑰循環策略中後續的重新產生。 如需如何使用附加來升級資料庫的資訊，請參閱[使用卸離與附加來升級資料庫](../../relational-databases/databases/upgrade-a-database-using-detach-and-attach-transact-sql.md)。

> [!IMPORTANT]
> 建議您不要附加來源不明或來源不受信任的資料庫。 這種資料庫可能包含惡意程式碼，因此可能執行非預期的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 程式碼，或是修改結構描述或實體資料庫結構而造成錯誤。 使用來源不明或來源不受信任的資料庫之前，請先在非實際執行伺服器的資料庫上執行 [DBCC CHECKDB](../../t-sql/database-console-commands/dbcc-checkdb-transact-sql.md)，同時也檢查資料庫中的程式碼，例如預存程序或其他使用者定義程式碼。
> [!NOTE]
> 附加資料庫時，**TRUSTWORTHY** 和 **DB_CHAINING** 選項沒有任何作用。

FOR ATTACH_REBUILD_LOG 指定資料庫是藉由附加一組現有的作業系統檔案所建立。 這個選項只適用於讀取/寫入資料庫。 必須要有一個 *\<filespec>* 項目用來指定主要檔案。 如果遺漏一個或多個交易記錄檔，記錄檔就會重建。 ATTACH_REBUILD_LOG 會自動建立新的 1 MB 記錄檔。 這個檔案會放置在預設的記錄檔位置中。 如需此位置的資訊，請參閱[檢視或變更資料及記錄檔的預設位置 - SSMS](../../database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files.md)。

> [!NOTE]
> 如果記錄檔是可用的，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 就會使用這些檔案，而不會重建記錄檔。

FOR ATTACH_REBUILD_LOG 需要下列項目：

- 正常關閉資料庫。
- 所有資料檔案 (MDF 和 NDF) 都必須是可用的。

> [!IMPORTANT]
> 這項作業會中斷記錄備份鏈結。 建議您在作業完成之後執行完整的資料庫備份。 如需詳細資訊，請參閱 [BACKUP](../../t-sql/statements/backup-transact-sql.md)。

一般而言，如果您要將一個含有大型記錄的讀/寫資料庫複製到其他伺服器，而該伺服器中，因為資料庫副本大部分用在讀取作業或只用在讀取作業，所以所需的記錄空間比原始資料庫少，在這種情況下，通常就會使用 FOR ATTACH_REBUILD_LOG。

資料庫快照集中無法指定 FOR ATTACH_REBUILD_LOG。

如需附加及卸離資料庫的詳細資訊，請參閱[資料庫卸離與附加](../../relational-databases/databases/database-detach-and-attach-sql-server.md)。

\<filespec> 可控制檔案屬性。

NAME *logical_file_name* 指定檔案的邏輯名稱。 除非指定其中一個 FOR ATTACH 子句，否則指定 FILENAME 時，NAME 是必要的。 FILESTREAM 檔案群組不能命名為 PRIMARY。

*logical_file_name* 這是在參考檔案時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所用的邏輯名稱。 *Logical_file_name* 在資料庫中必須是唯一的，且必須符合 [識別碼](../../relational-databases/databases/database-identifiers.md)的規則。 名稱可以是字元或 Unicode 常數，或是一般識別碼或分隔識別碼。

FILENAME { **'** _os\_file\_name_ **'** | **'** _filestream\_path_ **'** } 指定作業系統 (實體) 檔案名稱。

**'** *os_file_name* **'** 是當您建立檔案時作業系統所使用的路徑和檔案名稱。 該檔案必須位於下列其中一個裝置：從中安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的本機伺服器、存放區域網路 [SAN] 或 iSCSI 型網路。 執行 CREATE DATABASE 陳述式之前，指定的路徑必須存在。 如需詳細資訊，請參閱＜備註＞一節中的「資料庫檔案和檔案群組」。

當指定檔案的 UNC 路徑時，可以設定 SIZE、MAXSIZE 和 FILEGROWTH 參數。

如果檔案在原始磁碟分割中，*os_file_name* 只能指定現有原始磁碟分割的磁碟機代號。 每個原始分割區上只能建立一個資料檔案。

除非檔案是唯讀次要檔案，或者，資料庫是唯讀的，否則資料檔案不應該放在壓縮的檔案系統中。 記錄檔永遠不應放在壓縮的檔案系統中。

**'** *filestream_path* **'** 針對 FILESTREAM 檔案群組，FILENAME 會參考將儲存 FILESTREAM 資料的路徑。 到最後一個資料夾為止的路徑必須存在，而最後一個資料夾則不得存在。 例如，如果您指定 C:\MyFiles\MyFilestreamData 路徑，則在您執行 ALTER DATABASE 之前，C:\MyFiles 必須存在，但是 MyFilestreamData 資料夾不得存在。

檔案群組和檔案 (`<filespec>`) 必須在相同的陳述式中建立。

SIZE 和 FILEGROWTH 屬性不會套用到 FILESTREAM 檔案群組。

SIZE *size* 指定檔案的大小。

將 *os_file_name* 指定為 UNC 路徑時，不能指定 SIZE。 SIZE 不會套用到 FILESTREAM 檔案群組。

*size* 這是檔案的初始大小。

未提供主要檔案的 *size* 時，[!INCLUDE[ssDE](../../includes/ssde-md.md)]會使用模型資料庫中主要檔案的大小。 模型的預設大小是 8 MB (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始) 或 1 MB (適用於舊版)。 已指定次要資料檔或記錄檔，但未指定檔案的 *size* 時，[!INCLUDE[ssDE](../../includes/ssde-md.md)]會將檔案大小設定為 8 MB (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始) 或 1 MB (適用於舊版)。 所指定的主要檔案大小至少必須跟 model 資料庫的主要檔案大小一樣大。

您可以使用千位元組 (KB)、百萬位元組 (MB)、十億位元組 (GB) 或兆位元組 (TB) 後置詞。 預設值是 MB。 請指定一個整數，不包括小數點。 *Size* 是一個整數值。 如果是大於 2147483647 的值，請使用較大的單位。

MAXSIZE *max_size* 指定檔案所能成長的大小上限。 將 *os_file_name* 指定為 UNC 路徑時，不能指定 MAXSIZE。

*max_size* 這是檔案大小上限。 可以使用 KB、MB、GB 及 TB 後置詞。 預設值是 MB。 請指定一個整數，不包括小數點。 如果未指定 *max_size*，檔案就會成長到磁碟已滿為止。 *Max_size* 是一個整數值。 如果是大於 2147483647 的值，請使用較大的單位。

UNLIMITED 指定檔案可成長直到磁碟已滿。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，指定為無限成長的記錄檔，大小上限是 2 TB，資料檔案的大小上限是 16 TB。

> [!NOTE]
> 為 FILESTREAM 容器指定這個選項時沒有最大大小。 它會繼續成長，直到磁碟已滿。

FILEGROWTH *growth_increment* 指定檔案的自動成長遞增。 檔案的 FILEGROWTH 設定不能超過 MAXSIZE 設定。 將 *os_file_name* 指定為 UNC 路徑時，不能指定 FILEGROWTH。 FILEGROWTH 不會套用到 FILESTREAM 檔案群組。

*growth_increment* 這是每次需要新空間時，檔案所增加的空間量。

您可以利用 MB、KB、GB、TB 或百分比 (%) 來指定這個值。 如果指定的數字不含 MB、KB 或 % 後置詞，預設值是 MB。 當指定 % 時，成長遞增大小便是遞增發生時，檔案大小的指定百分比。 指定的大小會捨入到最接近 64 KB，最小值為 64 KB。

0 值指出自動成長是關閉的，且不允許其他空間。

如果未指定 FILEGROWTH，預設值為：

|版本|預設值|
|-------------|--------------------|
|從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始|資料 64 MB。 記錄檔 64 MB。|
|從 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 開始|資料 1 MB。 記錄檔 10%。|
|[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 之前|資料 10%。 記錄檔 10%。|

\<filegroup> 可控制檔案群組屬性。 資料庫快照集中無法指定檔案群組。

FILEGROUP *filegroup_name* 這是檔案群組的邏輯名稱。

*filegroup_name*
*filegroup_name* 在資料庫中必須是唯一的，且不能是系統提供的名稱 PRIMARY 和 PRIMARY_LOG。 名稱可以是字元或 Unicode 常數，或是一般識別碼或分隔識別碼。 名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。

CONTAINS FILESTREAM 指定檔案群組會將 FILESTREAM 二進位大型物件 (BLOB) 儲存在檔案系統中。

CONTAINS MEMORY_OPTIMIZED_DATA

**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 和更新版本

指定檔案群組將記憶體最佳化的資料儲存在檔案系統中。 如需詳細資訊，請參閱[記憶體內部 OLTP - 記憶體內部最佳化](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)。 每個資料庫只允許一個 MEMORY_OPTIMIZED_DATA 檔案群組。 如需可建立檔案群組來儲存記憶體最佳化資料的程式碼範例，請參閱[建立記憶體最佳化資料表和原生編譯的預存程序](../../relational-databases/in-memory-oltp/creating-a-memory-optimized-table-and-a-natively-compiled-stored-procedure.md)。

DEFAULT 指定具名的檔案群組必須是資料庫中預設檔案群組。

*database_snapshot_name* 這是新資料庫快照集的名稱。 資料庫快照集名稱在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體內必須是唯一的，且必須符合識別碼的規則。 *database_snapshot_name* 最多可有 128 個字元。

ON **(** NAME **=** _logical\_file\_name_ **,** FILENAME **='** _os\_file\_name_ **')** [ **,** ... *n* ] 若要建立資料庫快照集，請在來源資料庫中指定檔案清單。 必須個別指定所有資料檔案，快照集才能運作。 不過，記錄檔不能用在資料庫快照集。 資料庫快照集不支援 FILESTREAM 檔案群組。 如果 FILESTREAM 資料檔案包含在 CREATE DATABASE ON 子句中，此陳述式將會失敗，並引發錯誤。

如需 NAME 和 FILENAME 及其值的描述，請參閱對等 \<filespec> 值的描述。

> [!NOTE]
> 建立資料庫快照集時，不允許使用其他 \<filespec> 選項和 PRIMARY 關鍵字。

AS SNAPSHOT OF *source_database_name* 指定要建立的資料庫是 *source_database_name* 所指定來源資料庫的資料庫快照集。 該快照集和來源資料庫必須位於相同的執行個體上。

如需詳細資訊，請參閱＜備註＞一節中的[資料庫快照集](#database-snapshots)。

## <a name="remarks"></a>備註

每當建立、修改或卸除使用者資料庫時，都應該備份 [master 資料庫](../../relational-databases/databases/master-database.md)。

`CREATE DATABASE` 陳述式必須在自動認可模式 (預設的交易管理模式) 下執行，且不能用於明確或隱含的交易。

您可使用一個 `CREATE DATABASE` 陳述式來建立資料庫與儲存資料庫的檔案。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 利用下列步驟實作 CREATE DATABASE 陳述式：

1. [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會使用[模型資料庫](../../relational-databases/databases/model-database.md)的複本將資料庫及其中繼資料初始化。
2. 將 Service Broker GUID 指派給資料庫。
3. 之後，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 就會在其餘的資料庫中填入空白頁面，但不包括含有記錄資料庫中空間使用方式之內部資料的頁面。

在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的一個執行個體上，最多可以指定 32,767 個資料庫。

每個資料庫都有一個可以在資料庫中執行特殊活動的擁有者。 該擁有者就是建立資料庫的使用者。 使用 [ALTER AUTHORIZATION](../../t-sql/statements/alter-authorization-transact-sql.md) 可以變更資料庫擁有者。

有些資料庫功能需倚賴檔案系統中提供的功能，才能發揮資料庫的完整功能。 一些倚賴檔案系統功能集的功能範例包括：

- DBCC CHECKDB
- FileStream
- 使用 VSS 和檔案快照集來進行的線上備份
- 資料庫快照集建立
- 記憶體最佳化資料檔案群組

## <a name="database-files-and-filegroups"></a>資料庫檔案與檔案群組

每個資料庫都至少會有兩個檔案 (一個「主要檔案」和一個「交易記錄檔」)，以及至少一個檔案群組。 每個資料庫最多可以指定 32,767 個檔案和 32,767 個檔案群組。

當您建立資料庫時，請根據您預期之資料庫中的資料量上限，盡量使資料檔案有足夠的空間。

建議您利用存放區域網路 (SAN)、iSCSI 型網路或本機連接的磁碟來儲存 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫檔案，因為這個組態可使 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 效能和可靠性最佳化。

## <a name="database-snapshots"></a>資料庫快照集

您可以使用 `CREATE DATABASE` 陳述式來建立「來源資料庫」的唯讀靜態檢視表，即「資料庫快照集」。 資料庫快照集在交易上與來源資料庫是一致的，因為它是在快照集建立時即存在。 來源資料庫可以有多個快照集。

> [!NOTE]
> 當您建立資料庫快照集時，`CREATE DATABASE` 陳述式無法參考記錄檔、離線檔案、還原檔案及已解除功能的檔案。

如果建立資料庫快照集失敗，快照集會受到質疑且必須刪除。 如需詳細資訊，請參閱 [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)。

每個快照集都會繼續保存，直到利用 `DROP DATABASE` 加以刪除為止。

如需詳細資訊，請參閱[資料庫快照集](../../relational-databases/databases/database-snapshots-sql-server.md)。

## <a name="database-options"></a>資料庫選項

每當您建立資料庫時，系統就會自動設定數個資料庫選項。 如需這些選項的清單，請參閱 [ALTER DATABASE SET 選項](../../t-sql/statements/alter-database-transact-sql-set-options.md)。

## <a name="the-model-database-and-creating-new-databases"></a>model 資料庫和建立新資料庫

[模型資料庫](../../relational-databases/databases/model-database.md)中的所有使用者定義物件都會複製到所有新建立的資料庫中。 您可以將所有要併入新建立之資料庫中的任何物件 (如資料表、檢視表、預存程序、資料類型等) 加入至 model 資料庫。

當指定不含其他大小參數的 `CREATE DATABASE <database_name>` 陳述式時，會將主要資料檔案之大小設為與模型資料庫中主要檔案的大小相同。

除非指定 `FOR ATTACH`，否則每個新資料庫都會從模型資料庫繼承資料庫選項設定。 例如，在模型資料庫及您建立的任何新資料庫中，auto shrink 資料庫選項會設定為 **true**。 如果您在 model 資料庫中變更選項，您建立的任何新資料庫也會使用這些新的選項設定。 變更 model 資料庫中的作業不會影響現有的資料庫。 如果在 CREATE DATABASE 陳述式上指定 FOR ATTACH，新資料庫就會繼承原始資料庫的資料庫選項設定。

## <a name="viewing-database-information"></a>檢視資料庫資訊

您可以利用目錄檢視、系統函數和系統預存程序，以傳回資料庫、檔案和檔案群組的相關資訊。 如需詳細資訊，請參閱[系統檢視](../language-reference.md)。

## <a name="permissions"></a>權限

需要 `CREATE DATABASE`、`CREATE ANY DATABASE` 或 `ALTER ANY DATABASE` 權限。

為了維護 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體的磁碟控制，通常只有少數登入帳戶有建立資料庫的權限。

下列範例會將建立資料庫的權限提供給資料庫使用者 Fay。

```sql
USE master;
GO
GRANT CREATE DATABASE TO [Fay];
GO
```

### <a name="permissions-on-data-and-log-files"></a>資料和記錄檔的權限

在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，某些權限是針對每個資料庫的資料檔案和記錄檔所設定。 每當在資料庫上套用下列作業時，都會設定下列權限：

- 已附加
- 已備份
- 建立時間
- 已卸離
- 修改以加入新檔案
- 已還原

檔案所在的目錄如有開放權限，上述權限可防止檔案遭到意外竄改。

> [!NOTE]
> [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssExpressEd2005](../../includes/ssexpressed2005-md.md)] 不會設定資料和記錄檔的權限。

## <a name="examples"></a>範例

### <a name="a-creating-a-database-without-specifying-files"></a>A. 建立資料庫但不指定檔案

下列範例會建立資料庫 `mytest` 並建立相對應的主要記錄檔和交易記錄檔。 因為該陳述式沒有 \<filespec> 項目，所以主要資料庫檔案的大小就是模型資料庫主要檔案的大小。 交易記錄會設為這些值中的較大者：512KB 或主要資料檔大小的 25%。 因為沒有指定 MAXSIZE，所以檔案會成長，直到填滿所有可用的磁碟空間為止。 此範例也會示範如何在建立 `mytest` 資料庫之前，卸除名為 `mytest` 的資料庫 (若存在)。

```sql
USE master;
GO
IF DB_ID (N'mytest') IS NOT NULL
DROP DATABASE mytest;
GO
CREATE DATABASE mytest;
GO
-- Verify the database files and sizes
SELECT name, size, size*1.0/128 AS [Size in MBs]
FROM sys.master_files
WHERE name = N'mytest';
GO
```

### <a name="b-creating-a-database-that-specifies-the-data-and-transaction-log-files"></a>B. 建立指定資料檔案和交易記錄檔的資料庫

下列範例會建立資料庫 `Sales`。 因為未使用關鍵字 PRIMARY，所以第一個檔案 (`Sales_dat`) 會成為主要檔案。 因為 `Sales_dat` 檔的 SIZE 參數中沒有指定 MB 或 KB，所以它會使用 MB 並 MB 來配置。 每當建立、修改或卸除使用者資料庫時，都應該備份 `Sales_log` 檔會以 MB 為單位配置，因為 `MB` 參數中明確陳述 `SIZE` 後置詞。

```sql
USE master;
GO
CREATE DATABASE Sales
ON
( NAME = Sales_dat,
    FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\saledat.mdf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 5 )
LOG ON
( NAME = Sales_log,
    FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\salelog.ldf',
    SIZE = 5MB,
    MAXSIZE = 25MB,
    FILEGROWTH = 5MB ) ;
GO
```

### <a name="c-creating-a-database-by-specifying-multiple-data-and-transaction-log-files"></a>C. 利用指定多個資料檔案和交易記錄檔的方式建立資料庫

下列範例會建立資料庫 `Archive`，這個資料庫有三個 `100-MB` 的資料檔案和兩個 `100-MB` 的交易記錄檔。 主要檔案是清單中的第一個檔案，並以關鍵字 `PRIMARY` 明確指定。 交易記錄檔是以關鍵字 `LOG ON` 指定的。 請注意 `FILENAME` 選項中之檔案的副檔名：`.mdf` 用於主要資料庫，`.ndf` 用於次要資料檔案，`.ldf` 則用於交易記錄檔。 此範例會將此資料庫放在 `D:` 磁碟機上，而不是與 `master` 資料庫放在一起。

```sql
USE master;
GO
CREATE DATABASE Archive
ON
PRIMARY
    (NAME = Arch1,
    FILENAME = 'D:\SalesData\archdat1.mdf',
    SIZE = 100MB,
    MAXSIZE = 200,
    FILEGROWTH = 20),
    ( NAME = Arch2,
    FILENAME = 'D:\SalesData\archdat2.ndf',
    SIZE = 100MB,
    MAXSIZE = 200,
    FILEGROWTH = 20),
    ( NAME = Arch3,
    FILENAME = 'D:\SalesData\archdat3.ndf',
    SIZE = 100MB,
    MAXSIZE = 200,
    FILEGROWTH = 20)
LOG ON
  (NAME = Archlog1,
    FILENAME = 'D:\SalesData\archlog1.ldf',
    SIZE = 100MB,
    MAXSIZE = 200,
    FILEGROWTH = 20),
  (NAME = Archlog2,
    FILENAME = 'D:\SalesData\archlog2.ldf',
    SIZE = 100MB,
    MAXSIZE = 200,
    FILEGROWTH = 20) ;
GO
```

### <a name="d-creating-a-database-that-has-filegroups"></a>D. 建立含有檔案群組的資料庫

下列範例會建立含有下列檔案群組的資料庫 `Sales`：

- 含有檔案 `Spri1_dat` 和 `Spri2_dat` 的主要檔案群組。 這些檔案的 FILEGROWTH 遞增指定為 `15%`。
- 名為 `SalesGroup1` 的檔案群組，該檔案群組含有檔案 `SGrp1Fi1` 和 `SGrp1Fi2`。
- 名為 `SalesGroup2` 的檔案群組，該檔案群組含有檔案 `SGrp2Fi1` 和 `SGrp2Fi2`。

此範例將資料和記錄檔放在不同的磁碟上，藉此改進效能。

```sql
USE master;
GO
CREATE DATABASE Sales
ON PRIMARY
( NAME = SPri1_dat,
    FILENAME = 'D:\SalesData\SPri1dat.mdf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 15% ),
( NAME = SPri2_dat,
    FILENAME = 'D:\SalesData\SPri2dt.ndf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 15% ),
FILEGROUP SalesGroup1
( NAME = SGrp1Fi1_dat,
    FILENAME = 'D:\SalesData\SG1Fi1dt.ndf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 5 ),
( NAME = SGrp1Fi2_dat,
    FILENAME = 'D:\SalesData\SG1Fi2dt.ndf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 5 ),
FILEGROUP SalesGroup2
( NAME = SGrp2Fi1_dat,
    FILENAME = 'D:\SalesData\SG2Fi1dt.ndf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 5 ),
( NAME = SGrp2Fi2_dat,
    FILENAME = 'D:\SalesData\SG2Fi2dt.ndf',
    SIZE = 10,
    MAXSIZE = 50,
    FILEGROWTH = 5 )
LOG ON
( NAME = Sales_log,
    FILENAME = 'E:\SalesLog\salelog.ldf',
    SIZE = 5MB,
    MAXSIZE = 25MB,
    FILEGROWTH = 5MB ) ;
GO
```

### <a name="e-attaching-a-database"></a>E. 附加資料庫

下列範例會先卸離在範例 D 中建立的資料庫 `Archive`，再利用 `FOR ATTACH` 子句附加該資料庫。 `Archive` 定義為具有多個資料檔案和記錄檔。 不過，因為檔案建立之後並未改變位置，所以在 `FOR ATTACH` 子句中只需要指定主要檔案。 從 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 開始，所附加之資料庫中的任何全文檢索檔案，都會隨著資料庫而一起附加。

```sql
USE master;
GO
sp_detach_db Archive;
GO
CREATE DATABASE Archive
  ON (FILENAME = 'D:\SalesData\archdat1.mdf')
  FOR ATTACH ;
GO
```

### <a name="f-creating-a-database-snapshot"></a>F. 建立資料庫快照集

下列範例會建立資料庫快照集 `sales_snapshot0600`。 因為資料庫快照集是唯讀的，所以不能指定記錄檔。 依照語法規定，會指定來源資料庫中的每個檔案，但不會指定檔案群組。

這個範例中的來源資料庫就是在範例 D 中建立的資料庫 `Sales`。

```sql
USE master;
GO
CREATE DATABASE sales_snapshot0600 ON
    ( NAME = SPri1_dat, FILENAME = 'D:\SalesData\SPri1dat_0600.ss'),
    ( NAME = SPri2_dat, FILENAME = 'D:\SalesData\SPri2dt_0600.ss'),
    ( NAME = SGrp1Fi1_dat, FILENAME = 'D:\SalesData\SG1Fi1dt_0600.ss'),
    ( NAME = SGrp1Fi2_dat, FILENAME = 'D:\SalesData\SG1Fi2dt_0600.ss'),
    ( NAME = SGrp2Fi1_dat, FILENAME = 'D:\SalesData\SG2Fi1dt_0600.ss'),
    ( NAME = SGrp2Fi2_dat, FILENAME = 'D:\SalesData\SG2Fi2dt_0600.ss')
AS SNAPSHOT OF Sales ;
GO
```

### <a name="g-creating-a-database-and-specifying-a-collation-name-and-options"></a>G. 建立資料庫並指定定序名稱和選項

下列範例會建立資料庫 `MyOptionsTest`。 它指定定序名稱，並將 `TRUSTYWORTHY` 和 `DB_CHAINING` 選項設為 `ON`。

```sql
USE master;
GO
IF DB_ID (N'MyOptionsTest') IS NOT NULL
DROP DATABASE MyOptionsTest;
GO
CREATE DATABASE MyOptionsTest
COLLATE French_CI_AI
WITH TRUSTWORTHY ON, DB_CHAINING ON;
GO
--Verifying collation and option settings.
SELECT name, collation_name, is_trustworthy_on, is_db_chaining_on
FROM sys.databases
WHERE name = N'MyOptionsTest';
GO
```

### <a name="h-attaching-a-full-text-catalog-that-has-been-moved"></a>H. 附加已移動的全文檢索目錄

下列範例會附加全文檢索目錄 `AdvWksFtCat` 以及 `AdventureWorks2012` 資料檔案和記錄檔。 在這個範例中，全文檢索目錄會從預設位置移至新的位置 `c:\myFTCatalogs`。 資料檔案和記錄檔仍保留在它們的預設位置中。

```sql
USE master;
GO
--Detach the AdventureWorks2012 database
sp_detach_db AdventureWorks2012;
GO
-- Physically move the full text catalog to the new location.
--Attach the AdventureWorks2012 database and specify the new location of the full-text catalog.
CREATE DATABASE AdventureWorks2012 ON
    (FILENAME = 'c:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\Data\AdventureWorks2012_data.mdf'),
    (FILENAME = 'c:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\Data\AdventureWorks2012_log.ldf'),
    (FILENAME = 'c:\myFTCatalogs\AdvWksFtCat')
FOR ATTACH;
GO
```

### <a name="i-creating-a-database-that-specifies-a-row-filegroup-and-two-filestream-filegroups"></a>I. 建立可指定一個資料列檔案群組和兩個 FILESTREAM 檔案群組的資料庫

下列範例會建立 `FileStreamDB` 資料庫。 此資料庫是使用一個資料列檔案群組和兩個 FILESTREAM 檔案群組所建立。 每個檔案群組都包含一個檔案：

- `FileStreamDB_data` 包含資料列資料， 它包含一個檔案 `FileStreamDB_data.mdf` (具有預設路徑)。
- `FileStreamPhotos` 包含 FILESTREAM 資料。 其包含兩個 FILESTREAM 資料容器：一個是位於 `FSPhotos` 的 `C:\MyFSfolder\Photos`，一個是位於 `FSPhotos2` 的 `D:\MyFSfolder\Photos`。 它會標示為預設的 FILESTREAM 檔案群組。
- `FileStreamResumes` 包含 FILESTREAM 資料。 其包含一個 FILESTREAM 資料容器 `FSResumes` (位於 `C:\MyFSfolder\Resumes`)。

```sql
USE master;
GO
-- Get the SQL Server data path.
DECLARE @data_path nvarchar(256);
SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

 -- Execute the CREATE DATABASE statement.
EXECUTE ('CREATE DATABASE FileStreamDB
ON PRIMARY
    (
    NAME = FileStreamDB_data
    ,FILENAME = ''' + @data_path + 'FileStreamDB_data.mdf''
    ,SIZE = 10MB
    ,MAXSIZE = 50MB
    ,FILEGROWTH = 15%
    ),
FILEGROUP FileStreamPhotos CONTAINS FILESTREAM DEFAULT
    (
    NAME = FSPhotos
    ,FILENAME = ''C:\MyFSfolder\Photos''
-- SIZE and FILEGROWTH should not be specified here.
-- If they are specified an error will be raised.
, MAXSIZE = 5000 MB
    ),
    (
      NAME = FSPhotos2
      , FILENAME = ''D:\MyFSfolder\Photos''
      , MAXSIZE = 10000 MB
     ),
FILEGROUP FileStreamResumes CONTAINS FILESTREAM
    (
    NAME = FileStreamResumes
    ,FILENAME = ''C:\MyFSfolder\Resumes''
    )
LOG ON
    (
    NAME = FileStream_log
    ,FILENAME = ''' + @data_path + 'FileStreamDB_log.ldf''
    ,SIZE = 5MB
    ,MAXSIZE = 25MB
    ,FILEGROWTH = 5MB
    )'
);
GO
```

### <a name="j-creating-a-database-that-has-a-filestream-filegroup-with-multiple-files"></a>J. 建立包含多個檔案之 FILESTREAM 檔案群組的資料庫

下列範例會建立 `BlobStore1` 資料庫。 此資料庫是使用一個資料列檔案群組和一個 FILESTREAM 檔案群組 `FS` 所建立。 FILESTREAM 檔案群組包含兩個檔案：`FS1` 和 `FS2`。 然後，此範例會將第三個檔案 `FS3` 加入至 FILESTREAM 檔案群組，藉以改變資料庫。

```sql
USE master;
GO

CREATE DATABASE [BlobStore1]
CONTAINMENT = NONE
ON PRIMARY
(
    NAME = N'BlobStore1',
    FILENAME = N'C:\BlobStore\BlobStore1.mdf',
    SIZE = 100MB,
    MAXSIZE = UNLIMITED,
    FILEGROWTH = 1MB
),
FILEGROUP [FS] CONTAINS FILESTREAM DEFAULT
(  
    NAME = N'FS1',
    FILENAME = N'C:\BlobStore\FS1',
    MAXSIZE = UNLIMITED
),
(
    NAME = N'FS2',
    FILENAME = N'C:\BlobStore\FS2',
    MAXSIZE = 100MB
)
LOG ON
(
    NAME = N'BlobStore1_log',
    FILENAME = N'C:\BlobStore\BlobStore1_log.ldf',
    SIZE = 100MB,
    MAXSIZE = 1GB,
    FILEGROWTH = 1MB
);
GO

ALTER DATABASE [BlobStore1]
ADD FILE
(
    NAME = N'FS3',
    FILENAME = N'C:\BlobStore\FS3',
    MAXSIZE = 100MB
)
TO FILEGROUP [FS];
GO
```

## <a name="see-also"></a>另請參閱

- [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md)
- [資料庫卸離與附加](../../relational-databases/databases/database-detach-and-attach-sql-server.md)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)
- [EVENTDATA](../../t-sql/functions/eventdata-transact-sql.md)
- [ALTER AUTHORIZATION](../../t-sql/statements/alter-authorization-transact-sql.md)
- [sp_detach_db](../../relational-databases/system-stored-procedures/sp-detach-db-transact-sql.md)
- [sp_removedbreplication](../../relational-databases/system-stored-procedures/sp-removedbreplication-transact-sql.md)
- [資料庫快照集](../../relational-databases/databases/database-snapshots-sql-server.md)
- [移動資料庫檔案](../../relational-databases/databases/move-database-files.md)
- [資料庫](../../relational-databases/databases/databases.md)
- [二進位大型物件 - Blob 資料](../../relational-databases/blob/binary-large-object-blob-data-sql-server.md)

::: moniker-end
::: moniker range="=azuresqldb-current"

:::row:::
    :::column:::
        [SQL Server](create-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL Database \*_**
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](create-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-database-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-database"></a>SQL Database

## <a name="overview"></a>概觀

在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，此陳述式可與 Azure SQL Server 搭配使用，以建立單一資料庫或彈性集區中的資料庫。 使用此陳述式，您可以指定資料庫名稱、定序、大小上限、版本、服務目標，以及 (如果適用的話) 新資料庫的彈性集區。 它也可以用來在彈性集區中建立資料庫。 此外，它可以用來在其他 SQL Database 伺服器上建立資料庫複本。

## <a name="syntax"></a>語法

### <a name="create-a-database"></a>建立資料庫

```syntaxsql
CREATE DATABASE database_name [ COLLATE collation_name ]
{
  (<edition_options> [, ...n])
}
[ WITH <with_options> [,..n]]
[;]

<with_options> ::=
{
  CATALOG_COLLATION = { DATABASE_DEFAULT | SQL_Latin1_General_CP1_CI_AS }
  | BACKUP_STORAGE_REDUNDANCY = { 'LOCAL' | 'ZONE' | 'GEO' }
}

<edition_options> ::=
{

  MAXSIZE = { 100 MB | 500 MB | 1 ... 1024 ... 4096 GB }
  | ( EDITION = { 'Basic' | 'Standard' | 'Premium' | 'GeneralPurpose' | 'BusinessCritical' | 'Hyperscale' }
  | SERVICE_OBJECTIVE =
    { 'Basic' | 'S0' | 'S1' | 'S2' | 'S3' | 'S4'| 'S6'| 'S7'| 'S9'| 'S12'
      | 'P1' | 'P2' | 'P4'| 'P6' | 'P11' | 'P15'
      | 'GP_Gen4_1' | 'GP_Gen4_2' | 'GP_Gen4_3' | 'GP_Gen4_4' | 'GP_Gen4_5' | 'GP_Gen4_6'
      | 'GP_Gen4_7' | 'GP_Gen4_8' | 'GP_Gen4_9' | 'GP_Gen4_10' | 'GP_Gen4_16' | 'GP_Gen4_24'
      | 'GP_Gen5_2' | 'GP_Gen5_4' | 'GP_Gen5_6' | 'GP_Gen5_8' | 'GP_Gen5_10' | 'GP_Gen5_12' | 'GP_Gen5_14'
      | 'GP_Gen5_16' | 'GP_Gen5_18' | 'GP_Gen5_20' | 'GP_Gen5_24' | 'GP_Gen5_32' | 'GP_Gen5_40' | 'GP_Gen5_80'
      | 'GP_Fsv2_8' | 'GP_Fsv2_10' | 'GP_Fsv2_12' | 'GP_Fsv2_14' | 'GP_Fsv2_16' | 'GP_Fsv2_18'
      | 'GP_Fsv2_20' | 'GP_Fsv2_24' | 'GP_Fsv2_32' | 'GP_Fsv2_36' | 'GP_Fsv2_72'
      | 'GP_S_Gen5_1' | 'GP_S_Gen5_2' | 'GP_S_Gen5_4' | 'GP_S_Gen5_6' | 'GP_S_Gen5_8'
      | 'GP_S_Gen5_10' | 'GP_S_Gen5_12' | 'GP_S_Gen5_14' | 'GP_S_Gen5_16'
      | 'GP_S_Gen5_18' | 'GP_S_Gen5_20' | 'GP_S_Gen5_24' | 'GP_S_Gen5_32' | 'GP_S_Gen5_40'
      | 'BC_Gen4_1' | 'BC_Gen4_2' | 'BC_Gen4_3' | 'BC_Gen4_4' | 'BC_Gen4_5' | 'BC_Gen4_6'
      | 'BC_Gen4_7' | 'BC_Gen4_8' | 'BC_Gen4_9' | 'BC_Gen4_10' | 'BC_Gen4_16' | 'BC_Gen4_24'
      | 'BC_Gen5_2' | 'BC_Gen5_4' | 'BC_Gen5_6' | 'BC_Gen5_8' | 'BC_Gen5_10' | 'BC_Gen5_12' | 'BC_Gen5_14'
      | 'BC_Gen5_16' | 'BC_Gen5_18' | 'BC_Gen5_20' | 'BC_Gen5_24' | 'BC_Gen5_32' | 'BC_Gen5_40' | 'BC_Gen5_80'
      | 'BC_M_8' | 'BC_M_10' | 'BC_M_12' | 'BC_M_14' | 'BC_M_16' | 'BC_M_18'
      | 'BC_M_20' | 'BC_M_24' | 'BC_M_32' | 'BC_M_64' | 'BC_M_128'
      | 'HS_GEN4_1' | 'HS_GEN4_2' | 'HS_GEN4_4' | 'HS_GEN4_8' | 'HS_GEN4_16' | 'HS_GEN4_24'
      | 'HS_GEN5_2' | 'HS_GEN5_4' | 'HS_GEN5_8' | 'HS_GEN5_16' | 'HS_GEN5_24' | 'HS_GEN5_32' | 'HS_GEN5_48' | 'HS_GEN5_80'
      | { ELASTIC_POOL(name = <elastic_pool_name>) } })
}
```

### <a name="copy-a-database"></a>複製資料庫

```syntaxsql
CREATE DATABASE database_name
    AS COPY OF [source_server_name.] source_database_name
    [ ( SERVICE_OBJECTIVE =
      { 'Basic' |'S0' | 'S1' | 'S2' | 'S3'| 'S4'| 'S6'| 'S7'| 'S9'| 'S12'
      | 'P1' | 'P2' | 'P4'| 'P6' | 'P11' | 'P15'
      | 'GP_Gen4_1' | 'GP_Gen4_2' | 'GP_Gen4_3' | 'GP_Gen4_4' | 'GP_Gen4_5' | 'GP_Gen4_6'
      | 'GP_Gen4_7' | 'GP_Gen4_8' | 'GP_Gen4_9' | 'GP_Gen4_10' | 'GP_Gen4_16' | 'GP_Gen4_24'
      | 'GP_Gen5_2' | 'GP_Gen5_4' | 'GP_Gen5_6' | 'GP_Gen5_8' | 'GP_Gen5_10' | 'GP_Gen5_12' | 'GP_Gen5_14'
      | 'GP_Gen5_16' | 'GP_Gen5_18' | 'GP_Gen5_20' | 'GP_Gen5_24' | 'GP_Gen5_32' | 'GP_Gen5_40' | 'GP_Gen5_80'
      | 'GP_Fsv2_8' | 'GP_Fsv2_10' | 'GP_Fsv2_12' | 'GP_Fsv2_14' | 'GP_Fsv2_16' | 'GP_Fsv2_18'
      | 'GP_Fsv2_20' | 'GP_Fsv2_24' | 'GP_Fsv2_32' | 'GP_Fsv2_36' | 'GP_Fsv2_72'
      | 'GP_S_Gen5_1' | 'GP_S_Gen5_2' | 'GP_S_Gen5_4' | 'GP_S_Gen5_6' | 'GP_S_Gen5_8'
      | 'GP_S_Gen5_10' | 'GP_S_Gen5_12' | 'GP_S_Gen5_14' | 'GP_S_Gen5_16'
      | 'GP_S_Gen5_18' | 'GP_S_Gen5_20' | 'GP_S_Gen5_24' | 'GP_S_Gen5_32' | 'GP_S_Gen5_40'
      | 'BC_Gen4_1' | 'BC_Gen4_2' | 'BC_Gen4_3' | 'BC_Gen4_4' | 'BC_Gen4_5' | 'BC_Gen4_6'
      | 'BC_Gen4_7' | 'BC_Gen4_8' | 'BC_Gen4_9' | 'BC_Gen4_10' | 'BC_Gen4_16' | 'BC_Gen4_24'
      | 'BC_Gen5_2' | 'BC_Gen5_4' | 'BC_Gen5_6' | 'BC_Gen5_8' | 'BC_Gen5_10' | 'BC_Gen5_12' | 'BC_Gen5_14'
      | 'BC_Gen5_16' | 'BC_Gen5_18' | 'BC_Gen5_20' | 'BC_Gen5_24' | 'BC_Gen5_32' | 'BC_Gen5_40' | 'BC_Gen5_80'
      | 'BC_M_8' | 'BC_M_10' | 'BC_M_12' | 'BC_M_14' | 'BC_M_16' | 'BC_M_18'
      | 'BC_M_20' | 'BC_M_24' | 'BC_M_32' | 'BC_M_64' | 'BC_M_128'
      | 'HS_GEN4_1' | 'HS_GEN4_2' | 'HS_GEN4_4' | 'HS_GEN4_8' | 'HS_GEN4_16' | 'HS_GEN4_24'
      | 'HS_GEN5_2' | 'HS_GEN5_4' | 'HS_GEN5_8' | 'HS_GEN5_16' | 'HS_GEN5_24' | 'HS_GEN5_32' | 'HS_GEN5_48' | 'HS_GEN5_80'
      | { ELASTIC_POOL(name = <elastic_pool_name>) } })
   ]
[;]
```

## <a name="arguments"></a>引數

*database_name* 新資料庫的名稱。 這個名稱在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上必須是唯一的，且符合 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的識別碼規則。 如需詳細資訊，請參閱[識別碼](../../relational-databases/databases/database-identifiers.md)。

*Collation_name* 指定資料庫的預設定序。 定序名稱可以是 Windows 定序名稱或 SQL 定序名稱。 如果未指定，則會將資料庫指派預設定序，即 SQL_Latin1_General_CP1_CI_AS。

如需有關 Windows 和 SQL 定序名稱的詳細資訊，請參閱 [COLLATE (Transact-SQL)](../../t-sql/statements/collations.md)。

CATALOG_COLLATION 指定中繼資料目錄的預設定序。 *DATABASE_DEFAULT* 指定將用於系統檢視和系統資料表的中繼資料目錄加以定序，以符合資料庫的預設定序。 這是在 SQL Server 中發現的行為。

*SQL_Latin1_General_CP1_CI_AS* 指定將用於系統檢視和資料表的中繼資料目錄定序為固定 SQL_Latin1_General_CP1_CI_AS 定序。 如果未指定，這就是 Azure SQL Database 上的預設設定。

BACKUP_STORAGE_REDUNDANCY 會為資料庫指定時間點還原與長期保留備份的複寫方式。 異地還原或從區域中斷復原的能力，只有在使用「異地」備份儲存體備援建立資料庫時才可使用。 除非明確指定，否則以 T-SQL 建立的資料庫會使用異地備援備份儲存體。 

> [!IMPORTANT]
> Azure SQL 資料庫的 BACKUP_STORAGE_REDUNDANCY 選項在巴西南部推出公開預覽，且僅在 Azure 區域的東南亞正式推出。  

EDITION 指定資料庫的服務層級。

單一資料庫與集區資料庫。 可用的值為：'Basic'、'Standard'、'Premium'、'GeneralPurpose'、'BusinessCritical' 和 'Hyperscale'。

MAXSIZE 指定資料庫的大小上限。 MAXSIZE 對於指定的 EDITION (服務層) 而言必須有效。下表列出服務層支援的 MAXSIZE 值與預設值 (D)：

> [!NOTE]
> **MAXSIZE** 引數不適用於超大規模服務層中的單一資料庫。 超大規模層資料庫會視需要成長，最多 100 TB。 SQL Database 服務會自動新增儲存體；您不需要設定大小上限。

**SQL Database 伺服器上適用於單一和集區資料庫的 DTU 模型**

|**MAXSIZE**|**基本**|**S0-S2**|**S3-S12**|**P1-P6**| **P11-P15** |
|:---|:---|:---|:---|:---|:---|
|100 MB|√|√|√|√|√|
|500 MB|√|√|√|√|√|
|1 GB|√|√|√|√|√|
|2 GB|√ (D)|√|√|√|√|
|5 GB|N/A|√|√|√|√|
|10 GB|N/A|√|√|√|√|
|20 GB|N/A|√|√|√|√|
|30 GB|N/A|√|√|√|√|
|40 GB|N/A|√|√|√|√|
|50 GB|N/A|√|√|√|√|
|100 GB|N/A|√|√|√|√|
|150 GB|N/A|√|√|√|√|
|200 GB|N/A|√|√|√|√|
|250 GB|N/A|√ (D)|√ (D)|√|√|
|300 GB|N/A|N/A|√|√|√|
|400 GB|N/A|N/A|√|√|√|
|500 GB|N/A|N/A|√|√ (D)|√|
|750 GB|N/A|N/A|√|√|√|
|1024 GB|N/A|N/A|√|√|√ (D)|
|從 1024 GB 至最大 4096 GB (以每 256 GB 的大小遞增)* |N/A|N/A|不適用|不適用|√|√|

\* P11 和 P15 允許 MAXSIZE 最大至 4 TB，並以 1024 GB 作為預設大小。 P11 和 P15 最多可使用 4 TB 的隨附儲存體，且不另收費。 在進階層中，大於 1 TB 的 MAXSIZE 目前可用於下列區域：美國東部 2、美國西部、US Gov 維吉尼亞州、西歐、德國中部、東南亞、日本東部、澳大利亞東部、加拿大中部和加拿大東部。 如需 DTU 模型的資源限制的額外詳細資訊，請參閱 [DTU 資源限制](/azure/sql-database/sql-database-dtu-resource-limits) \(部分機器翻譯\)。

對於 DTU 模型，若指定了 MAXSIZE 值，則此值必須為上表中所示適用於所指定服務層的有效值。

**虛擬核心模型**

**一般用途 - 佈建的計算 - Gen4 (第 1 部分)**

|MAXSIZE|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6|
|:----- | ------: |-------: |-------: |-------: |-------: |--------:|
|資料大小上限 (GB)|1024|1024|1024|1536|1536|1536|

**一般用途 - 佈建的計算 - Gen4 (第 2 部分)**

|MAXSIZE|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:----- | ------: |-------: |-------: |-------: |-------: |--------:|
|資料大小上限 (GB)|1536|3072|3072|3072|4096|4096|

**一般用途 - 佈建的計算 - Gen5 (第 1 部分)**

|MAXSIZE|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:----- | ------: |-------: |-------: |-------: |--------: |---------:|--------: |
|資料大小上限 (GB)|1024|1024|1024|1536|1536|1536|1536|

**一般用途 - 佈建的計算 - Gen5 (第 2 部分)**

|MAXSIZE|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:----- | ------: |-------: |-------: |-------: |--------: |---------:|--------: |
|資料大小上限 (GB)|3072|3072|3072|4096|4096|4096|4096|

**一般用途 - 佈建的計算 - Fsv2-系列 (第 1 部分)**

|MAXSIZE|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14|GP_Fsv2_16|GP_Fsv2_18|
|:----- | ------: | ------: | ------: | ------: | ------: | ------: |
|資料大小上限 (GB)|1024|1024|1024|1024|1536|1536|

**一般用途 - 佈建的計算 - Fsv2-系列 (第 2 部分)**

|MAXSIZE|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32|GP_Fsv2_36|GP_Fsv2_72|
|:----- | ------: | ------: | ------: | ------: | ------: |
|資料大小上限 (GB)|1536|1536|3072|3072|4096|

**一般用途 - 無伺服器計算 - Gen5 (第 1 部分)**

|MAXSIZE|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:----- | ------: |-------: |-------: |-------: |--------: |
|虛擬核心數上限|1|2|4|6|8|

**一般用途 - 無伺服器計算 - Gen5 (第 2 部分)**

|MAXSIZE|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:----- | ------: |-------: |-------: |-------: |
|虛擬核心數上限|10|12|14|16|

**一般用途 - 無伺服器計算 - Gen5 (第 3 部分)**

|MAXSIZE|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:----- | ------: |-------: |-------: |-------: |--------: |
|虛擬核心數上限|18|20|24|32|40|

**商務關鍵性 - 佈建的計算 - Gen4 (第 1 部分)**

|計算大小 (服務目標)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--------------- | ------: |-------: |-------: |-------: |-------: |-------: |
|資料大小上限 (GB)|1024|1024|1024|1024|1024|1024|

**商務關鍵性 - 佈建的計算 - Gen4 (第 2 部分)**

|計算大小 (服務目標)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--------------- | ------: |-------: |-------: |--------: |--------: |--------: |
|資料大小上限 (GB)|1024|1024|1024|1024|1024|1024|

**商務關鍵性 - 佈建的計算 - Gen5 (第 1 部分)**

|MAXSIZE|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:----- | ------: |-------: |-------: |-------: |---------: |--------:|--------: |
|資料大小上限 (GB)|1024|1024|1024|1536|1536|1536|1536|

**商務關鍵性 - 佈建的計算 - Gen5 (第 2 部分)**

|MAXSIZE|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:----- | -------: |--------: |--------: |--------: |--------: |---------:|--------: |
|資料大小上限 (GB)|3072|3072|3072|4096|4096|4096|4096|

**商務關鍵性 - 佈建的計算 - M-系列 (第 1 部分)**

|MAXSIZE|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:----- | -------: | -------: | -------: | -------: | -------: | -------: |
|資料大小上限 (GB)|512|640|768|896|1024|1152|

**商務關鍵性 - 佈建的計算 - M-系列 (第 2 部分)**

|MAXSIZE|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:----- | -------: | -------: | -------: | -------: | -------: |
|資料大小上限 (GB)|1280|1536|2048|4096|4096|

當使用 vCore 模型時，如果未設定 `MAXSIZE` 值，預設值為 32 GB。 如需有關虛擬核心模型資源限制的其他詳細資訊，請參閱[虛擬核心資源限制](/azure/sql-database/sql-database-dtu-resource-limits)。

以下規則會套用到 MAXSIZE 和 EDITION 引數：

- 如果指定了 EDITION 但是未指定 MAXSIZE，就會使用版本的預設值。 例如，如果 EDITION 設定為 Standard，且未指定 MAXSIZE，則 MAXSIZE 會自動設定為 250 MB。
- 如果 MAXSIZE 和 EDITION 皆未指定，則 EDITION 會設定為 `GeneralPurpose` 而 MAXSIZE 則設定為 32 GB。

SERVICE_OBJECTIVE

- **針對單一和集區資料庫**

  - 指定計算大小 (服務目標)。 服務目標的可用值為：`S0`、`S1`、`S2`、`S3`、`S4`、`S6`、`S7`、`S9`、`S12`、`P1`、`P2`、`P4`、`P6`、`P11`、`P15`、`GP_GEN4_1`、`GP_GEN4_2`、`GP_GEN4_3`、`GP_GEN4_4`、`GP_GEN4_5`、`GP_GEN4_6`、`GP_GEN4_7`、`GP_GEN4_8`、`GP_GEN4_7`、`GP_GEN4_8`、`GP_GEN4_9`、`GP_GEN4_10`、`GP_GEN4_16`、`GP_GEN4_24`、`BC_GEN4_1`、`BC_GEN4_2`、`BC_GEN4_3`、`BC_GEN4_4`、`BC_GEN4_5`、`BC_GEN4_6`、`BC_GEN4_7`、`BC_GEN4_8`、`BC_GEN4_9`、`BC_GEN4_10`、`BC_GEN4_16`、`BC_GEN4_24`、`GP_Gen5_2`、`GP_Gen5_4`、`GP_Gen5_6`、`GP_Gen5_8`、`GP_Gen5_10`、`GP_Gen5_12`、`GP_Gen5_14`、`GP_Gen5_16`、`GP_Gen5_18`、`GP_Gen5_20`、`GP_Gen5_24`、`GP_Gen5_32`、`GP_Gen5_40`、`GP_Gen5_80`、`GP_Fsv2_8`、`GP_Fsv2_10`、`GP_Fsv2_12`、`GP_Fsv2_14`、`GP_Fsv2_16`、`GP_Fsv2_18`、`GP_Fsv2_20`、`GP_Fsv2_24`、`GP_Fsv2_32`、`GP_Fsv2_36`、`GP_Fsv2_72`、`BC_Gen5_2`、`BC_Gen5_4`、`BC_Gen5_6`、`BC_Gen5_8`、`BC_Gen5_10`、`BC_Gen5_12`、`BC_Gen5_14`、`BC_Gen5_16`、`BC_Gen5_18`、`BC_Gen5_20`、`BC_Gen5_24`、`BC_Gen5_32`、`BC_Gen5_40`、`BC_Gen5_80`、`BC_M_8`、`BC_M_10`、`BC_M_12`、`BC_M_14`、`BC_M_16`、`BC_M_18`、`BC_M_20`、`BC_M_24`、`BC_M_32`、`BC_M_64`、`BC_M_128`。

- **適用於無伺服器資料庫**
- 
  - 指定計算大小 (服務目標)。 服務目標的可用值為：`GP_S_Gen5_1`、`GP_S_Gen5_2`、`GP_S_Gen5_4`、`GP_S_Gen5_6`、`GP_S_Gen5_8`、`GP_S_Gen5_10`、`GP_S_Gen5_12`、`GP_S_Gen5_14`、`GP_S_Gen5_16`、`GP_S_Gen5_18`、`GP_S_Gen5_20`、`GP_S_Gen5_24`、`GP_S_Gen5_32`、`GP_S_Gen5_40`。

- **針對超大規模服務層中的單一資料庫**

  - 指定計算大小 (服務目標)。 服務目標的可用值為：`HS_GEN4_1`、`HS_GEN4_2`、`HS_GEN4_4`、`HS_GEN4_8`、`HS_GEN4_16`、`HS_GEN4_24`、`HS_Gen5_2`、`HS_Gen5_4`、`HS_Gen5_8`、`HS_Gen5_16`、`HS_Gen5_24`、`HS_Gen5_32`、`HS_Gen5_48`、`HS_Gen5_80`。

如需服務目標描述和大小、版本及服務目標組合的詳細資訊，請參閱 [Azure SQL Database 服務層](/azure/sql-database/sql-database-service-tiers)。 如果 EDITION 不支援指定的 SERVICE_OBJECTIVE，您就會收到錯誤。 若要將 SERVICE_OBJECTIVE 值從某一層變更為另一層 (例如，從 S1 到 P1)，您還必須變更 EDITION 值。 如需服務目標描述和大小、版本及服務目標組合的詳細資訊，請參閱 [Azure SQL Database 服務層和效能層級](/azure/azure-sql/database/purchasing-models) \(部分機器翻譯\)、[DTU 資源限制](/azure/sql-database/sql-database-dtu-resource-limits) \(部分機器翻譯\) 和[虛擬核心資源限制](/azure/sql-database/sql-database-dtu-resource-limits) \(部分機器翻譯\)。 目前已移除對 PRS 服務目標的支援。 如有疑問，請使用此電子郵件別名： premium-rs@microsoft.com。

ELASTIC_POOL (name = \<elastic_pool_name>) **適用於：** 僅單一和集區資料庫。 不適用於超大規模服務層中的資料庫。
若要在彈性資料庫集區中建立新資料庫，請將資料庫的 SERVICE_OBJECTIVE 設定為 ELASTIC_POOL 並提供集區的名稱。 如需詳細資訊，請參閱[建立和管理 SQL Database 彈性資料庫集區](/azure/azure-sql/database/elastic-pool-overview)。

AS COPY OF [source_server_name.]source_database_name **適用於：** 僅單一和集區資料庫。
用於將資料庫複製到相同或不同的 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 伺服器上。

*source_server_name* 來源資料庫所在的 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 伺服器名稱。 當來源資料庫和目的地資料庫位於相同的 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 伺服器上時，這個參數是選擇性的。

> [!NOTE]
> `AS COPY OF` 引數不支援唯一的完整網域名稱。 換句話說，如果您伺服器的完整網域名稱為 `serverName.database.windows.net`，則在資料庫複製期間僅可使用 `serverName`。

*source_database_name*

要複製的資料庫名稱。

## <a name="remarks"></a>備註

[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中的資料庫有數個預設設定，這些設定是在建立資料庫時所設定。 如需這些預設設定的詳細資訊，請參閱 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 中的值清單。

`MAXSIZE` 提供限制資料庫大小的功能。 如果資料庫的大小達到其 `MAXSIZE`，您將收到錯誤碼 40544。 發生這種情況時，您就無法插入或更新資料，或是建立新物件 (例如資料表、預存程序、檢視和函數)。 不過，您仍然可以讀取和刪除資料、截斷資料表、卸除資料表和索引，以及重建索引。 然後您可以將 `MAXSIZE` 升級為大於目前資料庫大小的值，或是刪除某些資料以釋出儲存空間。 在您能夠插入新資料之前，最長可能會有十五分鐘的延遲。

若之後要變更大小、版本或服務目標值，請使用 [ALTER DATABASE - Azure SQL Database](../../t-sql/statements/alter-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)。

只有在資料庫建立期間才可使用 `CATALOG_COLLATION` 引數。

## <a name="database-copies"></a>資料庫複本

**適用範圍：** 僅單一和集區資料庫。

使用 `CREATE DATABASE` 陳述式複製資料庫是一項非同步作業。 因此，整個複製程序期間都不需要連接至 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 伺服器。 `CREATE DATABASE` 陳述式會在 sys.databases 中的項目建立後，但在資料庫複製作業完成前，將控制權交還給使用者。 換句話說，`CREATE DATABASE` 陳述式會在資料庫複製仍進行時成功傳回。

- 監視 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] 伺服器上的複製程序：查詢 [dm_database_copies](../../relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database.md) 中的 `percentage_complete` 或 `replication_state_desc` 資料行，或是 **sys.databases** 檢視中的 `state` 資料行。 [sys.dm_operation_status](../../relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database.md) 檢視也可使用，因為其會傳回資料庫作業 (包括資料庫複製) 的狀態。

當複製程序順利完成時，目的地資料庫的交易會與來源資料庫一致。

下列語法和語意規則適用於使用 `AS COPY OF` 引數的情況：

- 來源伺服器名稱和複製目標的伺服器名稱可以相同，也可以不同。 兩個名稱相同時，則這是是選擇性參數，而且根據預設會使用目前工作階段的伺服器內容。
- 來源和目的地資料庫名稱必須加以指定、是唯一的，並且符合 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的識別碼規則。 如需詳細資訊，請參閱[識別碼](../../relational-databases/databases/database-identifiers.md)。
- `CREATE DATABASE` 陳述式必須在將要建立新資料庫之 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 伺服器的 master 資料庫內容中執行。
- 複製完成後，目的地資料庫必須做為獨立資料庫管理。 您可以在與來源資料庫不相關的情況下，單獨對新資料庫執行 `ALTER DATABASE` 和 `DROP DATABASE` 陳述式。 您也可以將新資料庫複製到另一個新資料庫。
- 資料庫複製正在進行時，可能會繼續存取來源資料庫。

如需詳細資訊，請參閱[使用 Transact-SQL 建立 Azure SQL 資料庫的複本](/azure/azure-sql/database/database-copy)。

> [!IMPORTANT]
> 根據預設，會使用與源資料庫相同的備份儲存體備援來建立資料庫複本。 不支援透過 T-SQL 在建立資料庫複本時變更備份儲存體備援。 


## <a name="permissions"></a>權限

若要建立資料庫，登入必須為下列其中一項：

- 伺服器層級主體登入
- 本機 Azure SQL Server 的 Azure AD 系統管理員
- `dbmanager` 資料庫角色成員的登入

**使用 `CREATE DATABASE ... AS COPY OF` 語法的額外需求：** 在本機伺服器上執行陳述式的登入必須至少也是來源伺服器上的 `db_owner`。 如果登入依據 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證進行，則在本機伺服器上執行陳述式的登入必須與來源 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 伺服器上登入相符，具有相同的名稱和密碼。

## <a name="examples"></a>範例

### <a name="simple-example"></a>簡單範例

建立資料庫的簡單範例。

```sql
CREATE DATABASE TestDB1;
```

### <a name="simple-example-with-edition"></a>使用 Edition 的簡單範例

建立一般用途資料庫的簡單範例。

```sql
CREATE DATABASE TestDB2
( EDITION = 'GeneralPurpose' );
```

### <a name="example-with-additional-options"></a>使用額外選項的範例

使用多個選項的範例。

```sql
CREATE DATABASE hito
COLLATE Japanese_Bushu_Kakusu_100_CS_AS_KS_WS
( MAXSIZE = 500 MB, EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_GEN4_8' ) ;
```

### <a name="creating-a-copy"></a>建立複本

建立資料庫複本的範例。

**適用範圍：** 僅單一和集區資料庫。

```sql
CREATE DATABASE escuela
AS COPY OF school;
```

### <a name="creating-a-database-in-an-elastic-pool"></a>在彈性集區中建立資料庫

在名為 S3M100 的集區中建立新的資料庫：

**適用範圍：** 僅單一和集區資料庫。

```sql
CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL ( name = S3M100 ) ) ;
```

### <a name="creating-a-copy-of-a-database-on-another-server"></a>在另一個伺服器上建立資料庫的複本

下列範例會建立 db_original 資料庫的複本，在單一資料庫的 P2 計算大小 (服務目標) 中名為 db_copy。 不論 db_original 位於彈性集區或單一資料庫的計算大小 (服務目標) 中，皆是如此。

**適用範圍：** 僅單一和集區資料庫。

```sql
CREATE DATABASE db_copy
  AS COPY OF ozabzw7545.db_original ( SERVICE_OBJECTIVE = 'P2' );
```

下列範例會建立 db_original 資料庫的複本，在名為 ep1 的彈性集區中名為 db_copy。 不論 db_original 位於彈性集區或單一資料庫的計算大小 (服務目標) 中，皆是如此。 如果 db_original 在彈性集區中，但使用不同的名稱，則 db_copy 仍會建立在 ep1 中。

**適用範圍：** 僅單一和集區資料庫。

```sql
CREATE DATABASE db_copy
  AS COPY OF ozabzw7545.db_original
  (SERVICE_OBJECTIVE = ELASTIC_POOL( name = ep1 ) ) ;
```

### <a name="create-database-with-specified-catalog-collation-value"></a>使用指定的目錄定序值建立資料庫

下列範例會在資料庫建立期間，將目錄定序設定為 DATABASE_DEFAULT ，其會將目錄定序設定為與資料庫定序相同。

```sql
CREATE DATABASE TestDB3 COLLATE Japanese_XJIS_140 (MAXSIZE = 100 MB, EDITION = 'Basic')
  WITH CATALOG_COLLATION = DATABASE_DEFAULT
```

### <a name="create-database-using-zone-redundancy-for-backups"></a>使用區域備援來建立備份的資料庫

下列範例會設定資料庫備份的區域備援。 時間點還原備份與長期保留備份 (如果已設定) 將會使用相同的備份儲存體備援。

```sql
CREATE DATABASE test_zone_redundancy 
  WITH BACKUP_STORAGE_REDUNDANCY = 'ZONE';
```

## <a name="see-also"></a>另請參閱

- [sys.dm_database_copies - Azure SQL Database](../../relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database.md)
- [ALTER DATABASE - Azure SQL Database](alter-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)

::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](create-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL 受控執行個體\*_**
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-database-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-sql-managed-instance"></a>Azure SQL 受控執行個體

## <a name="overview"></a>概觀

在 Azure SQL 受控執行個體中，此陳述式可用來建立資料庫。 在受控執行個體上建立資料庫時，您會指定資料庫名稱和定序。

## <a name="syntax"></a>語法

```syntaxsql
CREATE DATABASE database_name [ COLLATE collation_name ]
[;]
```

> [!IMPORTANT]
> 若要對受控執行個體中的資料庫新增檔案或設定內含項目，請使用 [ALTER DATABASE](alter-database-transact-sql.md?tabs=sqldbmi) 陳述式。

## <a name="arguments"></a>引數

*database_name* 新資料庫的名稱。 此名稱在 SQL 伺服器上必須是唯一的，並且符合 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的識別碼規則。 如需詳細資訊，請參閱[識別碼](../../relational-databases/databases/database-identifiers.md)。

*Collation_name* 指定資料庫的預設定序。 定序名稱可以是 Windows 定序名稱或 SQL 定序名稱。 如果未指定，則會將資料庫指派預設定序，即 SQL_Latin1_General_CP1_CI_AS。

如需有關 Windows 和 SQL 定序名稱的詳細資訊，請參閱 [COLLATE (Transact-SQL)](../../t-sql/statements/collations.md)。

## <a name="remarks"></a>備註

[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中的資料庫有數個預設設定，這些設定是在建立資料庫時所設定。 如需這些預設設定的詳細資訊，請參閱 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 中的值清單。

> [!IMPORTANT]
> `CREATE DATABASE` 陳述式必須是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 批次中唯一的陳述式。

以下是 `CREATE DATABASE` 的限制：

- 無法定義檔案和檔案群組。
- 不支援 `WITH` 選項。

  > [!TIP]
  > 因應措施為使用 [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)。 在 `CREATE DATABASE` 之後，以設定資料庫選項和新增檔案。

## <a name="permissions"></a>權限

若要建立資料庫，登入必須為下列其中一項：

- 伺服器層級主體登入
- 本機 Azure SQL Server 的 Azure AD 系統管理員
- `dbcreator` 資料庫角色成員的登入

## <a name="examples"></a>範例

### <a name="simple-example"></a>簡單範例

建立資料庫的簡單範例。

```sql
CREATE DATABASE TestDB1;
```

## <a name="see-also"></a>另請參閱

請參閱 [ALTER DATABASE](alter-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](create-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](create-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_**
    :::column-end:::
    :::column:::
        [Analytics Platform<br />System (PDW)](create-database-transact-sql.md?view=aps-pdw-2016&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

## <a name="overview"></a>概觀

在 Azure Synapse 中，此陳述式可與 Azure SQL Database 伺服器搭配使用，以建立 SQL Analytics 資料庫。 使用此陳述式，您可以指定資料庫名稱、定序、大小上限、版本及服務目標。

## <a name="syntax"></a>語法

### <a name="dedicated-sql-pool"></a>[專用 SQL 集區](#tab/sqlpool)
```syntaxsql
CREATE DATABASE database_name [ COLLATE collation_name ]
(
    [ MAXSIZE = {
          250 | 500 | 750 | 1024 | 5120 | 10240 | 20480 | 30720
        | 40960 | 51200 | 61440 | 71680 | 81920 | 92160 | 102400
        | 153600 | 204800 | 245760
      } GB ,
    ]
    EDITION = 'datawarehouse',
    SERVICE_OBJECTIVE = {
         'DW100' | 'DW200' | 'DW300' | 'DW400' | 'DW500' | 'DW600'
        | 'DW1000' | 'DW1200' | 'DW1500' | 'DW2000' | 'DW3000' | 'DW6000'
        |'DW100c' | 'DW200c' | 'DW300c' | 'DW400c' | 'DW500c'
        | 'DW1000c' | 'DW1500c' | 'DW2000c' | 'DW2500c' | 'DW3000c' | 'DW5000c'
        | 'DW6000c' | 'DW7500c' | 'DW10000c' | 'DW15000c' | 'DW30000c'
    }
)
[;]
```
### <a name="serverless-sql-pool"></a>[無伺服器 SQL 集區](#tab/sqlod)
```syntaxsql
CREATE DATABASE database_name [ COLLATE collation_name ]
[;] 
```
---

## <a name="arguments"></a>引數

*database_name* 新資料庫的名稱。 此名稱在 SQL Server 上必須是唯一名稱，其可同時裝載 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 資料庫和 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 資料庫，且必須符合 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的識別碼規則。 如需詳細資訊，請參閱[識別碼](../../relational-databases/databases/database-identifiers.md)。

*collation_name* 指定資料庫的預設定序。 定序名稱可以是 Windows 定序名稱或 SQL 定序名稱。 如果未指定，則會將資料庫指派預設定序，即 SQL_Latin1_General_CP1_CI_AS。

如需有關 Windows 和 SQL 定序名稱的詳細資訊，請參閱 [COLLATE (Transact-SQL)](./collations.md)。

*EDITION* 指定資料庫的服務層。 若是 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]，請使用 'datawarehouse'。

*MAXSIZE* 預設為 245,760 GB (240 TB)。

**適用範圍：** 針對「計算第 1 代」最佳化

資料庫的允許大小上限。 資料庫不可增大超過 MAXSIZE。

**適用範圍：** 針對「計算第 2 代」最佳化

資料庫中資料列存放區資料的允許大小上限。 儲存在資料列存放區資料表、資料行存放區索引的差異存放區，或叢集資料行存放區索引上非叢集索引的資料，其大小不可超過 MAXSIZE。壓縮成資料行存放區格式的資料沒有大小限制，因此不受 MAXSIZE 限制。

SERVICE_OBJECTIVE 指定計算大小 (服務目標)。 如需適用於 Azure Synapse 之服務目標的詳細資訊，請參閱[資料倉儲單位 (DWU)](/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu) \(部分機器翻譯\)。

## <a name="general-remarks"></a>一般備註

使用 [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md) 以查看資料庫屬性。

使用 [ALTER DATABASE - Azure Synapse Analytics](../../t-sql/statements/alter-database-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true) 來在稍後變更大小上限或服務目標值。

Azure Synapse 已設定為 COMPATIBILITY_LEVEL 130 且無法變更。 如需詳細資料，請參閱 [Azure SQL Database 中改善的查詢效能與相容性層級 130](./alter-database-transact-sql-compatibility-level.md)。

## <a name="permissions"></a>權限

必要權限：

- 由佈建程序建立的伺服器層級主體登入，或
- `dbmanager` 資料庫角色的成員。

## <a name="error-handling"></a>錯誤處理

如果資料庫的大小達到 MAXSIZE，您將收到錯誤碼 40544。 若發生這種情況，您就無法插入和更新資料，或是建立新物件 (例如資料表、預存程序、檢視和函式)。 您仍然可以讀取和刪除資料、截斷資料表、卸除資料表和索引，以及重建索引。 然後您可以將 MAXSIZE 升級為大於目前資料庫大小的值，或是刪除某些資料以釋出儲存空間。 在您能夠插入新資料之前，最長可能會有十五分鐘的延遲。

## <a name="limitations-and-restrictions"></a>限制事項

您必須連接到 master 資料庫才能建立新的資料庫。

`CREATE DATABASE` 陳述式必須是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 批次中唯一的陳述式。

在資料庫建立後，您就無法變更資料庫定序。

## <a name="examples-sssdwfull"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]

### <a name="a-simple-example"></a>A. 簡單範例
建立資料倉儲資料庫的簡單範例。 這所建立出的資料庫，其大小上限最小為 10240 GB、預設定序為 SQL_Latin1_General_CP1_CI_AS，且最小運算能力為 DW100。

```sql
CREATE DATABASE TestDW
(EDITION = 'datawarehouse', SERVICE_OBJECTIVE='DW100');
```

### <a name="b-create-a-data-warehouse-database-with-all-the-options"></a>B. 以所有選項建立資料倉儲資料庫
使用所有選項建立 10 TB 資料倉儲的範例。

```sql
CREATE DATABASE TestDW COLLATE Latin1_General_100_CI_AS_KS_WS
(MAXSIZE = 10240 GB, EDITION = 'datawarehouse', SERVICE_OBJECTIVE = 'DW1000');
```

## <a name="see-also"></a>另請參閱

- [ALTER DATABASE- Azure Synapse Analytics](../../t-sql/statements/alter-database-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
- [CREATE TABLE- Azure Synapse Analytics](../../t-sql/statements/create-table-azure-sql-data-warehouse.md)
- [DROP DATABASE - Transact-SQL](../../t-sql/statements/drop-database-transact-sql.md)

::: moniker-end
::: moniker range=">=aps-pdw-2016"

:::row:::
    :::column:::
        [SQL Server](create-database-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL Database](create-database-transact-sql.md?view=azuresqldb-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](create-database-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Analytics Platform<br />System (PDW) \*_**
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="analytics-platform-system"></a>分析平台系統

## <a name="overview"></a>概觀

在 Analytics Platform System 中，此陳述式可用來在 Analytics Platform System 設備上建立新的資料庫。 使用此陳述式建立和設備資料庫關聯的所有檔案，以及設定資料庫表格和交易記錄的大小上限與自動成長選項。

## <a name="syntax"></a>語法

```syntaxsql
CREATE DATABASE database_name
WITH (
    [ AUTOGROW = ON | OFF , ]
    REPLICATED_SIZE = replicated_size [ GB ] ,
    DISTRIBUTED_SIZE = distributed_size [ GB ] ,
    LOG_SIZE = log_size [ GB ] )
[;]
```

## <a name="arguments"></a>引數

*database_name* 新資料庫的名稱。 如需所允許資料庫名稱的詳細資訊，請參閱 [!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)] 中的「物件命名規則」和「保留的資料庫名稱」。

AUTOGROW = ON | **OFF** 指定此資料庫的 *replicated_size*、*distributed_size* 和 *log_size* 參數是否將會視需要自動成長至超出其指定大小。 預設值是 **OFF**。

如果 AUTOGROW 設為 ON，在每次插入資料、更新資料或進行其他動作而導致所需儲存空間大於已配置大小時，*replicated_size*、*distributed_size* 和 *log_size* 就會視需要成長 (不在初始指定大小的區塊中)。

如果 AUTOGROW 設為 OFF，大小就不會自動成長。 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 在嘗試進行需要 *replicated_size*、*distributed_size* 或 *log_size* 成長至超出其指定值的動作時，將會傳回錯誤。

只能針對所有大小將 AUTOGROW 設為 ON 或 OFF。 例如，如果為 *log_size* 將 AUTOGROW 設為 ON，就不得不為 *replicated_size* 將 AUTOGROW 設為 ON。

*replicated_size* [ GB ] 一個正數。 設定配置給「每個計算節點上」複寫資料表和相對應資料的總空間大小 (以整數或小數 GB 為單位)。 對於最低和最高 *replicated_size* 需求，請參閱 [!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)] 中的「最小值與最大值」。

如果 AUTOGROW 設為 ON，將允許複寫資料表成長至超出此限制。

當 AUTOGROW 設為 OFF 時，如果使用者嘗試建立新的複寫資料表、在現有的複寫資料表中插入資料，或更新現有的複寫資料表而導致大小增加至超出 *replicated_size*，便會傳回錯誤。

*distributed_size* [ GB ] 一個正數。 配置給「跨設備」分散式資料表 (和相對應資料) 的總空間大小 (以整數或小數 GB 為單位)。 對於最低和最高 *distributed_size* 需求，請參閱 [!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)] 中的「最小值與最大值」。

如果 AUTOGROW 設為 ON，將允許分散式資料表成長至超出此限制。

當 AUTOGROW 設為 OFF 時，如果使用者嘗試建立新的分散式資料表、在現有的分散式資料表中插入資料，或更新現有的分散式資料表而導致大小增加至超出 *distributed_size*，便會傳回錯誤。

*log_size* [ GB ] 一個正數。 *跨設備* 交易記錄的大小 (以整數或十進位 GB 為單位)。

對於最低和最高 *log_size* 需求，請參閱 [!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)] 中的「最小值與最大值」。

如果 AUTOGROW 設為 ON，會允許記錄檔成長至超過此限制。 使用 [DBCC SHRINKLOG (Azure Synapse Analytics)](../../t-sql/database-console-commands/dbcc-shrinklog-azure-sql-data-warehouse.md) 陳述式，將記錄檔大小縮小至其原始大小。

當 AUTOGROW 設為 OFF 時，如果出現會導致個別計算節點的記錄檔大小增加至超出 *log_size* 的任何動作，將會向使用者傳回錯誤。

## <a name="permissions"></a>權限

需要 master 資料庫中的 `CREATE ANY DATABASE` 權限，或 **系統管理員 (sysadmin)** 固定伺服器角色中的成員資格。

下列範例會將建立資料庫的權限提供給資料庫使用者 Fay。

```sql
USE master;
GO
GRANT CREATE ANY DATABASE TO [Fay];
GO
```

## <a name="general-remarks"></a>一般備註

建立資料庫時會使用資料庫相容性層級 120 建立，這是 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 的相容性層級。 這可確保資料庫將能使用 PDW 所使用的所有 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 功能。

## <a name="limitations-and-restrictions"></a>限制事項

在明確的交易中，並不允許使用 CREATE DATABASE 陳述式。 如需詳細資訊，請參閱[陳述式](../../t-sql/statements/statements.md)。

如需資料庫最小和最大條件約束的資訊，請參閱 [!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)] 中的「最小值和最大值」。

建立資料庫時，「每個計算節點上」都必須有足夠的可用空間，以配置下列大小的總和：

- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫的資料表大小為 *replicated_table_size*。
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫的資料表大小為 (*distributed_table_size* / 計算節點數目)。
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會記錄 (*log_size* / 計算節點數目) 的大小。

## <a name="locking"></a>鎖定

在 DATABASE 物件上採取共用鎖定。

## <a name="metadata"></a>中繼資料

在這項作業成功之後，[sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 和 [sys.objects](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md) 中繼資料檢視中將會顯示此資料庫的項目。

## <a name="examples-sspdw"></a>範例：[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

### <a name="a-basic-database-creation-examples"></a>A. 基本資料庫建立範例

以下範例會建立資料庫 `mytest`，其儲存區配置為每一計算節點具備 100 GB 以用於複寫資料表、每一設備 500 GB 以用於分散式資料表，以及每一設備 100 GB 以用於交易記錄。 在這個範例中，AUTOGROW 預設為關閉。

```sql
CREATE DATABASE mytest
  WITH
    (REPLICATED_SIZE = 100 GB,
    DISTRIBUTED_SIZE = 500 GB,
    LOG_SIZE = 100 GB );
```

以下範例會建立與上述參數相同的資料庫 `mytest`，但 AUTOGROW 已開啟。 這可以讓資料庫成長至超出指定的大小參數。

```sql
CREATE DATABASE mytest
  WITH
    (AUTOGROW = ON,
    REPLICATED_SIZE = 100 GB,
    DISTRIBUTED_SIZE = 500 GB,
    LOG_SIZE = 100 GB);
```

### <a name="b-creating-a-database-with-partial-gigabyte-sizes"></a>B. 建立具備部分 GB 大小的資料庫

以下範例會建立 AUTOGROW 設為關閉的資料庫 `mytest`，其儲存區配置為每一計算節點具備 1.5 GB 以用於複寫資料表、每一設備 5.25 GB 以用於分散式資料表，以及每一設備 10 GB 以用於交易記錄，。

```sql
CREATE DATABASE mytest
  WITH
    (REPLICATED_SIZE = 1.5 GB,
    DISTRIBUTED_SIZE = 5.25 GB,
    LOG_SIZE = 10 GB);
```

## <a name="see-also"></a>另請參閱

- [ALTER DATABASE - Analytics Platform System](../../t-sql/statements/alter-database-transact-sql.md?view=aps-pdw-2016-au7&preserve-view=true)
- [DROP DATABASE](../../t-sql/statements/drop-database-transact-sql.md)

::: moniker-end