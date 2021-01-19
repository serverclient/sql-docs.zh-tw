---
description: CREATE FUNCTION (Transact-SQL)
title: CREATE FUNCTION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FUNCTION
- CREATE FUNCTION
- CREATE_FUNCTION_TSQL
- FUNCTION_TSQL
- TVF
- MSTVF
dev_langs:
- TSQL
helpviewer_keywords:
- invoking functions
- extended stored procedures [SQL Server], functions
- table-valued parameters
- user-defined functions [SQL Server], creating
- CLR functions [SQL Server]
- CREATE FUNCTION statement
- nondeterministic functions
- user-defined data types
- functions [SQL Server], creating
- inline table-valued functions [SQL Server]
- multi-statement table-valued functions [SQL Server]
- table-valued functions [SQL Server], CREATE FUNCTION
- parameters [SQL Server], functions
- nesting user-defined functions
- deterministic functions
- scalar-valued functions
- scalar UDF
- MSTVF
- TVF
- functions [SQL Server], invoking
ms.assetid: 864b393f-225f-4895-8c8d-4db59ea60032
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 717ffe936bef35c9794e160e56c7ea7b0658058f
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170700"
---
# <a name="create-function-transact-sql"></a>CREATE FUNCTION (Transact-SQL)

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中建立使用者定義函數。 使用者定義函數是一種 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或 Common Language Runtime (CLR) 常式，它會接受參數、執行動作 (例如複雜計算) 並且將該動作的結果傳回成值。 傳回值可以是純量 (單一) 值或資料表。 您可以使用這個陳述式來建立可用下列方式使用的可重複使用常式：

- 在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式中，例如 SELECT

- 在呼叫函數的應用程式中

- 在另一個使用者自訂函數的定義中

- 若要將檢視參數化，或改善索引檢視的功能

- 若要在資料表中定義資料行

- 若要在資料行上定義 CHECK 條件約束

- 取代預存程序

- 使用內嵌函式作為安全性原則的篩選述詞

> [!NOTE]
>
> - 本主題將討論如何將 .NET Framework CLR 整合至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 CLR 整合不適用於 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。
> - 針對 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]，請參閱 [CREATE FUNCTION (Azure Synapse Analytics)](../../t-sql/statements/create-function-sql-data-warehouse.md)。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
-- Transact-SQL Scalar Function Syntax
CREATE [ OR ALTER ] FUNCTION [ schema_name. ] function_name
( [ { @parameter_name [ AS ][ type_schema_name. ] parameter_data_type
 [ = default ] [ READONLY ] }
    [ ,...n ]
  ]
)
RETURNS return_data_type
    [ WITH <function_option> [ ,...n ] ]
    [ AS ]
    BEGIN
        function_body
        RETURN scalar_expression
    END
[ ; ]
```

```syntaxsql
-- Transact-SQL Inline Table-Valued Function Syntax
CREATE [ OR ALTER ] FUNCTION [ schema_name. ] function_name
( [ { @parameter_name [ AS ] [ type_schema_name. ] parameter_data_type
    [ = default ] [ READONLY ] }
    [ ,...n ]
  ]
)
RETURNS TABLE
    [ WITH <function_option> [ ,...n ] ]
    [ AS ]
    RETURN [ ( ] select_stmt [ ) ]
[ ; ]
```

```syntaxsql
-- Transact-SQL Multi-Statement Table-Valued Function Syntax
CREATE [ OR ALTER ] FUNCTION [ schema_name. ] function_name
( [ { @parameter_name [ AS ] [ type_schema_name. ] parameter_data_type
    [ = default ] [READONLY] }
    [ ,...n ]
  ]
)
RETURNS @return_variable TABLE <table_type_definition>
    [ WITH <function_option> [ ,...n ] ]
    [ AS ]
    BEGIN
        function_body
        RETURN
    END
[ ; ]

```

```syntaxsql
-- Transact-SQL Function Clauses
<function_option>::=
{
    [ ENCRYPTION ]
  | [ SCHEMABINDING ]
  | [ RETURNS NULL ON NULL INPUT | CALLED ON NULL INPUT ]
  | [ EXECUTE_AS_Clause ]
  | [ INLINE = { ON | OFF }]
}

<table_type_definition>:: =
( { <column_definition> <column_constraint>
  | <computed_column_definition> }
    [ <table_constraint> ] [ ,...n ]
)
<column_definition>::=
{
    { column_name data_type }
    [ [ DEFAULT constant_expression ]
      [ COLLATE collation_name ] | [ ROWGUIDCOL ]
    ]
    | [ IDENTITY [ (seed , increment ) ] ]
    [ <column_constraint> [ ...n ] ]
}

<column_constraint>::=
{
    [ NULL | NOT NULL ]
    { PRIMARY KEY | UNIQUE }
      [ CLUSTERED | NONCLUSTERED ]
      [ WITH FILLFACTOR = fillfactor
        | WITH ( < index_option > [ , ...n ] )
      [ ON { filegroup | "default" } ]
  | [ CHECK ( logical_expression ) ] [ ,...n ]
}

<computed_column_definition>::=
column_name AS computed_column_expression

<table_constraint>::=
{
    { PRIMARY KEY | UNIQUE }
      [ CLUSTERED | NONCLUSTERED ]
      ( column_name [ ASC | DESC ] [ ,...n ]
        [ WITH FILLFACTOR = fillfactor
        | WITH ( <index_option> [ , ...n ] )
  | [ CHECK ( logical_expression ) ] [ ,...n ]
}

<index_option>::=
{
    PAD_INDEX = { ON | OFF }
  | FILLFACTOR = fillfactor
  | IGNORE_DUP_KEY = { ON | OFF }
  | STATISTICS_NORECOMPUTE = { ON | OFF }
  | ALLOW_ROW_LOCKS = { ON | OFF }
  | ALLOW_PAGE_LOCKS ={ ON | OFF }
}
```

```syntaxsql
-- CLR Scalar Function Syntax
CREATE [ OR ALTER ] FUNCTION [ schema_name. ] function_name
( { @parameter_name [AS] [ type_schema_name. ] parameter_data_type
    [ = default ] }
    [ ,...n ]
)
RETURNS { return_data_type }
    [ WITH <clr_function_option> [ ,...n ] ]
    [ AS ] EXTERNAL NAME <method_specifier>
[ ; ]
```

```syntaxsql
-- CLR Table-Valued Function Syntax
CREATE [ OR ALTER ] FUNCTION [ schema_name. ] function_name
( { @parameter_name [AS] [ type_schema_name. ] parameter_data_type
    [ = default ] }
    [ ,...n ]
)
RETURNS TABLE <clr_table_type_definition>
    [ WITH <clr_function_option> [ ,...n ] ]
    [ ORDER ( <order_clause> ) ]
    [ AS ] EXTERNAL NAME <method_specifier>
[ ; ]
```

```syntaxsql
-- CLR Function Clauses
<order_clause> ::=
{
   <column_name_in_clr_table_type_definition>
   [ ASC | DESC ]
} [ ,...n]

<method_specifier>::=
    assembly_name.class_name.method_name

<clr_function_option>::=
}
    [ RETURNS NULL ON NULL INPUT | CALLED ON NULL INPUT ]
  | [ EXECUTE_AS_Clause ]
}

<clr_table_type_definition>::=
( { column_name data_type } [ ,...n ] )
```

```syntaxsql
-- In-Memory OLTP: Syntax for natively compiled, scalar user-defined function
CREATE [ OR ALTER ] FUNCTION [ schema_name. ] function_name
 ( [ { @parameter_name [ AS ][ type_schema_name. ] parameter_data_type
    [ NULL | NOT NULL ] [ = default ] [ READONLY ] }
    [ ,...n ]
  ]
)
RETURNS return_data_type
     WITH <function_option> [ ,...n ]
    [ AS ]
    BEGIN ATOMIC WITH (set_option [ ,... n ])
        function_body
        RETURN scalar_expression
    END

<function_option>::=
{
  |  NATIVE_COMPILATION
  |  SCHEMABINDING
  | [ EXECUTE_AS_Clause ]
  | [ RETURNS NULL ON NULL INPUT | CALLED ON NULL INPUT ]
}

```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*OR ALTER*
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]

只有在函數已經存在時，才能有條件地更改它。

> [!NOTE]
> 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 CU1 開始有提供 CLR 的選擇性 [OR ALTER] 語法。

*schema_name*：這是使用者定義函式所屬的結構描述名稱。

*function_name*：這是使用者定義函式的名稱。 函式名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則，且在資料庫內及對其結構描述而言，必須是唯一的。

> [!NOTE]
> 即使沒有指定參數，函數名稱後面仍需要括號。

@*parameter_name*：這是使用者定義函式中的參數。 您可以宣告一個或多個參數。

函數最多可以有 2,100 個參數。 除非定義了參數的預設值，否則在執行函數時，使用者必須提供每個已宣告之參數的值。

使用 @ 記號當做第一個字元來指定參數名稱。 參數名稱必須符合識別碼的規則。 對函數而言，參數必須是本機參數；相同的參數名稱可以用在其他函數中。 參數只能取代常數，不能用來取代資料表名稱、資料行名稱或其他資料庫物件的名稱。

> [!NOTE]
> 在預存程序或使用者定義函數中傳遞參數，或在批次陳述式中宣告和設定變數時，不接受 ANSI_WARNINGS。 例如，若將變數定義為 **char(3)** ，然後將其設為大於三個字元的值，資料便會被截斷成定義的大小，且 `INSERT` 或 `UPDATE` 陳述式會執行成功。

[ *type_schema_name*. ] *parameter_data_type*：這是參數資料類型，對於其所屬的結構描述而言為選擇性。 就 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式而言，所有資料類型 (包括 CLR 使用者定義類型和使用者定義資料表類型) 都是允許的資料類型，但 **timestamp** 資料類型除外。 就 CLR 函式而言，所有資料類型 (包括 CLR 使用者定義類型) 都是允許的資料類型，但 **text**、**ntext**、**image**、使用者定義資料表類型及 **timestamp** 資料類型除外。 非純量類型 **cursor** 和 **table** 不能指定為 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或 CLR 函式中的參數資料類型。

如果未指定 *type_schema_name*，[!INCLUDE[ssDE](../../includes/ssde-md.md)]就會依照下列順序尋找 *scalar_parameter_data_type*：

- 內含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統資料類型名稱的結構描述。
- 目前資料庫中之目前使用者的預設結構描述。
- 目前資料庫中的 **dbo** 結構描述。

[ =*default* ]：這是參數的預設值。 如果已定義 *default* 值，則不需為該參數指定值，即可執行函式。

> [!NOTE]
> 您可以針對 CLR 函式指定預設參數值，但 **varchar(max)** 和 **varbinary(max)** 資料類型除外。

如果函數的參數有預設值，則必須在呼叫函數來擷取該預設值時指定關鍵字 DEFAULT。 這個行為與使用預存程序中具有預設值的參數不一樣，因為在預存程序中，省略參數也意味著使用預設值。 不過，使用 EXECUTE 陳述式來叫用純量函數時，不需要 DEFAULT 關鍵字。

READONLY：指示無法在函式的定義內更新或修改參數。 使用者定義資料表類型參數 (TVP) 需要 READONLY，且 READONLY 不能用於任何其他參數類型。

*return_data_type*：這是純量使用者定義函式的傳回值。 就 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式而言，所有資料類型 (包括 CLR 使用者定義型別) 都是允許的資料類型，但 **timestamp** 資料類型除外。 就 CLR 函式而言，所有資料類型 (包括 CLR 使用者定義類型) 都是允許的資料類型，但 **text** **ntext** **image** 及 **timestamp** 資料類型除外。 非純量類型 **cursor** 和 **table** 不能指定為 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或 CLR 函式中的傳回資料類型。

*function_body*：指定一系列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式 (同時使用這些陳述式不會造成任何副作用，例如修改資料表等) 定義函式的值。 *function_body* 僅用於純量函式和多重陳述式資料表值函式 (MSTVF) 中。

在純量函式中，*function_body* 是一系列的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，這些陳述式會一起評估為純量值。

在 MSTVF 中，*function_body* 是一系列的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，這些陳述式會填入 TABLE 傳回變數。

*scalar_expression*：指定純量函式傳回的純量值。

TABLE：指定資料表值函式 (TVF) 的傳回值是資料表。 只有常數和 @*local_variables* 才能傳遞給 TVF。

在內嵌 TVF 中，TABLE 傳回值是透過單一 SELECT 陳述式定義。 內嵌函數沒有相關聯的傳回變數。

<a name="mstvf"></a> 在 MSTVF 中，\@*return_variable* 是一個 TABLE 變數，可用來儲存及累積應作為函式值傳回的資料列。 您只能針對 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式指定 \@*return_variable*，不能針對 CLR 函式指定。

*select_stmt*：這是單一 SELECT 陳述式，可定義內嵌資料表值函式 (TVF) 的傳回值。

ORDER (\<order_clause>) 指定從資料表值函式傳回結果的順序。 如需詳細資訊，請參閱本主題稍後的[在 CLR 資料表值函式中使用排序次序](#using-sort-order-in-clr-table-valued-functions)。

EXTERNAL NAME \<method_specifier> *assembly_name*.*class_name*.*method_name*
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] SP1 及更新版本)

指定建立函式名稱時應該要參考的組件和方法。

- *assembly_name* - 必須符合 `name` 資料行中的值，此資料行屬於 `SELECT * FROM sys.assemblies;`。

  這是 `CREATE ASSEMBLY` 陳述式中所使用的名稱。

- *class_name* - 必須符合 `assembly_name` 資料行中的值，此資料行屬於 `SELECT * FROM sys.assembly_modules;`。

  值常包含內嵌的句號或點。 在這類情況下，TRANSACT-SQL 語法會要求以一組方括弧 [] 括住值，或以一組雙引號 "" 括住值。

- *method_name* - 必須符合 `method_name` 資料行中的值，此資料行屬於 `SELECT * FROM sys.assembly_modules;`。

  方法必須為靜態。

在典型的範例中，針對所有類型皆位於 MyFood 命名空間的 MyFood.DLL，`EXTERNAL NAME` 值可能是：`MyFood.[MyFood.MyClass].MyStaticMethod`

> [!NOTE]
>
> - 依預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不能執行 CLR 程式碼。 您可以建立、修改和卸除參考通用語言執行平台模組的資料庫物件；不過，必須等到您啟用 [clr enabled 選項](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md)之後，才能在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中執行這些參考。 若要啟用這個選項，請使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)。
> - 自主資料庫無法使用這個選項。

*\<*table_type_definition*>* ({\<column_definition> \<column_constraint>| \<computed_column_definition>} [\<table_constraint>] [,...*n*]) 定義 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式的資料表資料類型。 資料表宣告包括資料行定義和資料行或資料表條件約束。 資料表一律放在主要檔案群組中。

*\< clr_table_type_definition >* ({*column_name**data_type*} [,...*n*]) **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] SP1 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] ([某些區域為預覽版本](/azure/azure-sql/database/features-comparison?WT.mc_id=TSQL_GetItTag))。

定義 CLR 函數的資料表資料類型。 資料表宣告只包含資料行名稱和資料類型。 資料表一律放在主要檔案群組中。

NULL|NOT NULL：只支援針對原生編譯的純量使用者定義函式。 如需詳細資訊，請參閱[記憶體內部 OLTP 的純量使用者定義函數](../../relational-databases/in-memory-oltp/scalar-user-defined-functions-for-in-memory-oltp.md)。

NATIVE_COMPILATION：指出使用者定義函式是否為原生編譯函式。 此引數是原生編譯之純量使用者定義函式的必要引數。

BEGIN ATOMIC WITH：只支援針對原生編譯的純量使用者定義函式，且為必要項目。 如需詳細資訊，請參閱 [Atomic Blocks](../../relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures.md)。

SCHEMABINDING：SCHEMABINDING 是原生編譯的純量使用者定義函式必要引數。

EXECUTE AS：EXECUTE AS 是原生編譯的純量使用者定義函式必要項目。

**\<function_option>::= 與 \<clr_function_option>::=**

指定此函數將會有下列其中一個或多個選項。

ENCRYPTION **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] SP1 及更新版本)

指出 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會將 CREATE FUNCTION 陳述式的原始文字轉換為模糊化格式。 無法直接從任何目錄檢視中看見模糊化的輸出。 對系統資料表或資料庫檔案沒有存取權的使用者，無法擷取混亂格式的文字。 不過，可透過 [DAC 連接埠](../../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)存取系統資料表或直接存取資料庫檔案的具特殊權限使用者，則可使用該文字。 另外，可將偵錯工具附加至伺服器處理序的使用者，可以在執行階段從記憶體擷取原始程序。 如需如何存取系統中繼資料的詳細資訊，請參閱[中繼資料可見性組態](../../relational-databases/security/metadata-visibility-configuration.md)。

使用此選項可防止在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 複寫中發行這個函數。 無法為 CLR 函數指定此選項。

SCHEMABINDING：指定函式必須繫結至其所參考的資料庫物件。 當指定 SCHEMABINDING 時，無法依照會影響函數定義的方式來修改基底物件。 您必須先修改或卸除函數定義才能移除對於要修改之物件的相依性。

只有在發生以下其中一個動作時，才會移除函式與其所參考物件之間的繫結：

- 已卸除這個函數。
- 您可以使用未指定 `SCHEMABINDING` 選項的 `ALTER` 陳述式來修改函式。

只有當下列條件成立時，函數才可繫結結構描述：

- 函數是一個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函數。
- 函數參考的使用者定義函數和檢視表也繫結結構描述。
- 函數參考的物件是利用兩部分名稱來參考。
- 函數及其參考的物件屬於相同的資料庫。
- 執行 `CREATE FUNCTION` 陳述式的使用者在函式所參考資料庫物件上具備 `REFERENCES` 權限。

RETURNS NULL ON NULL INPUT | **CALLED ON NULL INPUT**：指定純量函式的 **OnNULLCall** 屬性。 若未指定，預設情況下意味著 CALLED ON NULL INPUT。 這表示，即使傳遞 NULL 做為引數，函數主體仍會執行。

如果在 CLR 函數中指定 RETURNS NULL ON NULL INPUT，它會指出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可以在它接收的任何引數是 NULL 時傳回 NULL，而不必實際叫用函數主體。 若 \<method_specifier> 中指定的 CLR 函式方法已經表示 RETURNS NULL ON NULL INPUT 的自訂屬性，但 CREATE FUNCTION 陳述式表示 CALLED ON NULL INPUT，則會優先使用 CREATE FUNCTION 陳述式。 無法為 CLR 資料表值函式指定 **OnNULLCall** 屬性。

EXECUTE AS 子句：指定執行使用者定義函式時所在的資訊安全內容。 因此，您可以控制 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 要利用哪個使用者帳戶來驗證在函數參考的任何資料庫物件上的權限。

> [!NOTE]
> 無法為內嵌資料表值函式指定 `EXECUTE AS`。

如需詳細資訊，請參閱 [EXECUTE AS 子句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)。

INLINE = { ON | OFF } **適用於**：SQL Server 2019 和更新版本。

指定是否應該內嵌此純量 UDF。 此子句僅適用於純量使用者定義函式。 `INLINE` 子句非為強制。 如果未指定 `INLINE` 子句，它會根據是否可以內嵌 UDF 自動設為 ON/OFF。 如果指定 `INLINE=ON`，但發現不可內嵌 UDF，則會擲回錯誤。 如需詳細資訊，請參閱[純量 UDF 內嵌](../../relational-databases/user-defined-functions/scalar-udf-inlining.md)。

**\< column_definition >::=**

定義資料表資料類型。 資料表宣告包括資料行定義和條件約束。 針對 CLR 函式，只能指定 *column_name* 和 *data_type*。

*column_name*：這是資料表中的資料行名稱。 資料行名稱必須符合識別碼規則，在資料表中也必須是唯一的。 *column_name* 可由 1 到 128 個字元組成。

*data_type*：指定資料行資料類型。 就 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式而言，允許所有資料類型 (包括 CLR 使用者定義型別)，但 **timestamp** 除外。 就 CLR 函式而言，允許所有資料類型 (包括 CLR 使用者定義型別)，但 **text**、**ntext**、**image**、**char**、**varchar**、**varchar(max)** 及 **timestamp** 除外。無法指定非純量類型 **cursor** 作為 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或 CLR 函式中的資料行資料類型。

DEFAULT *constant_expression*：指定在插入期間未明確提供值時，提供給資料行的值。 *constant_expression* 是常數、NULL 或系統函式值。 除了含有 IDENTITY 屬性的資料行之外，任何資料行都可以套用 DEFAULT 定義。 無法為 CLR 資料表值函式指定 DEFAULT。

COLLATE *collation_name* 指定資料行的定序。 若未指定，就會將資料庫的預設定序指派給資料行。 定序名稱可以是 Windows 定序名稱或 SQL 定序名稱。 如需定序的清單和相關詳細資訊，請參閱 [Windows 定序名稱 &#40;Transact-SQL&#41;](../../t-sql/statements/windows-collation-name-transact-sql.md) 和 [SQL Server 定序名稱 &#40;Transact-SQL&#41;](../../t-sql/statements/sql-server-collation-name-transact-sql.md)。

COLLATE 子句只能用來變更 **char**、**varchar**、**nchar** 與 **nvarchar** 資料類型之資料行的定序。

> [!NOTE]
> 無法為 CLR 資料表值函式指定 `COLLATE`。

ROWGUIDCOL：指出新資料行是一個資料列全域唯一識別碼資料行。 每個資料表只能有一個 **uniqueidentifier** 資料行指定為 ROWGUIDCOL 資料行。 ROWGUIDCOL 屬性只能指派給 **uniqueidentifier** 資料行。

ROWGUIDCOL 屬性不會強制執行資料行中所儲存之值的唯一性。 它也不會自動為插入資料表中的新資料列產生值。 若要為每個資料行產生唯一值，請在 INSERT 陳述式上使用 NEWID 函數。 可以指定預設值；不過，NEWID 不能指定為預設值。

IDENTITY 指出新資料行是識別欄位。 新資料列加入至資料表時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會提供資料行的唯一累加值。 識別欄位通常用來搭配 PRIMARY KEY 條件約束一起使用，當做資料表的唯一資料列識別碼。 可以將 IDENTITY 屬性指派給 **tinyint**、**smallint**、**int**、**bigint**、**decimal(p,0)** 或 **numeric(p,0)** 資料行。 每份資料表都只能建立一個識別欄位。 繫結的預設值和 DEFAULT 條件約束無法搭配識別欄位使用。 您必須同時指定 *seed* 和 *increment*，或兩者都不指定。 如果同時不指定這兩者，預設值便是 (1,1)。

無法為 CLR 資料表值函式指定 IDENTITY。

*seed*：這是要指派給資料表中第一個資料列的整數值。

*increment*：這是要新增至資料表中後續資料列 *seed* 值的整數值。

 **\< column_constraint >::= 與 \< table_constraint>::=**

定義指定之資料行或資料表的條件約束。 就 CLR 函數而言，唯一允許的條件約束類型是 NULL。 不允許具名條件約束。

NULL | NOT NULL：決定資料行中是否允許使用 Null 值。 嚴格來說，NULL 並不算是條件約束，但是您可以如同指定 NOT NULL 一樣加以指定。 無法為 CLR 資料表值函式指定 NOT NULL。

PRIMARY KEY：這是一項條件約束，其透過唯一索引來強制執行指定資料行的實體完整性。 在資料表值使用者定義函數中，PRIMARY KEY 條件約束只能建立在每份資料表的一個資料行上。 無法為 CLR 資料表值函式指定 PRIMARY KEY。

UNIQUE 這是一個條件約束，它透過唯一索引為一或多個指定資料行提供實體完整性。 一份資料表可以有多個 UNIQUE 條件約束。 無法為 CLR 資料表值函式指定 UNIQUE。

CLUSTERED | NONCLUSTERED 指出針對 PRIMARY KEY 或 UNIQUE 條件約束建立叢集或非叢集索引。 PRIMARY KEY 條件約束使用 CLUSTERED，UNIQUE 條件約束則使用 NONCLUSTERED。

只能為一個條件約束指定 CLUSTERED。 如果針對 UNIQUE 條件約束指定 CLUSTERED，且指定了 PRIMARY KEY 條件約束，則 PRIMARY KEY 會使用 NONCLUSTERED。

無法為 CLR 資料表值函式指定 CLUSTERED 和 NONCLUSTERED。

CHECK 這是一個條件約束，藉由限制可能輸入一或多個資料行的值來強制執行值域完整性。 無法為 CLR 資料表值函式指定 CHECK 條件約束。

*logical_expression* 這是一個傳回 TRUE 或 FALSE 的邏輯運算式。

**\<computed_column_definition>::=**

指定計算資料行。 如需有關計算資料行的詳細資訊，請參閱 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)。

*column_name*：這是計算資料行的名稱。

*computed_column_expression*：這是定義計算資料行值的運算式。

**\<index_option>::=**

指定 PRIMARY KEY 或 UNIQUE 索引的索引選項。 如需索引選項的詳細資訊，請參閱 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)。

PAD_INDEX = { ON | **OFF** }：指定索引填補。 預設值為 OFF。

FILLFACTOR = *fillfactor*：指定一個百分比來指出在建立或變更索引期間，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 應該使各索引頁分葉層級填滿的程度。 *fillfactor* 必須是 1 到 100 之間的整數值。 預設值是 0。

IGNORE_DUP_KEY = { ON | **OFF** } 指定當插入操作嘗試將重複索引鍵值插入唯一索引時所產生的錯誤回應。 IGNORE_DUP_KEY 選項只適用於在建立或重建索引之後所發生的插入作業。 預設值為 OFF。

STATISTICS_NORECOMPUTE = { ON | **OFF** }：指定是否要重新計算散發統計資料。 預設值為 OFF。

ALLOW_ROW_LOCKS = { **ON** | OFF }：指定是否允許資料列鎖定。 預設值是 ON。

ALLOW_PAGE_LOCKS = { **ON** | OFF }：指定是否允許頁面鎖定。 預設值是 ON。

## <a name="best-practices"></a>最佳做法

如果未以 `SCHEMABINDING` 子句建立使用者定義函式，叫用該函式時，對基礎物件所進行的變更可能會影響函式定義並產生非預期結果。 建議您實作下列其中一個方法，以確保函數不會因為其基礎物件的變更而變成過期：

- 當您要建立函式時，請指定 `WITH SCHEMABINDING` 子句。 這可以確保系統無法修改函數定義中參考的物件 (除非同時修改函數)。
- 在修改函式定義中指定的任何物件之後，執行 [sp_refreshsqlmodule](../../relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql.md) 預存程序。

> [!IMPORTANT]
> 如需內嵌資料表值函式 (內嵌 TVF) 和多重陳述式資料表值函式 (MSTVF) 的詳細資訊和效能考量事項，請參閱[建立使用者定義函式 &#40;資料庫引擎&#41;](../../relational-databases/user-defined-functions/create-user-defined-functions-database-engine.md)。

## <a name="data-types"></a>資料類型

如果在 CLR 函式中指定參數，這些參數應該是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 類型，如同先前針對 *scalar_parameter_data_type* 所下的定義。 如需有關比較 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統資料類型與 CLR 整合資料類型或 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 通用語言執行平台資料類型的詳細資訊，請參閱[對應 CLR 參數資料](../../relational-databases/clr-integration-database-objects-types-net-framework/mapping-clr-parameter-data.md)。

若要讓 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在正確方法於類別中出現多載時參考該正確方法，則 \<method_specifier> 中所指出的方法必須具有下列特性：

- 接收的參數數目與 [ ,...*n* ] 中所指定的數目相同。
- 依值 (而不是依參考) 接收所有參數。
- 使用與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 函數中指定之類型相容的參數類型。

若 CLR 函式的傳回資料類型指定資料表類型 (RETURNS TABLE)，則 \<method_specifier> 中方法的傳回資料類型應該屬於 **IEnumerator** 或 **IEnumerable** 類型，且會假設由函式建立者實作介面。 與 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式不同，CLR 函式無法在 \<table_type_definition> 中包含 PRIMARY KEY、UNIQUE 或 CHECK 條件約束。 \<table_type_definition> 中所指定資料行其資料類型必須符合 \<method_specifier> 方法設定在執行時所傳回結果對應資料行的類型。 這項類型檢查作業不會在建立函數時執行。

如需有關如何設計 CLR 函式的詳細資訊，請參閱 [CLR 使用者定義函式](../../relational-databases/clr-integration-database-objects-user-defined-functions/clr-user-defined-functions.md)。

## <a name="general-remarks"></a>一般備註

純量函式可在使用純量運算式的情況下叫用。 這包括計算資料行和 CHECK 條件約束定義。 您也可以使用 [EXECUTE](../../t-sql/language-elements/execute-transact-sql.md) 陳述式來執行純量函式。 叫用純量函式必須至少使用函式的兩部分名稱 ( *<schema>.<function>* )。 如需多部分名稱的詳細資訊，請參閱 [Transact-SQL 語法慣例 (Transact-SQL)](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。 資料表值函式可在 `SELECT`、`INSERT`、`UPDATE` 或 `DELETE` 陳述式的 `FROM` 子句中允許資料表運算式時叫用。 如需詳細資訊，請參閱[執行使用者定義函式](../../relational-databases/user-defined-functions/execute-user-defined-functions.md)。

## <a name="interoperability"></a>互通性

以下是函數中的有效陳述式：

- 指派陳述式。
- 除 `TRY...CATCH` 陳述式之外的流程控制陳述式。
- 定義區域資料變數和區域資料指標的 `DECLARE` 陳述式。
- 包含選取清單的 `SELECT` 陳述式，其中含有將值指派給區域變數的運算式。
- 資料指標作業 - 參考函數中之已宣告、已開啟、已關閉及已取消配置的本機資料指標。 只允許使用 `INTO` 子句將值指派給區域變數的 `FETCH` 陳述式；不允許將資料傳回用戶端的 `FETCH` 陳述式。
- 修改區域資料表變數的 `INSERT`、`UPDATE` 和 `DELETE` 陳述式。
- 呼叫擴充預存程序的 `EXECUTE` 陳述式。

如需詳細資訊，請參閱[建立使用者定義函式 &#40;資料庫引擎&#41;](../../relational-databases/user-defined-functions/create-user-defined-functions-database-engine.md)。

### <a name="computed-column-interoperability"></a>計算資料行的互通性

函數具有下列屬性。 這些屬性的值會決定是否可以在可保存或索引的計算資料行中使用函數。

|屬性|描述|注意|
|--------------|-----------------|-----------|
|**IsDeterministic**|函數可分為具決定性或不具決定性。|具決定性函數中允許本機資料存取。 例如，每當利用一組特定輸入值來呼叫函數時都一律傳回相同結果且含有相同資料庫狀態的函數，就會被標示為具決定性。|
|**IsPrecise**|函數可分為精確或不精確。|不精確函數內含浮點作業之類的作業。|
|**IsSystemVerified**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可以驗證函數的有效位數和決定性屬性。||
|**SystemDataAccess**|函數會存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之本機執行個體中的系統資料 (系統目錄或虛擬系統資料表)。||
|**UserDataAccess**|函數會存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之本機執行個體中的使用者資料。|包含使用者定義資料表和暫存資料表，但不包含資料表變數。|

[!INCLUDE[tsql](../../includes/tsql-md.md)] 會自動判斷 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 函數的有效位數和決定性屬性。 使用者可以指定 CLR 函數的資料存取和決定性屬性。 如需詳細資訊，請參閱 [CLR 整合自訂屬性的概觀](../../relational-databases/clr-integration/database-objects/clr-integration-custom-attributes-for-clr-routines.md)。

若要顯示這些屬性目前的值，請使用 [OBJECTPROPERTYEX](../../t-sql/functions/objectpropertyex-transact-sql.md)。

> [!IMPORTANT]
> 建立函式時，必須使用具決定性的 `SCHEMABINDING` 來建立。

當使用者定義函數有下列屬性值時，可以在索引中使用可叫用使用者定義函數的計算資料行。

- **IsDeterministic** = true
- **IsSystemVerified** = true (除非保存計算資料行)
- **UserDataAccess** = false
- **SystemDataAccess** = false

如需詳細資訊，請參閱 [計算資料行的索引](../../relational-databases/indexes/indexes-on-computed-columns.md)。

### <a name="calling-extended-stored-procedures-from-functions"></a>從函數呼叫擴充預存程序

從函數內呼叫擴充預存程序時，擴充預存程序無法將結果集傳回用戶端。 任何將結果集傳回用戶端的 ODS API 都會傳回 FAIL。 擴充預存程序可能會連回到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體；不過，它不應該嘗試將相同的交易聯結為叫用擴充預存程序的函數。

與從批次或預存程序進行的引動過程相似，擴充預存程序會在執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 Windows 安全性帳戶所在的內容中執行。 當預存程序的擁有者將預存程序上的 EXECUTE 權限提供給使用者時，該擁有者應考量前述的情形。

## <a name="limitations-and-restrictions"></a>限制事項

使用者定義函數不能用來執行修改資料庫狀態的動作。

使用者定義函式不得包含具有資料表作為其目標的 `OUTPUT INTO` 子句。

下列 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 陳述式不能併入 [!INCLUDE[tsql](../../includes/tsql-md.md)] 使用者定義函數的定義中：

- `BEGIN DIALOG CONVERSATION`
- `END CONVERSATION`
- `GET CONVERSATION GROUP`
- `MOVE CONVERSATION`
- `RECEIVE`
- `SEND`

使用者定義函數可以具有巢狀結構；也就是說，某個使用者定義函數可以呼叫另一個使用者定義函數。 被呼叫的函數開始執行時，巢狀層級會遞增；被呼叫的函數完成執行時，巢狀層級會遞減。 使用者定義函數所建立的巢狀結構最多可以有 32 個層級。 超過巢狀層級上限會導致整個呼叫函數鏈結失敗。 依照 32 個層級巢狀限制，[!INCLUDE[tsql](../../includes/tsql-md.md)] 使用者定義函數之 Managed 程式碼的任何參考都算是一個層級。 從 Managed 程式碼內叫用的方法，不列入這項限制。

### <a name="using-sort-order-in-clr-table-valued-functions"></a>在 CLR 資料表值函式中使用排序次序

當您在 CLR 資料表值函式中使用 `ORDER` 子句時，請遵循以下指導方針：

- 您必須確保結果一定會以指定的順序來排序。 如果結果未以指定的順序來排序，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將會在執行查詢時產生錯誤訊息。
- 如果指定了 `ORDER` 子句，資料表值函式的輸出必須根據資料行的定序 (明確或隱含) 來排序。 例如，如果資料行定序為中文 (指定在資料表值函式的 DDL 中或是從資料庫定序取得)，傳回的結果必須根據中文排序規則來排序。
- 如果指定了 `ORDER` 子句，一律會在傳回結果時由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 來加以驗證，不論查詢處理器是否會使用它來執行進一步的最佳化。 只有在您知道 `ORDER` 子句對查詢處理器很有用時使用它。
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢處理器會在下列情況下自動利用 `ORDER` 子句：

  - `ORDER` 子句與索引相容的插入查詢。
  - 與 `ORDER` 子句相容的 `ORDER BY` 子句。
  - `GROUP BY` 與 `ORDER` 子句相容的彙總。
  - 相異資料行與 `ORDER` 子句相容的 `DISTINCT` 彙總。

除非同時在查詢中指定 `ORDER BY` 子句，否則 `ORDER` 子句並不保證在執行 SELECT 查詢時會傳回排序的結果。 如需有關如何查詢資料表值函式之排序次序中所含資料行的資訊，請參閱 [sys.function_order_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-function-order-columns-transact-sql.md)。

## <a name="metadata"></a>中繼資料

下表列出您可以用來傳回使用者定義函數之中繼資料的系統目錄檢視表。

|系統檢視表|描述|
|-----------------|-----------------|
|[sys.sql_modules](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)|請參閱下方＜範例＞一節中的範例 E。|
|[sys.assembly_modules](../../relational-databases/system-catalog-views/sys-assembly-modules-transact-sql.md)|顯示 CLR 使用者定義函數的相關資訊。|
|[sys.parameters](../../relational-databases/system-catalog-views/sys-parameters-transact-sql.md)|顯示使用者定義函數中定義之參數的相關資訊。|
|[sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md)|顯示函數所參考的基礎物件。|

## <a name="permissions"></a>權限

需要資料庫中的 `CREATE FUNCTION` 權限，以及建立此函式所在結構描述上的 `ALTER` 權限。 如果此函式指定使用者定義型別，則需要該型別的 `EXECUTE` 權限。

## <a name="examples"></a>範例

> [!NOTE]
> 如需 UDF 的更多範例和效能考量事項，請參閱[建立使用者定義函式 &#40;資料庫引擎&#41;](../../relational-databases/user-defined-functions/create-user-defined-functions-database-engine.md)。

### <a name="a-using-a-scalar-valued-user-defined-function-that-calculates-the-iso-week"></a>A. 使用計算 ISO 週的純量值使用者定義函數

下列範例會建立使用者定義函數 `ISOweek`。 這個函數採用日期引數並計算 ISO 週數。 若要使函數能夠正確計算，必須先叫用 `SET DATEFIRST 1`，才能呼叫該函數。

此範例也說明如何使用 [EXECUTE AS](../../t-sql/statements/execute-as-clause-transact-sql.md) 子句來指定可執行預存程序的安全性內容。 在這個範例中，選項 `CALLER` 會指定將在呼叫程序之使用者的內容中執行程序。 您可以指定的其他選項為 `SELF`、`OWNER` 及 *user_name*。

以下是函數呼叫。 請注意，`DATEFIRST` 是設為 `1`。

```sql
CREATE FUNCTION dbo.ISOweek (@DATE datetime)
RETURNS int
WITH EXECUTE AS CALLER
AS
BEGIN
    DECLARE @ISOweek int;
    SET @ISOweek= DATEPART(wk,@DATE)+1
        -DATEPART(wk,CAST(DATEPART(yy,@DATE) as CHAR(4))+'0104');
--Special cases: Jan 1-3 may belong to the previous year
    IF (@ISOweek=0)
        SET @ISOweek=dbo.ISOweek(CAST(DATEPART(yy,@DATE)-1
            AS CHAR(4))+'12'+ CAST(24+DATEPART(DAY,@DATE) AS CHAR(2)))+1;
--Special case: Dec 29-31 may belong to the next year
    IF ((DATEPART(mm,@DATE)=12) AND
        ((DATEPART(dd,@DATE)-DATEPART(dw,@DATE))>= 28))
    SET @ISOweek=1;
    RETURN(@ISOweek);
END;
GO
SET DATEFIRST 1;
SELECT dbo.ISOweek(CONVERT(DATETIME,'12/26/2004',101)) AS 'ISO Week';
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
ISO Week
----------------
52
```

### <a name="b-creating-an-inline-table-valued-function"></a>B. 建立內嵌資料表值函式

下列範例會傳回 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的內嵌資料表值函式。 它會傳回三個資料行：`ProductID`、`Name`，以及年初至今銷售到商店之每項產品的總計彙總 `YTD Total` (依商店區分)。

```sql
CREATE FUNCTION Sales.ufn_SalesByStore (@storeid int)
RETURNS TABLE
AS
RETURN
(
    SELECT P.ProductID, P.Name, SUM(SD.LineTotal) AS 'Total'
    FROM Production.Product AS P
    JOIN Sales.SalesOrderDetail AS SD ON SD.ProductID = P.ProductID
    JOIN Sales.SalesOrderHeader AS SH ON SH.SalesOrderID = SD.SalesOrderID
    JOIN Sales.Customer AS C ON SH.CustomerID = C.CustomerID
    WHERE C.StoreID = @storeid
    GROUP BY P.ProductID, P.Name
);
GO
```

若要叫用函數，請執行這項查詢。

```sql
SELECT * FROM Sales.ufn_SalesByStore (602);
```

### <a name="c-creating-a-multi-statement-table-valued-function"></a>C. 建立多重陳述式資料表值函式

下列範例會在 AdventureWorks2012 資料庫中建立資料表值函式 `fn_FindReports(InEmpID)`。 當提供有效的員工識別碼時，此函數會傳回對應於所有員工的資料表，該資料表會直接或間接報告給員工。 此函數會利用遞迴通用資料表運算式 (CTE) 來產生階層式員工清單。 如需有關遞迴 CTE 的詳細資訊，請參閱 [WITH common_table_expression &#40;Transact-SQL&#41;](../../t-sql/queries/with-common-table-expression-transact-sql.md)。

```sql
CREATE FUNCTION dbo.ufn_FindReports (@InEmpID INTEGER)
RETURNS @retFindReports TABLE
(
    EmployeeID int primary key NOT NULL,
    FirstName nvarchar(255) NOT NULL,
    LastName nvarchar(255) NOT NULL,
    JobTitle nvarchar(50) NOT NULL,
    RecursionLevel int NOT NULL
)
--Returns a result set that lists all the employees who report to the
--specific employee directly or indirectly.*/
AS
BEGIN
WITH EMP_cte(EmployeeID, OrganizationNode, FirstName, LastName, JobTitle, RecursionLevel) -- CTE name and columns
    AS (
        -- Get the initial list of Employees for Manager n
        SELECT e.BusinessEntityID, e.OrganizationNode, p.FirstName, p.LastName, e.JobTitle, 0
        FROM HumanResources.Employee e
INNER JOIN Person.Person p
ON p.BusinessEntityID = e.BusinessEntityID
        WHERE e.BusinessEntityID = @InEmpID
        UNION ALL
        -- Join recursive member to anchor
        SELECT e.BusinessEntityID, e.OrganizationNode, p.FirstName, p.LastName, e.JobTitle, RecursionLevel + 1
        FROM HumanResources.Employee e
            INNER JOIN EMP_cte
            ON e.OrganizationNode.GetAncestor(1) = EMP_cte.OrganizationNode
INNER JOIN Person.Person p
ON p.BusinessEntityID = e.BusinessEntityID
        )
-- copy the required columns to the result of the function
    INSERT @retFindReports
    SELECT EmployeeID, FirstName, LastName, JobTitle, RecursionLevel
    FROM EMP_cte
    RETURN
END;
GO
-- Example invocation
SELECT EmployeeID, FirstName, LastName, JobTitle, RecursionLevel
FROM dbo.ufn_FindReports(1);

GO
```

### <a name="d-creating-a-clr-function"></a>D. 建立 CLR 函數

此範例會建立 CLR 函式 `len_s`。 在建立這個函數之前，已在本機資料庫中註冊組件 `SurrogateStringFunction.dll`。

**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] SP1 及更新版本)

```sql
DECLARE @SamplesPath nvarchar(1024);
-- You may have to modify the value of this variable if you have
-- installed the sample in a location other than the default location.
SELECT @SamplesPath = REPLACE
    (  physical_name
     , 'Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\master.mdf'
     , 'Microsoft SQL Server\130\Samples\Engine\Programmability\CLR\'
    )
  FROM master.sys.database_files
  WHERE name = 'master';

CREATE ASSEMBLY [SurrogateStringFunction]
FROM @SamplesPath + 'StringManipulate\CS\StringManipulate\bin\debug\SurrogateStringFunction.dll'
WITH PERMISSION_SET = EXTERNAL_ACCESS;
GO

CREATE FUNCTION [dbo].[len_s] (@str nvarchar(4000))
RETURNS bigint
AS EXTERNAL NAME [SurrogateStringFunction].[Microsoft.Samples.SqlServer.SurrogateStringFunction].[LenS];
GO
```

如需如何建立 CLR 資料表值函式的範例，請參閱 [CLR 資料表值函式](../../relational-databases/clr-integration-database-objects-user-defined-functions/clr-table-valued-functions.md)。

### <a name="e-displaying-the-definition-of-user-defined-functions"></a>E. 顯示使用者定義函式的定義

```sql
SELECT definition, type
FROM sys.sql_modules AS m
JOIN sys.objects AS o ON m.object_id = o.object_id
    AND type IN ('FN', 'IF', 'TF');
GO
```

使用 `ENCRYPTION` 選項建立的函式定義無法使用 sys.sql_modules 來檢視，但是會顯示與加密函式相關的其他資訊。

## <a name="see-also"></a>另請參閱

- [建立使用者定義函式 &#40;資料庫引擎&#41;](../../relational-databases/user-defined-functions/create-user-defined-functions-database-engine.md)
- [ALTER FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-function-transact-sql.md)
- [DROP FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-function-transact-sql.md)
- [OBJECTPROPERTYEX &#40;Transact-SQL&#41;](../../t-sql/functions/objectpropertyex-transact-sql.md)
- [sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)
- [sys.assembly_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-assembly-modules-transact-sql.md)
- [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)
- [CLR 使用者定義函式](../../relational-databases/clr-integration-database-objects-user-defined-functions/clr-user-defined-functions.md)
- [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)
- [CREATE SECURITY POLICY &#40;Transact-SQL&#41;](../../t-sql/statements/create-security-policy-transact-sql.md)