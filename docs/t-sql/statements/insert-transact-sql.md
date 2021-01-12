---
description: INSERT (Transact-SQL)
title: INSERT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/21/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- INSERT_TSQL
- INSERT
dev_langs:
- TSQL
helpviewer_keywords:
- inserting multiple rows
- user-defined types [SQL Server], inserting values
- DML [SQL Server], INSERT statement
- bulk load [SQL Server]
- row additions [SQL Server], INSERT statement
- inserting rows
- table value constructor [SQL Server]
- adding data
- INSERT statement [SQL Server], about INSERT statement
- INSERT statement [SQL Server]
- UDTs [SQL Server], inserting values
- adding rows
- INSERT INTO statement
- data manipulation language [SQL Server], INSERT statement
- inserting data
ms.assetid: 1054c76e-0fd5-4131-8c07-a6c5d024af50
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6cccb11c84b9b5354d418dae19557f7f76e027cf
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095728"
---
# <a name="insert-transact-sql"></a>INSERT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]


在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的資料表或檢視表中加入一個或多個資料列。 如需範例，請參閱[範例](#InsertExamples)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  

[ WITH <common_table_expression> [ ,...n ] ]  
INSERT   
{  
        [ TOP ( expression ) [ PERCENT ] ]   
        [ INTO ]   
        { <object> | rowset_function_limited   
          [ WITH ( <Table_Hint_Limited> [ ...n ] ) ]  
        }  
    {  
        [ ( column_list ) ]   
        [ <OUTPUT Clause> ]  
        { VALUES ( { DEFAULT | NULL | expression } [ ,...n ] ) [ ,...n     ]   
        | derived_table   
        | execute_statement  
        | <dml_table_source>  
        | DEFAULT VALUES   
        }  
    }  
}  
[;]  
  
<object> ::=  
{   
    [ server_name . database_name . schema_name .   
      | database_name .[ schema_name ] .   
      | schema_name .   
    ]  
  table_or_view_name  
}  
  
<dml_table_source> ::=  
    SELECT <select_list>  
    FROM ( <dml_statement_with_output_clause> )   
      [AS] table_alias [ ( column_alias [ ,...n ] ) ]  
    [ WHERE <search_condition> ]  
        [ OPTION ( <query_hint> [ ,...n ] ) ]  
```  
  
```syntaxsql
-- External tool only syntax  

INSERT   
{  
    [BULK]  
    { database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }  
    ( <column_definition> )  
    [ WITH (  
        [ [ , ] CHECK_CONSTRAINTS ]  
        [ [ , ] FIRE_TRIGGERS ]  
        [ [ , ] KEEP_NULLS ]  
        [ [ , ] KILOBYTES_PER_BATCH = kilobytes_per_batch ]  
        [ [ , ] ROWS_PER_BATCH = rows_per_batch ]  
        [ [ , ] ORDER ( { column [ ASC | DESC ] } [ ,...n ] ) ]  
        [ [ , ] TABLOCK ]  
    ) ]  
}  
  
[; ] <column_definition> ::=  
 column_name <data_type>  
    [ COLLATE collation_name ]  
    [ NULL | NOT NULL ]  
  
<data type> ::=   
[ type_schema_name . ] type_name   
    [ ( precision [ , scale ] | max ]  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  

INSERT INTO { database_name.schema_name.table_name | schema_name.table_name | table_name }
    [ ( column_name [ ,...n ] ) ]  
    {   
      VALUES ( { NULL | expression } )  
      | SELECT <select_criteria>  
    }  
    [ OPTION ( <query_option> [ ,...n ] ) ]  
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 WITH \<common_table_expression>  
 指定定義在 INSERT 陳述式範圍內的暫存具名結果集，也稱為通用資料表運算式。 這個結果集是從 SELECT 陳述式衍生而來。 如需詳細資訊，請參閱 [WITH common_table_expression &#40;Transact-SQL&#41;](../../t-sql/queries/with-common-table-expression-transact-sql.md)。  
  
 TOP (*expression*) [ PERCENT ]  
 指定將插入的隨機資料列數或百分比。 *expression* 可以是一個數字，也可以是資料列的百分比。 如需詳細資訊，請參閱 [TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md)。  
  
 INTO  
 這是一個選擇性的關鍵字，您可以在 INSERT 和目標資料表之間使用它。  
  
 *server_name*  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 這是資料表或檢視表所在之連結伺服器的名稱。 您可將 *server_name* 指定為 [連結的伺服器](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)名稱，或使用 [OPENDATASOURCE](../../t-sql/functions/opendatasource-transact-sql.md) 函式來指定。  
  
 若將 *server_name* 指定為連結的伺服器，*database_name* 和 *schema_name* 都為必要項目。 若使用 OPENDATASOURCE 來指定 *server_name*，*database_name* 和 *schema_name* 可能無法套用至所有資料來源，並會受限於存取遠端物件之 OLE DB 提供者的功能。  
  
 *database_name*  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 這是資料庫的名稱。  
  
 *schema_name*  
 這是資料表或檢視表所屬的結構描述名稱。  
  
 *table_or view_name*  
 這是將接收資料之資料表或檢視表的名稱。  
  
 在 [table](../../t-sql/data-types/table-transact-sql.md) 變數的範圍內，您可以將它作為 INSERT 陳述式中的資料表來源。  
  
 *table_or_view_name* 所參考的檢視必須能夠更新，而且只能參考該檢視 FROM 子句中的單一基底資料表。 例如，在多資料表檢視中使用 INSERT 時，其使用的 *column_list* 只能參考單一基底資料表的各個資料行。 如需可更新檢視的詳細資訊，請參閱 [CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)。  
  
 *rowset_function_limited*  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 這是 [OPENQUERY](../../t-sql/functions/openquery-transact-sql.md) 或 [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) 函式。 這些函數的使用方式受限於存取遠端物件之 OLE DB 提供者的功能。  
  
 WITH ( \<table_hint_limited> [... *n* ] )  
 指定目標資料表允許使用的一個或多個資料表提示。 WITH 關鍵字和括號都是必要的。  
  
 不允許使用 READPAST、NOLOCK 和 READUNCOMMITTED。 如需資料表提示的詳細資訊，請參閱[資料表提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 未來的版本將移除指定 INSERT 陳述式的目標資料表之 HOLDLOCK、SERIALIZABLE、READCOMMITTED、REPEATABLEREAD 或 UPDLOCK 提示的功能。 這些提示不會影響 INSERT 陳述式的效能。 請避免在新的開發工作中使用它們，並規劃修改目前在使用它們的應用程式。  
  
 指定 INSERT 陳述式目標資料表之 TABLOCK 提示的效果，與指定 TABLOCKX 提示相同。 獨佔鎖定是在資料表上取得的。  
  
 (*column_list*)  
 這是要插入資料的一個或多個資料行所組成的清單。 *column_list* 必須以括弧括住，並以逗號分隔。  
  
 如果資料行不在 *column_list* 中，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 必須能夠依據資料行定義來提供值；否則，便無法載入這個資料列。 如果資料行符合下列條件，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會自動提供資料行值：  
  
-   具有 IDENTITY 屬性。 使用下一個累加識別值。  
  
-   有預設值。 使用資料行的預設值。  
  
-   具有 **timestamp** 資料類型。 使用目前的時間戳記值。  
  
-   可為 Null。 使用 Null 值。  
  
-   是計算資料行。 使用計算的值。  
  
當您將明確的值插入識別欄位時，必須使用 *column_list*，而且資料表的 SET IDENTITY_INSERT 選項必須是 ON。  
  
OUTPUT 子句  
 在插入作業中，傳回插入的資料列。 這些結果可以傳回給處理應用程式或插入資料表或資料表變數，以便進一步處理。  
  
 OUTPUT 子句不支援 DML 陳述式 (其參考本機資料分割檢視、分散式資料分割檢視或遠端資料表)，或包含 *execute_statement* 的 INSERT 陳述式。 OUTPUT INTO 子句不支援含有 \<dml_table_source> 子句的 INSERT 陳述式。 如需此子句的引數和行為詳細資訊，請參閱 [OUTPUT 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)。
  
 VALUES  
 導入要插入的資料值清單。 *column_list* (如果有指定) 或資料表中的每個資料行，都必須有一個資料值。 這份值清單必須括在括號中。  
  
 如果值清單中的值與資料表中的資料行順序不同，或每個資料表資料行並未各有一個值，就必須使用 *column_list* 來明確指定儲存每個內送值的資料行。  
  
 您可以使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 資料列建構函式 (也稱為資料表值建構函式)，在單一 INSERT 陳述式中指定多個資料列。 資料列建構函式是由單一 VALUES 子句所組成，其中包含括號所括住的多值清單，而且會以逗號分隔。 如需詳細資訊，請參閱[資料表值建構函式 &#40;Transact-SQL&#41;](../../t-sql/queries/table-value-constructor-transact-sql.md)。  
  
 DEFAULT  
 強制 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 載入定義給資料行的預設值。 如果資料行的預設值不存在，而且資料行允許 Null 值，就會插入 NULL。 如果是以 **timestamp** 資料類型來定義的資料行，就會插入下一個時間戳記值。 DEFAULT 對識別欄位無效。  
  
 *expression*  
 這是一個常數、變數或運算式。 此運算式不能包含 EXECUTE 陳述式。  
  
 參考 Unicode 字元資料類型 **nchar**、**nvarchar** 及 **ntext** 時，'*expression*' 的前面應該要有大寫字母 'N'。 如果沒有指定 'N'，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將字串轉換成對應至資料庫預設定序或資料行的字碼頁。 在此字碼頁中找不到的任何字元都會遺失。  
  
 *derived_table*  
 這是傳回要載入資料表之資料列的任何有效 SELECT 陳述式。 SELECT 陳述式不能包含通用資料表運算式 (CTE)。  
  
 *execute_statement*  
 這是任何隨著 SELECT 或 READTEXT 陳述式而傳回資料的有效 EXECUTE 陳述式。 如需詳細資訊，請參閱 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)。  
  
 INSERT...EXEC 陳述式中不能指定 EXECUTE 陳述式的 RESULT SETS 選項。  
  
 如果搭配使用 *execute_statement* 與 INSERT，則每個結果集都必須相容於資料表或 *column_list* 中的資料行。  
  
 您可以使用 *execute_statement*，以在相同伺服器或遠端伺服器中執行預存程序。 執行遠端伺服器中的程序，而且結果集會傳回本機伺服器，且載入本機伺服器的資料表中。 在分散式交易中，如果連線已啟用 Multiple Active Result Set (MARS)，即無法對回送的連結伺服器發出 *execute_statement*。  
  
 如果 *execute_statement* 要以 READTEXT 陳述式傳回資料，則每個 READTEXT 陳述式最多可以傳回 1 MB (1024 KB) 的資料。 *execute_statement* 也可搭配擴充程序使用。 *execute_statement* 會插入擴充程序主要執行緒所傳回的資料；但不會插入主要執行緒以外之執行緒的輸出。  
  
 您無法將資料表值參數指定為 INSERT EXEC 陳述式的目標。不過，您可以在 INSERT EXEC 字串或預存程序中，將它指定為來源。 如需詳細資訊，請參閱[使用資料表值參數 &#40;Database Engine&#41;](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)。  
  
 \<dml_table_source>  
 指定插入目標資料表中的資料列就是 INSERT、UPDATE、DELETE 或 MERGE 陳述式的 OUTPUT 子句所傳回的資料列 (可選擇由 WHERE 子句篩選)。 如果指定 \<dml_table_source>，則外部 INSERT 陳述式的目標必須符合下列限制： 
  
-   它必須是基底資料表，而不是檢視表。  
  
-   它不能是遠端資料表。  
  
-   它不能有任何定義的觸發程序。  
  
-   它不能參與任何主索引鍵-外部索引鍵關聯性。  
  
-   它不能參與合併式複寫或是異動複寫的可更新訂閱。  
  
 資料庫的相容性層級必須設定為 100 以上。 如需詳細資訊，請參閱 [OUTPUT 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)。  
  
 \<select_list>  
 這是逗號分隔的清單，可指定要插入之 OUTPUT 子句所傳回的資料行。 \<select_list> 中的資料行必須與插入值的目標資料行相容。 \<select_list> 不能參考彙總函式或 TEXTPTR。 
  
> [!NOTE]  
>  不論在 \<dml_statement_with_output_clause> 中對 SELECT 清單所列的變數做何變更，這些變數都會參考其原始值。  
  
 \<dml_statement_with_output_clause>  
 這是有效的 INSERT、UPDATE、DELETE 或 MERGE 陳述式，可在 OUTPUT 子句中傳回受影響的資料列。 此陳述式不能包含 WITH 子句，也不能以遠端資料表或資料分割檢視表為目標。 如果指定了 UPDATE 或 DELETE，它不能是以資料指標為基礎的 UPDATE 或 DELETE。 來源資料列不能當做巢狀 DML 陳述式來參考。  
  
 WHERE \<search_condition>  
 這是包含有效 \<search_condition> 的任何 WHERE 子句，可篩選 \<dml_statement_with_output_clause> 傳回的資料列。 如需詳細資訊，請參閱[搜尋條件 &#40;Transact-SQL&#41;](../../t-sql/queries/search-condition-transact-sql.md)。 當 \<search_condition> 用於此內容時，其不能包含子查詢、可執行資料存取的純量使用者定義函式、彙總函式、TEXTPTR 或是全文檢索搜尋述詞。 
  
 DEFAULT VALUES  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 強制新的資料列包含定義給每個資料行的預設值。  
  
 BULK  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 由外部工具用來上傳二進位資料流。 這個選項無法與 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]、SQLCMD 或 OSQL 等工具或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client 等資料存取應用程式開發介面搭配使用。  
  
 FIRE_TRIGGERS  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定在二進位資料流上傳作業期間，執行目的地資料表上所定義的任何插入觸發程序。 如需詳細資訊，請參閱 [BULK INSERT&#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)。  
  
 CHECK_CONSTRAINTS  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定在二進位資料流上傳作業期間，必須檢查目標資料表或檢視表的所有條件約束。 如需詳細資訊，請參閱 [BULK INSERT&#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)。  
  
 KEEPNULLS  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定在二進位資料流上傳作業期間，空白資料行應該保留 Null 值。 如需詳細資訊，請參閱[大量匯入期間保留 Null 或使用預設值 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)。  
  
 KILOBYTES_PER_BATCH = kilobytes_per_batch  
 以 *kilobytes_per_batch* 指定每一批資料的大約 KB 數。 如需詳細資訊，請參閱 [BULK INSERT&#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)。  
  
 ROWS_PER_BATCH =*rows_per_batch*  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指出二進位資料流中大約的資料列數。 如需詳細資訊，請參閱 [BULK INSERT&#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)。  
  
> [!NOTE]
>  如果未提供資料行清單，則會引發語法錯誤。  

## <a name="remarks"></a>備註  
如需將資料插入 SQL Graph 資料表的特定資訊，請參閱 [INSERT (SQL Graph)](../../t-sql/statements/insert-sql-graph.md)。 

## <a name="best-practices"></a>最佳做法  
 您可以使用 @@ROWCOUNT 函式，將插入的資料列數目傳回給用戶端應用程式。 如需詳細資訊，請參閱 [@@ROWCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/rowcount-transact-sql.md)。  
  
### <a name="best-practices-for-bulk-importing-data"></a>大量匯入資料的最佳做法  
  
#### <a name="using-insert-intoselect-to-bulk-import-data-with-minimal-logging-and-parallelism"></a>使用 INSERT INTO...SELECT 以最低限度記錄和平行處理原則來大量匯入資料 
您可以搭配使用 `INSERT INTO <target_table> SELECT <columns> FROM <source_table>` 最低限度記錄，有效率地將大量資料列從某份資料表 (例如暫存表格) 傳送至另一份資料表。 最低限度記錄可以改善此陳述式的效能並且降低交易期間作業填滿可用交易記錄空間的可能性。  
  
此陳述式的最低限度記錄具有下列需求：  
-   資料庫的復原模式設為簡單或大量記錄。  
-   目標資料表是空白或非空白的堆積。  
-   目標資料表未用於複寫。  
-   已針對目標資料表指定 `TABLOCK` 提示。  
  
由於 MERGE 陳述式中的插入動作而插入堆積的資料列也可以採用最低限度記錄。  
  
與 `BULK INSERT` 陳述式 (其持有較不嚴格的大量更新 (BU) 鎖定) 不同，具 `TABLOCK` 提示的 `INSERT INTO … SELECT` 對資料表持有獨佔 (X) 鎖定。 這代表您無法使用同時執行的多個插入作業來插入資料列。 

不過，從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和資料庫相容性層級 130 開始，`INSERT INTO … SELECT` 陳述式可以在插入堆積或叢集資料行存放區索引 (CCI) 時，以平行方式執行。 使用 `TABLOCK` 提示時，可以平行插入。  

上述陳述式的平行處理原則有下列需求，其類似於最低限度記錄的需求：  
-   目標資料表是空白或非空白的堆積。  
-   目標資料表具有叢集資料行存放區索引 (CCI)，但沒有非叢集索引。  
-   目標資料表沒有 IDENTITY_INSERT 設定為 OFF 的識別欄位。  
-   已針對目標資料表指定 `TABLOCK` 提示。

在滿足最低限度記錄和平行插入需求的案例中，這兩個改進功能將會共同運作，以確保資料載入作業的最大輸送量。

> [!NOTE]
> 針對本機暫存資料表 (以 # 前置詞識別) 和全域暫存資料表 (以 ## 前置詞識別) 所進行的插入，也可以透過使用 TABLOCK 提示來啟用平行處理原則。
  
#### <a name="using-openrowset-and-bulk-to-bulk-import-data"></a>使用 OPENROWSET 和 BULK 來大量匯入資料  
 OPENROWSET 函數可接受下列資料表提示，這些提示會針對大量載入最佳化提供 INSERT 陳述式：  
  
-   `TABLOCK` 提示可以將插入作業的記錄數目最小化。 資料庫的復原模式必須設定為簡單或大量記錄，而且目標資料表不得用於複寫。 如需詳細資訊，請參閱[在大量匯入中採用最低限度記錄的必要條件](../../relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import.md)。  
-   `TABLOCK` 提示可以啟用平行插入作業。 目標資料表是沒有非叢集索引的堆積或叢集資料行存放區索引 (CCI)，且目標資料表不能有已指定的識別欄位。  
-   `IGNORE_CONSTRAINTS` 提示可以暫時停用 FOREIGN KEY 和 CHECK 條件約束檢查。  
-   `IGNORE_TRIGGERS` 提示可以暫時停用觸發程序執行。  
-   `KEEPDEFAULTS` 提示允許在資料記錄缺少資料行的值時，改為插入資料表資料行的預設值 (若有的話)，而不是插入 NULL。  
-   `KEEPIDENTITY` 提示允許將已匯入資料檔中的識別值用於目標資料表的識別欄位。  
  
這些最佳化類似於 `BULK INSERT` 命令所提供的最佳化。 如需詳細資訊，請參閱[資料表提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)。  
  
## <a name="data-types"></a>資料類型  
 當您插入資料列時，請考量以下資料類型行為：  
  
-   如果要將值載入 **char**、**varchar** 或 **varbinary** 資料類型的資料行，系統會依據建立資料表時為資料行定義的 SET ANSI_PADDING 設定，來決定填補或截斷尾端空白 (**char** 和 **varchar** 是空格，**varbinary** 是零)。 如需詳細資訊，請參閱 [SET ANSI_PADDING &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-padding-transact-sql.md)。  
  
     下表顯示 SET ANSI_PADDING OFF 的預設作業。  
  
    |資料類型|預設作業|  
    |---------------|-----------------------|  
    |**char**|值填補空格到定義的資料行寬度。|  
    |**varchar**|移除尾端空格到最後一個非空格字元，或到只由空格組成之字串的一個空格字元。|  
    |**varbinary**|移除尾端零。|  
  
-   如果將空字串 (' ') 載入 **varchar** 或 **text** 資料類型的資料行，預設作業即為載入零長度字串。  
  
-   將 Null 值插入 **text** 或 **image** 資料行時，並不會建立有效的文字指標，也不會預先配置 8 KB 文字頁面。  
  
-   若是以 **uniqueidentifier** 資料類型建立的資料行，其會儲存特殊格式的 16 位元組二進位值。 不同於識別欄位，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 不會自動為 **uniqueidentifier** 資料類型的資料行產生值。 在插入作業期間，**uniqueidentifier** 資料行可以使用 **uniqueidentifier** 資料類型的變數以及 *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* 格式的字串常數 (36 個字元，包括連字號；其中 *x* 是 0-9 或 a-f 範圍內的十六進位數字)。 例如，6F9619FF-8B86-D011-B42D-00C04FC964FF 即為 **uniqueidentifier** 變數或資料行的有效值。 請使用 [NEWID()](../../t-sql/functions/newid-transact-sql.md) 函式來取得全域唯一識別碼 (GUID)。  
  
### <a name="inserting-values-into-user-defined-type-columns"></a>將值插入使用者定義型別資料行  
 您可以利用下列方式，在使用者定義型別資料行中插入值：  
  
-   提供使用者定義型別的值。  
  
-   只要使用者定義型別支援從這個類型進行隱含或明確的轉換，便在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統資料類型中提供一個值。 下列範例會顯示如何從字串進行明確的轉換，以便在使用者定義的 `Point` 類型資料行中插入一個值。  
  
    ```sql
    INSERT INTO Cities (Location)  
    VALUES ( CONVERT(Point, '12.3:46.2') );  
    ```  
  
     您不需要執行明確的轉換，便可以提供二進位值，因為所有使用者定義型別都隱含從二進位轉換的功能。  
  
-   呼叫傳回使用者定義型別之值的使用者定義函數。 下列範例會利用使用者定義函數 `CreateNewPoint()` 來建立使用者定義型別 `Point` 的新值，且將這個值插入 `Cities` 資料表中。  
  
    ```sql
    INSERT INTO Cities (Location)  
    VALUES ( dbo.CreateNewPoint(x, y) );  
    ```  
  
## <a name="error-handling"></a>錯誤處理  
 您可以在 TRY...CATCH 建構中指定 INSERT 陳述式，實作此陳述式的錯誤處理。  
  
 如果 INSERT 陳述式違反條件約束或規則，或它有不相容於資料行資料類型的值，陳述式便會失敗，而且系統會傳回一則錯誤訊息。  
  
 如果 INSERT 利用 SELECT 或 EXECUTE 來載入多個資料列，當載入的值違反規則或條件約束時，陳述式便會停止運作，而且不會載入任何資料列。  
  
 當 INSERT 陳述式在運算式評估期間發生算術錯誤 (溢位、除以零或範圍錯誤) 時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會依照 SET ARITHABORT 設為 ON 的方式來處理這些錯誤。 此時，批次會停止運作，而且系統會傳回錯誤訊息。 在運算式評估期間，當 SET ARITHABORT 和 SET ANSI_WARNINGS 是 OFF 時，如果 INSERT、DELETE 或 UPDATE 陳述式發現算術錯誤、溢位、除以零或範圍錯誤，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會插入或更新 NULL 值。 如果目標資料行不可設為 Null，插入或更新動作就會失敗而且使用者會收到錯誤。  
  
## <a name="interoperability"></a>互通性  
 當 `INSTEAD OF` 觸發程序定義於針對資料表或檢視的 INSERT 動作時，系統會執行觸發程序，而不是 INSERT 陳述式。 如需 `INSTEAD OF` 觸發程序的詳細資訊，請參閱 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)。  
  
## <a name="limitations-and-restrictions"></a>限制事項  
 當您將值插入遠端資料表時，如果並未完整指定所有資料行的所有值，您必須識別要插入指定值的資料行。  
  
 搭配 INSERT 使用 TOP 時，不會以任何順序排列參考的資料列，也不可以直接在這個陳述式中指定 ORDER BY 子句。 如果您要使用 TOP 以具有意義的時序來插入資料列，則 TOP 必須與 Subselect 陳述式中指定的 ORDER BY 子句一起使用。 請參閱本主題稍後的＜範例＞一節。
 
若是搭配使用 SELECT 與 ORDER BY 來填入資料列的 INSERT 查詢，其可保證識別值的計算方式，但不能保證資料列的插入順序。

在平行資料倉儲中，除非也指定了 TOP 或，否則 ORDER BY 子句在 VIEWS、CREATE TABLE AS SELECT、INSERT SELECT、內嵌函式、衍生資料表、子查詢及通用資料表運算式中均無效。
  
## <a name="logging-behavior"></a>記錄行為  
 除了搭配使用 BULK 關鍵字與 OPENROWSET 函式或是使用 `INSERT INTO <target_table> SELECT <columns> FROM <source_table>` 以外，系統一定都會完整記錄 INSERT 陳述式。 這些作業可以進行最低限度記錄。 如需詳細資訊，請參閱本主題前面的＜大量載入資料的最佳做法＞一節。  
  
## <a name="security"></a>安全性  
 在連結的伺服器連接期間，傳送端伺服器會提供一個登入名稱與密碼來代表它本身，以連接到接收端伺服器。 若要讓這個連線有所作用，您必須使用 [sp_addlinkedsrvlogin](../../relational-databases/system-stored-procedures/sp-addlinkedsrvlogin-transact-sql.md) 在連結伺服器之間建立登入對應。  
  
 當您使用 OPENROWSET(BULK...) 時，一定要了解 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 如何處理模擬。 如需詳細資訊，請參閱[使用 BULK INSERT 或 OPENROWSET&#40;BULK...&#41; 匯入大量資料 &#40;SQL Server&#41;](../../relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server.md) 中的＜安全性考量＞。  
  
### <a name="permissions"></a>權限  
 需要目標資料表的 INSERT 權限。  
  
 INSERT 權限預設會設定給 `sysadmin` 固定伺服器角色的成員、`db_owner` 和 `db_datawriter` 固定資料庫角色的成員，以及資料表擁有者。 `sysadmin`、`db_owner` 和 `db_securityadmin` 角色的成員及資料表擁有者可將權限移轉給其他使用者。  
  
 若要搭配 OPENROWSET 函式 BULK 選項來執行 INSERT，您必須是 `sysadmin` 或 `bulkadmin` 固定伺服器角色的成員。  
  
##  <a name="examples"></a><a name="InsertExamples"></a> 範例  
  
|類別|代表性語法元素|  
|--------------|------------------------------|  
|[基本語法](#BasicSyntax)|INSERT • 資料表值建構函式|  
|[處理資料行值](#ColumnValues)|IDENTITY • NEWID • 預設值 • 使用者定義型別|  
|[從其他資料表插入資料](#OtherTables)|INSERT...SELECT • INSERT...EXECUTE • WITH 通用資料表運算式 • TOP • OFFSET FETCH|  
|[指定標準資料表以外的目標物件](#TargetObjects)|檢視 • 資料表變數|  
|[將資料列插入遠端資料表](#RemoteTables)|連結的伺服器 • OPENQUERY 資料列集函數 • OPENDATASOURCE 資料列集函數|  
|[大量載入資料表或資料檔案中的資料](#BulkLoad)|INSERT...SELECT • OPENROWSET 函式|  
|[使用提示來覆寫查詢最佳化工具的預設行為](#TableHints)|資料表提示|  
|[擷取 INSERT 陳述式的結果](#CaptureResults)|OUTPUT 子句|  
  
###  <a name="basic-syntax"></a><a name="BasicSyntax"></a> 基本語法  
 本節的範例會使用所需的最少語法來示範 INSERT 陳述式的基本功能。  
  
#### <a name="a-inserting-a-single-row-of-data"></a>A. 插入單一資料列  
 下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `Production.UnitMeasure` 資料表中插入一個資料列。 此資料表中的資料行為 `UnitMeasureCode`、`Name` 和 `ModifiedDate`。 由於所有資料行的值均已提供，並按資料表中資料行的相同順序列出；因此，您不需要在資料行清單中指定資料行名稱。  
  
```sql
INSERT INTO Production.UnitMeasure  
VALUES (N'FT', N'Feet', '20080414');  
```  
  
#### <a name="b-inserting-multiple-rows-of-data"></a>B. 插入多個資料列  
 下列範例會在單一 INSERT 陳述式中使用[資料表值建構函式](../../t-sql/queries/table-value-constructor-transact-sql.md)，將三個資料列插入至 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `Production.UnitMeasure` 資料表。 由於提供了所有資料行的值，而且依照資料表中資料行的相同順序來列出它們，因此，不需要在資料行清單中指定資料行名稱。  
  
```sql
INSERT INTO Production.UnitMeasure  
VALUES (N'FT2', N'Square Feet ', '20080923'), (N'Y', N'Yards', '20080923')
    , (N'Y3', N'Cubic Yards', '20080923');  
```  
  
#### <a name="c-inserting-data-that-is-not-in-the-same-order-as-the-table-columns"></a>C. 插入與資料表資料行順序不同的資料  
 下列範例會利用資料行清單來明確指定插入每個資料行的值。 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `Production.UnitMeasure` 資料表的資料行順序是 `UnitMeasureCode`、`Name`、`ModifiedDate`，但在 *column_list* 中不會依照該順序列出這些資料行。  
  
```sql
INSERT INTO Production.UnitMeasure (Name, UnitMeasureCode,  
    ModifiedDate)  
VALUES (N'Square Yards', N'Y2', GETDATE());  
```  
  
###  <a name="handling-column-values"></a><a name="ColumnValues"></a> 處理資料行值  
 本節中的範例會示範將值插入至資料行的方法，這些資料行定義了 IDENTITY 屬性、DEFAULT 值、**uniqueidentifier** 資料類型，或使用者定義類型的資料行。  
  
#### <a name="d-inserting-data-into-a-table-with-columns-that-have-default-values"></a>D. 將資料插入包含有預設值之資料行的資料表  
 下列範例會顯示如何將資料列插入含有自動產生值或有預設值的資料行之資料表中。 `Column_1` 是一個計算資料行，它會將字串與插入 `column_2` 的值串連起來，自動產生某個值。 `Column_2` 會使用預設條件約束來定義。 如果此資料行並未指定值，就會使用預設值。 `Column_3` 定義了 **rowversion** 資料類型，該資料類型會自動產生唯一的遞增二進位數字。 `Column_4` 不會自動產生值。 如果未指定這個資料行的值，就會插入 NULL。 INSERT 陳述式會插入包含部分 (而非全部) 資料行值的資料列。 在最後一個 INSERT 陳述式中，並未指定任何資料行，只會使用 DEFAULT VALUES 子句插入預設值。  
  
```sql
CREATE TABLE dbo.T1   
(  
    column_1 AS 'Computed column ' + column_2,   
    column_2 varchar(30)   
        CONSTRAINT default_name DEFAULT ('my column default'),  
    column_3 rowversion,  
    column_4 varchar(40) NULL  
);  
GO  
INSERT INTO dbo.T1 (column_4)   
    VALUES ('Explicit value');  
INSERT INTO dbo.T1 (column_2, column_4)   
    VALUES ('Explicit value', 'Explicit value');  
INSERT INTO dbo.T1 (column_2)   
    VALUES ('Explicit value');  
INSERT INTO T1 DEFAULT VALUES;   
GO  
SELECT column_1, column_2, column_3, column_4  
FROM dbo.T1;  
GO  
```  
  
#### <a name="e-inserting-data-into-a-table-with-an-identity-column"></a>E. 將資料插入含識別資料行的資料表  
 下列範例會顯示將資料插入識別欄位的不同方法。 前兩個 INSERT 陳述式允許可用於產生新資料列的識別值。 第三個 INSERT 陳述式利用 SET IDENTITY_INSERT 陳述式來覆寫資料行的 IDENTITY 屬性，且會將明確的值插入識別欄位中。  
  
```sql
CREATE TABLE dbo.T1 ( column_1 int IDENTITY, column_2 VARCHAR(30));  
GO  
INSERT T1 VALUES ('Row #1');  
INSERT T1 (column_2) VALUES ('Row #2');  
GO  
SET IDENTITY_INSERT T1 ON;  
GO  
INSERT INTO T1 (column_1,column_2)   
    VALUES (-99, 'Explicit identity value');  
GO  
SELECT column_1, column_2  
FROM T1;  
GO  
```  
  
#### <a name="f-inserting-data-into-a-uniqueidentifier-column-by-using-newid"></a>F. 利用 NEWID() 將資料插入 uniqueidentifier 資料行  
 下列範例會使用 [NEWID](../../t-sql/functions/newid-transact-sql.md)() 函式來取得 `column_2` 的 GUID。 不同於識別欄位，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 並不會自動產生 [uniqueidentifier](../../t-sql/data-types/uniqueidentifier-transact-sql.md) 資料類型的資料行值，如第二個 `INSERT` 陳述式所示。  
  
```sql
CREATE TABLE dbo.T1   
(  
    column_1 int IDENTITY,   
    column_2 uniqueidentifier,  
);  
GO  
INSERT INTO dbo.T1 (column_2)   
    VALUES (NEWID());  
INSERT INTO T1 DEFAULT VALUES;   
GO  
SELECT column_1, column_2  
FROM dbo.T1;  
```  
  
#### <a name="g-inserting-data-into-user-defined-type-columns"></a>G. 將資料插入使用者定義型別的資料行  
 下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式會將三個資料列插入 `PointValue` 資料表的 `Points` 資料行。 這個資料行會使用 [CLR 使用者定義類型](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md) (UDT)。 `Point` 資料類型包括公開為 UDT 屬性的 X 及 Y 整數值。 您必須使用 CAST 或 CONVERT 函數，將以逗號分隔的 X 及 Y 值轉換為 `Point` 類型。 前兩個陳述式使用 CONVERT 函式，將字串值轉換為 `Point` 類型，而第三個陳述式則使用 CAST 函式。 如需詳細資訊，請參閱[操作 UDT 資料](../../relational-databases/clr-integration-database-objects-user-defined-types/working-with-user-defined-types-manipulating-udt-data.md)。  
  
```sql
INSERT INTO dbo.Points (PointValue) VALUES (CONVERT(Point, '3,4'));  
INSERT INTO dbo.Points (PointValue) VALUES (CONVERT(Point, '1,5'));  
INSERT INTO dbo.Points (PointValue) VALUES (CAST ('1,99' AS Point));  
```  
  
###  <a name="inserting-data-from-other-tables"></a><a name="OtherTables"></a> 從其他資料表插入資料  
 本節的範例示範將一個資料表的資料列插入另一個資料表的方法。  
  
#### <a name="h-using-the-select-and-execute-options-to-insert-data-from-other-tables"></a>H. 使用 SELECT 和 EXECUTE 選項插入其他資料表的資料  
 下列範例會示範如何使用 INSERT...SELECT 或 INSERT...EXECUTE 將一個資料表中的資料插入另一個資料表。 每個方法都是以多資料表的 SELECT 陳述式為基礎，而該 SELECT 陳述式在資料行清單中包括一個運算式及一個常值。  
  
 第一個 INSERT 陳述式會使用 SELECT 陳述式，以從 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的來源資料表 (`Employee`、`SalesPerson` 和 `Person`) 衍生資料，並將結果集儲存在 `EmployeeSales` 資料表中。 第二個 INSERT 陳述式會使用 EXECUTE 子句來呼叫包含 SELECT 陳述式的預存程序，而第三個 INSERT 陳述式會使用 EXECUTE 子句將 SELECT 陳述式當做常值字串來參考。  
  
```sql
CREATE TABLE dbo.EmployeeSales  
( DataSource   varchar(20) NOT NULL,  
  BusinessEntityID   varchar(11) NOT NULL,  
  LastName     varchar(40) NOT NULL,  
  SalesDollars money NOT NULL  
);  
GO  
CREATE PROCEDURE dbo.uspGetEmployeeSales   
AS   
    SET NOCOUNT ON;  
    SELECT 'PROCEDURE', sp.BusinessEntityID, c.LastName,   
        sp.SalesYTD   
    FROM Sales.SalesPerson AS sp    
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.BusinessEntityID LIKE '2%'  
    ORDER BY sp.BusinessEntityID, c.LastName;  
GO  
--INSERT...SELECT example  
INSERT INTO dbo.EmployeeSales  
    SELECT 'SELECT', sp.BusinessEntityID, c.LastName, sp.SalesYTD   
    FROM Sales.SalesPerson AS sp  
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.BusinessEntityID LIKE '2%'  
    ORDER BY sp.BusinessEntityID, c.LastName;  
GO  
--INSERT...EXECUTE procedure example  
INSERT INTO dbo.EmployeeSales   
EXECUTE dbo.uspGetEmployeeSales;  
GO  
--INSERT...EXECUTE('string') example  
INSERT INTO dbo.EmployeeSales   
EXECUTE   
('  
SELECT ''EXEC STRING'', sp.BusinessEntityID, c.LastName,   
    sp.SalesYTD   
    FROM Sales.SalesPerson AS sp   
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.BusinessEntityID LIKE ''2%''  
    ORDER BY sp.BusinessEntityID, c.LastName  
');  
GO  
--Show results.  
SELECT DataSource,BusinessEntityID,LastName,SalesDollars  
FROM dbo.EmployeeSales;  
```  
  
#### <a name="i-using-with-common-table-expression-to-define-the-data-inserted"></a>I. 使用 WITH 通用資料表運算式定義插入的資料  
 下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中建立 `NewEmployee` 資料表。 通用資料表運算式 (`EmployeeTemp`) 會定義一個或多個資料表中要插入 `NewEmployee` 資料表的資料列。 INSERT 陳述式會在通用資料表運算式中參考此資料行。  
  
```sql
CREATE TABLE HumanResources.NewEmployee  
(  
    EmployeeID int NOT NULL,  
    LastName nvarchar(50) NOT NULL,  
    FirstName nvarchar(50) NOT NULL,  
    PhoneNumber Phone NULL,  
    AddressLine1 nvarchar(60) NOT NULL,  
    City nvarchar(30) NOT NULL,  
    State nchar(3) NOT NULL,   
    PostalCode nvarchar(15) NOT NULL,  
    CurrentFlag Flag  
);  
GO  
WITH EmployeeTemp (EmpID, LastName, FirstName, Phone,   
                   Address, City, StateProvince,   
                   PostalCode, CurrentFlag)  
AS (SELECT   
       e.BusinessEntityID, c.LastName, c.FirstName, pp.PhoneNumber,  
       a.AddressLine1, a.City, sp.StateProvinceCode,   
       a.PostalCode, e.CurrentFlag  
    FROM HumanResources.Employee e  
        INNER JOIN Person.BusinessEntityAddress AS bea  
        ON e.BusinessEntityID = bea.BusinessEntityID  
        INNER JOIN Person.Address AS a  
        ON bea.AddressID = a.AddressID  
        INNER JOIN Person.PersonPhone AS pp  
        ON e.BusinessEntityID = pp.BusinessEntityID  
        INNER JOIN Person.StateProvince AS sp  
        ON a.StateProvinceID = sp.StateProvinceID  
        INNER JOIN Person.Person as c  
        ON e.BusinessEntityID = c.BusinessEntityID  
    )  
INSERT INTO HumanResources.NewEmployee   
    SELECT EmpID, LastName, FirstName, Phone,   
           Address, City, StateProvince, PostalCode, CurrentFlag  
    FROM EmployeeTemp;  
GO  
```  
  
#### <a name="j-using-top-to-limit-the-data-inserted-from-the-source-table"></a>J. 使用 TOP 限制從來源資料表中插入的資料  
 下列範例會建立 `EmployeeSales` 資料表，而且會將 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `HumanResources.Employee` 資料表的前 5 名隨機員工的姓名和今年到目前的銷售資料插入其中。 INSERT 陳述式會選擇 `SELECT` 陳述式所傳回的任 5 個資料列。 OUTPUT 子句會顯示插入到 `EmployeeSales` 資料表的資料列。 請注意，SELECT 陳述式中的 ORDER BY 子句不會用來判斷前 5 名員工。  
  
```sql
CREATE TABLE dbo.EmployeeSales  
( EmployeeID   nvarchar(11) NOT NULL,  
  LastName     nvarchar(20) NOT NULL,  
  FirstName    nvarchar(20) NOT NULL,  
  YearlySales  money NOT NULL  
 );  
GO  
INSERT TOP(5)INTO dbo.EmployeeSales  
    OUTPUT inserted.EmployeeID, inserted.FirstName, 
        inserted.LastName, inserted.YearlySales  
    SELECT sp.BusinessEntityID, c.LastName, c.FirstName, sp.SalesYTD   
    FROM Sales.SalesPerson AS sp  
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.SalesYTD > 250000.00  
    ORDER BY sp.SalesYTD DESC;  
```  
  
 如果您必須使用 TOP 依有意義的時序來插入資料列，就必須搭配子選擇陳述式中指定的 ORDER BY 子句來使用 TOP，如下列範例所示。 OUTPUT 子句會顯示插入到 `EmployeeSales` 資料表的資料列。 請注意，現在插入的前 5 名員工是依據 ORDER BY 子句的結果，而不是隨機資料列。  
  
```sql
INSERT INTO dbo.EmployeeSales  
    OUTPUT inserted.EmployeeID, inserted.FirstName, 
        inserted.LastName, inserted.YearlySales  
    SELECT TOP (5) sp.BusinessEntityID, c.LastName, c.FirstName, sp.SalesYTD   
    FROM Sales.SalesPerson AS sp  
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.SalesYTD > 250000.00  
    ORDER BY sp.SalesYTD DESC;  
```  
  
###  <a name="specifying-target-objects-other-than-standard-tables"></a><a name="TargetObjects"></a> 指定標準資料表以外的目標物件  
 本節的範例示範如何指定檢視表或資料表變數來插入資料列。  
  
#### <a name="k-inserting-data-by-specifying-a-view"></a>K. 指定檢視表以插入資料  
 下列範例會將檢視表名稱指定為目標物件；不過，新資料列會插入基礎基底資料表中。 `INSERT` 陳述式中的值順序必須符合檢視表的資料行順序。 如需詳細資訊，請參閱[透過檢視修改資料](../../relational-databases/views/modify-data-through-a-view.md)。  
  
```sql
CREATE TABLE T1 ( column_1 int, column_2 varchar(30));  
GO  
CREATE VIEW V1 AS   
SELECT column_2, column_1   
FROM T1;  
GO  
INSERT INTO V1   
    VALUES ('Row 1',1);  
GO  
SELECT column_1, column_2   
FROM T1;  
GO  
SELECT column_1, column_2  
FROM V1;  
GO  
```  
  
#### <a name="l-inserting-data-into-a-table-variable"></a>L. 將資料插入資料表變數  
 下列範例會將 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的資料表變數指定為目標物件。  
  
```sql
-- Create the table variable.  
DECLARE @MyTableVar table(  
    LocationID int NOT NULL,  
    CostRate smallmoney NOT NULL,  
    NewCostRate AS CostRate * 1.5,  
    ModifiedDate datetime);  
  
-- Insert values into the table variable.  
INSERT INTO @MyTableVar (LocationID, CostRate, ModifiedDate)  
    SELECT LocationID, CostRate, GETDATE() 
    FROM Production.Location  
    WHERE CostRate > 0;  
  
-- View the table variable result set.  
SELECT * FROM @MyTableVar;  
GO  
```  
  
###  <a name="inserting-rows-into-a-remote-table"></a><a name="RemoteTables"></a> 將資料列插入至遠端資料表  
 本節中的範例會示範如何使用[連結的伺服器](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)或[資料列集函式](../functions/opendatasource-transact-sql.md)來參考遠端資料表，以將資料列插入至遠端資料表。  
  
#### <a name="m-inserting-data-into-a-remote-table-by-using-a-linked-server"></a>M. 使用連結的伺服器將資料插入遠端資料表  
 下列範例會將資料列插入遠端資料表。 此範例一開始會使用 [sp_addlinkedserver](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md) 建立遠端資料來源的連結。 接下來，會將連結的伺服器名稱 `MyLinkServer` 指定為 *server.catalog.schema.object* 格式之四部分物件名稱的一部分。  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
```sql
USE master;  
GO  
-- Create a link to the remote data source.   
-- Specify a valid server name for @datasrc as 'server_name' 
-- or 'server_nameinstance_name'.  
  
EXEC sp_addlinkedserver @server = N'MyLinkServer',  
    @srvproduct = N' ',  
    @provider = N'SQLNCLI',   
    @datasrc = N'server_name',  
    @catalog = N'AdventureWorks2012';  
GO  
```  
  
```sql
-- Specify the remote data source in the FROM clause using a four-part name   
-- in the form linked_server.catalog.schema.object.  
  
INSERT INTO MyLinkServer.AdventureWorks2012.HumanResources.Department (Name, GroupName)  
VALUES (N'Public Relations', N'Executive General and Administration');  
GO  
```  
  
#### <a name="n-inserting-data-into-a-remote-table-by-using-the-openquery-function"></a>N. 使用 OPENQUERY 函數將資料插入遠端資料表  
 下列範例會指定 [OPENQUERY](../../t-sql/functions/openquery-transact-sql.md) 資料列集函式，以將資料列插入至遠端資料表。 上一個範例所建立之連結的伺服器名稱會用於這個範例。  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
```sql
INSERT OPENQUERY (MyLinkServer, 
    'SELECT Name, GroupName 
     FROM AdventureWorks2012.HumanResources.Department')  
VALUES ('Environmental Impact', 'Engineering');  
GO  
```  
  
#### <a name="o-inserting-data-into-a-remote-table-by-using-the-opendatasource-function"></a>O. 使用 OPENDATASOURCE 函數將資料插入遠端資料表  
 下列範例會指定 [OPENDATASOURCE](../../t-sql/functions/opendatasource-transact-sql.md) 資料列集函式，以將資料列插入至遠端資料表。 使用 *server_name* 或 *server_name\instance_name* 格式，為資料來源指定有效的伺服器名稱。  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
```sql
-- Use the OPENDATASOURCE function to specify the remote data source.  
-- Specify a valid server name for Data Source using the format 
-- server_name or server_nameinstance_name.  
  
INSERT INTO OPENDATASOURCE('SQLNCLI',  
    'Data Source= <server_name>; Integrated Security=SSPI')  
    .AdventureWorks2012.HumanResources.Department (Name, GroupName)  
    VALUES (N'Standards and Methods', 'Quality Assurance');  
GO  
```  
  
#### <a name="p-inserting-into-an-external-table-created-using-polybase"></a>P. 插入至以 PolyBase 建立的外部資料表  
 將資料從 SQL Server 匯出至 Hadoop 或 Azure 儲存體。 首先，建立指向目的地檔案或目錄的外部資料表。 然後，使用 INSERT INTO 將資料從本機 SQL Server 資料表匯出至外部資料來源。 如果目的地檔案或目錄不存在，INSERT INTO 陳述式會加以建立，並將 SELECT 陳述式的結果以指定檔案格式匯出至指定位置。  如需詳細資訊，請參閱 [開始使用 PolyBase](../../relational-databases/polybase/polybase-guide.md)。  
  
**適用於**： [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]。  
  
```sql
-- Create an external table.   
CREATE EXTERNAL TABLE [dbo].[FastCustomers2009] (  
        [FirstName] char(25) NOT NULL,   
        [LastName] char(25) NOT NULL,   
        [YearlyIncome] float NULL,   
        [MaritalStatus] char(1) NOT NULL  
)  
WITH (  
        LOCATION='/old_data/2009/customerdata.tbl',  
        DATA_SOURCE = HadoopHDP2,  
        FILE_FORMAT = TextFileFormat,  
        REJECT_TYPE = VALUE,  
        REJECT_VALUE = 0  
);  
  
-- Export data: Move old data to Hadoop while keeping 
-- it query-able via external table.  

INSERT INTO dbo.FastCustomer2009  
SELECT T.* FROM Insured_Customers T1 JOIN CarSensor_Data T2  
ON (T1.CustomerKey = T2.CustomerKey)  
WHERE T2.YearMeasured = 2009 and T2.Speed > 40;  
```  
  
###  <a name="bulk-loading-data-from-tables-or-data-files"></a><a name="BulkLoad"></a> 大量載入資料表或資料檔案中的資料  
 本節的範例示範利用 INSERT 陳述式將資料大量載入資料表中的兩個方法。  
  
#### <a name="q-inserting-data-into-a-heap-with-minimal-logging"></a>Q. 在記錄最少的情況下將資料插入堆積中  
 下列範例會建立新的資料表 (堆積)，然後使用最低限度記錄，將另一個資料表中的資料插入其中。 此範例會假設 `AdventureWorks2012` 資料庫的復原模式設定為 FULL。 為了確保使用最低限度記錄，`AdventureWorks2012` 資料庫的復原模式會在插入資料列之前設定為 BULK_LOGGED，然後在 INSERT INTO...SELECT 陳述式之後重設為 FULL。 此外，針對目標資料表 `Sales.SalesHistory` 指定了 TABLOCK 提示。 這樣做可確保此陳述式會在交易記錄中使用最小的空間並有效率地執行作業。  
  
```sql
-- Create the target heap.  
CREATE TABLE Sales.SalesHistory(  
    SalesOrderID int NOT NULL,  
    SalesOrderDetailID int NOT NULL,  
    CarrierTrackingNumber nvarchar(25) NULL,  
    OrderQty smallint NOT NULL,  
    ProductID int NOT NULL,  
    SpecialOfferID int NOT NULL,  
    UnitPrice money NOT NULL,  
    UnitPriceDiscount money NOT NULL,  
    LineTotal money NOT NULL,  
    rowguid uniqueidentifier ROWGUIDCOL  NOT NULL,  
    ModifiedDate datetime NOT NULL );  
GO  
-- Temporarily set the recovery model to BULK_LOGGED.  
ALTER DATABASE AdventureWorks2012  
SET RECOVERY BULK_LOGGED;  
GO  
-- Transfer data from Sales.SalesOrderDetail to Sales.SalesHistory  
INSERT INTO Sales.SalesHistory WITH (TABLOCK)  
    (SalesOrderID,   
     SalesOrderDetailID,  
     CarrierTrackingNumber,   
     OrderQty,   
     ProductID,   
     SpecialOfferID,   
     UnitPrice,   
     UnitPriceDiscount,  
     LineTotal,   
     rowguid,   
     ModifiedDate)  
SELECT * FROM Sales.SalesOrderDetail;  
GO  
-- Reset the recovery model.  
ALTER DATABASE AdventureWorks2012  
SET RECOVERY FULL;  
GO  
```  
  
#### <a name="r-using-the-openrowset-function-with-bulk-to-bulk-load-data-into-a-table"></a>R. 搭配 BULK 使用 OPENROWSET 函數，將資料大量載入資料表  
 下列範例會藉由指定 OPENROWSET 函數，將資料檔案中的資料列插入資料表。 將會指定 IGNORE_TRIGGERS 資料表提示來讓效能最佳化。 如需其他範例，請參閱[使用 BULK INSERT 或 OPENROWSET&#40;BULK...&#41; 來匯入大量資料 &#40;SQL Server&#41;](../../relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server.md)。  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
```sql
INSERT INTO HumanResources.Department WITH (IGNORE_TRIGGERS) (Name, GroupName)  
SELECT b.Name, b.GroupName   
FROM OPENROWSET (  
    BULK 'C:SQLFilesDepartmentData.txt',  
    FORMATFILE = 'C:SQLFilesBulkloadFormatFile.xml',  
    ROWS_PER_BATCH = 15000)AS b ;  
```  
  
###  <a name="overriding-the-default-behavior-of-the-query-optimizer-by-using-hints"></a><a name="TableHints"></a> 使用提示來覆寫查詢最佳化工具的預設行為  
 本節中的範例示範如何使用[資料表提示](../../t-sql/queries/hints-transact-sql-table.md)，在處理 INSERT 陳述式時暫時覆寫查詢最佳化工具的預設行為。  
  
> [!CAUTION]  
>  由於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢最佳化工具通常會選取最好的查詢執行計畫，因此，我們建議資深的開發人員和資料庫管理員只將提示當做最後的解決辦法。  
  
#### <a name="s-using-the-tablock-hint-to-specify-a-locking-method"></a>S. 使用 TABLOCK 提示指定鎖定方法  
 下列範例指定在 Production.Location 資料表上採用獨佔 (X) 鎖定，並且將鎖定保留到 INSERT 陳述式結束為止。  
  
**適用於**：[!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)]、[!INCLUDE[ssSDS_md](../../includes/sssds-md.md)]。  
  
```sql
INSERT INTO Production.Location WITH (XLOCK)  
(Name, CostRate, Availability)  
VALUES ( N'Final Inventory', 15.00, 80.00);  
```  
  
###  <a name="capturing-the-results-of-the-insert-statement"></a><a name="CaptureResults"></a> 擷取 INSERT 陳述式的結果  
 本節中的範例示範如何使用 [OUTPUT 子句](../../t-sql/queries/output-clause-transact-sql.md)傳回 INSERT 陳述式所影響之每個資料列的資訊，或是以該資料列為基礎的運算式。 這些結果可以傳回給負責處理的應用程式，以便用在確認訊息、封存或其他這類應用程式需求等用途上。  
  
#### <a name="t-using-output-with-an-insert-statement"></a>T. 搭配 INSERT 陳述式使用 OUTPUT  
 下列範例會將資料列插入 `ScrapReason` 資料表中，並且利用 `OUTPUT` 子句，將陳述式的結果傳回 `@MyTableVar` 資料表變數。 由於 `ScrapReasonID` 資料行定義了 `IDENTITY` 屬性，因此，`INSERT` 陳述式並未指定這個資料行的值。 不過請注意，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 針對這個資料行所產生的值，會在 `OUTPUT` 資料行的 `INSERTED.ScrapReasonID` 子句中傳回。  
  
```sql
DECLARE @MyTableVar table( NewScrapReasonID smallint,  
                           Name varchar(50),  
                           ModifiedDate datetime);  
INSERT Production.ScrapReason  
    OUTPUT INSERTED.ScrapReasonID, INSERTED.Name, INSERTED.ModifiedDate  
        INTO @MyTableVar  
VALUES (N'Operator error', GETDATE());  
  
--Display the result set of the table variable.  
SELECT NewScrapReasonID, Name, ModifiedDate FROM @MyTableVar;  
--Display the result set of the table.  
SELECT ScrapReasonID, Name, ModifiedDate   
FROM Production.ScrapReason;  
```  
  
#### <a name="u-using-output-with-identity-and-computed-columns"></a>U. 搭配識別和計算資料行使用 OUTPUT  
 下列範例會建立 `EmployeeSales` 資料表，之後再利用含有 SELECT 陳述式的 INSERT 陳述式來擷取來源資料表中的資料，以插入幾個資料列。 `EmployeeSales` 資料表包含一個識別欄位 (`EmployeeID`) 和一個計算資料行 (`ProjectedSales`)。 由於這些值都是 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 在插入作業期間所產生的，因此，這些資料行都不能定義在 `@MyTableVar` 中。  
  
```sql
CREATE TABLE dbo.EmployeeSales  
( EmployeeID   int IDENTITY (1,5)NOT NULL,  
  LastName     nvarchar(20) NOT NULL,  
  FirstName    nvarchar(20) NOT NULL,  
  CurrentSales money NOT NULL,  
  ProjectedSales AS CurrentSales * 1.10   
);  
GO  
DECLARE @MyTableVar table(  
  LastName     nvarchar(20) NOT NULL,  
  FirstName    nvarchar(20) NOT NULL,  
  CurrentSales money NOT NULL  
  );  
  
INSERT INTO dbo.EmployeeSales (LastName, FirstName, CurrentSales)  
  OUTPUT INSERTED.LastName,   
         INSERTED.FirstName,   
         INSERTED.CurrentSales  
  INTO @MyTableVar  
    SELECT c.LastName, c.FirstName, sp.SalesYTD  
    FROM Sales.SalesPerson AS sp  
    INNER JOIN Person.Person AS c  
        ON sp.BusinessEntityID = c.BusinessEntityID  
    WHERE sp.BusinessEntityID LIKE '2%'  
    ORDER BY c.LastName, c.FirstName;  
  
SELECT LastName, FirstName, CurrentSales  
FROM @MyTableVar;  
GO  
SELECT EmployeeID, LastName, FirstName, CurrentSales, ProjectedSales  
FROM dbo.EmployeeSales;  
```  
  
#### <a name="v-inserting-data-returned-from-an-output-clause"></a>V. 插入從 OUTPUT 子句傳回的資料  
 下列範例將擷取從 MERGE 陳述式的 OUTPUT 子句中傳回的資料，並將該資料插入另一個資料表中。 MERGE 陳述式會根據在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `Quantity` 資料表內處理的順序，每天更新 `ProductInventory` 資料表的 `SalesOrderDetail` 資料行。 它也會刪除產品存貨降到 0 的資料列。 此範例會擷取已刪除的資料列，並將其插入另一個資料表 `ZeroInventory`，該資料表會追蹤沒有存貨的產品。  
  
```sql
--Create ZeroInventory table.  
CREATE TABLE Production.ZeroInventory (DeletedProductID int, RemovedOnDate DateTime);  
GO  
  
INSERT INTO Production.ZeroInventory (DeletedProductID, RemovedOnDate)  
SELECT ProductID, GETDATE()  
FROM  
(   MERGE Production.ProductInventory AS pi  
    USING (SELECT ProductID, SUM(OrderQty) FROM Sales.SalesOrderDetail AS sod  
           JOIN Sales.SalesOrderHeader AS soh  
           ON sod.SalesOrderID = soh.SalesOrderID  
           AND soh.OrderDate = '20070401'  
           GROUP BY ProductID) AS src (ProductID, OrderQty)  
    ON (pi.ProductID = src.ProductID)  
    WHEN MATCHED AND pi.Quantity - src.OrderQty <= 0  
        THEN DELETE  
    WHEN MATCHED  
        THEN UPDATE SET pi.Quantity = pi.Quantity - src.OrderQty  
    OUTPUT $action, deleted.ProductID) AS Changes (Action, ProductID)  
WHERE Action = 'DELETE';  
IF @@ROWCOUNT = 0  
PRINT 'Warning: No rows were inserted';  
GO  
SELECT DeletedProductID, RemovedOnDate FROM Production.ZeroInventory;  
```  

#### <a name="w-inserting-data-using-the-select-option"></a>W. 使用 SELECT 選項來插入資料  
 下列範例會示範如何搭配使用 INSERT 陳述式與 SELECT 選項，插入多個資料列的資料。 第一個 `INSERT` 陳述式會使用 `SELECT` 陳述式，直接擷取來源資料表的資料，並將結果集儲存在 `EmployeeTitles` 資料表中。  
  
```sql
CREATE TABLE EmployeeTitles  
( EmployeeKey   INT NOT NULL,  
  LastName     varchar(40) NOT NULL,  
  Title      varchar(50) NOT NULL  
);  
INSERT INTO EmployeeTitles  
    SELECT EmployeeKey, LastName, Title   
    FROM ssawPDW.dbo.DimEmployee  
    WHERE EndDate IS NULL;  
```  
  
#### <a name="x-specifying-a-label-with-the-insert-statement"></a>X. 搭配 INSERT 陳述式指定標籤  
 下列範例示範如何搭配使用 INSERT 陳述式與標籤。  
  
```sql
-- Uses AdventureWorks  
  
INSERT INTO DimCurrency   
VALUES (500, N'C1', N'Currency1')  
OPTION ( LABEL = N'label1' );  
```  
  
#### <a name="y-using-a-label-and-a-query-hint-with-the-insert-statement"></a>Y. 搭配 INSERT 陳述式使用標籤及查詢提示  
 此查詢示範搭配使用 INSERT 陳述式、標籤及查詢聯結提示的基本語法。 向控制節點提交查詢之後，在計算節點上執行的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會於產生 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢計劃時套用雜湊聯結策略。 如需聯結提示及如何使用 OPTION 子句的詳細資訊，請參閱 [OPTION (SQL Server PDW)](../../t-sql/queries/option-clause-transact-sql.md)。  
  
```sql
-- Uses AdventureWorks  
  
INSERT INTO DimCustomer (CustomerKey, CustomerAlternateKey, 
    FirstName, MiddleName, LastName )   
SELECT ProspectiveBuyerKey, ProspectAlternateKey, 
    FirstName, MiddleName, LastName  
FROM ProspectiveBuyer p JOIN DimGeography g ON p.PostalCode = g.PostalCode  
WHERE g.CountryRegionCode = 'FR'  
OPTION ( LABEL = 'Add French Prospects', HASH JOIN);  
```  
  
## <a name="see-also"></a>另請參閱  
 [BULK INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/bulk-insert-transact-sql.md)   
 [DELETE &#40;Transact-SQL&#41;](../../t-sql/statements/delete-transact-sql.md)   
 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)   
 [FROM &#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)   
 [IDENTITY &#40;屬性&#41; &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql-identity-property.md)   
 [NEWID &#40;Transact-SQL&#41;](../../t-sql/functions/newid-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [UPDATE &#40;Transact-SQL&#41;](../../t-sql/queries/update-transact-sql.md)   
 [MERGE &#40;Transact-SQL&#41;](../../t-sql/statements/merge-transact-sql.md)   
 [OUTPUT 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)   
 [使用插入或刪除的資料表](../../relational-databases/triggers/use-the-inserted-and-deleted-tables.md)  
  
