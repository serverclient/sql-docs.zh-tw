---
title: FROM 子句與 JOIN、APPLY、PIVOT (T-SQL)
description: FROM 子句與 JOIN、APPLY、PIVOT (Transact-SQL)
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- JOIN
- FROM_TSQL
- FROM
- JOIN_TSQL
- OUTER_JOIN_TSQL
- INNER_JOIN_TSQL
- CROSS_TSQL
- CROSS_APPLY_TSQL
- APPLY_TSQL
- CROSS_JOIN_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- OUTER APPLY operator
- hints [SQL Server], FROM clause
- SELECT statement [SQL Server], FROM clause
- ISO syntax
- DELETE statement [SQL Server], FROM clause
- CROSS APPLY operator
- FROM clause
- APPLY operator
- joins [SQL Server], FROM clause
- UPDATE statement [SQL Server], FROM clause
- derived tables
ms.assetid: 36b19e68-94f6-4539-aeb1-79f5312e4263
author: VanMSFT
ms.author: vanto
ms.custom: ''
ms.date: 06/01/2019
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 70cda7e45f17bb1dbeeaa69178e0538296572ae7
ms.sourcegitcommit: b652ff2f0f7edbb5bd2f8fdeac56348e4d84f8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98112670"
---
# <a name="from-clause-plus-join-apply-pivot-transact-sql"></a>FROM 子句與 JOIN、APPLY、PIVOT (Transact-SQL)

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa-pdw.md)]

在 Transact-SQL 中，FROM 子句可用於下列陳述式：

- [DELETE](../statements/delete-transact-sql.md)
- [UPDATE](update-transact-sql.md)
- [SELECT](select-transact-sql.md)

SELECT 陳述式通常必須使用 FROM 子句。 例外狀況如下：未列出任何資料表資料行，且唯一列出的項目是常值、變數或算術運算式時。

本文也會說明下列可用於 FROM 子句的關鍵字：

- [JOIN](../../relational-databases/performance/joins.md)
- APPLY
- [PIVOT](from-using-pivot-and-unpivot.md)

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
[ FROM { <table_source> } [ ,...n ] ]   
<table_source> ::=   
{  
    table_or_view_name [ FOR SYSTEM_TIME <system_time> ] [ AS ] table_alias ]   
        [ <tablesample_clause> ]   
        [ WITH ( < table_hint > [ [ , ]...n ] ) ]   
    | rowset_function [ [ AS ] table_alias ]   
        [ ( bulk_column_alias [ ,...n ] ) ]   
    | user_defined_function [ [ AS ] table_alias ]  
    | OPENXML <openxml_clause>   
    | derived_table [ [ AS ] table_alias ] [ ( column_alias [ ,...n ] ) ]   
    | <joined_table>   
    | <pivoted_table>   
    | <unpivoted_table>  
    | @variable [ [ AS ] table_alias ]  
    | @variable.function_call ( expression [ ,...n ] )   
        [ [ AS ] table_alias ] [ (column_alias [ ,...n ] ) ]     
}  
<tablesample_clause> ::=  
    TABLESAMPLE [SYSTEM] ( sample_number [ PERCENT | ROWS ] )   
        [ REPEATABLE ( repeat_seed ) ]   
  
<joined_table> ::=   
{  
    <table_source> <join_type> <table_source> ON <search_condition>   
    | <table_source> CROSS JOIN <table_source>   
    | left_table_source { CROSS | OUTER } APPLY right_table_source   
    | [ ( ] <joined_table> [ ) ]   
}  
<join_type> ::=   
    [ { INNER | { { LEFT | RIGHT | FULL } [ OUTER ] } } [ <join_hint> ] ]  
    JOIN  
  
<pivoted_table> ::=  
    table_source PIVOT <pivot_clause> [ [ AS ] table_alias ]  
  
<pivot_clause> ::=  
        ( aggregate_function ( value_column [ [ , ]...n ])   
        FOR pivot_column   
        IN ( <column_list> )   
    )   
  
<unpivoted_table> ::=  
    table_source UNPIVOT <unpivot_clause> [ [ AS ] table_alias ]  
  
<unpivot_clause> ::=  
    ( value_column FOR pivot_column IN ( <column_list> ) )   
  
<column_list> ::=  
    column_name [ ,...n ]   
  
<system_time> ::=  
{  
       AS OF <date_time>  
    |  FROM <start_date_time> TO <end_date_time>  
    |  BETWEEN <start_date_time> AND <end_date_time>  
    |  CONTAINED IN (<start_date_time> , <end_date_time>)   
    |  ALL  
}  
  
    <date_time>::=  
        <date_time_literal> | @date_time_variable  
  
    <start_date_time>::=  
        <date_time_literal> | @date_time_variable  
  
    <end_date_time>::=  
        <date_time_literal> | @date_time_variable  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
FROM { <table_source> [ ,...n ] }  
  
<table_source> ::=   
{  
    [ database_name . [ schema_name ] . | schema_name . ] table_or_view_name [ AS ] table_or_view_alias 
    [<tablesample_clause>]  
    | derived_table [ AS ] table_alias [ ( column_alias [ ,...n ] ) ]  
    | <joined_table>  
}  
  
<tablesample_clause> ::=
    TABLESAMPLE ( sample_number [ PERCENT ] ) -- Azure Synapse Analytics only  
 
<joined_table> ::=   
{  
    <table_source> <join_type> <table_source> ON search_condition   
    | <table_source> CROSS JOIN <table_source> 
    | left_table_source { CROSS | OUTER } APPLY right_table_source   
    | [ ( ] <joined_table> [ ) ]   
}  
  
<join_type> ::=   
    [ INNER ] [ <join hint> ] JOIN  
    | LEFT  [ OUTER ] JOIN  
    | RIGHT [ OUTER ] JOIN  
    | FULL  [ OUTER ] JOIN  
  
<join_hint> ::=   
    REDUCE  
    | REPLICATE  
    | REDISTRIBUTE  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
\<table_source>  
 指定 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式中使用的資料表、檢視表、資料表變數或衍生資料表來源 (含有別名或不含別名)。 陳述式中最多只能使用 256 個資料表來源 (雖然這項限制會隨著可用記憶體和查詢中之其他運算式的複雜度而改變)。 個別查詢可能無法支援多達 256 個資料表來源。  
  
> [!NOTE]  
>  如果查詢中參考大量資料表，則可能會降低查詢效能。 編譯和最佳化時間也會受其他因素影響。 這些因素包括每個 \<table_source> 上是否有索引和索引檢視表，以及 SELECT 陳述式中 \<select_list> 的大小。  
  
 FROM 關鍵字後面的資料表來源順序不會影響傳回的結果集。 當 FROM 子句中出現重複的名稱時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回錯誤。  
  
 *table_or_view_name*  
 這是資料表或檢視表的名稱。  
  
 如果資料表或檢視表位於同一個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的另一個資料庫中，請使用 *database*.*schema*.*object_name* 格式的完整名稱。  
  
 如果資料表或檢視表位於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之外，請使用 *linked_server*.*catalog*.*schema*.*object* 格式的四部分名稱。 如需詳細資訊，請參閱 [sp_addlinkedserver &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql.md)的資料。 如果四部分的名稱是利用 [OPENDATASOURCE](../../t-sql/functions/opendatasource-transact-sql.md) 函數來建構為名稱的伺服器部分，則該名稱也可用來指定遠端資料表來源。 已指定 OPENDATASOURCE 時，*database_name* 和 *schema_name* 就無法套用至所有資料來源，並且會受到存取遠端物件之 OLE DB 提供者的功能限制。  
  
 [AS] *table_alias*  
 這是 *table_source* 的別名，您可以為了方便而使用它，或是用來區別自我聯結或子查詢中的資料表或檢視表。 別名通常是一個縮短的資料表名稱，可用來參考聯結中之資料表的特定資料行。 如果相同的資料行名稱存在於聯結中的多個資料表中，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會要求資料行名稱必須被資料表名稱、檢視表名稱或別名所限定。 如果已定義別名，就不能使用資料表名稱。  
  
 使用衍生資料表、資料列集、資料表值函數或運算子子句 (例如 PIVOT 或 UNPIVOT) 時，子句尾端所需的 *table_alias* 是所傳回之所有資料行 (包括群組資料行) 的相關資料表名稱。  
  
 WITH (\<table_hint> )  
 指定查詢最佳化工具必須搭配這份資料表，並針對這個陳述式來使用最佳化或鎖定策略。 如需詳細資訊，請參閱[資料表提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)。  
  
*rowset_function*  
**適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本和 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
指定資料列集函數之一 (如 OPENROWSET)，利用該函數來傳回可使用的物件，而不是傳回資料表參考。 如需有關資料列集函數清單的詳細資訊，請參閱[資料列集函數 &#40;Transact-SQL&#41;](../functions/opendatasource-transact-sql.md)。  
  
 使用 OPENROWSET 和 OPENQUERY 函數來指定遠端物件時，主要取決於存取此物件之 OLE DB 提供者的功能。  
  
 *bulk_column_alias*  
**適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本和 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
 這是一個用以取代結果集中之資料行名稱的選擇性別名。 資料行別名只能用在搭配 BULK 選項使用 OPENROWSET 函數的 SELECT 陳述式中。 當您使用 *bulk_column_alias* 時，請依照與檔案中資料行相同的順序，指定每個資料表資料行的別名。  
  
> [!NOTE]  
>  這個別名會覆寫 XML 格式檔之 COLUMN 元素中的 NAME 屬性。  
  
 *user_defined_function*  
 指定資料表值函式。  
  
OPENXML \<openxml_clause>  
**適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本和 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
透過 XML 文件提供資料列集的檢視。 如需詳細資訊，請參閱 [OPENXML &#40;Transact-SQL&#41;](../../t-sql/functions/openxml-transact-sql.md)。  
  
 *derived_table*  
 這是從資料庫中擷取資料列的子查詢。 *derived_table* 可用來作為外部查詢的輸入。  
  
 *derived_table* 可以使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 資料表值建構函式功能來指定多個資料列。 例如： `SELECT * FROM (VALUES (1, 2), (3, 4), (5, 6), (7, 8), (9, 10) ) AS MyTable(a, b);` 。 如需詳細資訊，請參閱[資料表值建構函式 &#40;Transact-SQL&#41;](../../t-sql/queries/table-value-constructor-transact-sql.md)。  
  
 *column_alias*  
 這是一個用以取代衍生資料表結果集中之資料行名稱的選擇性別名。 選取清單中的每個資料行都包含一個資料行別名，且會利用括號包住資料行別名的完整清單。  
  
 *table_or_view_name* FOR SYSTEM_TIME \<system_time>
**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和更新版本以及 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
指定從所指定的時態表及其連結的系統版本設定記錄資料表，傳回特定版本的資料  
  
### <a name="tablesample-clause"></a>TABLESAMPLE 子句
**適用範圍：** SQL Server、SQL Database 指定必須從資料表傳回資料範例。 該範例可能只是近似資料。 這個子句可用於 SELECT 或 UPDATE 陳述式中的任何主要或聯結資料表。 TABLESAMPLE 不能利用檢視表來指定。  
  
> [!NOTE]  
>  當您針對升級到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的資料庫使用 TABLESAMPLE 時，而且資料庫的相容性層級設定為 110 或更高層級，則遞迴通用資料表運算式 (CTE) 查詢中不允許 PIVOT。 如需詳細資訊，請參閱 [ALTER DATABASE 相容性層級 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。  
  
 系統  
 這是 ISO 標準所指定之依實作方式而定的取樣方法。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，這是唯一的可用取樣方法，而且預設會採用這種方法。 SYSTEM 採用頁面型取樣方法，這種方法會從資料表中為範例選擇一組隨機頁面，然後將這些頁面上的所有資料列當做範例子集傳回。  
  
 *sample_number*  
 這是用以代表資料列之百分比或數目的精確或近似常數數值運算式。 以 PERCENT 指定時，*sample_number* 會以隱含方式轉換成 **float** 值；否則會轉換成 **bigint**。 PERCENT 是預設值。  
  
 PERCENT  
 指定應該從資料表中擷取百分之 *sample_number* 的資料表資料列。 當指定 PERCENT 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回所指定之百分比的近似值。 已指定 PERCENT 時，*sample_number* 運算式必須評估為 0 到 100 的值。  
  
 ROWS  
 指定將擷取大約 *sample_number* 個資料列。 當指定 ROWS 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回所指定之資料列數的近似值。 已指定 ROWS 時，*sample_number* 運算式必須評估為大於零的整數值。  
  
 REPEATABLE  
 指出所選範例可以重新傳回。 以相同的 *repeat_seed* 值指定時，只要沒有對資料表中的任何資料列進行任何變更，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 就會傳回相同的資料列子集。 以不同的 *repeat_seed* 值指定時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將可能傳回資料表中一些不同的資料列樣本。 對資料表執行的下列動作均視為變更：插入、更新、刪除、索引重建或重新組織，以及資料庫還原或附加。  
  
 *repeat_seed*  
 這是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 為了產生隨機數字所使用的常數整數運算式。 *repeat_seed* 是 **bigint**。 如果未指定 *repeat_seed*，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 就會隨機指派一個值。 針對特定的 *repeat_seed* 值，只要尚未對資料表套用任何變更，取樣結果一律會相同。 *repeat_seed* 運算式必須評估為大於零的整數。  
  
### <a name="tablesample-clause"></a>TABLESAMPLE 子句
**適用對象：** [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]

 指定必須從資料表傳回資料範例。 該範例可能只是近似資料。 這個子句可用於 SELECT 或 UPDATE 陳述式中的任何主要或聯結資料表。 TABLESAMPLE 不能利用檢視表來指定。 

 PERCENT  
 指定應該從資料表中擷取百分之 *sample_number* 的資料表資料列。 當指定 PERCENT 時，[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 會傳回所指定之百分比的近似值。 當指定 PERCENT 時，*sample_number* 運算式必須評估為 0 到 100 的值。  


### <a name="joined-table"></a>聯結的資料表 
聯結的資料表是指由兩個以上的資料表所產生的結果集。 如果是多個聯結，請利用括號來變更聯結的自然順序。  
  
### <a name="join-type"></a>聯結類型
指定聯結動作的類型。  
  
 INNER  
 指定必須傳回所有相符的資料列配對。 捨棄兩份資料表中不相符的資料列。 如果未指定聯點類型，這就是預設值。  
  
 FULL [ OUTER ]  
 指定左資料表或右資料表中不符合聯結條件的資料列必須併入結果集中，且對應於其他資料表的輸出資料行必須設為 NULL。 這是通常由 INNER JOIN 傳回之所有資料列以外的項目。  
  
 LEFT [ OUTER ]  
 指定左資料表中不符合聯結條件的所有資料列必須併入結果集中，而且，除了內部聯結所傳回的所有資料列以外，還必須將其他資料表中的輸出資料行設為 NULL。  
  
 RIGHT [OUTER]  
 指定右資料表中不符合聯結條件的所有資料列必須併入結果集中，而且，除了內部聯結所傳回的所有資料列以外，還必須將對應於其他資料表的輸出資料行設為 NULL。  
  
### <a name="join-hint"></a>聯結提示  
就 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 而言，這會指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢最佳化工具針對查詢 FROM 子句中指定的每個聯結使用一個聯結提示 (或執行演算法)。 如需詳細資訊，請參閱[聯結提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-join.md)。  
  
 就 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]和[!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 而言，這些連結提示適用於兩個散發不相容資料行上的 INNER 連結。 它們可藉由限制在查詢處理期間可進行的資料移動數量來改善效能。 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)]和[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]所允許的聯結提示如下：  
  
 REDUCE  
 減少要針對聯結右方資料表移動的資料列數量，以便讓兩個散發不相容資料表變成相容。 REDUCE 提示也稱為半聯結提示。  
  
 REPLICATE  
 使來自聯結左方資料表的聯結資料行值複寫至所有節點。 右方資料表會聯結至這些資料行的複寫版本。  
  
 REDISTRIBUTE  
 強制將兩個資料來源散發在 JOIN 子句中所指定的資料行上。 針對分散式資料表，[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]會執行隨機移動。 針對複寫資料表，[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]會執行修剪移動。 若要了解這些移動類型，請參閱[!INCLUDE[pdw-product-documentation](../../includes/pdw-product-documentation-md.md)]中的＜DMS Query Plan Operations＞(DMS 查詢計劃作業) 一節。 當查詢計劃使用廣播移動來解決散發不相容聯結的問題時，此提示可以改善效能。  
  
 JOIN  
 指出所指定的聯結作業必須發生在所指定的資料表來源或檢視表之間。  
  
 ON \<search_condition>  
 指定聯結所根據的條件。 條件可以指定任何述詞 (雖然通常都是使用資料行和比較運算子)，例如：  
  
```sql
SELECT p.ProductID, v.BusinessEntityID  
FROM Production.Product AS p   
JOIN Purchasing.ProductVendor AS v  
ON (p.ProductID = v.ProductID);  
  
```  
  
 當條件指定資料行時，這些資料行不必有相同的名稱或相同的資料類型；不過，如果資料類型不同，它們必須相容的類型或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可以利用隱含方式轉換的類型。 如果資料類型無法利用隱含方式轉換，條件必須藉由 CONVERT 函數，利用明確方式轉換資料類型。  
  
 ON 子句中可以有僅涉及其中一個聯結資料表的述詞。 這類述詞也可以在查詢的 WHERE 子句中。 雖然這類述詞的放置不會影響 INNER 聯結，不過，如果涉及 OUTER 聯結，就可能會造成不同的結果。 這是因為 ON 子句中的述詞會套用至聯結之前的資料表，但在語意上，WHERE 子句則套用至聯結的結果。  
  
 如需有關搜尋條件和述詞的詳細資訊，請參閱[搜尋條件 &#40;Transact-SQL&#41;](../../t-sql/queries/search-condition-transact-sql.md)。  
  
 CROSS JOIN  
 指定兩個資料表的交叉乘積。 所傳回的資料列與舊式非 SQL-92 樣式聯結中不指定任何 WHERE 子句時所傳回的資料列相同。  
  
 *left_table_source* { CROSS | OUTER } APPLY *right_table_source*  
 指定針對 *left_table_source* 的每個資料列評估 APPLY 運算子的 *right_table_source*。 當 *right_table_source* 所包含的資料表值函數會從 *left_table_source* 取得資料行值來作為它的其中一個引數時，此功能會相當有用。  
  
 必須利用 APPLY 指定 CROSS 或 OUTER。 已指定 CROSS 時，如果針對 *left_table_source* 中的某個指定資料列評估 *right_table_source* 並傳回空的結果集，就不會產生任何資料列。  
  
 已指定 OUTER 時，則會為 *left_table_source* 的每個資料列產生一個資料列，即使針對該資料列評估 *right_table_source* 並傳回空的結果集時也一樣。  
  
 如需詳細資訊，請參閱＜備註＞一節。  
  
 *left_table_source*  
 這是資料表來源，如前一個引數所定義的一樣。 如需詳細資訊，請參閱＜備註＞一節。  
  
 *right_table_source*  
 這是資料表來源，如前一個引數所定義的一樣。 如需詳細資訊，請參閱＜備註＞一節。  
  
### <a name="pivot-clause"></a>PIVOT 子句

 *table_source* PIVOT \<pivot_clause>  
 指定根據 *pivot_column*.對 *table_source* 進行樞紐作業。 *table_source* 是一個資料表或資料表運算式。 輸出是一個包含 *table_source* 之所有資料行 (*pivot_column* 和 *value_column* 除外) 的資料表。 *table_source* 的資料行 (*pivot_column* 和 *value_column* 除外) 稱為樞紐運算子的群組資料行。 如需有關 PIVOT 和 UNPIVOT 的詳細資訊，請參閱[使用 PIVOT 和 UNPIVOT](../../t-sql/queries/from-using-pivot-and-unpivot.md)。  
  
 PIVOT 會對與群組作業資料行相關的輸入資料表執行群組作業，並為每個群組傳回一個資料列。 此外，輸出還會針對 *column_list* (出現在 *input_table* 的 *pivot_column* 中) 所指定的每個值各包含一個資料行。  
  
 如需詳細資訊，請參閱稍後的＜備註＞一節。  
  
 *aggregate_function*  
 為接受一個或多個輸入的系統或使用者定義的彙總函式。 該彙總函式對 Null 值必須是不變的。 對 Null 值不變的彙總函式在評估彙總值時，不會考量群組中的 Null 值。  
  
 不允許使用 COUNT(*) 系統彙總函式。  
  
 *value_column*  
 這是 PIVOT 運算子的值資料行。 與 UNPIVOT 搭配使用時，*value_column* 不可以是輸入 *table_source* 中現有資料行的名稱。  
  
 FOR *pivot_column*  
 這是 PIVOT 運算子的樞紐資料行。 *pivot_column* 的類型必須可以隱含或明確地轉換成 **nvarchar()** 。 這個資料行不可以是 **image** 或 **rowversion**。  
  
 使用 UNPIVOT 時，*pivot_column* 係指從 *table_source* 縮小範圍的輸出資料行名稱。 *table_source* 中不可以有具有該名稱的現有資料欄。  
  
 IN (*column_list* )  
 在 PIVOT 子句中，列出 *pivot_column* 中將成為輸出資料表之資料行名稱的值。 此清單不能指定任何已經存在於要進行樞紐作業之輸入 *table_source* 中的資料行名稱。  
  
 在 UNPIVOT 子句中，列出 *table_source* 中要縮減為單一 *pivot_column* 的資料行。  
  
 *table_alias*  
 這是輸出資料表的別名名稱。 必須指定 *pivot_table_alias*。  
  
 UNPIVOT \<unpivot_clause>  
 指定將輸入資料表從 *column_list* 中的多個資料行縮減成一個名為 *pivot_column* 的單一資料行。 如需有關 PIVOT 和 UNPIVOT 的詳細資訊，請參閱[使用 PIVOT 和 UNPIVOT](../../t-sql/queries/from-using-pivot-and-unpivot.md)。  
  
AS OF \<date_time>  
**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本和 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
傳回含有每個資料列單一記錄的資料表，內含的值在過去的指定時間點為實際 (目前)。 就內部而言，時態表和其歷程記錄資料表之間會執行聯集，且會將結果篩選為傳回資料列中的值，該資料列在 *\<date_time>* 參數所指定的時間點有效。 如果 *system_start_time_column_name* 值小於或等於 *\<date_time>* 參數值，且 *system_end_time_column_name* 值大於 *\<date_time>* 參數值，資料列的值即視為有效。   
  
FROM \<start_date_time> TO \<end_date_time>
**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和更新版本以及 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。
傳回資料表，其中內含所有記錄版本的值，該值在所指定的時間範圍內有效，而不論其是否在 FROM 引數的 *\<start_date_time>* 的參數值之前為作用中，或是在 TO 引數的 *\<end_date_time>* 參數值之後就不在作用中。 就內部而言，時態表和其歷程記錄資料表之間會執行等位，且會將結果篩選為傳回所有資料列版本的值，該值在指定的時間範圍任何時間點內皆為作用中。 這包含正好在 FROM 端點所定義範圍下限變成作用中的資料列，但不包含正好在 TO 端點所定義範圍上限變成作用中的資料列。  
  
BETWEEN \<start_date_time> AND \<end_date_time>  
**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本和 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
與上面的 **FROM \<start_date_time> TO \<end_date_time>** 描述相同，唯一差別在於其包含在 \<end_date_time> 端點所定義的範圍上限變成作用中的資料列。  
  
CONTAINED IN (\<start_date_time> , \<end_date_time>)  
**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本和 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
傳回資料表，其中內含所有記錄版本的值，該值在 CONTAINED IN 引數兩個日期時間值所定義的指定時間範圍內為開啟及關閉。 包含恰好在範圍下限變為作用中的資料列，或是恰好在範圍上限就不在作用中的資料列。  
  
ALL  
傳回含有目前資料表及記錄資料表之所有資料列值的資料表。  
  
## <a name="remarks"></a>備註  
 FROM 子句支援聯結資料表和衍生資料表的 SQL-92-SQL 語法。 SQL-92 語法提供 INNER、LEFT OUTER、RIGHT OUTER、FULL OUTER 及 CROSS 聯結運算子。  
  
 在檢視表內的衍生資料表和子查詢中，都支援 FROM 子句內的 UNION 和 JOIN。  
  
 自我聯結是指一份聯結至本身的資料表。 以自我聯結為基礎的插入或更新作業會遵照 FROM 子句中的順序。  
  
 因為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會考量提供資料行散發統計資料之連結伺服器的散發和基數統計資料，所以並不需要利用 REMOTE 聯結提示，從遠端強制評估聯結。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢處理器會考量遠端統計資料，然後判斷遠端聯結策略是否適當。 對於不提供資料行散發統計資料的提供者而言，REMOTE 聯結提示是很有用處的。  
  
## <a name="using-apply"></a>使用 APPLY  
 APPLY 運算子的左運算元和右運算元都是資料表運算式。 這些運算元的主要差異在於 *right_table_source* 可以使用資料表值函數，從 *left_table_source* 取得資料行來作為該函數的其中一個引數。 *left_table_source* 可以包含資料表值函數，但不能包含由 *right_table_source*.的資料行所構成的引數。  
  
APPLY 運算子利用下列方式來產生 FROM 子句的資料表來源：  
  
1.  針對 *left_table_source* 的每個資料列評估 *right_table_source* 以產生資料列集。  
  
    *right_table_source* 中的值取決於 *left_table_source*。 *right_table_source* 大致上可以下列方式表示：`TVF(left_table_source.row)`，其中 `TVF` 是資料表值函數。  
  
2.  執行 UNION ALL 作業，將針對 *right_table_source* 之評估中每個資料列產生的結果集與 *left_table_source* 結合在一起。  
  
    APPLY 運算子結果所產生的資料行清單就是與 *right_table_source* 資料行清單結合的 *left_table_source* 資料行集。  
  
## <a name="using-pivot-and-unpivot"></a>使用 PIVOT 和 UNPIVOT  
 *pivot_column* 和 *value_column* 是 PIVOT 運算子所使用的群組資料行。 PIVOT 會遵照下列處理序來取得輸出結果集：  
  
1.  在其 *input_table* 上針對群組資料行執行 GROUP BY，然後為每個群組各產生一個輸出資料列。  
  
     輸出資料列中的群組資料行會為 *input_table* 中的該群組取得對應的資料行值。  
  
2.  執行下列作業，在每個輸出資料列的資料行清單中產生資料行的值：  
  
    1.  對於在前一步驟的 GROUP BY 中產生的資料列，再另外針對 *pivot_column* 進行群組作業。  
  
         針對 *column_list* 中的每個輸出資料行，選取滿足下列條件的子群組：  
  
         `pivot_column = CONVERT(<data type of pivot_column>, 'output_column')`  
  
    2.  *aggregate_function* 的評估對象為此子群組上的 *value_column*，且其結果是作為相對應 *output_column*.的值來傳回。 如果子群組是空的，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會為該 *output_column* 產生 Null 值。 如果彙總函式是 COUNT，且子群組是空的，就會傳回零 (0)。  

> [!NOTE]
> `UNPIVOT` 子句中的資料行識別碼會依照目錄定序。 就 [!INCLUDE[ssSDS_md](../../includes/sssds-md.md)] 而言，定序一律為 `SQL_Latin1_General_CP1_CI_AS`。 就 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] 部分自主資料庫而言，定序一律為 `Latin1_General_100_CI_AS_KS_WS_SC`。 如果資料行與其他資料行結合，就必須使用定序子句 (`COLLATE DATABASE_DEFAULT`) 來避免衝突。   
  
 如需有關 PIVOT 和 UNPIVOT 的詳細資訊 (包括範例)，請參閱[使用 PIVOT 和 UNPIVOT](../../t-sql/queries/from-using-pivot-and-unpivot.md)。  
  
## <a name="permissions"></a>權限  
 需要 DELETE、SELECT 或 UPDATE 陳述式的權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-a-simple-from-clause"></a>A. 使用簡單的 FROM 子句  
 下列範例從 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 範例資料庫的 `TerritoryID` 資料表中擷取 `Name` 和 `SalesTerritory` 資料行。  
  
```sql    
SELECT TerritoryID, Name  
FROM Sales.SalesTerritory  
ORDER BY TerritoryID ;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
TerritoryID Name                            
----------- ------------------------------  
1           Northwest                       
2           Northeast                       
3           Central                         
4           Southwest                       
5           Southeast                       
6           Canada                          
7           France                          
8           Germany                         
9           Australia                       
10          United Kingdom                  
(10 row(s) affected)  
```  
  
### <a name="b-using-the-tablock-and-holdlock-optimizer-hints"></a>B. 使用 TABLOCK 和 HOLDLOCK 最佳化工具提示  
 下列部分交易顯示如何將明確共用資料表鎖定放在 `Employee` 上，以及如何讀取索引。 整個交易從頭到尾都會保留鎖定。  
  
```sql    
BEGIN TRAN  
SELECT COUNT(*)   
FROM HumanResources.Employee WITH (TABLOCK, HOLDLOCK) ;  
```  
  
### <a name="c-using-the-sql-92-cross-join-syntax"></a>C. 使用 SQL-92 CROSS JOIN 語法  
 下列範例傳回 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `Employee` 和 `Department` 這兩個資料表的交叉乘積。 傳回一份清單，其中包含 `BusinessEntityID` 資料列與所有 `Department` 名稱資料列的所有可能組合。  
  
```sql    
SELECT e.BusinessEntityID, d.Name AS Department  
FROM HumanResources.Employee AS e  
CROSS JOIN HumanResources.Department AS d  
ORDER BY e.BusinessEntityID, d.Name ;  
```  
  
### <a name="d-using-the-sql-92-full-outer-join-syntax"></a>D. 使用 SQL-92 FULL OUTER JOIN 語法  
 下列範例傳回產品名稱和 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `SalesOrderDetail` 資料表中所有相對應的銷售訂單。 它也傳回未在 `Product` 資料表中列出產品的所有銷售訂單，並傳回含有不同於 `Product` 資料表所列銷售訂單之銷售訂單的所有產品。  
  
```sql  
-- The OUTER keyword following the FULL keyword is optional.  
SELECT p.Name, sod.SalesOrderID  
FROM Production.Product AS p  
FULL OUTER JOIN Sales.SalesOrderDetail AS sod  
ON p.ProductID = sod.ProductID  
ORDER BY p.Name ;  
```  
  
### <a name="e-using-the-sql-92-left-outer-join-syntax"></a>E. 使用 SQL-92 LEFT OUTER JOIN 語法  
 下列範例聯結 `ProductID` 上的兩份資料表，並保留左資料表中不相符的資料列。 在每一份資料表中，`Product` 資料表與 `SalesOrderDetail` 資料行上的 `ProductID` 資料表相符。 所有產品 (已訂購或未訂購) 都會出現在結果集中。  
  
```sql    
SELECT p.Name, sod.SalesOrderID  
FROM Production.Product AS p  
LEFT OUTER JOIN Sales.SalesOrderDetail AS sod  
ON p.ProductID = sod.ProductID  
ORDER BY p.Name ;  
```  
  
### <a name="f-using-the-sql-92-inner-join-syntax"></a>F. 使用 SQL-92 INNER JOIN 語法  
 下列範例傳回所有產品名稱和銷售訂單識別碼。  
  
```sql    
-- By default, SQL Server performs an INNER JOIN if only the JOIN   
-- keyword is specified.  
SELECT p.Name, sod.SalesOrderID  
FROM Production.Product AS p  
INNER JOIN Sales.SalesOrderDetail AS sod  
ON p.ProductID = sod.ProductID  
ORDER BY p.Name ;  
```  
  
### <a name="g-using-the-sql-92-right-outer-join-syntax"></a>G. 使用 SQL-92 RIGHT OUTER JOIN 語法  
 下列範例聯結 `TerritoryID` 上的兩份資料表，並保留與右資料表中不相符的資料列。 在每一份資料表中，`SalesTerritory` 資料表與 `SalesPerson` 資料行上的 `TerritoryID` 資料表相符。 所有的銷售員都會出現在結果集中 (不論是否已指派地區給這些銷售員)。  
  
```sql    
SELECT st.Name AS Territory, sp.BusinessEntityID  
FROM Sales.SalesTerritory AS st   
RIGHT OUTER JOIN Sales.SalesPerson AS sp  
ON st.TerritoryID = sp.TerritoryID ;  
```  
  
### <a name="h-using-hash-and-merge-join-hints"></a>H. 使用 HASH 和 MERGE 聯結提示  
 下列範例從 `Product`、`ProductVendor` 及 `Vendor` 資料表中執行三資料表聯結，來產生產品及其供應商的清單。 查詢最佳化工具利用 MERGE 聯結來聯結 `Product` 和 `ProductVendor` ( `p` 和 `pv` )。 接著，再利用 HASH 聯結將 `Product` 和 `ProductVendor` MERGE 聯結 (`p` 和 `pv`) 的結果聯結至所要產生的 `Vendor` 資料表 (`p` 和 `pv`) 及 `v`。  
  
> [!IMPORTANT]  
>  指定聯結提示之後，INNER 關鍵字就不再是選擇性質，而是必須針對要執行的 INNER JOIN 加以明確陳述。  
  
```sql    
SELECT p.Name AS ProductName, v.Name AS VendorName  
FROM Production.Product AS p   
INNER MERGE JOIN Purchasing.ProductVendor AS pv   
ON p.ProductID = pv.ProductID  
INNER HASH JOIN Purchasing.Vendor AS v  
ON pv.BusinessEntityID = v.BusinessEntityID  
ORDER BY p.Name, v.Name ;  
```  
  
### <a name="i-using-a-derived-table"></a>I. 使用衍生資料表  
 下列範例會利用衍生資料表 ( `SELECT` 子句後面的 `FROM` 陳述式) 來傳回所有員工的名字和姓氏，以及員工所居住的城市。  
  
```sql    
SELECT RTRIM(p.FirstName) + ' ' + LTRIM(p.LastName) AS Name, d.City  
FROM Person.Person AS p  
INNER JOIN HumanResources.Employee e ON p.BusinessEntityID = e.BusinessEntityID   
INNER JOIN  
   (SELECT bea.BusinessEntityID, a.City   
    FROM Person.Address AS a  
    INNER JOIN Person.BusinessEntityAddress AS bea  
    ON a.AddressID = bea.AddressID) AS d  
ON p.BusinessEntityID = d.BusinessEntityID  
ORDER BY p.LastName, p.FirstName;  
```  
  
### <a name="j-using-tablesample-to-read-data-from-a-sample-of-rows-in-a-table"></a>J. 利用 TABLESAMPLE，從資料表的資料列樣本中讀取資料  
 下列範例會利用 `TABLESAMPLE` 子句中的 `FROM` 來傳回 `10` 資料表中大約百分之 `Customer` 的所有資料列。  
  
```sql    
SELECT *  
FROM Sales.Customer TABLESAMPLE SYSTEM (10 PERCENT) ;  
```  
  
### <a name="k-using-apply"></a>K. 使用 APPLY  
下列範例假設資料庫中存在下列資料表和資料表值函式：  

|物件名稱|資料行名稱|      
|---|---|   
|部門|DeptID、DivisionID、DeptName、DeptMgrID|      
|EmpMgr|MgrID、EmpID|     
|Employees|EmpID、EmpLastName、EmpFirstName、EmpSalary|  
|GetReports(MgrID)|EmpID、EmpLastName、EmpSalary|     
  
`GetReports` 資料表值函式會傳回直接或間接向指定 `MgrID` 報告的所有員工清單。  
  
這個範例利用 `APPLY` 來傳回所有部門和各部門中的所有員工。 如果某特定部門沒有員工，就不會針對該部門傳回任何資料列。  
  
```sql
SELECT DeptID, DeptName, DeptMgrID, EmpID, EmpLastName, EmpSalary  
FROM Departments d    
CROSS APPLY dbo.GetReports(d.DeptMgrID) ;  
```  
  
如果您想讓查詢產生沒有員工的部門資料列 (這些資料列會針對 `EmpID`、`EmpLastName` 及 `EmpSalary` 資料行產生 Null 值)，請改用 `OUTER APPLY`。  
  
```sql
SELECT DeptID, DeptName, DeptMgrID, EmpID, EmpLastName, EmpSalary  
FROM Departments d   
OUTER APPLY dbo.GetReports(d.DeptMgrID) ;  
```  
  
### <a name="l-using-cross-apply"></a>L. 使用 CROSS APPLY  
下列範例會查詢 `sys.dm_exec_cached_plans` 動態管理檢視來擷取快取中所有查詢計畫的計畫控制代碼，藉以擷取位於計畫快取中所有查詢計畫的快照集。 然後，指定 `CROSS APPLY` 運算子，以便將計畫控制代碼傳遞給 `sys.dm_exec_query_plan`。 目前在計畫快取中的每項計畫之 XML 顯示計畫輸出，都是在傳回的資料表之 `query_plan` 資料行中。  
  
```sql
USE master;  
GO  
SELECT dbid, object_id, query_plan   
FROM sys.dm_exec_cached_plans AS cp   
CROSS APPLY sys.dm_exec_query_plan(cp.plan_handle);   
GO  
```  
  
### <a name="m-using-for-system_time"></a>M. Using FOR SYSTEM_TIME  
  
**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本和 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
下列範例會使用 FOR SYSTEM_TIME AS OF date_time_literal_or_variable 引數來傳回截至 2014 年 1 月 1 日為止實際 (目前) 的資料表資料列。  
  
```sql
SELECT DepartmentNumber,   
    DepartmentName,   
    ManagerID,   
    ParentDepartmentNumber   
FROM DEPARTMENT  
FOR SYSTEM_TIME AS OF '2014-01-01'  
WHERE ManagerID = 5;
```  
  
下列範例會使用 FOR SYSTEM_TIME FROM date_time_literal_or_variable TO date_time_literal_or_variable 引數來傳回在所定義期間 (從 2013 年 1 月 1 日起，到 2014 年 1 月 1 日止，不含範圍上限) 處於作用中的所有資料列。  
  
```sql
SELECT DepartmentNumber,   
    DepartmentName,   
    ManagerID,   
    ParentDepartmentNumber   
FROM DEPARTMENT  
FOR SYSTEM_TIME FROM '2013-01-01' TO '2014-01-01'  
WHERE ManagerID = 5;
```  
  
 下列範例會使用 FOR SYSTEM_TIME BETWEEN date_time_literal_or_variable AND date_time_literal_or_variable 引數來傳回在所定義期間 (從 2013 年 1 月 1 日起，到 2014 年 1 月 1 日止，包含範圍上限) 處於作用中的所有資料列。  
  
```sql
SELECT DepartmentNumber,   
    DepartmentName,   
    ManagerID,   
    ParentDepartmentNumber   
FROM DEPARTMENT  
FOR SYSTEM_TIME BETWEEN '2013-01-01' AND '2014-01-01'  
WHERE ManagerID = 5;
```  
  
 下列範例會使用 FOR SYSTEM_TIME CONTAINED IN ( date_time_literal_or_variable, date_time_literal_or_variable ) 引數來傳回在所定義期間 (從 2013 年 1 月 1 日起，到 2014 年 1 月 1 日止) 開啟和關閉的所有資料列。  
  
```sql
SELECT DepartmentNumber,   
    DepartmentName,   
    ManagerID,   
    ParentDepartmentNumber   
FROM DEPARTMENT  
FOR SYSTEM_TIME CONTAINED IN ( '2013-01-01', '2014-01-01' )  
WHERE ManagerID = 5;
```  
  
 下列範例會使用變數 (而不是常值) 來提供查詢的日期界限值。  
  
```sql
DECLARE @AsOfFrom datetime2 = dateadd(month,-12, sysutcdatetime());
DECLARE @AsOfTo datetime2 = dateadd(month,-6, sysutcdatetime());
  
SELECT DepartmentNumber,   
    DepartmentName,   
    ManagerID,   
    ParentDepartmentNumber   
FROM DEPARTMENT  
FOR SYSTEM_TIME FROM @AsOfFrom TO @AsOfTo  
WHERE ManagerID = 5;
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="n-using-the-inner-join-syntax"></a>N. 使用 INNER JOIN 語法  
 下列範例會從 `FactInternetSales` 和 `DimProduct` 資料表中，傳回聯結索引鍵 `ProductKey` 在兩個資料表中都相符的 `SalesOrderNumber`、`ProductKey`及 `EnglishProductName` 資料行。 `SalesOrderNumber` 和 `EnglishProductName` 資料行個別僅存在於其中一個資料表，因此不需要依照所示的方式指定這些資料行的相關資料表別名；包含這些別名只是為了方便閱讀。 別名名稱前的 **AS** 一字並非必要，但為了方便閱讀及符合 ANSI 標準，建議使用此字。  
  
```sql
-- Uses AdventureWorks  
  
SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
FROM FactInternetSales AS fis 
INNER JOIN DimProduct AS dp  
    ON dp.ProductKey = fis.ProductKey;  
```  
  
 由於內部聯結並不需要 `INNER` 關鍵字，因此可以將這個相同的查詢撰寫成：  
  
```sql
-- Uses AdventureWorks  
  
SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
FROM FactInternetSales AS fis 
JOIN DimProduct AS dp  
ON dp.ProductKey = fis.ProductKey;  
```  
  
 `WHERE` 子句也可以與這個查詢搭配使用來限制結果。 下列範例會將結果限制為高於 'SO5000' 的 `SalesOrderNumber` 值：  
  
```sql
-- Uses AdventureWorks  
  
SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
FROM FactInternetSales AS fis 
JOIN DimProduct AS dp  
    ON dp.ProductKey = fis.ProductKey  
WHERE fis.SalesOrderNumber > 'SO50000'  
ORDER BY fis.SalesOrderNumber;  
```  
  
### <a name="o-using-the-left-outer-join-and-right-outer-join-syntax"></a>O. 使用 LEFT OUTER JOIN 和 RIGHT OUTER JOIN 語法  
 下列範例會在 `ProductKey` 資料行上將 `FactInternetSales` 與 `DimProduct` 資料表聯結。 左方外部聯結語法會保留來自左方 (`FactInternetSales`) 資料表的不相符資料列。 由於 `FactInternetSales` 資料表並未包含任何與 `DimProduct` 資料表不符的 `ProductKey` 值，因此這個查詢會傳回與上面第一個內部聯結範例相同的資料列。  
  
```sql
-- Uses AdventureWorks  
  
SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
FROM FactInternetSales AS fis 
LEFT OUTER JOIN DimProduct AS dp  
    ON dp.ProductKey = fis.ProductKey;  
```  
  
 您也可以不搭配 `OUTER` 關鍵字來撰寫此查詢。  
  
 在右方外部聯結中，會保留來自右方資料表的不相符資料列。 下列範例會傳回與上面左方外部聯結範例相同的資料列。  
  
```sql
-- Uses AdventureWorks  
  
SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
FROM DimProduct AS dp 
RIGHT OUTER JOIN FactInternetSales AS fis  
    ON dp.ProductKey = fis.ProductKey;  
```  
  
 下列查詢會使用 `DimSalesTerritory` 資料表作為左方外部聯結中的左方資料表。 它會從 `FactInternetSales`資料表擷取 `SalesOrderNumber` 值。 如果特定 `SalesTerritoryKey` 沒有任何訂單，查詢就會針對該資料列的 `SalesOrderNumber` 傳回 NULL。 此查詢會依據 `SalesOrderNumber` 資料行排序，因此這個資料行中的所有 NULL 都會出現在結果頂端。  
  
```sql
-- Uses AdventureWorks  
  
SELECT dst.SalesTerritoryKey, dst.SalesTerritoryRegion, fis.SalesOrderNumber  
FROM DimSalesTerritory AS dst 
LEFT OUTER JOIN FactInternetSales AS fis  
    ON dst.SalesTerritoryKey = fis.SalesTerritoryKey  
ORDER BY fis.SalesOrderNumber;  
```  
  
 您可以使用右方外部聯結來撰寫此查詢以擷取相同的結果：  
  
```sql
-- Uses AdventureWorks  
  
SELECT dst.SalesTerritoryKey, dst.SalesTerritoryRegion, fis.SalesOrderNumber  
FROM FactInternetSales AS fis 
RIGHT OUTER JOIN DimSalesTerritory AS dst  
    ON fis.SalesTerritoryKey = dst.SalesTerritoryKey  
ORDER BY fis.SalesOrderNumber;  
```  
  
### <a name="p-using-the-full-outer-join-syntax"></a>P. 使用 FULL OUTER JOIN 語法  
 下列範例示範完整外部聯結，其中會從所連結的兩個資料表傳回所有資料列，但針對與另一方資料表不符的值會傳回 NULL。  
  
```sql
-- Uses AdventureWorks  
  
SELECT dst.SalesTerritoryKey, dst.SalesTerritoryRegion, fis.SalesOrderNumber  
FROM DimSalesTerritory AS dst 
FULL OUTER JOIN FactInternetSales AS fis  
    ON dst.SalesTerritoryKey = fis.SalesTerritoryKey  
ORDER BY fis.SalesOrderNumber;  
```  
  
 您也可以不搭配 `OUTER` 關鍵字來撰寫此查詢。  
  
```sql
-- Uses AdventureWorks  
  
SELECT dst.SalesTerritoryKey, dst.SalesTerritoryRegion, fis.SalesOrderNumber  
FROM DimSalesTerritory AS dst 
FULL JOIN FactInternetSales AS fis  
    ON dst.SalesTerritoryKey = fis.SalesTerritoryKey  
ORDER BY fis.SalesOrderNumber;  
```  
  
### <a name="q-using-the-cross-join-syntax"></a>Q. 使用 CROSS JOIN 語法  
 下列範例會傳回 `FactInternetSales` 與 `DimSalesTerritory` 資料表的交叉乘積。 其中會傳回 `SalesOrderNumber` 與 `SalesTerritoryKey`的所有可能組合清單。 請注意，交叉聯結查詢中沒有 `ON` 子句。  
  
```sql
-- Uses AdventureWorks  
  
SELECT dst.SalesTerritoryKey, fis.SalesOrderNumber  
FROM DimSalesTerritory AS dst 
CROSS JOIN FactInternetSales AS fis  
ORDER BY fis.SalesOrderNumber;  
```  
  
### <a name="r-using-a-derived-table"></a>R. 使用衍生資料表  
 下列範例會使用衍生資料表 (`FROM` 子句後面的 `SELECT` 陳述式) 來傳回 `DimCustomer` 資料表中 `BirthDate` 值在 1970 年 1 月 1 日之後且姓氏為 'Smith' 之所有客戶的 `CustomerKey` 和 `LastName` 資料行。  
  
```sql
-- Uses AdventureWorks  
  
SELECT CustomerKey, LastName  
FROM  
   (SELECT * FROM DimCustomer  
    WHERE BirthDate > '01/01/1970') AS DimCustomerDerivedTable  
WHERE LastName = 'Smith'  
ORDER BY LastName;  
```  
  
### <a name="s-reduce-join-hint-example"></a>S. REDUCE 聯結提示範例  
 下列範例會使用 `REDUCE` 聯結提示來更改查詢內衍生資料表的處理方式。 在此查詢中使用 `REDUCE` 聯結提示時，會針對 `fis.ProductKey` 進行預計、複寫及區別，然後在於 `ProductKey` 上隨機移動 `DimProduct` 的期間聯結至 `DimProduct`。 產生的衍生資料表會在 `fis.ProductKey` 上散發。  
  
```sql
-- Uses AdventureWorks  
  
EXPLAIN SELECT SalesOrderNumber  
FROM  
   (SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
    FROM DimProduct AS dp   
      INNER REDUCE JOIN FactInternetSales AS fis   
          ON dp.ProductKey = fis.ProductKey  
   ) AS dTable  
ORDER BY SalesOrderNumber;  
```  
  
### <a name="t-replicate-join-hint-example"></a>T. REPLICATE 聯結提示範例  
 這個接下來的範例示範與上一個範例相同的查詢，唯一差別在於使用的是 `REPLICATE` 聯結提示，而不是 `REDUCE` 聯結提示。 使用 `REPLICATE` 提示會導致將來自 `FactInternetSales` 資料表之 `ProductKey` (聯結端) 資料行中的值複寫至所有節點。 `DimProduct` 資料表會聯結至這些資料值的複寫版本。  
  
```sql
-- Uses AdventureWorks  
  
EXPLAIN SELECT SalesOrderNumber  
FROM  
   (SELECT fis.SalesOrderNumber, dp.ProductKey, dp.EnglishProductName  
    FROM DimProduct AS dp   
      INNER REPLICATE JOIN FactInternetSales AS fis  
          ON dp.ProductKey = fis.ProductKey  
   ) AS dTable  
ORDER BY SalesOrderNumber;  
```  
  
### <a name="u-using-the-redistribute-hint-to-guarantee-a-shuffle-move-for-a-distribution-incompatible-join"></a>U. 使用 REDISTRIBUTE 提示來確保針對散發不相容聯結使用隨機移動  
 下列查詢會在散發不相容聯結上使用 REDISTRIBUTE 查詢提示。 這可確保查詢最佳化工具會在查詢計劃中使用「隨機」移動。 此外，也可確保查詢計劃不會使用會將分散式資料表移至複寫資料表的「廣播」移動。  
  
 在下列範例中，REDISTRIBUTE 提示會強制在 FactInternetSales 資料表上進行「隨機」移動，因為 ProductKey 是 DimProduct 的散發資料行，而不是 FactInternetSales 的散發資料行。  
  
```sql
-- Uses AdventureWorks  
  
EXPLAIN  
SELECT dp.ProductKey, fis.SalesOrderNumber, fis.TotalProductCost  
FROM DimProduct AS dp 
INNER REDISTRIBUTE JOIN FactInternetSales AS fis  
    ON dp.ProductKey = fis.ProductKey;  
```  

### <a name="v-using-tablesample-to-read-data-from-a-sample-of-rows-in-a-table"></a>V. 利用 TABLESAMPLE，從資料表的資料列樣本中讀取資料  
 下列範例會利用 `TABLESAMPLE` 子句中的 `FROM` 來傳回 `10` 資料表中大約百分之 `Customer` 的所有資料列。  
  
```sql    
SELECT *  
FROM Sales.Customer TABLESAMPLE SYSTEM (10 PERCENT) ;
```
  
## <a name="see-also"></a>另請參閱  
 [CONTAINSTABLE &#40;Transact-SQL&#41;](../../relational-databases/system-functions/containstable-transact-sql.md)   
 [FREETEXTTABLE &#40;Transact-SQL&#41;](../../relational-databases/system-functions/freetexttable-transact-sql.md)   
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [OPENQUERY &#40;Transact-SQL&#41;](../../t-sql/functions/openquery-transact-sql.md)   
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)
