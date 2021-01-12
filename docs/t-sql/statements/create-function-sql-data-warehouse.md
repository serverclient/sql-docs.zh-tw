---
description: CREATE FUNCTION (Azure Synapse Analytics)
title: CREATE FUNCTION (Azure Synapse Analytics) | Microsoft Docs
ms.custom: ''
ms.date: 09/17/2020
ms.prod: sql
ms.prod_service: sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: 8cad1b2c-5ea0-4001-9060-2f6832ccd057
author: julieMSFT
ms.author: jrasnick
monikerRange: '>= aps-pdw-2016 || = azure-sqldw-latest'
ms.openlocfilehash: 7a7c60b45397f0a06cb7543dad9d15fff107a15f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095761"
---
# <a name="create-function-azure-synapse-analytics"></a>CREATE FUNCTION (Azure Synapse Analytics)
[!INCLUDE[applies-to-version/asa-pdw](../../includes/applies-to-version/asa-pdw.md)]

  在 [!INCLUDE[ssSDW](../../includes/ssazuresynapse_md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 中建立使用者定義函數。 使用者定義函式是一種 [!INCLUDE[tsql](../../includes/tsql-md.md)] 常式，它會接受參數、執行動作 (例如複雜計算) 並且將該動作的結果傳回成值。 在 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] 中，傳回值必須是純量 (單一) 值。 在 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 中，CREATE FUNCTION 可以使用內嵌資料表值函式 (預覽) 的語法來傳回資料表，或是使用純量函式的語法傳回單一值。 您可以使用這個陳述式來建立可用下列方式使用的可重複使用常式：  
  
-   在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式中，例如 SELECT  
  
-   在呼叫函數的應用程式中  
  
-   在另一個使用者自訂函數的定義中  
  
-   若要在資料行上定義 CHECK 條件約束  
  
-   取代預存程序  
  
-   使用內嵌函式作為安全性原則的篩選述詞  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Transact-SQL Scalar Function Syntax  (in Azure Synapse Analytics and Parallel Data Warehouse)
CREATE FUNCTION [ schema_name. ] function_name   
( [ { @parameter_name [ AS ] parameter_data_type   
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
  
<function_option>::=   
{  
    [ SCHEMABINDING ]  
  | [ RETURNS NULL ON NULL INPUT | CALLED ON NULL INPUT ]  
}  
```

```syntaxsql
-- Transact-SQL Inline Table-Valued Function Syntax (Preview in Azure Synapse Analytics only)
CREATE FUNCTION [ schema_name. ] function_name
( [ { @parameter_name [ AS ] parameter_data_type
    [ = default ] }
    [ ,...n ]
  ]
)
RETURNS TABLE
    [ WITH SCHEMABINDING ]
    [ AS ]
    RETURN [ ( ] select_stmt [ ) ]
[ ; ]
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]
  
## <a name="arguments"></a>引數  
 *schema_name*  
 這是使用者定義函數所屬的結構描述名稱。  
  
 *function_name*  
 這是使用者定義函數的名稱。 函式名稱必須符合識別碼的規則。另外，函式名稱在資料庫內必須是唯一的，且對於它的結構描述也必須是唯一的。  
  
> [!NOTE]  
>  即使沒有指定參數，函數名稱後面仍需要括號。  
  
 @*parameter_name*  
 這是使用者定義函數中的參數。 您可以宣告一個或多個參數。  
  
 函數最多可以有 2,100 個參數。 除非定義了參數的預設值，否則在執行函數時，使用者必須提供每個已宣告之參數的值。  
  
 使用 @ 記號當做第一個字元來指定參數名稱。 參數名稱必須符合識別碼的規則。 對函數而言，參數必須是本機參數；相同的參數名稱可以用在其他函數中。 參數只能取代常數，不能用來取代資料表名稱、資料行名稱或其他資料庫物件的名稱。  
  
> [!NOTE]  
>  在預存程序或使用者定義函數中傳遞參數，或在批次陳述式中宣告和設定變數時，不接受 ANSI_WARNINGS。 例如，如果將變數定義為 **char(3)** ，然後設定為大於三個字元的值，資料就會被截斷成定義的大小，而 INSERT 或 UPDATE 陳述式會執行成功。  
  
 *parameter_data_type*  
 參數資料類型。 針對 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式，[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 中支援的所有純量資料類型皆允許。 時間戳記 (rowversion) 資料類型是不支援的類型。  
  
 [ =*default* ]  
 這是參數的預設值。 如果已定義 *default* 值，則不需為該參數指定值，即可執行函式。  
  
 如果函數的參數有預設值，則必須在呼叫函數來擷取該預設值時指定關鍵字 DEFAULT。 這個行為與使用預存程序中具有預設值的參數不一樣，因為在預存程序中，省略參數也意味著使用預設值。  
  
 *return_data_type*  
 這是純量使用者定義函數的傳回值。 針對 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函式，[!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 中支援的所有純量資料類型皆允許。 時間戳記 (rowversion) 資料類型是不支援的類型。 不允許資料指標和資料表的非純量類型。  
  
 *function_body*  
 一連串的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。  function_body 無法包含 SELECT 陳述式，且無法參考資料庫資料。  function_body 無法參考資料表或檢視。 函式主體可以呼叫其他具決定性的函式，但是無法呼叫非決定性函式。 
  
 在純量函式中，*function_body* 是一系列的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，這些陳述式會一起評估為純量值。  
  
 *scalar_expression*  
 指定純量函數傳回的純量值。  

 *select_stmt* **適用於**：Azure Synapse Analytics  
 這是單一 SELECT 陳述式，可定義內嵌資料表值函式 (預覽) 的傳回值。

 TABLE **適用於**：Azure Synapse Analytics  
 指定資料表值函式 (TVF) 的傳回值是資料表。 只有常數和 @*local_variables* 才能傳遞給 TVF。

 在內嵌 TVF (預覽) 中，TABLE 傳回值是透過單一 SELECT 陳述式定義。 內嵌函數沒有相關聯的傳回變數。
  
 **\<function_option>::=** 
  
 指定此函數將會有下列其中一個或多個選項。  
  
 SCHEMABINDING  
 指定函數必須繫結到它所參考的資料庫物件。 當指定 SCHEMABINDING 時，無法依照會影響函數定義的方式來修改基底物件。 您必須先修改或卸除函數定義才能移除對於要修改之物件的相依性。  
  
 只有在下發生下列其中一個動作時，才會移除函數與其參考的物件之間的繫結：  
  
-   已卸除這個函數。  
  
-   您可以利用未指定 SCHEMABINDING 選項的 ALTER 陳述式來修改函數。  
  
 只有當下列條件成立時，函數才可繫結結構描述：  
  
-   函式參考的任何使用者定義函式也繫結結構描述。  
  
-   函式和函式所參考的其他 UDF 會使單一部分或兩部分名稱進行參考。  
  
-   僅有內建函式以及相同資料庫中的其他 UDF 可以在 UDF 的主體內參考。  
  
-   執行 CREATE FUNCTION 陳述式的使用者在函數參考的資料庫物件上有 REFERENCES 權限。  
  
 若要移除 SCHEMABINDING，請使用 ALTER  
  
 RETURNS NULL ON NULL INPUT | **CALLED ON NULL INPUT**  
 指定純量值函式的 **OnNULLCall** 屬性。 若未指定，預設情況下意味著 CALLED ON NULL INPUT。 這表示，即使傳遞 NULL 做為引數，函數主體仍會執行。  
  
## <a name="best-practices"></a>最佳做法  
 如果未以 SCHEMABINDING 子句建立使用者定義函數，叫用該函數時，對基礎物件所進行的變更可能會影響函數的定義並產生非預期的結果。 建議您實作下列其中一個方法，以確保函數不會因為其基礎物件的變更而變成過期：  
  
-   當您要建立函數時，指定 WITH SCHEMABINDING 子句。 這可以確保系統無法修改函數定義中參考的物件 (除非同時修改函數)。  
  
## <a name="interoperability"></a>互通性  
 以下是純量值函式中的有效陳述式：  
  
-   指派陳述式。  
  
-   流程控制陳述式 (但不包括 TRY...CATCH 陳述式)。  
  
-   DECLARE 陳述式 - 定義區域資料變數。  

在內嵌資料表值函式 (預覽) 中，僅允許單一 SELECT 陳述式。
  
## <a name="limitations-and-restrictions"></a>限制事項  
 使用者定義函數不能用來執行修改資料庫狀態的動作。  
  
 使用者定義函數可以具有巢狀結構；也就是說，某個使用者定義函數可以呼叫另一個使用者定義函數。 被呼叫的函數開始執行時，巢狀層級會遞增；被呼叫的函數完成執行時，巢狀層級會遞減。 使用者定義函數所建立的巢狀結構最多可以有 32 個層級。 超過巢狀層級上限會導致整個呼叫函數鏈結失敗。   
  
## <a name="metadata"></a>中繼資料  
 此小節列出您可以用來傳回使用者定義函式之中繼資料的系統目錄檢視表。  
  
 [sys.sql_modules](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)：顯示 [!INCLUDE[tsql](../../includes/tsql-md.md)] 使用者定義函式的定義。 例如：  
  
```sql  
SELECT definition, type   
FROM sys.sql_modules AS m  
JOIN sys.objects AS o   
    ON m.object_id = o.object_id   
    AND type = ('FN');  
GO  
  
```  
  
 [sys.parameters](../../relational-databases/system-catalog-views/sys-parameters-transact-sql.md)：顯示使用者定義函數中定義之參數的相關資訊。  
  
 [sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md)：顯示函數所參考的基礎物件。  
  
## <a name="permissions"></a>權限  
 需要資料庫中的 CREATE FUNCTION 權限，以及此函數建立所在之結構描述上的 ALTER 權限。  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="a-using-a-scalar-valued-user-defined-function-to-change-a-data-type"></a>A. 使用純量值使用者定義函式來變更資料類型  
 這個簡單的函式會採用 **int** 資料類型作為輸入，並傳回 **decimal(10,2)** 資料類型作為輸出。  
  
```sql  
CREATE FUNCTION dbo.ConvertInput (@MyValueIn int)  
RETURNS decimal(10,2)  
AS  
BEGIN  
    DECLARE @MyValueOut int;  
    SET @MyValueOut= CAST( @MyValueIn AS decimal(10,2));  
    RETURN(@MyValueOut);  
END;  
GO  
  
SELECT dbo.ConvertInput(15) AS 'ConvertedValue';  
```  

## <a name="examples-sssdwfull"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]  

### <a name="a-creating-an-inline-table-valued-function-preview"></a>A. 建立內嵌資料表值函式 (預覽)
 下列範例會建立內嵌資料表值函式以傳回模組上的某些重要資訊，並依 `objectType` 參數篩選。 其包含會在搭配 DEFAULT 參數呼叫函式時傳回所有模組的預設值。 此範例會利用在[中繼資料](#metadata)中提到的一些系統目錄檢視。

```sql
CREATE FUNCTION dbo.ModulesByType(@objectType CHAR(2) = '%%')
RETURNS TABLE
AS
RETURN
(
    SELECT 
        sm.object_id AS 'Object Id',
        o.create_date AS 'Date Created',
        OBJECT_NAME(sm.object_id) AS 'Name',
        o.type AS 'Type',
        o.type_desc AS 'Type Description', 
        sm.definition AS 'Module Description'
    FROM sys.sql_modules AS sm  
    JOIN sys.objects AS o ON sm.object_id = o.object_id
    WHERE o.type like '%' + @objectType + '%'
);
GO
```
接著可以使用下列內容呼叫函式，以傳回所有檢視 (**V**) 物件：
```sql
select * from dbo.ModulesByType('V');
```

### <a name="b-combining-results-of-an-inline-table-valued-function-preview"></a>B. 合併內嵌資料表值函式 (預覽) 的結果
 這個簡單的範例會使用先前所建立的內嵌 TVF，示範如何使用 CROSS APPLY 將其結果與其他資料表合併。 在這裡，我們會選取來自 sys.objects 和 `ModulesByType` 結果的所有資料行，以取得符合 *type* 資料行的所有資料列。 如需使用 APPLY 的詳細資料，請參閱 [FROM 子句與 JOIN、APPLY、PIVOT](../../t-sql/queries/from-transact-sql.md)。

```sql
SELECT * 
FROM sys.objects o
CROSS APPLY dbo.ModulesByType(o.type);
GO
```
  
## <a name="see-also"></a>另請參閱  
 [ALTER FUNCTION (SQL Server PDW)](/previous-versions/sql/)   
 [DROP FUNCTION (SQL Server PDW)](/previous-versions/sql/)  
  
  

