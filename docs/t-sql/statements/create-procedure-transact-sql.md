---
description: CREATE PROCEDURE (Transact-SQL)
title: CREATE PROCEDURE (Transact-SQL)
ms.custom: ''
ms.date: 09/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- PROC
- PROCEDURE
- CREATE PROCEDURE
- CREATE_PROC_TSQL
- PROCEDURE_TSQL
- CREATE PROC
- PROC_TSQL
- CREATE_PROCEDURE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- parameters [SQL Server], stored procedures
- table-valued parameters
- SET statement, stored procedures
- stored procedures [SQL Server], creating
- wildcard parameters [SQL Server]
- maximum size of stored procedures
- WITH RECOMPILE clause
- common language runtime [SQL Server], stored procedures
- CREATE PROCEDURE statement
- local temporary procedures [SQL Server]
- WITH ENCRYPTION clause
- output parameters [SQL Server]
- nesting stored procedures
- user-defined stored procedures [SQL Server]
- system stored procedures [SQL Server], creating
- deferred name resolution, stored procedures
- referenced tables [SQL Server]
- global temporary procedures [SQL Server]
- cursor data type
- temporary stored procedures [SQL Server]
- size [SQL Server], stored procedures
- automatic stored procedure execution
- creating stored procedures
ms.assetid: afe3d86d-c9ab-44e4-b74d-4e3dbd9cc58c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d8ebd5ecac550537f13d20e25a19222477ccebb6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092092"
---
# <a name="create-procedure-transact-sql"></a>CREATE PROCEDURE (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]、[!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 中建立 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或 Common Language Runtime (CLR) 預存程序。 預存程序類似於其他程式設計語言中的程序，這些程序可以：

- 接受輸入參數，並以輸出參數的形式將多個數值傳回呼叫程序或批次處理。
- 包含可在資料庫中執行作業的程式陳述式，包括呼叫其他程序。
- 將狀態值傳回呼叫程序或批次處理，以指示成功或失敗 (及失敗原因)。

使用此陳述式在目前的資料庫中建立永久程序，或在 **tempdb** 資料庫中建立暫存程序。

> [!NOTE]
> 本主題將討論如何將 .NET Framework CLR 整合至 SQL Server。 CLR 整合不適用於 Azure [!INCLUDE[ssSDS](../../includes/sssds-md.md)]。

若要略過語法詳細資料，並了解基本預存程序的簡單範例，可跳到[簡單範例](#Simple)。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
-- Transact-SQL Syntax for Stored Procedures in SQL Server and Azure SQL Database

CREATE [ OR ALTER ] { PROC | PROCEDURE }
    [schema_name.] procedure_name [ ; number ]
    [ { @parameter [ type_schema_name. ] data_type }
        [ VARYING ] [ = default ] [ OUT | OUTPUT | [READONLY]
    ] [ ,...n ]
[ WITH <procedure_option> [ ,...n ] ]
[ FOR REPLICATION ]
AS { [ BEGIN ] sql_statement [;] [ ...n ] [ END ] }
[;]

<procedure_option> ::=
    [ ENCRYPTION ]
    [ RECOMPILE ]
    [ EXECUTE AS Clause ]
```

```syntaxsql
-- Transact-SQL Syntax for CLR Stored Procedures

CREATE [ OR ALTER ] { PROC | PROCEDURE }
    [schema_name.] procedure_name [ ; number ]
    [ { @parameter [ type_schema_name. ] data_type }
        [ = default ] [ OUT | OUTPUT ] [READONLY]
    ] [ ,...n ]
[ WITH EXECUTE AS Clause ]
AS { EXTERNAL NAME assembly_name.class_name.method_name }
[;]
```

```syntaxsql
-- Transact-SQL Syntax for Natively Compiled Stored Procedures

CREATE [ OR ALTER ] { PROC | PROCEDURE } [schema_name.] procedure_name
    [ { @parameter data_type } [ NULL | NOT NULL ] [ = default ]
        [ OUT | OUTPUT ] [READONLY]
    ] [ ,... n ]
  WITH NATIVE_COMPILATION, SCHEMABINDING [ , EXECUTE AS clause ]
AS
{
  BEGIN ATOMIC WITH (set_option [ ,... n ] )
sql_statement [;] [ ... n ]
 [ END ]
}
 [;]

<set_option> ::=
    LANGUAGE = [ N ] 'language'
  | TRANSACTION ISOLATION LEVEL = { SNAPSHOT | REPEATABLE READ | SERIALIZABLE }
  | [ DATEFIRST = number ]
  | [ DATEFORMAT = format ]
  | [ DELAYED_DURABILITY = { OFF | ON } ]
```

```syntaxsql
-- Transact-SQL Syntax for Stored Procedures in Azure Synapse Analytics
-- and Parallel Data Warehouse

-- Create a stored procedure
CREATE { PROC | PROCEDURE } [ schema_name.] procedure_name
    [ { @parameterdata_type } [ OUT | OUTPUT ] ] [ ,...n ]
AS { [ BEGIN ] sql_statement [;][ ,...n ] [ END ] }
[;]
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

OR ALTER

**適用於**：Azure [!INCLUDE[ssSDS](../../includes/sssds-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] SP1 開始)。

如果程序已經存在，即會將它改變。

*schema_name* 程序所屬結構描述的名稱。 程序是以結構描述繫結的。 如果在建立程序時未指定結構描述名稱，就會自動指派建立程序之使用者的預設結構描述。

*procedure_name* 程序名稱。 程序名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則，在結構描述內也必須是唯一的。

為程序命名時，請避免使用 **sp_** 前置詞。 這個前置詞是供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 指定系統程序時使用。 如果有相同名稱的系統程序，使用前置詞可能造成應用程式的程式碼中斷。

若要建立本機或全域暫存程序，您可以在 *procedure_name* 前面使用一個數字記號 (#) 來建立本機暫存程序 ( *#procedure_name*)；使用兩個數字記號來建立全域暫存程序 ( *##procedure_name*)。 只有建立本機暫存程序的連線可以看到它，而且關閉連線時就會卸除該程序。 全域暫存程序適用於所有連線，而且在最後一個工作階段結束時，會使用程序卸除。 無法為 CLR 程序指定暫存名稱。

程序或全域暫存程序的完整名稱 (包括 ##) 不能超過 128 個字元。 本機暫存程序的完整名稱 (包括 #) 不能超過 116 個字元。

**;** *number*

**適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

用來將同名程序分組的選擇性整數。 您可以利用一個 DROP PROCEDURE 陳述式一併卸除這些分組的程序。

> [!NOTE]
> [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]

編號程序無法使用 **xml** 或 CLR 使用者定義的類型，亦無法用於計劃指南。

**@** *parameter* 在程序中宣告的參數。 將 at 記號 ( **@** ) 作為第一個字元來指定參數名稱。 參數名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。 對程序而言，參數必須是本機參數；相同的參數名稱可以用在其他程序中。

您可以宣告一個或多個參數，最大值為 2,100。 除非定義了參數的預設值或將值設為等於其他參數，否則，在呼叫程序時，使用者必須提供每個已宣告參數的值。 如果程序包含[資料表值參數](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)，但呼叫中缺少此參數，則會傳入空白資料表。 參數只可取代常數運算式，而無法取代資料表名稱、資料行名稱或其他資料庫物件的名稱。 如需詳細資訊，請參閱 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)。

如果指定了 FOR REPLICATION，就不能宣告參數。

[ _type\_schema\_name_ **.** ] *data_type* 參數資料類型及該資料類型所屬的結構描述。

**[!INCLUDE[tsql](../../includes/tsql-md.md)] 程序的指導方針**：

- 所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 資料類型都可以作為參數使用。
- 您可以使用使用者定義資料表類型建立資料表值參數。 資料表值參數只能是 INPUT 參數，而且必須與 READONLY 關鍵字一起使用。 如需詳細資訊，請參閱[使用資料表值參數 &#40;資料庫引擎&#41;](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)。
- **cursor** 資料類型只能是 OUTPUT 參數，而且必須與 VARYING 關鍵字一起使用。

**CLR 程序的指導方針**：

- 在 Managed 程式碼中具有對等類型的所有原生 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料類型都可以當做參數使用。 如需 CLR 類型與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統資料類型之間的對應詳細資訊，請參閱[對應 CLR 參數資料](../../relational-databases/clr-integration-database-objects-types-net-framework/mapping-clr-parameter-data.md)。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統資料類型及其語法的詳細資訊，請參閱[資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)。

- 資料表值或 **cursor** 資料類型都無法作為參數使用。
- 如果參數的資料類型是 CLR 使用者定義型別，您必須具有在該類型上的 EXECUTE 權限。

VARYING 指定支援作為輸出參數的結果集。 這個參數由程序動態建構，可能會有不同的內容。 只適用於 **cursor** 參數。 這個選項不適用於 CLR 程序。

*default* 參數的預設值。 如果定義了參數的預設值，不必指定該參數的值就可以執行程序。 預設值必須是常數，或者，可以是 NULL。 常數值可以採用萬用字元格式，讓您可以在將參數傳入程序時使用 LIKE 關鍵字。

**sys.parameters.default** 資料行中只會記錄 CLR 程序的預設值。 如果是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 程序參數，該資料行為 NULL。

OUT | OUTPUT 指出這個參數是輸出參數。 您可以利用 OUTPUT 參數將值傳回程序的呼叫者。 除非程序是 CLR 程序，否則 **text**、**ntext** 和 **image** 參數無法作為 OUTPUT 參數使用。 除非此程序是一個 CLR 程序，否則輸出參數可以當做資料指標預留位置使用。 資料表值資料類型無法指定為程序的 OUTPUT 參數。

READONLY 指出在程序的主體內無法更新或修改該參數。 如果參數類型是資料表值類型，就必須指定 READONLY。

RECOMPILE 指出 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 不會快取這個程序的查詢計畫，以強制在每次執行該程序時對其進行編譯。 如需為何會強制重新編譯的詳細資訊，請參閱[重新編譯預存程序](../../relational-databases/stored-procedures/recompile-a-stored-procedure.md)。 指定 FOR REPLICATION 時，或是 CLR 程序時，無法使用此選項。

若要指示 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 捨棄程序內個別查詢的查詢計劃，請使用查詢定義中的 RECOMPILE 查詢提示。 如需詳細資訊，請參閱[查詢提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)。

ENCRYPTION

**適用於**：SQL Server ( [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本)、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

指出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將 CREATE PROCEDURE 陳述式的原始文字轉換為模糊化格式。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，無法直接從任何目錄檢視中看見混亂格式的輸出。 對系統資料表或資料庫檔案沒有存取權的使用者無法擷取模糊化的文字。 不過，如果是特殊權限使用者 (可以透過 [DAC 通訊埠](../../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)存取系統資料表，或直接存取資料庫檔案)，則可使用該文字。 另外，可將偵錯工具附加至伺服器處理序的使用者，還可以在執行階段從記憶體擷取解密程序。 如需如何存取系統中繼資料的詳細資訊，請參閱[中繼資料可見性組態](../../relational-databases/security/metadata-visibility-configuration.md)。

這個選項不適用於 CLR 程序。

使用這個選項建立的程序，不能發行為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 複寫的一部分。

EXECUTE AS 「子句」 指定執行程序時所在的資訊安全內容。

針對原生編譯的預存程序，從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 的 EXECUTE AS 子句均無限制。 在 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 中，SELF、OWNER 和 *'user_name'* 子句均支援原生編譯的預存程序。

如需詳細資訊，請參閱 [EXECUTE AS 子句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)。

FOR REPLICATION

**適用於**：SQL Server ( [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本)、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

指定針對複寫建立的程序。 因此無法針對訂閱者執行該程序。 利用 FOR REPLICATION 選項建立的程序會當做程序篩選來使用，而且只有在複寫期間才會執行它。 如果指定了 FOR REPLICATION，就不能宣告參數。 無法為 CLR 程序指定 FOR REPLICATION。 使用 FOR REPLICATION 建立的程序，會忽略 RECOMPILE 選項。

`FOR REPLICATION` 程序在 **sys.objects** 和 **sys.procedures** 中具有 **RF** 物件類型。

{ [ BEGIN ] *sql_statement* [;] [ ...*n* ] [ END ] } 組成程序主體的一或多個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 您可以使用選用的 BEGIN 和 END 關鍵字來括住陳述式。 如需詳細資訊，請參閱以下的＜最佳作法＞、＜一般備註＞以及＜限制事項＞這幾節。

EXTERNAL NAME _assembly\_name_ **.** _class\_name_ **.** _method\_name_

**適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。

指定 CLR 程序所要參考之 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 組件的方法。 *class_name* 必須是有效的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼，且必須是組件中的類別。 如果該類別具有命名空間限定的名稱，且該名稱使用句號 ( **.** ) 來分隔命名空間的各個部分，您就必須使用方括弧 ( **[]** ) 或引號 ( **""** ) 來分隔類別名稱。 指定的方法必須是類別的靜態方法。

依預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不能執行 CLR 程式碼。 您可以建立、修改和卸除參考通用語言執行平台模組的資料庫物件；不過，必須等到您啟用 [clr enabled 選項](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md)之後，才能在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中執行這些參考。 若要啟用這個選項，請使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)。

> [!NOTE]
> 自主資料庫不支援 CLR 程序。

ATOMIC WITH

**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

表示不可部分完成的預存程序執行。 變更會全部認可或透過擲回例外狀況全部回復。 原生編譯預存程序需要 ATOMIC WITH 區塊。

如果此程序傳回 (透過 RETURN 陳述式明確傳回或透過完成執行隱含傳回)，則會認可程序所執行的工作。 如果程序擲回，則會回復程序所執行的工作。

不可部分完成區塊內的 XACT_ABORT 預設為 ON，且無法變更。 XACT_ABORT 指定當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 陳述式產生執行階段錯誤時，[!INCLUDE[tsql](../../includes/tsql-md.md)] 是否自動回復目前的交易。

 ATOMIC 區塊中的下列 SET 選項永遠是 ON，選項無法變更。

- CONCAT_NULL_YIELDS_NULL
- QUOTED_IDENTIFIER, ARITHABORT
- NOCOUNT
- ANSI_NULLS
- ANSI_WARNINGS

ATOMIC 區塊內的 SET 選項無法變更。 在原生編譯預存程序的範圍中不使用使用者工作階段的 SET 選項。 這些選項在編譯時間是固定的。

BEGIN、ROLLBACK 和 COMMIT 作業無法使用於不可部分完成區塊內。

在程序的外部範圍，每個原生編譯預存程序有一個 ATOMIC 區塊。 區塊不可以是巢狀的。 如需 ATOMIC 區塊的詳細資訊，請參閱[原生編譯的預存程序](../../relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables.md)。

**NULL** | NOT NULL 判斷參數中是否允許 Null 值。 預設值是 NULL。

NATIVE_COMPILATION

**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

表示程序是原生編譯的。 NATIVE_COMPILATION、SCHEMABINDING 和 EXECUTE AS 可以依照任何順序來指定。 如需詳細資訊，請參閱[原生編譯的預存程序](../../relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables.md)。

SCHEMABINDING

**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

確定程序所參考的資料表無法卸除或改變。 原生編譯預存程序需要 SCHEMABINDING。 (如需詳細資訊，請參閱[原生編譯的預存程序](../../relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables.md)。)SCHEMABINDING 限制和使用者定義函式的相關限制相同。 如需詳細資訊，請參閱 [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md) 中的＜SCHEMABINDING＞一節。

LANGUAGE = [N] 'language'

**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

相當於 [SET LANGUAGE &#40;Transact-SQL&#41;](../../t-sql/statements/set-language-transact-sql.md) 工作階段選項。 需要 LANGUAGE = [N] 'language'。

TRANSACTION ISOLATION LEVEL

**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

原生編譯預存程序所需的。 指定預存程序的交易隔離等級。 選項如下：

如需這些選項的詳細資訊，請參閱 [SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)。

REPEATABLE READ 指定陳述式無法讀取已經由其他交易所修改但尚未認可的資料。 如果其他交易對目前交易已讀取的資料進行修改，則目前交易會失敗。

SERIALIZABLE 指定下列項目：

- 陳述式無法讀取其他交易已修改但尚未認可的資料。
- 如果其他交易對目前交易已讀取的資料進行修改，則目前交易會失敗。
- 如果其他交易插入了新資料列，且其索引鍵值落在目前交易之任何陳述式已讀取的索引鍵範圍中，則目前交易會失敗。

SNAPSHOT 指定交易中任何陳述式所讀取的資料，都是交易開始時就存在的資料交易一致版本。

DATEFIRST = *number*

**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

將每週的第一天指定為 1 到 7 的數字。 DATEFIRST 是選擇性的。 如果未指定，則會從指定的語言來推斷設定。

如需詳細資訊，請參閱 [SET DATEFIRST &#40;Transact-SQL&#41;](../../t-sql/statements/set-datefirst-transact-sql.md)。

DATEFORMAT = *format*

**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

指定解譯 date、smalldatetime、datetime、datetime2 和 datetimeoffset 字元字串之月份、日期與年份日期部分的順序。 DATEFORMAT 是選擇性的。 如果未指定，則會從指定的語言來推斷設定。

如需詳細資訊，請參閱 [SET DATEFIRST &#40;Transact-SQL&#41;](../../t-sql/statements/set-dateformat-transact-sql.md)。

DELAYED_DURABILITY = { OFF | ON }

**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 交易認可可能是完全持久、預設值或延遲的持久。

如需詳細資訊，請參閱[控制交易持久性](../../relational-databases/logs/control-transaction-durability.md)。

## <a name="simple-examples"></a><a name="Simple"></a> 簡單範例

為了協助您開始使用，以下是兩個簡單的範例：`SELECT DB_NAME() AS ThisDB;` 會傳回目前資料庫的名稱。
您可以將該陳述式包裝在預存程序中，例如：

```sql
CREATE PROC What_DB_is_this
AS
SELECT DB_NAME() AS ThisDB;
```

使用下列陳述式呼叫預存程序：`EXEC What_DB_is_this;`

稍微複雜一點的方法，是提供讓程序更有彈性的輸入參數。 例如：

```sql
CREATE PROC What_DB_is_that @ID INT
AS
SELECT DB_NAME(@ID) AS ThatDB;
```

在您呼叫程序時，提供資料庫識別碼。 例如 `EXEC What_DB_is_that 2;` 會傳回 `tempdb`。

如需更多範例，請參閱本主題結尾的[範例](#Examples)。

## <a name="best-practices"></a>最佳做法

雖然這不是最詳盡的最佳作法清單，但這些建議可能會改善程序效能。

- 使用 SET NOCOUNT ON 陳述式做為程序主體中的第一個陳述式。 亦即，將該陳述式放在 AS 關鍵字正後方。 這樣會關閉 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在執行任何 SELECT、INSERT、UPDATE、MERGE 和 DELETE 陳述式之後，傳回用戶端的訊息。 這樣能讓所產生的輸出為最小，以保持清晰。 但在現今硬體上沒有可測量的效能優勢。 如需詳細資訊，請參閱 [SET NOCOUNT &#40;Transact-SQL&#41;](../../t-sql/statements/set-nocount-transact-sql.md)。
- 建立或參考程序中的資料庫物件時，請使用結構描述名稱。 如果 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 不必搜尋多個結構描述，解析物件名稱所需的處理時間會較少。 它也能防止在不指定結構描述的情況下建立物件時，因指派使用者預設結構描述所引起的權限和存取問題。
- 避免在 WHERE 和 JOIN 子句中指定的資料行周圍使用包裝函數。 這麼做會使資料行變成非決定性，而且會使查詢處理器無法使用索引。
- 避免在 SELECT 陳述式中使用傳回許多資料列的純量函數。 純量函數必須套用到每個資料列，因此，所產生的行為類似以資料列為主的處理，而且會降低效能。
- 請避免使用 `SELECT *`。 請改為指定所需的資料行名稱。 這樣可以防止停止程序執行的部分 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 錯誤。 例如，假設 `SELECT *` 陳述式會從 12 欄式資料表傳回資料並將該資料插入 12 欄式暫存資料表，那麼，直到資料表中資料行的數目或順序變更時，該陳述式才會成功。
- 請避免處理或傳回太多資料。 盡早將程序程式碼中的結果範圍縮小，讓該程序所執行的所有後續作業都可以使用最小的資料集完成。 只將基本資料傳送到用戶端應用程式。 此作法比透過網路傳送額外資料並強制用戶端應用程式處理過大的結果集更有效率。
- 透過 BEGIN/COMMIT TRANSACTION 使用明確的交易，並讓交易越短越好。 交易越久表示記錄鎖定越久，而且發生死結的可能性也就越大。
- 請針對程序內部的錯誤處理使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] TRY...CATCH 功能。 TRY...CATCH 可以封裝 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的整個區塊。 這樣不但會使效能負擔較小，而且還會使用更少的程式讓錯誤報告更精確。
- 在程序主體中 CREATE TABLE 或 ALTER TABLE [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式所參考的所有資料表資料行上使用 DEFAULT 關鍵字。 這會防止將 NULL 傳遞到不允許 null 值的資料行。
- 在暫存資料表中的每個資料行使用 NULL 或 NOT NULL。 當 CREATE TABLE 或 ALTER TABLE 陳述式中沒有指定 NULL 或 NOT NULL 屬性時，ANSI_DFLT_ON 和 ANSI_DFLT_OFF 選項可控制 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 將這些屬性指派給資料行的方式。 如果某個連接執行程序時所用的選項設定，不同於建立程序的連接所用設定，針對第二個連接建立的資料表資料行，可以有不同的 Null 屬性，且可以展現不同的行為。 如果針對每個資料行明確陳述 NULL 或 NOT NULL，就會利用執行程序之所有連接的相同 Null 屬性來建立暫存資料表。
- 使用轉換 null 的修改陳述式，然後使用查詢中的 null 值加入排除資料列的邏輯。 請注意，在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 中，NULL 不是空值或 "nothing" 值。 它是未知值的預留位置，而且可能造成非預期的行為，特別是在查詢結果集或使用 AGGREGATE 函數時。
- 除非不同的值有特定需要，否則請使用 UNION ALL 運算子代替 UNION 或 OR 運算子。 UNION ALL 運算子需要的處理負擔較少，因為不會從結果集中篩選出重複項目。

## <a name="general-remarks"></a>一般備註

程序沒有預先定義的大小上限。

程序中指定的變數可以是使用者自訂變數或系統變數，如 @@SPID。

第一次執行程序時，會編譯它來決定擷取資料的最佳存取計畫。 如果已經產生的計畫仍保留在 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的計畫快取中，則程序的後續執行作業可以重複使用該計畫。

當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 啟動時，會自動執行一個或多個程序。 這些程序必須由 **master** 資料庫中的系統管理員建立，且必須在 **sysadmin** 固定伺服器角色下以背景處理序執行。 這些程序不可以有任何輸入或輸出參數。 如需詳細資訊，請參閱[執行預存程序](../../relational-databases/stored-procedures/execute-a-stored-procedure.md)。

當某個程序參考 CLR 常式、類型或彙總，藉以呼叫其他程序或執行 Managed 程式碼時，這些程序即稱為巢狀程序。 程序和 Managed 程式碼參考的巢狀結構最多可有 32 個層級。 當被呼叫的程序或 Managed 程式碼參考開始執行時，巢狀層級會加一；當被呼叫的程序或 Managed 程式碼參考執行完畢時，巢狀層級會減一。 從 Managed 程式碼內部叫用的方法不受巢狀層級的限制。 但當 CLR 預存程序透過 SQL Server Managed 提供者執行資料存取作業時，會在 Managed 程式碼轉換成 SQL 的過程中，加入一個額外的巢狀層級。

企圖超越最大巢狀層級將會導致整個呼叫鏈結失敗。 您可以使用 @@NESTLEVEL 函數傳回目前預存程序執行的巢狀層級。

## <a name="interoperability"></a>互通性

當建立或修改 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 程序時，[!INCLUDE[tsql](../../includes/tsql-md.md)] 會將 SET QUOTED_IDENTIFIER 和 SET ANSI_NULLS 的設定一併儲存。 這些原始設定是在執行程序時使用的。 因此，當程序正在執行時，SET QUOTED_IDENTIFIER 和 SET ANSI_NULLS 的任何用戶端工作階段設定，都會被忽略。

當建立或修改程序時，不會儲存其他 SET 選項，例如：SET ARITHABORT、SET ANSI_WARNINGS 或 SET ANSI_PADDINGS。 如果程序的邏輯取決於特定設定，請在程序的開頭併入 SET 陳述式，以確保可以有適當的設定。 如果從程序中執行 SET 陳述式，設定的有效性只能維持到程序完成執行。 之後，該設定就會還原為程序被呼叫時所具有的值。 這可讓個別用戶端設定本身想要的選項，而不影響程序的邏輯。

除了 SET SHOWPLAN_TEXT 和 SET SHOWPLAN_ALL 以外，其他所有 SET 陳述式都可以在程序中指定。 這些陳述式必須是批次中唯一的陳述式。 所選 SET 選項在程序執行期間仍然有效，然後會還原為先前的設定。

> [!NOTE]
> 當在程序或使用者定義函數中傳遞參數時，或在批次陳述式中宣告和設定變數時，未接受 SET ANSI_WARNINGS。 例如，如果將變數定義為 **char(3)** ，然後設定為大於三個字元的值，資料就會被截斷成定義的大小，而 INSERT 或 UPDATE 陳述式會執行成功。

## <a name="limitations-and-restrictions"></a>限制事項

CREATE PROCEDURE 陳述式無法在單一批次中，與其他 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式結合起來。

下列陳述式無法在預存程序主體中的任何位置使用。

| CREATE | SET | USE |
|--------|-----|-----|
| CREATE AGGREGATE | SET SHOWPLAN_TEXT | USE *database_name*|
| CREATE DEFAULT | SET SHOWPLAN_XML
| CREATE RULE | SET PARSEONLY |
| CREATE SCHEMA | SET SHOWPLAN_ALL |
| CREATE 或 ALTER TRIGGER |
| CREATE 或 ALTER FUNCTION |
| CREATE 或 ALTER PROCEDURE |
| CREATE 或 ALTER VIEW |

 程序可以參考尚未存在的資料表。 在建立時，只會執行語法檢查。 在第一次執行程序之前，不會編譯該程序。 只有在編譯期間才會解析程序中參考的所有物件。 因此，即使程序參考了不存在的資料表，仍可在語意正確的情況下順利建立；不過，如果參考的資料表不存在，該程序就會在執行階段時失敗。

 執行程序時，您無法將函數名稱指定為參數預設值或傳遞至參數的值。 但您可以變數形式傳遞函數，如下列範例所示。

```sql
-- Passing the function value as a variable.
DECLARE @CheckDate DATETIME = GETDATE();
EXEC dbo.uspGetWhereUsedProductID 819, @CheckDate;
GO
```

如果此程序變更了 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的遠端執行個體，將無法回復這些變更。 遠端程序不會參與交易。

當正確的方法在 .NET Framework 中多載時，若要讓 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 參考正確的方法，EXTERNAL NAME 子句中所指定的方法必須具有下列性質：

- 宣告為靜態方法。
- 接收與程序的參數數目相同的參數數目。
- 使用與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 程序之相對應參數的資料類型相容的參數類型。 如需如何比對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料類型與 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 資料類型的詳細資訊，請參閱[對應 CLR 參數資料](../../relational-databases/clr-integration-database-objects-types-net-framework/mapping-clr-parameter-data.md)。

## <a name="metadata"></a>中繼資料

下表列出可用於傳回預存程序之詳細資訊的目錄檢視和動態管理檢視。

|檢視|描述|
|----------|-----------------|
|[sys.sql_modules](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)|傳回 [!INCLUDE[tsql](../../includes/tsql-md.md)] 程序的定義。 您無法使用 **sys.sql_modules** 目錄檢視來檢視以 ENCRYPTION 選項建立的程序文字。|
|[sys.assembly_modules](../../relational-databases/system-catalog-views/sys-assembly-modules-transact-sql.md)|傳回 CLR 程序的詳細資訊。|
|[sys.parameters](../../relational-databases/system-catalog-views/sys-parameters-transact-sql.md)|傳回程序所定義之參數的詳細資訊。|
|[sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md) [sys.dm_sql_referenced_entities](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referenced-entities-transact-sql.md) [sys.dm_sql_referencing_entities](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referencing-entities-transact-sql.md)|傳回程序所參考的物件。|

若要預估已編譯的程序大小，請使用下列效能監視器計數器。

|效能監視器物件名稱|效能監視器計數器名稱|
|-------------------------------------|--------------------------------------|
|SQLServer：計畫快取物件|快取點擊率|
||快取頁面|
||快取物件計數*|

\* 這些計數器可供各種類別目錄的快取物件使用，包括隨選 [!INCLUDE[tsql](../../includes/tsql-md.md)]、已備妥的 [!INCLUDE[tsql](../../includes/tsql-md.md)]、程序、觸發程序等等。 如需詳細資訊，請參閱 [SQL Server 的 Plan Cache 物件](../../relational-databases/performance-monitor/sql-server-plan-cache-object.md)。

## <a name="security"></a>安全性

### <a name="permissions"></a>權限

需要資料庫的 **CREATE PROCEDURE** 權限，以及要在其中建立程序之結構描述的 **ALTER** 權限，或者需要 **db_ddladmin** 固定資料庫角色的成員資格。

如果是 CLR 預存程序，需要 EXTERNAL NAME 子句中所參考組件的擁有權，或該組件的 **REFERENCES** 權限。

## <a name="create-procedure-and-memory-optimized-tables"></a><a name="mot"></a> CREATE PROCEDURE 和經記憶體最佳化的資料表

從傳統和原生編譯的預存程序存取經記憶體最佳化資料表，都可以達到最高效率。 在大部分情況下，原生程序是更有效率的方式。 如需詳細資訊，請參閱[原生編譯的預存程序](../../relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables.md)。

下列範例示範如何建立原生編譯的預存程序，以存取經記憶體最佳化的資料表 `dbo.Departments`：

```sql
CREATE PROCEDURE dbo.usp_add_kitchen @dept_id INT, @kitchen_count INT NOT NULL
WITH EXECUTE AS OWNER, SCHEMABINDING, NATIVE_COMPILATION
AS
BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = N'us_english')

UPDATE dbo.Departments
SET kitchen_count = ISNULL(kitchen_count, 0) + @kitchen_count
WHERE ID = @dept_id
END;
GO
```

 未使用 NATIVE_COMPILATION 建立的程序，無法更改為原生編譯預存程序。

 如需原生編譯預存程序的可程式性、支援的查詢介面區及運算子的說明，請參閱[原生編譯的 T-SQL 模組支援的功能](../../relational-databases/in-memory-oltp/supported-features-for-natively-compiled-t-sql-modules.md)。

## <a name="examples"></a><a name="Examples"></a> 範例

|類別|代表性語法元素|
|--------------|------------------------------|
|[基本語法](#BasicSyntax)|CREATE PROCEDURE|
|[傳遞參數](#Parameters)|@parameter <br> &nbsp;&nbsp;• = 預設 <br> &nbsp;&nbsp; • 輸出 <br> &nbsp;&nbsp; • 資料表值參數類型 <br> &nbsp;&nbsp; • CURSOR VARYING|
|[使用預存程序修改資料](#Modify)|UPDATE|
|[錯誤處理](#Error)|TRY...CATCH|
|[模糊化程序定義](#Encrypt)|WITH ENCRYPTION|
|[強制程序重新編譯](#Recompile)|WITH RECOMPILE|
|[設定安全性內容](#Security)|EXECUTE AS|

### <a name="basic-syntax"></a><a name="BasicSyntax"></a> 基本語法

本節的範例使用所需的最少語法示範 CREATE PROCEDURE 陳述式的基本功能。

#### <a name="a-creating-a-simple-transact-sql-procedure"></a>A. 建立簡單的 Transact-SQL 程序

以下範例所建立的預存程序會從 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的檢視表傳回所有員工 (所提供的姓氏和名字)、工作職稱及部門名稱。 這個程序沒有使用任何參數。 其會示範執行程序的三種方法。

```sql
CREATE PROCEDURE HumanResources.uspGetAllEmployees
AS
    SET NOCOUNT ON;
    SELECT LastName, FirstName, JobTitle, Department
    FROM HumanResources.vEmployeeDepartment;
GO

SELECT * FROM HumanResources.vEmployeeDepartment;
```

您可以利用下列方式執行 `uspGetEmployees` 程序：

```sql
EXECUTE HumanResources.uspGetAllEmployees;
GO
-- Or
EXEC HumanResources.uspGetAllEmployees;
GO
-- Or, if this procedure is the first statement within a batch:
HumanResources.uspGetAllEmployees;
```

#### <a name="b-returning-more-than-one-result-set"></a>B. 傳回一個以上的結果集。

下列程序會傳回兩個結果集。

```sql
CREATE PROCEDURE dbo.uspMultipleResults
AS
SELECT TOP(10) BusinessEntityID, Lastname, FirstName FROM Person.Person;
SELECT TOP(10) CustomerID, AccountNumber FROM Sales.Customer;
GO
```

#### <a name="c-creating-a-clr-stored-procedure"></a>C. 建立 CLR 預存程序

下列範例會建立 `GetPhotoFromDB` 程序，以參考 `HandlingLOBUsingCLR` 組件中 `LargeObjectBinary` 類別的 `GetPhotoFromDB` 方法。 建立程序之前，會先在本機資料庫中註冊 `HandlingLOBUsingCLR` 組件。

**適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] (若使用從 *assembly_bits* 建立的組件)。

```sql
CREATE ASSEMBLY HandlingLOBUsingCLR
FROM '\\MachineName\HandlingLOBUsingCLR\bin\Debug\HandlingLOBUsingCLR.dll';
GO
CREATE PROCEDURE dbo.GetPhotoFromDB
(
    @ProductPhotoID INT
    , @CurrentDirectory NVARCHAR(1024)
    , @FileName NVARCHAR(1024)
)
AS EXTERNAL NAME HandlingLOBUsingCLR.LargeObjectBinary.GetPhotoFromDB;
GO
```

### <a name="passing-parameters"></a><a name="Parameters"></a> 傳遞參數

本節的範例會示範如何使用輸入和輸出參數，在預存程序之間來回傳遞值。

#### <a name="d-creating-a-procedure-with-input-parameters"></a>D. 使用輸入參數建立程序

下列範例所建立的預存程序，會傳遞特定員工的名字和姓氏值，藉以傳回該員工的資訊。 此程序只會接受與所傳遞參數完全相符的項目。

```sql
IF OBJECT_ID ( 'HumanResources.uspGetEmployees', 'P' ) IS NOT NULL
    DROP PROCEDURE HumanResources.uspGetEmployees;
GO
CREATE PROCEDURE HumanResources.uspGetEmployees
    @LastName NVARCHAR(50),
    @FirstName NVARCHAR(50)
AS

    SET NOCOUNT ON;
    SELECT FirstName, LastName, JobTitle, Department
    FROM HumanResources.vEmployeeDepartment
    WHERE FirstName = @FirstName AND LastName = @LastName;
GO

```

您可以利用下列方式執行 `uspGetEmployees` 程序：

```sql
EXECUTE HumanResources.uspGetEmployees N'Ackerman', N'Pilar';
-- Or
EXEC HumanResources.uspGetEmployees @LastName = N'Ackerman', @FirstName = N'Pilar';
GO
-- Or
EXECUTE HumanResources.uspGetEmployees @FirstName = N'Pilar', @LastName = N'Ackerman';
GO
-- Or, if this procedure is the first statement within a batch:
HumanResources.uspGetEmployees N'Ackerman', N'Pilar';

```

#### <a name="e-using-a-procedure-with-wildcard-parameters"></a>E. 使用程序與萬用字元參數

下列範例所建立的預存程序，會傳遞一些員工的全名或部分姓名值，藉以傳回這些員工的資訊。 這個程序模式會與所傳遞的參數相符，或者，如果沒有提供參數的話，該模式會使用預設值 (字母 `D` 開頭的姓氏)。

```sql
IF OBJECT_ID ( 'HumanResources.uspGetEmployees2', 'P' ) IS NOT NULL
    DROP PROCEDURE HumanResources.uspGetEmployees2;
GO
CREATE PROCEDURE HumanResources.uspGetEmployees2
    @LastName NVARCHAR(50) = N'D%',
    @FirstName NVARCHAR(50) = N'%'
AS
    SET NOCOUNT ON;
    SELECT FirstName, LastName, JobTitle, Department
    FROM HumanResources.vEmployeeDepartment
    WHERE FirstName LIKE @FirstName AND LastName LIKE @LastName;
```

您可以利用多種組合執行 `uspGetEmployees2` 程序。 此處僅示範其中幾種可能的組合。

```sql
EXECUTE HumanResources.uspGetEmployees2;
-- Or
EXECUTE HumanResources.uspGetEmployees2 N'Wi%';
-- Or
EXECUTE HumanResources.uspGetEmployees2 @FirstName = N'%';
-- Or
EXECUTE HumanResources.uspGetEmployees2 N'[CK]ars[OE]n';
-- Or
EXECUTE HumanResources.uspGetEmployees2 N'Hesse', N'Stefen';
-- Or
EXECUTE HumanResources.uspGetEmployees2 N'H%', N'S%';
```

#### <a name="f-using-output-parameters"></a>F. 使用 OUTPUT 參數

下列範例會建立 `uspGetList` 程序。 這個程序傳回一份產品清單，其中產品的價格都沒有超過指定的金額。 這個範例顯示多個 `SELECT` 陳述式和多個 `OUTPUT` 參數的用法。 OUTPUT 參數可讓外部程序、批次，或多個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式在程序執行期間存取值集。

```sql
IF OBJECT_ID ( 'Production.uspGetList', 'P' ) IS NOT NULL
    DROP PROCEDURE Production.uspGetList;
GO  
CREATE PROCEDURE Production.uspGetList @Product VARCHAR(40)
    , @MaxPrice MONEY
    , @ComparePrice MONEY OUTPUT
    , @ListPrice MONEY OUT
AS  
    SET NOCOUNT ON;
    SELECT p.[Name] AS Product, p.ListPrice AS 'List Price'
    FROM Production.Product AS p
    JOIN Production.ProductSubcategory AS s
      ON p.ProductSubcategoryID = s.ProductSubcategoryID
    WHERE s.[Name] LIKE @Product AND p.ListPrice < @MaxPrice;
-- Populate the output variable @ListPprice.
SET @ListPrice = (SELECT MAX(p.ListPrice)
    FROM Production.Product AS p
    JOIN Production.ProductSubcategory AS s
      ON p.ProductSubcategoryID = s.ProductSubcategoryID
    WHERE s.[Name] LIKE @Product AND p.ListPrice < @MaxPrice);
-- Populate the output variable @compareprice.
SET @ComparePrice = @MaxPrice;
GO
```

執行 `uspGetList` 以傳回成本低於 `$700` 的 [!INCLUDE[ssSampleDBCoShort](../../includes/sssampledbcoshort-md.md)] 產品 (自行車) 清單。 您可以搭配流程控制語言使用 `OUTPUT` 參數 `@Cost` 和 `@ComparePrices`，以在 [訊息] 視窗中傳回訊息。

> [!NOTE]
> 建立程序以及使用變數時，都必須定義 OUTPUT 變數。 參數名稱與變數名稱不一定要相符；不過，除非使用 `@ListPrice` = *variable*，否則資料類型與參數定位必須相符。

```sql
DECLARE @ComparePrice MONEY, @Cost MONEY;
EXECUTE Production.uspGetList '%Bikes%', 700,
    @ComparePrice OUT,
    @Cost OUTPUT
IF @Cost <= @ComparePrice
BEGIN
    PRINT 'These products can be purchased for less than
    $'+RTRIM(CAST(@ComparePrice AS VARCHAR(20)))+'.'
END
ELSE
    PRINT 'The prices for all products in this category exceed
    $'+ RTRIM(CAST(@ComparePrice AS VARCHAR(20)))+'.';
```

部分結果集如下：

```
Product                     List Price
--------------------------  ----------
Road-750 Black, 58          539.99
Mountain-500 Silver, 40     564.99
Mountain-500 Silver, 42     564.99
...
Road-750 Black, 48          539.99
Road-750 Black, 52          539.99

(14 row(s) affected)

These items can be purchased for less than $700.00.
```

#### <a name="g-using-a-table-valued-parameter"></a>G. 使用資料表值參數

下列範例會使用資料表值參數類型，將多個資料列插入資料表中。 此範例會建立此參數類型、宣告資料表變數進行參考、填入參數清單，然後將值傳遞給預存程序。 預存程序會使用這些值，將多個資料列插入資料表中。

```sql
/* Create a table type. */
CREATE TYPE LocationTableType AS TABLE
( LocationName VARCHAR(50)
, CostRate INT );
GO

/* Create a procedure to receive data for the table-valued parameter. */
CREATE PROCEDURE usp_InsertProductionLocation
    @TVP LocationTableType READONLY
    AS
    SET NOCOUNT ON
    INSERT INTO [AdventureWorks2012].[Production].[Location]
       ([Name]
       , [CostRate]
       , [Availability]
       , [ModifiedDate])
    SELECT *, 0, GETDATE()
    FROM @TVP;
GO

/* Declare a variable that references the type. */
DECLARE @LocationTVP
AS LocationTableType;

/* Add data to the table variable. */
INSERT INTO @LocationTVP (LocationName, CostRate)
    SELECT [Name], 0.00
    FROM
    [AdventureWorks2012].[Person].[StateProvince];

/* Pass the table variable data to a stored procedure. */
EXEC usp_InsertProductionLocation @LocationTVP;
GO
```

##### <a name="h-using-an-output-cursor-parameter"></a>H. 使用 OUTPUT 資料指標參數

下列範例會使用 OUTPUT 資料指標參數，將某個資料指標 (對程序而言，其為本機資料指標) 傳遞回呼叫的批次、程序或觸發程序。

首先，建立宣告的程序，然後在 `Currency` 資料表上開啟資料指標：

```sql
CREATE PROCEDURE dbo.uspCurrencyCursor
    @CurrencyCursor CURSOR VARYING OUTPUT
AS
    SET NOCOUNT ON;
    SET @CurrencyCursor = CURSOR
    FORWARD_ONLY STATIC FOR
      SELECT CurrencyCode, Name
      FROM Sales.Currency;
    OPEN @CurrencyCursor;
GO
```

接著，執行宣告本機資料指標變數的批次、執行程序將資料指標指派給本機變數，然後從資料指標提取資料列。

```sql
DECLARE @MyCursor CURSOR;
EXEC dbo.uspCurrencyCursor @CurrencyCursor = @MyCursor OUTPUT;
WHILE (@@FETCH_STATUS = 0)
BEGIN;
    FETCH NEXT FROM @MyCursor;
END;
CLOSE @MyCursor;
DEALLOCATE @MyCursor;
GO
```

### <a name="modifying-data-by-using-a-stored-procedure"></a><a name="Modify"></a> 使用預存程序修改資料

本節範例將示範如何在程序的定義中包含資料操作語言 (DML) 陳述式，藉以在資料表或檢視表中插入或修改資料。

#### <a name="i-using-update-in-a-stored-procedure"></a>I. 在預存程序中使用 UPDATE

下列範例會在預存程序中使用 UPDATE 陳述式。 此程序會採用一個輸入參數 `@NewHours` 和一個輸出參數 `@RowCount`。 UPDATE 陳述式內會使用 `@NewHours` 參數值，以更新資料表 `HumanResources.Employee` 中的資料行 `VacationHours`。 `@RowCount` 輸出參數是用來將受影響的資料列數目傳回給區域變數。 SET 子句會使用 CASE 運算式，以條件方式判斷針對 `VacationHours` 所設定的值。 按照時數支付薪資給員工時 (`SalariedFlag` = 0)，`VacationHours` 會設定為目前的時數加上 `@NewHours` 中指定的值，否則 `VacationHours` 會設定為 `@NewHours` 中指定的值。

```sql
CREATE PROCEDURE HumanResources.Update_VacationHours
@NewHours SMALLINT, @Rowcount INT OUTPUT
AS
SET NOCOUNT ON;
UPDATE HumanResources.Employee
SET VacationHours =
    ( CASE
        WHEN SalariedFlag = 0 THEN VacationHours + @NewHours
        ELSE @NewHours
        END
    )
WHERE CurrentFlag = 1;
SET @Rowcount = @@rowcount;

GO
DECLARE @Rowcount INT
EXEC HumanResources.Update_VacationHours 40, @Rowcount OUTPUT
PRINT @Rowcount;
```

### <a name="error-handling"></a><a name="Error"></a> 錯誤處理

本節範例將示範如何處理在執行預存程序時可能會發生的錯誤。

#### <a name="j-using-trycatch"></a>J. 使用 TRY...CATCH

下列範例使用 TRY...CATCH 建構傳回預存程序執行期間所攔截到的錯誤資訊。

```sql
CREATE PROCEDURE Production.uspDeleteWorkOrder ( @WorkOrderID INT )
AS
SET NOCOUNT ON;
BEGIN TRY
  BEGIN TRANSACTION
  -- Delete rows from the child table, WorkOrderRouting, for the specified work order.
    DELETE FROM Production.WorkOrderRouting
    WHERE WorkOrderID = @WorkOrderID;
  -- Delete the rows from the parent table, WorkOrder, for the specified work order.
    DELETE FROM Production.WorkOrder
    WHERE WorkOrderID = @WorkOrderID;
  COMMIT
END TRY

BEGIN CATCH
  -- Determine if an error occurred.
  IF @@TRANCOUNT > 0
    ROLLBACK

  -- Return the error information.
  DECLARE @ErrorMessage NVARCHAR(4000), @ErrorSeverity INT;
  SELECT @ErrorMessage = ERROR_MESSAGE(),@ErrorSeverity = ERROR_SEVERITY();
  RAISERROR(@ErrorMessage, @ErrorSeverity, 1);
END CATCH;

GO
EXEC Production.uspDeleteWorkOrder 13;

/* Intentionally generate an error by reversing the order in which rows
   are deleted from the parent and child tables. This change does not
   cause an error when the procedure definition is altered, but produces
   an error when the procedure is executed.
*/
ALTER PROCEDURE Production.uspDeleteWorkOrder ( @WorkOrderID INT )
AS

BEGIN TRY
  BEGIN TRANSACTION
  -- Delete the rows from the parent table, WorkOrder, for the specified work order.
    DELETE FROM Production.WorkOrder
    WHERE WorkOrderID = @WorkOrderID;

  -- Delete rows from the child table, WorkOrderRouting, for the specified work order.
    DELETE FROM Production.WorkOrderRouting
    WHERE WorkOrderID = @WorkOrderID;
  COMMIT TRANSACTION
END TRY

BEGIN CATCH
  -- Determine if an error occurred.
  IF @@TRANCOUNT > 0
    ROLLBACK TRANSACTION

  -- Return the error information.
  DECLARE @ErrorMessage NVARCHAR(4000), @ErrorSeverity INT;
  SELECT @ErrorMessage = ERROR_MESSAGE(),@ErrorSeverity = ERROR_SEVERITY();
  RAISERROR(@ErrorMessage, @ErrorSeverity, 1);
END CATCH;
GO
-- Execute the altered procedure.
EXEC Production.uspDeleteWorkOrder 15;

DROP PROCEDURE Production.uspDeleteWorkOrder;
```

### <a name="obfuscating-the-procedure-definition"></a><a name="Encrypt"></a> 模糊化程序定義

本節範例將示範如何模糊化預存程序的定義。

#### <a name="k-using-the-with-encryption-option"></a>K. 使用 WITH ENCRYPTION 選項

下列範例會建立 `HumanResources.uspEncryptThis` 程序。

**適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本、SQL Database。

```sql
CREATE PROCEDURE HumanResources.uspEncryptThis
WITH ENCRYPTION
AS
    SET NOCOUNT ON;
    SELECT BusinessEntityID, JobTitle, NationalIDNumber,
        VacationHours, SickLeaveHours
    FROM HumanResources.Employee;
GO
```

`WITH ENCRYPTION` 選項會在查詢系統目錄或使用中繼資料函數時模糊化程序的定義，如下列範例所示。

執行 `sp_helptext`：

```sql
EXEC sp_helptext 'HumanResources.uspEncryptThis';
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

`The text for object 'HumanResources.uspEncryptThis' is encrypted.`

直接查詢 `sys.sql_modules` 目錄檢視：

```sql
SELECT definition FROM sys.sql_modules
WHERE object_id = OBJECT_ID('HumanResources.uspEncryptThis');
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]

```
definition
--------------------------------
NULL
```

### <a name="forcing-the-procedure-to-recompile"></a><a name="Recompile"></a> 強制程序重新編譯

本節範例將會使用 WITH RECOMPILE 子句強制程序在每次執行時重新編譯。

#### <a name="l-using-the-with-recompile-option"></a>L. 使用 WITH RECOMPILE 選項

如果提供給程序的參數不是典型的參數，且不應將新的執行計畫快取或儲存在記憶體中時，`WITH RECOMPILE` 子句十分好用。

```sql
IF OBJECT_ID ( 'dbo.uspProductByVendor', 'P' ) IS NOT NULL
    DROP PROCEDURE dbo.uspProductByVendor;
GO
CREATE PROCEDURE dbo.uspProductByVendor @Name VARCHAR(30) = '%'
WITH RECOMPILE
AS
    SET NOCOUNT ON;
    SELECT v.Name AS 'Vendor name', p.Name AS 'Product name'
    FROM Purchasing.Vendor AS v
    JOIN Purchasing.ProductVendor AS pv
      ON v.BusinessEntityID = pv.BusinessEntityID
    JOIN Production.Product AS p
      ON pv.ProductID = p.ProductID
    WHERE v.Name LIKE @Name;
```

### <a name="setting-the-security-context"></a><a name="Security"></a> 設定安全性內容

本節範例將使用 EXECUTE AS 子句設定用以執行預存程序的安全性內容。

#### <a name="m-using-the-execute-as-clause"></a>M. 使用 EXECUTE AS 子句

下列範例顯示利用 [EXECUTE AS](../../t-sql/statements/execute-as-clause-transact-sql.md) 子句來指定可執行程序的安全性內容。 在這個範例中，選項 `CALLER` 指定可以在呼叫程序之使用者的內容中執行程序。

```sql
CREATE PROCEDURE Purchasing.uspVendorAllInfo
WITH EXECUTE AS CALLER
AS
    SET NOCOUNT ON;
    SELECT v.Name AS Vendor, p.Name AS 'Product name',
      v.CreditRating AS 'Rating',
      v.ActiveFlag AS Availability
    FROM Purchasing.Vendor v
    INNER JOIN Purchasing.ProductVendor pv
      ON v.BusinessEntityID = pv.BusinessEntityID
    INNER JOIN Production.Product p
      ON pv.ProductID = p.ProductID
    ORDER BY v.Name ASC;
GO
```

#### <a name="n-creating-custom-permission-sets"></a>N. 建立自訂權限集合

下列範例會使用 EXECUTE AS 建立資料庫作業的自訂權限。 某些作業 (例如 TRUNCATE TABLE) 沒有可授與的權限。 將 TRUNCATE TABLE 陳述式併入預存程序，並且將該程序指定成以有權修改資料表的使用者身分執行，即可針對您授與程序之 EXECUTE 權限的使用者，擴充截斷資料表的權限。

```sql
CREATE PROCEDURE dbo.TruncateMyTable
WITH EXECUTE AS SELF
AS TRUNCATE TABLE MyDB..MyTable;
```

## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]

### <a name="o-create-a-stored-procedure-that-runs-a-select-statement"></a>O. 建立預存程序以執行 SELECT 陳述式

這個範例會說明建立和執行程序的基本語法。 當執行批次時，CREATE PROCEDURE 必須是第一個陳述式。 例如，若要在 [!INCLUDE[ssawPDW](../../includes/ssawpdw-md.md)] 中建立下列預存程序，請先設定資料庫內容，然後再執行 CREATE PROCEDURE 陳述式。

```sql
-- Uses AdventureWorksDW database

--Run CREATE PROCEDURE as the first statement in a batch.
CREATE PROCEDURE Get10TopResellers
AS
BEGIN
    SELECT TOP (10) r.ResellerName, r.AnnualSales
    FROM DimReseller AS r
    ORDER BY AnnualSales DESC, ResellerName ASC;
END
;
GO

--Show 10 Top Resellers
EXEC Get10TopResellers;
```

## <a name="see-also"></a>另請參閱

- [ALTER PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-procedure-transact-sql.md)
- [流程控制語言 &#40;Transact-SQL&#41;](~/t-sql/language-elements/control-of-flow.md)
- [資料指標](../../relational-databases/cursors.md)
- [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)
- [DECLARE @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-local-variable-transact-sql.md)
- [DROP PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-procedure-transact-sql.md)
- [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)
- [EXECUTE AS &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-transact-sql.md)
- [預存程序 &#40;Database Engine&#41;](../../relational-databases/stored-procedures/stored-procedures-database-engine.md)
- [sp_procoption &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-procoption-transact-sql.md)
- [sp_recompile &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-recompile-transact-sql.md)
- [sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)
- [sys.parameters &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-parameters-transact-sql.md)
- [sys.procedures &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-procedures-transact-sql.md)
- [sys.sql_expression_dependencies &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md)
- [sys.assembly_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-assembly-modules-transact-sql.md)
- [sys.numbered_procedures &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-numbered-procedures-transact-sql.md)
- [sys.numbered_procedure_parameters &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-numbered-procedure-parameters-transact-sql.md)
- [OBJECT_DEFINITION &#40;Transact-SQL&#41;](../../t-sql/functions/object-definition-transact-sql.md)
- [建立預存程序](../../relational-databases/stored-procedures/create-a-stored-procedure.md)
- [使用資料表值參數 &#40;資料庫引擎&#41;](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)
- [sys.dm_sql_referenced_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referenced-entities-transact-sql.md)
- [sys.dm_sql_referencing_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-sql-referencing-entities-transact-sql.md)  
