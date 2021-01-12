---
title: DECLARE @local_variable (Transact-SQL) | Microsoft Docs
description: 使用 DECLARE 定義區域變數的 Transact-SQL 參考，可用於批次或程序中。
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DECLARE
- DECLARE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- table-valued parameters
- variables [SQL Server], declaring
- DECLARE statement
- declaring variables
ms.assetid: d1635ebb-f751-4de1-8bbc-cae161f90821
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ce70338a29d271151a0431a157290e5c09241f42
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102484"
---
# <a name="declare-local_variable-transact-sql"></a>DECLARE @local_variable (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  變數是利用 DECLARE 陳述式宣告在批次或程序的主體中，並利用 SET 或 SELECT 陳述式來指派值。 資料指標變數可以是利用這個陳述式來宣告，且可以搭配其他與資料指標相關的陳述式來使用。 在宣告之後，所有變數都會初始化成 NULL，除非在宣告中有提供值。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
-- Syntax for SQL Server and Azure SQL Database  
  
DECLARE   
{   
    { @local_variable [AS] data_type  [ = value ] }  
  | { @cursor_variable_name CURSOR }  
} [,...n]   
| { @table_variable_name [AS] <table_type_definition> }   
  
<table_type_definition> ::=   
     TABLE ( { <column_definition> | <table_constraint> } [ ,...n] )   
  
<column_definition> ::=   
     column_name { scalar_data_type | AS computed_column_expression }  
     [ COLLATE collation_name ]   
     [ [ DEFAULT constant_expression ] | IDENTITY [ (seed ,increment ) ] ]   
     [ ROWGUIDCOL ]   
     [ <column_constraint> ]   
  
<column_constraint> ::=   
     { [ NULL | NOT NULL ]   
     | [ PRIMARY KEY | UNIQUE ]   
     | CHECK ( logical_expression )   
     | WITH ( <index_option > )  
     }   
  
<table_constraint> ::=   
     { { PRIMARY KEY | UNIQUE } ( column_name [ ,...n] )   
     | CHECK ( search_condition )   
     }   
  
<index_option> ::=  
See CREATE TABLE for index option syntax.  
  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
DECLARE   
{{ @local_variable [AS] data_type } [ =value [ COLLATE <collation_name> ] ] } [,...n]  
  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
@*local_variable*  
 此為變數的名稱。 變數名稱的開頭必須是 at (@) 符號。 區域變數名稱必須遵循[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
*data_type*  
 這是任何系統提供的 Common Language Runtime (CLR) 使用者定義資料表類型或別名資料類型。 變數的資料類型不可以是 **text**、**ntext** 或 **image**。  
  
 如需系統資料類型的詳細資訊，請參閱[資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)。 如需有關 CLR 使用者自訂類型或別名資料類型的詳細資訊，請參閱 [CREATE TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/create-type-transact-sql.md)。  
  
 =*value*  
 以內嵌方式指派值給變數。 此值可以是常數或運算式，但是它必須符合變數宣告類型，或是必須可隱含轉換成該類型。 如需詳細資訊，請參閱[運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
@*cursor_variable_name*  
 這是資料指標變數的名稱。 資料指標變數名稱的開頭必須是 at (@) 符號，且必須符合識別碼的規則。  
  
CURSOR  
 指定變數是本機資料指標變數。  
  
@*table_variable_name*  
 這是 **table** 類型的變數名稱。 變數名稱的開頭必須是 at (@) 符號，且必須符合識別碼的規則。  
  
<table_type_definition>  
定義 **table** 資料類型。 資料表宣告包括資料行定義、名稱、資料類型和條件約束。 允許使用的條件約束類型只有 PRIMARY KEY、UNIQUE、NULL 和 CHECK。 如果規則或預設定義繫結至別名資料類型，就無法利用別名資料類型來當做資料行純量資料類型。
  
\<table_type_definiton> 是在 CREATE TABLE 中用來定義資料表的部分資訊。 這裡包括元素和必要定義。 如需詳細資訊，請參閱 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)。  
  
 *n*  
 這是一個預留位置，表示可以指定多個變數，且可以指派這些變數的值。 當宣告 **table** 變數時，**table** 變數必須是 DECLARE 陳述式所宣告的唯一變數。  
  
 *column_name*  
 這是資料表中之資料行的名稱。  
  
 *scalar_data_type*  
 指定資料行是一種純量資料類型。  
  
 *computed_column_expression*  
 這是定義計算資料行值的運算式。 它是從運算式中，利用相同資料表中其他資料行計算而得。 例如，計算資料行的定義可以是 **cost** AS **price \* qty**。運算式可以是非計算的資料行名稱、常數、內建函式、變數，或這些項目由一或多個運算子連接的任何組合。 運算式不能是子查詢或使用者定義函數。 運算式不能參考 CLR 使用者定義類型。  
  
 [ COLLATE *collation_name*]  
 指定資料行的定序。 *collation_name* 可以是 Windows 定序名稱，也可以是 SQL 定序名稱，而且只適用於 **char**、**varchar**、**text**、**nchar**、**nvarchar** 和 **ntext** 等資料類型的資料行。 若未指定，便會將使用者定義資料類型的定序指派給這個資料行 (如果資料行是使用者定義資料類型)，否則，便會指派目前資料庫的定序。  
  
 如需有關 Windows 和 SQL 定序名稱的詳細資訊，請參閱 [COLLATE &#40;Transact-SQL&#41;](~/t-sql/statements/collations.md)。  
  
 DEFAULT  
 指定在插入期間未明確提供值時，提供給資料行的值。 除了定義為 **timestamp** 或含有 IDENTITY 屬性的資料行之外，任何資料行都可以套用 DEFAULT 定義。 當卸除資料表時，便會移除 DEFAULT 定義。 預設值只能使用常數值 (如字元字串)、系統函數 (如 SYSTEM_USER()) 或 NULL。 若要維護與舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的相容性，您可以將條件約束名稱指派給 DEFAULT。  
  
 *constant_expression*  
 這是用來當做資料行預設值的常數、NULL 或系統函數。  
  
 IDENTITY  
 指出新資料行是識別欄位。 當新資料列加入資料表時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會提供資料行的唯一累加值。 識別欄位通常用來結合 PRIMARY KEY 條件約束一起使用，當做資料表的唯一資料列識別碼。 可以將 IDENTITY 屬性指派給 **tinyint**、**smallint**、**int**、**decimal(p,0)** 或 **numeric(p,0)** 資料行。 每份資料表都只能建立一個識別欄位。 繫結的預設值和 DEFAULT 條件約束無法搭配識別欄位使用。 您必須同時指定種子和遞增，或同時不指定這兩者。 如果同時不指定這兩者，預設值便是 (1,1)。  
  
 *seed*  
 這是載入資料表的第一個資料列所用的值。  
  
 *increment*  
 這是加入先前載入的資料列之識別值的累加值。  
  
 ROWGUIDCOL  
 指出新資料行是一個資料列全域唯一識別碼資料行。 每個資料表只能有一個 **uniqueidentifier** 資料行指定為 ROWGUIDCOL 資料行。 ROWGUIDCOL 屬性只能指派給 **uniqueidentifier** 資料行。  
  
 NULL | NOT NULL  
 指出變數中是否允許 null。 預設值是 NULL。  
  
 PRIMARY KEY  
 這是一個條件約束，它利用唯一索引來強制執行一個或多個給定資料行的實體完整性。 每份資料表都只能建立一個 PRIMARY KEY 條件約束。  
  
 UNIQUE  
 這是一個條件約束，它利用唯一索引來提供一個或多個給定資料行的實體完整性。 一份資料表可以有多個 UNIQUE 條件約束。  
  
 CHECK  
 這是一個條件約束，藉由限制可能輸入一個或多個資料行的值，強制執行範圍完整性。  
  
 *logical_expression*  
 這是一個傳回 TRUE 或 FALSE 的邏輯運算式。  
  
## <a name="remarks"></a>備註  
 批次或程序通常會利用變數來當做 WHILE、LOOP 或 IF...ELSE 區塊的計數器。  
  
 變數只能用在運算式中，不能用來取代物件名稱或關鍵字。 若要建構動態 SQL 陳述式，請使用 EXECUTE。  
  
 本機變數的範圍是宣告它的批次。  
 
 資料表變數不一定會常駐記憶體。 在記憶體壓力下，屬於資料表變數的分頁可以推送到 tempdb。
  
 下列陳述式可以將目前指派了資料指標的資料指標變數當做一項來源來參考：  
  
-   CLOSE 陳述式。  
  
-   DEALLOCATE 陳述式。  
  
-   FETCH 陳述式。  
  
-   OPEN 陳述式。  
  
-   定位 DELETE 或 UPDATE 陳述式。  
  
-   SET CURSOR 變數陳述式 (在右側)。  
  
 在所有的這些陳述式中，如果參考的資料指標變數存在，但目前未配置資料指標給它，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 便會引發錯誤。 如果所參考的資料指標變數不存在，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 便會產生其他類型之未宣告的變數所產生的相同錯誤。  
  
 資料指標變數：  
  
-   可以是資料指標類型或另一個資料指標變數的目標。 如需詳細資訊，請參閱 [SET @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/set-local-variable-transact-sql.md)。  
  
-   如果資料指標變數目前未指派任何資料指標，就可以在 EXECUTE 陳述式中，將它當做輸出資料指標參數的目標來參考。  
  
-   應該視為指向資料指標的指標。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-declare"></a>A. 使用 DECLARE  
 下列範例會利用名稱為 `@find` 的本機變數來擷取開頭是 `Man` 的所有姓氏的連絡資訊。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @find VARCHAR(30);   
/* Also allowed:   
DECLARE @find VARCHAR(30) = 'Man%';   
*/  
SET @find = 'Man%';   
SELECT p.LastName, p.FirstName, ph.PhoneNumber  
FROM Person.Person AS p   
JOIN Person.PersonPhone AS ph ON p.BusinessEntityID = ph.BusinessEntityID  
WHERE LastName LIKE @find;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
LastName            FirstName               Phone
------------------- ----------------------- -------------------------
Manchepalli         Ajay                    1 (11) 500 555-0174
Manek               Parul                   1 (11) 500 555-0146
Manzanares          Tomas                   1 (11) 500 555-0178
  
(3 row(s) affected)
```  
  
### <a name="b-using-declare-with-two-variables"></a>B. 使用 DECLARE 與兩個變數  
 下列範例會擷取在北美洲銷售地區且年度銷售額至少 $2,000,000 的 [!INCLUDE[ssSampleDBCoFull](../../includes/sssampledbcofull-md.md)] 銷售代表姓名。  
  
```sql  
USE AdventureWorks2012;  
GO  
SET NOCOUNT ON;  
GO  
DECLARE @Group nvarchar(50), @Sales MONEY;  
SET @Group = N'North America';  
SET @Sales = 2000000;  
SET NOCOUNT OFF;  
SELECT FirstName, LastName, SalesYTD  
FROM Sales.vSalesPerson  
WHERE TerritoryGroup = @Group and SalesYTD >= @Sales;  
```  
  
### <a name="c-declaring-a-variable-of-type-table"></a>C. 宣告類型資料表的變數  
 下列範例會建立一個 `table` 變數來儲存 UPDATE 陳述式的 OUTPUT 子句所指定的值。 之後的兩個 `SELECT` 陳述式會傳回 `@MyTableVar` 中的值，以及 `Employee` 資料表中更新作業的結果。 請注意，`INSERTED.ModifiedDate` 資料行中的結果不同於 `Employee` 資料表中 `ModifiedDate` 資料行的值。 這是因為將 `AFTER UPDATE` 值更新成目前日期的 `ModifiedDate` 觸發程序是定義在 `Employee` 資料表上。 不過，從 `OUTPUT` 傳回的資料行會反映引發觸發程序之前的資料。 如需詳細資訊，請參閱 [OUTPUT 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/output-clause-transact-sql.md)。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @MyTableVar TABLE(  
    EmpID INT NOT NULL,  
    OldVacationHours INT,  
    NewVacationHours INT,  
    ModifiedDate DATETIME);  
UPDATE TOP (10) HumanResources.Employee  
SET VacationHours = VacationHours * 1.25   
OUTPUT INSERTED.BusinessEntityID,  
       DELETED.VacationHours,  
       INSERTED.VacationHours,  
       INSERTED.ModifiedDate  
INTO @MyTableVar;  
--Display the result set of the table variable.  
SELECT EmpID, OldVacationHours, NewVacationHours, ModifiedDate  
FROM @MyTableVar;  
GO  
--Display the result set of the table.  
--Note that ModifiedDate reflects the value generated by an  
--AFTER UPDATE trigger.  
SELECT TOP (10) BusinessEntityID, VacationHours, ModifiedDate  
FROM HumanResources.Employee;  
GO  
```  
  
### <a name="d-declaring-a-variable-of-user-defined-table-type"></a>D. 宣告使用者定義資料表類型的變數  
 下列範例會建立資料表值參數或稱為 `@LocationTVP` 的資料表變數。 這需要稱為 `LocationTableType` 的對應使用者定義資料表類型。 如需有關如何建立使用者定義資料表類型的詳細資訊，請參閱 [CREATE TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/create-type-transact-sql.md)。 如需詳細資訊，請參閱[使用資料表值參數 &#40;Database Engine&#41;](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)。  
  
```sql  
DECLARE @LocationTVP   
AS LocationTableType;  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-declare"></a>E. 使用 DECLARE  
 下列範例會利用名稱為 `@find` 的本機變數來擷取開頭是 `Walt` 的所有姓氏的連絡資訊。  
  
```sql  
-- Uses AdventureWorks  
  
DECLARE @find VARCHAR(30);  
/* Also allowed:   
DECLARE @find VARCHAR(30) = 'Man%';  
*/  
SET @find = 'Walt%';  
  
SELECT LastName, FirstName, Phone  
FROM DimEmployee   
WHERE LastName LIKE @find;  
```  
  
### <a name="f-using-declare-with-two-variables"></a>F. 使用 DECLARE 與兩個變數  
 下列範例會擷取使用變數來指定 `DimEmployee` 資料表中員工的名字和姓氏。  
  
```sql  
-- Uses AdventureWorks  
  
DECLARE @lastName VARCHAR(30), @firstName VARCHAR(30);  
  
SET @lastName = 'Walt%';  
SET @firstName = 'Bryan';  
  
SELECT LastName, FirstName, Phone  
FROM DimEmployee   
WHERE LastName LIKE @lastName AND FirstName LIKE @firstName;  
```  
  
## <a name="see-also"></a>另請參閱  
 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [table &#40;Transact-SQL&#41;](../../t-sql/data-types/table-transact-sql.md)   
 [比較具類型的 XML 與不具類型的 XML](../../relational-databases/xml/compare-typed-xml-to-untyped-xml.md)  
  
  




