---
description: ALTER FUNCTION (Transact-SQL)
title: ALTER FUNCTION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/07/2017
ms.prod: sql
ms.prod_service: database-engine, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_FUNCTION_TSQL
- ALTER FUNCTION
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER FUNCTION statement
- modifying functions
- functions [SQL Server], modifying
ms.assetid: 89f066ee-05ac-4439-ab04-d8c3d5911179
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 1785a65f08952660bd3a8e3cad72355d8db5b373
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98081882"
---
# <a name="alter-function-transact-sql"></a>ALTER FUNCTION (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-asdb-xxxx-pdw-md](../../includes/tsql-appliesto-ss2008-asdb-xxxx-pdw-md.md)]

  改變先前執行 CREATE FUNCTION 陳述式所建立的現有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或 CLR 函數，而不變更權限及不影響任何相依的函數、預存程序或觸發程序。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Transact-SQL Scalar Function Syntax    
ALTER FUNCTION [ schema_name. ] function_name   
( [ { @parameter_name [ AS ][ type_schema_name. ] parameter_data_type   
    [ = default ] }   
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
ALTER FUNCTION [ schema_name. ] function_name   
( [ { @parameter_name [ AS ] [ type_schema_name. ] parameter_data_type   
    [ = default ] }   
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
-- Transact-SQL Multistatement Table-valued Function Syntax
ALTER FUNCTION [ schema_name. ] function_name   
( [ { @parameter_name [ AS ] [ type_schema_name. ] parameter_data_type   
    [ = default ] }   
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
      ( column_name [ ASC | DESC ] [ ,...n ] )  
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
-- CLR Scalar and Table-Valued Function Syntax
ALTER FUNCTION [ schema_name. ] function_name   
( { @parameter_name [AS] [ type_schema_name. ] parameter_data_type   
    [ = default ] }   
    [ ,...n ]  
)  
RETURNS { return_data_type | TABLE <clr_table_type_definition> }  
    [ WITH <clr_function_option> [ ,...n ] ]  
    [ AS ] EXTERNAL NAME <method_specifier>  
[ ; ]  
```
  
```syntaxsql
-- CLR Function Clauses
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
-- Syntax for In-Memory OLTP: Natively compiled, scalar user-defined function  
ALTER FUNCTION [ schema_name. ] function_name    
 ( [ { @parameter_name [ AS ][ type_schema_name. ] parameter_data_type   
    [ NULL | NOT NULL ] [ = default ] }   
    [ ,...n ]   
  ]   
)   
RETURNS return_data_type  
    [ WITH <function_option> [ ,...n ] ]   
    [ AS ]   
    BEGIN ATOMIC WITH (set_option [ ,... n ])  
        function_body   
        RETURN scalar_expression  
    END  
    
<function_option>::=   
{ |  NATIVE_COMPILATION   
  |  SCHEMABINDING   
  | [ EXECUTE_AS_Clause ]   
  | [ RETURNS NULL ON NULL INPUT | CALLED ON NULL INPUT ]   
}  
```   
  
## <a name="arguments"></a>引數  
 *schema_name*  
 這是使用者定義函數所屬的結構描述名稱。  
  
 *function_name*  
 這是要變更的使用者定義函數。  
  
> [!NOTE]  
>  即使沒有指定參數，函數名稱後面仍需要括號。  
  
 **@** _parameter_name_  
 這是使用者定義函數中的參數。 您可以宣告一個或多個參數。  
  
 函數最多可以有 2,100 個參數。 除非定義了參數的預設值，否則在執行函數時，使用者必須提供每個已宣告之參數的值。  
  
 使用 "at" 記號 ( **@** ) 當作第一個字元來指定參數名稱。 參數名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。 對函數而言，參數必須是本機參數；相同的參數名稱可以用在其他函數中。 參數只能取代常數，不能用來取代資料表名稱、資料行名稱或其他資料庫物件的名稱。  
  
> [!NOTE]  
>  當在預存程序或使用者自訂函數中傳遞參數時，或在批次陳述式中宣告和設定變數時，會忽略 ANSI_WARNINGS。 例如，如果將變數定義為 **char(3)** ，然後設定為大於三個字元的值，資料就會被截斷成定義的大小，而 INSERT 或 UPDATE 陳述式會執行成功。  
  
 [ *type_schema_name.* ] *parameter_data_type*  
 這是參數資料類型，對於其所屬的結構描述而言為選擇性。 就 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式而言，所有資料類型 (包括 CLR 使用者定義型別) 都是允許的資料類型，但 **timestamp** 資料類型除外。 就 CLR 函式而言，所有資料類型 (包括 CLR 使用者定義型別) 都是允許的資料類型，但 **text**、**ntext**、**image** 及 **timestamp** 資料類型除外。 非純量類型 **cursor** 和 **table** 不能指定為 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或 CLR 函式中的參數資料類型。  
  
 如果未指定 *type_schema_name*，[!INCLUDE[ssDEversion2005](../../includes/ssdeversion2005-md.md)] 就會依照下列順序尋找 *scalar_parameter_data_type*：  
  
-   內含 SQL Server 系統資料類型的結構描述。  
  
-   目前資料庫中之目前使用者的預設結構描述。  
  
-   目前資料庫中的 **dbo** 結構描述。  
  
 [ **=** _default_ ]  
 這是參數的預設值。 如果已定義 *default* 值，則不需為該參數指定值，即可執行函式。  
  
> [!NOTE]  
>  您可以針對 CLR 函式指定預設參數值，但 **varchar(max)** 和 **varbinary(max)** 資料類型除外。  
  
 如果函數的參數有預設值，則必須在呼叫函數來擷取該預設值時指定關鍵字 DEFAULT。 這個行為與使用預存程序中具有預設值的參數不一樣，因為在預存程序中，省略參數也意味著使用預設值。  
  
 *return_data_type*  
 這是純量使用者定義函數的傳回值。 就 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式而言，所有資料類型 (包括 CLR 使用者定義型別) 都是允許的資料類型，但 **timestamp** 資料類型除外。 就 CLR 函式而言，所有資料類型 (包括 CLR 使用者定義型別) 都是允許的資料類型，但 **text**、**ntext**、**image** 及 **timestamp** 資料類型除外。 非純量類型 **cursor** 和 **table** 不能指定為 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或 CLR 函式中的傳回資料類型。  
  
 *function_body*  
 指定一系列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式 (並用這些陳述式並不會造成任何副作用，例如修改資料表等) 定義函數的值。 *function_body* 僅用於純量函式和多重陳述式資料表值函式中。  
  
 在純量函式中，*function_body* 是一系列的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，這些陳述式會一起評估為純量值。  
  
 在多重陳述式資料表值函式中，*function_body* 是一系列的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，這些陳述式會填入 TABLE 傳回變數。  
  
 *scalar_expression*  
 指定純量函數傳回純量值。  
  
 TABLE  
 指定資料表值函式的傳回值是資料表。 只有常數和 **@** _local\_variables_ 才能傳遞給資料表值函式。  
  
 在內嵌資料表值函式中，TABLE 傳回值是利用單一 SELECT 陳述式所定義。 內嵌函數沒有相關聯的傳回變數。  
  
 在多重陳述式資料表值函式中， **@** _return\_variable_ 是一個 TABLE 變數，可用來儲存及累積應作為函式值來傳回的資料列。 **@** _return\_variable_ 只能指定給 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式，但不能指定給 CLR 函式。  
  
 *select-stmt*  
 這是單一 SELECT 陳述式，可定義嵌入資料表值函式的傳回值。  
  
 EXTERNAL NAME \<method_specifier>*assembly_name.class_name*.*method_name*  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定繫結函數之組件的方法。 *assembly_name* 必須符合目前所顯示之資料庫的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的現有組件。 *class_name* 必須是有效的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼，且必須是組件中的類別。 如果該類別具有命名空間限定的名稱，且該名稱使用句號 ( **.** ) 來分隔命名空間的各個部分，您就必須使用方括弧 ( **[]** ) 或引號 ( **""** ) 來分隔類別名稱。 *method_name* 必須是有效的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼，且必須是指定類別中的靜態方法。  
  
> [!NOTE]  
>  依預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不能執行 CLR 程式碼。 您可以建立、修改和卸除參考通用語言執行平台模組的資料庫物件；不過，必須等到您啟用 [clr enabled 選項](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md)之後，才能在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中執行這些參考。 若要啟用這個選項，請使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
 _\<_table\_type\_definition_\>_ **(** { \<column_definition\> \<column\_constraint\> | \<computed\_column\_definition\> } [ \<table\_constraint\> ] [ **,** ...*n* ] **)**  
 定義 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函數的資料表資料類型。 資料表宣告包括資料行定義和資料行或資料表條件約束。  
  
\< clr_table_type_definition \> **(** { *column_name**data_type* } [ **,** ...*n* ] **)** **適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] ([在某些區域中為預覽版本](/azure/azure-sql/database/features-comparison?WT.mc_id=TSQL_GetItTag))。  
  
 定義 CLR 函數的資料表資料類型。 資料表宣告只包含資料行名稱和資料類型。  
  
 NULL|NOT NULL  
 只有針對原生編譯的純量使用者定義函式才支援。 如需詳細資訊，請參閱[記憶體內部 OLTP 的純量使用者定義函數](../../relational-databases/in-memory-oltp/scalar-user-defined-functions-for-in-memory-oltp.md)。  
  
 NATIVE_COMPILATION  
 指出使用者定義函式是否為原生編譯函式。 此引數是原生編譯之純量使用者定義函式的必要引數。  
  
 NATIVE_COMPILATION 引數在您 ALTER 函式時為必要項目，且只能在函式是透過 NATIVE_COMPILATION 引數建立時才能使用。  
  
 BEGIN ATOMIC WITH  
 只有針對原生編譯的純量使用者定義函式才支援，並且為必要項目。 如需詳細資訊，請參閱 [Atomic Blocks](../../relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures.md)。  
  
 SCHEMABINDING  
 SCHEMABINDING 是原生編譯之純量使用者定義函式的必要引數。  
  
 **\<function_option>::= 和 \<clr_function_option>::=**  
  
 指定函數必須有下列其中一個或多個選項。  
  
 ENCRYPTION  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指出 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會對包含 ALTER FUNCTION 陳述式文字的目錄檢視表資料行進行加密。 使用 ENCRYPTION 可防止在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 複寫中發行這個函數。 無法為 CLR 函數指定 ENCRYPTION。  
  
 SCHEMABINDING  
 指定函數必須繫結到它所參考的資料庫物件。 當指定 SCHEMABINDING 時，無法依照會影響函數定義的方式來修改基底物件。 您必須先修改或卸除函數定義，才能移除對於所要修改物件的相依性。  
  
 只有在下發生下列其中一個動作時，才會移除函數與其參考的物件之間的繫結：  
  
-   已卸除這個函數。  
  
-   您可以利用未指定 SCHEMABINDING 選項的 ALTER 陳述式來修改函數。  
  
如需函式可以繫結結構描述前所必須符合的條件清單，請參閱 [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md)。  
  
 RETURNS NULL ON NULL INPUT | CALLED ON NULL INPUT  
 指定純量值函式的 **OnNULLCall** 屬性。 若未指定，預設情況下意味著 CALLED ON NULL INPUT。 這表示，即使傳遞 NULL 做為引數，函數主體仍會執行。  
  
 如果在 CLR 函數中指定 RETURNS NULL ON NULL INPUT，它會指出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可以在它接收的任何引數是 NULL 時傳回 NULL，而不必實際叫用函數主體。 如果 \<method_specifier> 中所指定方法已經有一個指出 RETURNS NULL ON NULL INPUT 的自訂屬性，但 ALTER FUNCTION 陳述式卻指出 CALLED ON NULL INPUT，則優先使用 ALTER FUNCTION 陳述式。 無法為 CLR 資料表值函式指定 **OnNULLCall** 屬性。  
  
 EXECUTE AS 子句  
 指定執行使用者定義函數時所在的安全性內容。 因此，您可以控制 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 要利用哪個使用者帳戶來驗證在函數參考的任何資料庫物件上的權限。  
  
> [!NOTE]  
>  無法為內嵌使用者定義函數指定 EXECUTE AS。  
  
 如需詳細資訊，請參閱 [EXECUTE AS 子句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)。  
  
**\< column_definition >::=**
  
 定義資料表資料類型。 資料表宣告包括資料行定義和條件約束。 針對 CLR 函式，只能指定 *column_name* 和 *data_type*。  
  
 *column_name*  
 這是資料表中的資料行名稱。 資料行名稱必須符合識別碼規則，在資料表中也必須是唯一的。 *column_name* 可由 1 到 128 個字元組成。  
  
 *data_type*  
 指定資料行資料類型。 就 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式而言，允許所有資料類型 (包括 CLR 使用者定義型別)，但 **timestamp** 除外。 就 CLR 函式而言，允許所有資料類型 (包括 CLR 使用者定義型別)，但 **text**、**ntext**、**image**、**char**、**varchar**、**varchar(max)** 及 **timestamp** 除外。無法指定非純量類型 **cursor** 作為 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或 CLR 函式中的資料行資料類型。  
  
 DEFAULT *constant_expression*  
 指定在插入期間未明確提供值時，提供給資料行的值。 *constant_expression* 是常數、NULL 或系統函式值。 除了含有 IDENTITY 屬性的資料行之外，任何資料行都可以套用 DEFAULT 定義。 無法為 CLR 資料表值函式指定 DEFAULT。  
  
 COLLATE *collation_name*  
 指定資料行的定序。 若未指定，就會將資料庫的預設定序指派給資料行。 定序名稱可以是 Windows 定序名稱或 SQL 定序名稱。 如需清單和詳細資訊，請參閱 [Windows 定序名稱 &#40;Transact-SQL&#41;](../../t-sql/statements/windows-collation-name-transact-sql.md) 和 [SQL Server 定序名稱 &#40;Transact-SQL&#41;](../../t-sql/statements/sql-server-collation-name-transact-sql.md)。  
  
 COLLATE 子句只能用來變更 **char**、**varchar**、**nchar** 與 **nvarchar** 資料類型之資料行的定序。  
  
 無法為 CLR 資料表值函式指定 COLLATE。  
  
 ROWGUIDCOL  
 指出新資料行是一個資料列全域唯一識別碼資料行。 每個資料表只能有一個 **uniqueidentifier** 資料行指定為 ROWGUIDCOL 資料行。 ROWGUIDCOL 屬性只能指派給 **uniqueidentifier** 資料行。  
  
 ROWGUIDCOL 屬性不會強制執行資料行中所儲存之值的唯一性。 它也不會自動為插入資料表中的新資料列產生值。 若要為每個資料行產生唯一值，請在 INSERT 陳述式上使用 NEWID 函數。 可以指定預設值；不過，NEWID 不能指定為預設值。  
  
 IDENTITY  
 指出新資料行是識別欄位。 新資料列加入至資料表時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會提供資料行的唯一累加值。 識別欄位通常用來搭配 PRIMARY KEY 條件約束一起使用，當做資料表的唯一資料列識別碼。 可以將 IDENTITY 屬性指派給 **tinyint**、**smallint**、**int**、**bigint**、**decimal(p,0)** 或 **numeric(p,0)** 資料行。 每份資料表都只能建立一個識別欄位。 繫結的預設值和 DEFAULT 條件約束無法搭配識別欄位使用。 您必須同時指定 *seed* 和 *increment*，或兩者都不指定。 如果同時不指定這兩者，預設值便是 (1,1)。  
  
 無法為 CLR 資料表值函式指定 IDENTITY。  
  
 *seed*  
 這是要指派給資料表中第一個資料列的整數值。  
  
 *increment*  
 這是要新增至資料表中後續資料列之 *seed* 值的整數值。  
  
**\< column_constraint >::= 和 \< table_constraint>::=**
  
 定義指定之資料行或資料表的條件約束。 就 CLR 函數而言，唯一允許的條件約束類型是 NULL。 不允許具名條件約束。  
  
 NULL | NOT NULL  
 判斷資料行中是否允許 Null 值。 嚴格來說，NULL 並不算是條件約束，但是您可以如同指定 NOT NULL 一樣加以指定。 無法為 CLR 資料表值函式指定 NOT NULL。  
  
 PRIMARY KEY  
 這是一項條件約束，它利用唯一索引強制執行指定之資料行的實體完整性。 在資料表值使用者定義函數中，PRIMARY KEY 條件約束只能建立在每份資料表的一個資料行上。 無法為 CLR 資料表值函式指定 PRIMARY KEY。  
  
 UNIQUE  
 這是一項條件約束，它透過唯一索引為指定的一個或多個資料行提供實體完整性。 一份資料表可以有多個 UNIQUE 條件約束。 無法為 CLR 資料表值函式指定 UNIQUE。  
  
 CLUSTERED | NONCLUSTERED  
 指出針對 PRIMARY KEY 或 UNIQUE 條件約束建立叢集或非叢集索引。 PRIMARY KEY 條件約束使用 CLUSTERED，UNIQUE 條件約束則使用 NONCLUSTERED。  
  
 只能為一個條件約束指定 CLUSTERED。 如果針對 UNIQUE 條件約束指定 CLUSTERED，且指定了 PRIMARY KEY 條件約束，則 PRIMARY KEY 會使用 NONCLUSTERED。  
  
 無法為 CLR 資料表值函式指定 CLUSTERED 和 NONCLUSTERED。  
  
 CHECK  
 這是一個條件約束，藉由限制可能輸入一個或多個資料行的值，強制執行範圍完整性。 無法為 CLR 資料表值函式指定 CHECK 條件約束。  
  
 *logical_expression*  
 這是一個傳回 TRUE 或 FALSE 的邏輯運算式。  
  
 **\<computed_column_definition>::=**  
  
 指定計算資料行。 如需有關計算資料行的詳細資訊，請參閱 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)。  
  
 *column_name*  
 這是計算資料行的名稱。  
  
 *computed_column_expression*  
 這是定義計算資料行值的運算式。  
  
 **\<index_option>::=**  
  
 指定 PRIMARY KEY 或 UNIQUE 索引的索引選項。 如需索引選項的詳細資訊，請參閱 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)。  
  
 PAD_INDEX = { ON | OFF }  
 指定索引填補。 預設值為 OFF。  
  
 FILLFACTOR = *fillfactor*  
 指定指出在建立或變更索引期間，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 各索引頁面之分葉層級填滿程度的百分比。 *fillfactor* 必須是 1 到 100 之間的整數值。 預設值是 0。  
  
 IGNORE_DUP_KEY = { ON | OFF }  
 指定當插入作業嘗試將重複的索引鍵值插入唯一索引時所產生的錯誤回應。 IGNORE_DUP_KEY 選項只適用於在建立或重建索引之後所發生的插入作業。 預設值為 OFF。  
  
 STATISTICS_NORECOMPUTE = { ON | OFF }  
 指定是否要重新計算散發統計資料。 預設值為 OFF。  
  
 ALLOW_ROW_LOCKS = { ON | OFF }  
 指定是否允許資料列鎖定。 預設值是 ON。  
  
 ALLOW_PAGE_LOCKS = { ON | OFF }  
 指定是否允許頁面鎖定。 預設值是 ON。  
  
## <a name="remarks"></a>備註  
 ALTER FUNCTION 不能用來將純量值函式變更為資料表值函式，反之亦然。 另外，ALTER FUNCTION 也不能用來將內嵌函數變更為多重陳述式函數，反之亦然。 ALTER FUNCTION 不能用來將 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函數變更為 CLR 函數，反之亦然。  
  
 下列 Service Broker 陳述式不能包含在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 使用者定義函式的定義中：  
  
-   BEGIN DIALOG CONVERSATION  
-   END CONVERSATION  
-   GET CONVERSATION GROUP  
-   MOVE CONVERSATION  
-   RECEIVE  
-   SEND  
  
## <a name="permissions"></a>權限  
 需要函數或結構描述的 ALTER 權限。 如果此函數指定使用者定義型別，則需要該型別的 EXECUTE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md)   
 [DROP FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-function-transact-sql.md)   
 [對發行集資料庫進行結構描述變更](../../relational-databases/replication/publish/make-schema-changes-on-publication-databases.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
