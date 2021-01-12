---
description: CREATE EXTERNAL LIBRARY (Transact-SQL) - SQL Server
title: CREATE EXTERNAL LIBRARY (Transact-SQL) - SQL Server | Microsoft Docs
ms.custom: ''
ms.date: 08/26/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: machine-learning
ms.topic: language-reference
f1_keywords:
- CREATE EXTERNAL LIBRARY
- CREATE_EXTERNAL_LIBRARY_TSQL
- EXTERNAL LIBRARY
- EXTERNAL_LIBRARY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE EXTERNAL LIBRARY
author: dphansen
ms.author: davidph
manager: cgronlund
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 9839a6fa8bd678afdce426f9dcd5657500c5a6cd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100781"
---
# <a name="create-external-library-transact-sql"></a>CREATE EXTERNAL LIBRARY (Transact-SQL)  
[!INCLUDE [SQL Server 2017 SQL MI](../../includes/applies-to-version/sqlserver2017-asdbmi.md)]

::: moniker range=">=sql-server-2017||>=sql-server-linux-ver15"
將 R、Python 或 Java 套件檔案從指定的位元組資料流或檔案路徑上傳到資料庫。 此陳述式可作為一般機制，供資料庫管理員針對 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] 支援的任何新外部語言執行階段和 OS 平台，上傳所需的成品。 

> [!NOTE]
> 在 SQL Server 2017 中，支援 R 語言和 Windows 平台。 SQL Server 2019 和更新版本支援 Windows 和 Linux 平台上的 R、Python 和外部語言。
::: moniker-end

::: moniker range="=azuresqldb-mi-current"
將 R 或 Python 套件檔案從指定的位元組資料流或檔案路徑上傳到資料庫。 此陳述式可以作為資料庫管理員上傳所需成品的一般機制。 

> [!NOTE]
> 在 Azure SQL 受控執行個體中，您可使用 **sqlmlutils** 來安裝程式庫。 如需詳細資料，請參閱[使用 sqlmlutils 安裝 Python 套件](../../machine-learning/package-management/install-additional-python-packages-on-sql-server.md?context=%252fazure%252fazure-sql%252fmanaged-instance%252fcontext%252fml-context&view=azuresqldb-mi-current&preserve-view=true)和[使用 sqlmlutils 安裝新的 R 套件](../../machine-learning/package-management/install-additional-r-packages-on-sql-server.md?context=%252fazure%252fazure-sql%252fmanaged-instance%252fcontext%252fml-context&view=azuresqldb-mi-current&preserve-view=true)。
::: moniker-end

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
## <a name="syntax-for-sql-server-2019"></a>SQL Server 2019 的語法

```syntaxsql
CREATE EXTERNAL LIBRARY library_name  
[ AUTHORIZATION owner_name ]  
FROM <file_spec> [ ,...2 ]  
WITH ( LANGUAGE = <language> )  
[ ; ]  

<file_spec> ::=  
{  
    (CONTENT = { <client_library_specifier> | <library_bits> }  
    [, PLATFORM = <platform> ])  
}  

<client_library_specifier> :: = 
{
    '[file_path\]manifest_file_name'  
} 

<library_bits> :: =  
{ 
      varbinary_literal 
    | varbinary_expression 
}

<platform> :: = 
{
      WINDOWS
    | LINUX
}

<language> :: = 
{
      'R'
    | 'Python'
    | <external_language>
}

```
::: moniker-end
::: moniker range=">=sql-server-2017 <=sql-server-2017"
## <a name="syntax-for-sql-server-2017"></a>SQL Server 2017 的語法

```syntaxsql
CREATE EXTERNAL LIBRARY library_name  
[ AUTHORIZATION owner_name ]  
FROM <file_spec> [ ,...2 ]  
WITH ( LANGUAGE = 'R' )  
[ ; ]  

<file_spec> ::=  
{  
    (CONTENT = { <client_library_specifier> | <library_bits> }  
    [, PLATFORM = WINDOWS ])  
}  

<client_library_specifier> :: = 
{
    '[file_path\]manifest_file_name'
} 

<library_bits> :: =  
{ 
      varbinary_literal 
    | varbinary_expression 
}
```
::: moniker-end

::: moniker range="=azuresqldb-mi-current"
## <a name="syntax-for-azure-sql-managed-instance"></a>Azure SQL 受控執行個體的語法

```syntaxsql
CREATE EXTERNAL LIBRARY library_name  
[ AUTHORIZATION owner_name ]  
FROM <file_spec> [ ,...2 ]  
WITH ( LANGUAGE = <language> )  
[ ; ]  

<file_spec> ::=  
{  
    (CONTENT = <library_bits>)  
}  

<library_bits> :: =  
{ 
      varbinary_literal 
    | varbinary_expression 
}

<language> :: = 
{
      'R'
    | 'Python'
}
```
::: moniker-end

### <a name="arguments"></a>引數

**library_name**

上傳至執行個體的程式庫可以是公用或私用程式庫。 如果程式庫是由 `dbo` 的成員所建立，該程式庫就是公用程式庫，而可以與所有使用者共用。 否則，程式庫就只是該使用者的私用程式庫。

程式庫名稱在特定使用者或擁有者的內容中必須是唯一的。 例如，兩個使用者 **RUser1** 和 **RUser2** 都可以各自且分別上傳 R 程式庫 `ggplot2`。 不過，如果 **RUser1** 想要上傳 `ggplot2` 的新版本，則第二個執行個體必須有不同名稱，否則會取代現有的程式庫。

不可任意指派程式庫名稱；程式庫名稱應該與在外部指令碼載入程式庫時所需的名稱相同。

**owner_name**

指定擁有外部程式庫的使用者或角色名稱。 若未指定，擁有權便歸目前使用者。

資料庫擁有者所擁有的程式庫會被視為資料庫和執行階段的全域程式庫。 換句話說，資料庫擁有者所建立的程式庫可以包含許多使用者所共用的一組通用程式庫或套件。 當外部程式庫是由 `dbo` 使用者以外的使用者所建立時，該外部程式庫僅是該使用者的私用程式庫。

當使用者 **RUser1** 執行外部指令碼時，`libPath` 的值可能包含多個路徑。 第一個路徑一律是資料庫擁有者所建立之共用程式庫的路徑。 `libPath` 的第二個部分會指定包含 **RUser1** 個別上傳之套件的路徑。

::: moniker range=">=sql-server-2017||>=sql-server-linux-ver15"
**file_spec**

指定適用於特定平台的套件內容。 只支援每個平台一個檔案成品。

指定檔案時，可以採用本機路徑或網路路徑的形式來指定。

當您試圖存取在 **<client_library_specifier>** 中所指定的檔案時，SQL Server 會模擬目前 Windows 登入的安全性內容。 由於委派限制之故，如果 **<client_library_specifier>** 指定網路位置 (UNC 路徑)，目前登入的模擬並不會在網路位置發揮作用。 此時就得利用 SQL Server 服務帳戶的資訊安全內容進行存取。 如需詳細資訊，請參閱[認證 (資料引擎)](../../relational-databases/security/authentication-access/credentials-database-engine.md)。

(選擇性) 可以指定檔案的 OS 平台。 針對特定語言或執行階段，每個 OS 平台只允許一個檔案成品或內容。
::: moniker-end

**library_bits**

以十六進位常值的形式指定套件的內容，類似於組件。 

如果您必須建立程式庫或更改現有的程式庫 (並且具備執行此操作所需的權限)，但伺服器上的檔案系統受到限制，而您無法將程式庫檔案複製到伺服器能夠存取的位置，此選項會相當有用。

::: moniker range="=sql-server-2017"
**PLATFORM = WINDOWS**

指定程式庫內容的平台。 此值會預設為 SQL Server 執行所在的主機平台。 因此，使用者不需要指定此值。 當支援多個平台或使用者必須指定不同的平台時，才需要指定此值。
在 SQL Server 2017 中，Windows 是唯一支援的平台。
::: moniker-end

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
**平台**

指定程式庫內容的平台。 此值會預設為 SQL Server 執行所在的主機平台。 因此，使用者不需要指定此值。 當支援多個平台或使用者必須指定不同的平台時，才需要指定此值。
在 SQL Server 2019 中，支援 Windows 和 Linux 平台。
::: moniker-end

::: moniker range="=sql-server-2017"
**LANGUAGE = 'R'**

指定套件的語言。 SQL Server 2017 支援 R。
::: moniker-end

::: moniker range="=azuresqldb-mi-current"
**language**

指定套件的語言。 在 Azure SQL 受控執行個體中，這個值可以是 `R` 或 `Python`。
::: moniker-end

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
**language**

指定套件的語言。 此值可以是 `R`、`Python` 或外部語言的名稱 (請參閱[建立外部語言](create-external-language-transact-sql.md))。
::: moniker-end

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註

::: moniker range=">=sql-server-2017 <=sql-server-2017"
針對 R 語言，當使用檔案時，必須針對 Windows，以具有 .ZIP 副檔名的 ZIP 壓縮封存檔案形式備妥套件。 
在 SQL Server 2017 中僅支援 Windows 平台。
::: moniker-end

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
針對 R 語言，在使用檔案時，必須以具有 .ZIP 副檔名的 ZIP 壓縮封存檔案形式備妥套件。  

針對 Python 語言，.whl 或 .zip 檔案套件必須以壓縮封存檔案的型式準備。 若套件已經是 .zip 檔案，它必須包含在新的 .zip 檔案中。 目前不支援直接上傳 .whl 或 .zip 檔案的套件。
::: moniker-end

`CREATE EXTERNAL LIBRARY` 陳述式會將程式庫位元上傳至資料庫。 當使用者使用 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)來執行外部指令碼並呼叫套件或程式庫時，就會安裝程式庫。

上傳至執行個體的程式庫可以是公用或私用程式庫。 如果程式庫是由 `dbo` 的成員所建立，該程式庫就是公用程式庫，而可以與所有使用者共用。 否則，程式庫就只是該使用者的私用程式庫。

SQL 執行個體中已預先安裝一些套件 (稱為「系統套件」)。 使用者無法新增、更新或移除系統套件。

## <a name="permissions"></a>權限

需要 `CREATE EXTERNAL LIBRARY` 權限。 根據預設，任何具有 **dbo** 或為 **db_owner** 角色成員的使用者，都有建立外部程式庫的權限。 對於其他所有使用者，您必須使用 [GRANT](./grant-database-permissions-transact-sql.md) 陳述式指定 CREATE EXTERNAL LIBRARY 做為權限，以明確授予權限給他們。

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
在 SQL Server 2019 中，除了 'CREATE EXTERNAL LIBRARY' 權限外，使用者也需要有外部語言的參考權限才能建立該外部語言的外部程式庫。

```sql
GRANT REFERENCES ON EXTERNAL LANGUAGE::Java to user
GRANT CREATE EXTERNAL LIBRARY to user
```

::: moniker-end

若要修改任何程式庫，則需要不同的權限，`ALTER ANY EXTERNAL LIBRARY`。

若要使用檔案路徑建立外部程式庫，使用者必須是 Windows 驗證的登入，或是 sysadmin 固定伺服器角色的成員。

## <a name="examples"></a>範例

::: moniker range=">=sql-server-2017||>=sql-server-linux-ver15"
### <a name="add-an-external-library-to-a-database"></a>將外部程式庫新增至資料庫  

下列範例會將名為 `customPackage` 的外部程式庫新增至資料庫。

```sql
CREATE EXTERNAL LIBRARY customPackage
FROM (CONTENT = 'C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\customPackage.zip') WITH (LANGUAGE = 'R');
```

將程式庫成功上傳至執行個體之後，使用者需執行 `sp_execute_external_script` 程序來安裝該程式庫。

```sql
EXEC sp_execute_external_script 
@language =N'R', 
@script=N'library(customPackage)'
```
::: moniker-end

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
針對 SQL Server 2019 中的 Python 語言，只需要將範例中的 `'R'` 替換成 `'Python'` 即可正常運作。
::: moniker-end

### <a name="installing-packages-with-dependencies"></a>安裝具有相依性的套件

如果您想要安裝的套件具有許多相依性，請務必在嘗試安裝目標套件「之前」，先分析第一層和第二層的相依性，並確定所有必要的套件都可供使用。

例如，假設您想要安裝新套件 `packageA`：

+ `packageA` 具有 `packageB` 相依性
+ `packageB` 具有 `packageC` 相依性

若要成功安裝 `packageA`，您必須在將 `packageA` 新增至 SQL Server 時，為 `packageB` 和 `packageC` 建立程式庫。 請務必一併檢查所需的套件版本。

實際上，常用套件的套件相依性通常比這個簡單範例複雜許多。 例如，**ggplot2** 可能需要超過 30 個套件，而這些套件可能需要伺服器上所未提供的額外套件。 任何套件遺失或套件版本錯誤都可能造成安裝失敗。

由於只從查看套件資訊清單很難判斷所有相依性，因此建議您使用 [miniCRAN](https://cran.r-project.org/web/packages/miniCRAN/index.html) 之類的套件，以識別成功完成安裝可能需要的所有套件。

::: moniker range=">=sql-server-2017||>=sql-server-linux-ver15"

+ 上傳目標套件及其相依性。 所有檔案都必須位於伺服器能夠存取的一個資料夾中。

    ```sql
    CREATE EXTERNAL LIBRARY packageA 
    FROM (CONTENT = 'C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\packageA.zip') 
    WITH (LANGUAGE = 'R'); 
    GO

    CREATE EXTERNAL LIBRARY packageB FROM (CONTENT = 'C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\packageB.zip') 
    WITH (LANGUAGE = 'R');
    GO

    CREATE EXTERNAL LIBRARY packageC FROM (CONTENT = 'C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\packageC.zip') 
    WITH (LANGUAGE = 'R');
    GO
    ```

+ 先安裝必要套件。

    如果已經將必要套件上傳至執行個體，就無須再次新增。 只要確認現有套件的版本是否正確即可。 
    
    第一次執行 `sp_execute_external_script` 來安裝套件 `packageA` 時，會依照正確順序安裝必要套件 `packageC` 和 `packageB`。

    不過，如果有任何必要套件無法供使用，安裝目標套件 `packageA` 時就會失敗。

    ```sql
    EXEC sp_execute_external_script 
    @language =N'R', 
    @script=N'
    # load the desired package packageA
    library(packageA)
    '
    ```
::: moniker-end

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
針對 SQL Server 2019 中的 Python 語言，只需要將範例中的 `'R'` 替換成 `'Python'` 即可正常運作。
::: moniker-end

### <a name="create-a-library-from-a-byte-stream"></a>從位元組資料流建立程式庫

如果您無法將套件檔案儲存在伺服器上的位置，則可以在變數中傳遞套件內容。 下列範例會將位元作為十六進位常值傳遞以建立程式庫。

```SQL
CREATE EXTERNAL LIBRARY customLibrary FROM (CONTENT = 0xABC123...) WITH (LANGUAGE = 'R');
```

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
針對 SQL Server 2019 中的 Python 語言，只需要將範例中的 **'R'** 替換成 **'Python'** 即可正常運作。
::: moniker-end

> [!NOTE]
> 此程式碼範例僅示範語法；`CONTENT =` 中的二進位值已被截斷以提高可讀性，且並不會建立可運作的程式庫。 二進位變數的實際內容會更長。

### <a name="change-an-existing-package-library"></a>變更現有的套件程式庫

`ALTER EXTERNAL LIBRARY` DDL 陳述式可用來新增程式庫內容，或修改現有的程式庫內容。 若要修改現有的程式庫，需要 `ALTER ANY EXTERNAL LIBRARY` 權限。

如需詳細資訊，請參閱 [ALTER EXTERNAL LIBRARY](alter-external-library-transact-sql.md)。

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
### <a name="add-a-java-jar-file-to-a-database"></a>將 Java.jar 檔案新增至資料庫  

下列範例會將稱為 `customJar` 的外部 Jar 檔案新增至資料庫。

```sql
CREATE EXTERNAL LIBRARY customJar
FROM (CONTENT = 'C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\customJar.jar') 
WITH (LANGUAGE = 'Java');
```

將程式庫成功上傳至執行個體之後，使用者需執行 `sp_execute_external_script` 程序來安裝該程式庫。

```sql
EXEC sp_execute_external_script
    @language = N'Java'
    , @script = N'customJar.MyCLass.myMethod'
    , @input_data_1 = N'SELECT * FROM dbo.MyTable'
WITH RESULT SETS ((column1 int))
```

### <a name="add-an-external-package-for-both-windows-and-linux"></a>新增適用於 Windows 和 Linux 的外部套件

您最多可以指定兩個 `<file_spec>`，一個用於 Windows，一個用於 Linux。

```sql
CREATE EXTERNAL LIBRARY lazyeval 
FROM (CONTENT = 'C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\packageA.zip', PLATFORM = WINDOWS),
(CONTENT = 'C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\packageA.tar.gz', PLATFORM = LINUX)
WITH (LANGUAGE = 'R')
```

當您使用 `sp_execute_external_script` 安裝套件時，將會根據 SQL Server 執行個體執行所在的平台，使用該平台的程式庫內容。

```sql
EXECUTE sp_execute_external_script 
    @LANGUAGE = N'R',
    @SCRIPT = N'
library(packageA)
```
::: moniker-end

## <a name="see-also"></a>另請參閱

[ALTER EXTERNAL LIBRARY (Transact-SQL)](alter-external-library-transact-sql.md)  
[DROP EXTERNAL LIBRARY (Transact-SQL)](drop-external-library-transact-sql.md)  
[sys.external_library_files](../../relational-databases/system-catalog-views/sys-external-library-files-transact-sql.md)  
[sys.external_libraries](../../relational-databases/system-catalog-views/sys-external-libraries-transact-sql.md)