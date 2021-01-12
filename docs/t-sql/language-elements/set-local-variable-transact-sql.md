---
description: SET @local_variable (Transact-SQL)
title: SET @local_variable (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- SET @local_variable
- variables [SQL Server], assigning
- SET statement, @local_variable
- local variables [SQL Server]
ms.assetid: d410e06e-061b-4c25-9973-b2dc9b60bd85
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: caf0fcb2a9b00c8f391280395629348f055d4039
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100948"
---
# <a name="set-local_variable-transact-sql"></a>SET @local_variable (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

將先前利用 DECLARE @*local_variable* 陳述式來建立的指定區域變數設為指定的值。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
SQL Server 和 Azure SQL Database 的語法：

```syntaxsql
SET   
{ @local_variable  
    [ . { property_name | field_name } ] = { expression | udt_name { . | :: } method_name }  
}  
|  
{ @SQLCLR_local_variable.mutator_method  
}  
|  
{ @local_variable  
    {+= | -= | *= | /= | %= | &= | ^= | |= } expression  
}  
|   
  { @cursor_variable =   
    { @cursor_variable | cursor_name   
    | { CURSOR [ FORWARD_ONLY | SCROLL ]   
        [ STATIC | KEYSET | DYNAMIC | FAST_FORWARD ]   
        [ READ_ONLY | SCROLL_LOCKS | OPTIMISTIC ]   
        [ TYPE_WARNING ]   
    FOR select_statement   
        [ FOR { READ ONLY | UPDATE [ OF column_name [ ,...n ] ] } ]   
      }   
    }  
}   
```  
Azure Synapse Analytics 和平行處理資料倉儲的語法：  
```syntaxsql
SET @local_variable { = | += | -= | *= | /= | %= | &= | ^= | |= } expression  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
**@** _local_variable_  
除了 **cursor**、**text**、**ntext**、**image** 或 **table** 以外的任何類型變數名稱。 變數名稱的開頭必須是 at 記號 ( **@** )。 變數名稱必須遵照[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
*property_name*  
使用者定義型別的屬性。  
  
*field_name*  
使用者定義型別的公用欄位。  
  
*udt_name*  
Common Language Runtime (CLR) 使用者定義型別的名稱。  
  
`{ . | :: }`  
指定 CLR 使用者自訂類型的方法。 對於執行個體 (非靜態) 方法，請使用句點 (**.**)。 對於靜態方法，請使用兩個冒號 (**::**)。 若要叫用 CLR 使用者自訂類型的方法、屬性或欄位，您必須具有類型的 EXECUTE 權限。  
  
_method_name_ **(** _argument_ [ **,** ... *n* ] **)**  
利用一個或多個引數來修改類型執行個體狀態的使用者定義型別方法。 靜態方法必須是公用的。  
  
**@** _SQLCLR_local_variable_  
類型位於組件中的變數。 如需詳細資訊，請參閱 [Common Language Runtime &#40;CLR&#41; 整合程式設計概念](../../relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts.md)。  
  
*mutator_method*  
組件中可以變更物件狀態的方法。 SQLMethodAttribute.IsMutator 會套用到這個方法。  
  
`{ += | -= | *= | /= | %= | &= | ^= | |= }`  
複合指派運算子：  
  
 +=              加並指派  
  
 -=              減並指派  
  
 *=              乘並指派  
  
 /=              除並指派  
  
 %=              取餘數並指派  
  
 &=              位元 AND 並指派  
  
 ^=              位元 XOR 並指派  
  
 |=              位元 OR 並指派  
  
*expression*  
任何有效的[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
*cursor_variable*  
資料指標變數的名稱。 如果目標資料指標先前參考不同的資料指標，就會移除先前的參考。  
  
*cursor_name*  
使用 DECLARE CURSOR 陳述式來宣告的資料指標名稱。  
  
CURSOR  
指定 SET 陳述式包含資料指標的宣告。  
  
SCROLL  
指定資料指標支援所有提取選項：FIRST、LAST、NEXT、PRIOR、RELATIVE 和 ABSOLUTE。 當您也指定了 FAST_FORWARD 時，便不能指定 SCROLL。  
  
FORWARD_ONLY  
指定資料指標只支援 FETCH NEXT 選項。 您只能依單一方向，從第一個到最後一個資料列擷取資料指標。 當您指定不含 STATIC、KEYSET 或 DYNAMIC 關鍵字的 FORWARD_ONLY 時，會將資料指標實作成 DYNAMIC。 如果您沒有指定 FORWARD_ONLY 或 SCROLL，除非指定了 STATIC、KEYSET 或 DYNAMIC 關鍵字，否則，預設值是 FORWARD_ONLY。 如果是 STATIC、KEYSET 和 DYNAMIC 資料指標，預設值便是 SCROLL。  
  
STATIC  
定義一個資料指標，它會建立資料暫存複本供資料指標本身使用。 對於資料指標的所有要求都會透過 tempdb 中的這個暫存資料表來回答。 因此，資料指標開啟後對基底資料表所做的修改，不會反映在資料指標提取所傳回的資料中。 而且，這個資料指標不支援修改。  
  
KEYSET  
指定在開啟資料指標時，修正資料指標中之資料列的成員資格和順序。 可唯一識別資料列的索引鍵組內建在 tempdb 的 keysettable 中。 當資料指標擁有者捲動資料指標時，基底資料表中非索引鍵值的變更 (不論是資料指標擁有者所做的變更還是其他使用者所認可的變更) 都是可見的。 其他使用者的插入便不可見，且無法透過 [!INCLUDE[tsql](../../includes/tsql-md.md)] 伺服器資料指標來插入。  
  
如果刪除某個資料列，嘗試擷取該資料列的動作會傳回值為 -2 的 @@FETCH_STATUS。 從資料指標之外更新索引鍵值，類似於先刪除舊資料列，再插入新資料列。 含有新值的資料列不可見，且試圖擷取含有舊值的資料列會傳回值為 -2 的 @@FETCH_STATUS。 如果更新是藉由指定 WHERE CURRENT OF 子句透過資料指標進行，便可看到新值。  
  
DYNAMIC  
定義一個資料指標，使資料指標擁有者捲動資料指標時，資料指標能夠反映結果集資料列的所有資料變更。 每次提取時，資料列的資料值、順序和成員資格都有可能改變。 動態資料指標不支援絕對和相對提取選項。  
  
FAST_FORWARD  
指定啟用了最佳化的 FORWARD_ONLY、READ_ONLY 資料指標。 當您也指定了 SCROLL 時，便不能指定 FAST_FORWARD。  
  
READ_ONLY  
防止利用這個資料指標來更新。 UPDATE 或 DELETE 陳述式中的 WHERE CURRENT OF 子句無法參考這個資料指標。 這個選項會覆寫要更新之資料指標的預設功能。  
  
SCROLL LOCKS  
指定藉由資料指標進行的定位更新或刪除一定會成功。 當資料列讀入資料指標時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會鎖定這些資料列，以確保之後可對它們進行修改。 當您也指定了 FAST_FORWARD 時，便不能指定 SCROLL_LOCKS。  
  
OPTIMISTIC  
指定如果將資料列讀入資料指標之後，又更新了這些資料列，則透過資料指標來進行的定位更新或刪除不會成功。 當資料列讀入資料指標時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不會鎖定資料列。 相反地，它會利用 timestamp 資料行值的比較 (如果資料表沒有 timestamp 資料行，便會利用總和檢查碼值) 來判斷資料列讀入資料指標之後，是否會修改資料列。 如果修改了資料列，試圖執行的定位更新或刪除便會失敗。 當您也指定了 FAST_FORWARD 時，便不能指定 OPTIMISTIC。  
  
TYPE_WARNING  
指定當資料指標從要求的類型隱含地轉換成另一個類型時，便傳送一則警告訊息給用戶端。  
  
FOR *select_statement*  
這是定義資料指標結果集的標準 SELECT 陳述式。 資料指標宣告的 *select_statement* 內不允許有 FOR BROWSE 和 INTO 關鍵字。  
  
如果您使用 DISTINCT、UNION、GROUP BY 或 HAVING，或在 *select_list* 中包含彙總運算式，就會將資料指標建立成 STATIC。  
  
如果每個基礎資料表都沒有唯一索引，且要求 ISO SCROLL 資料指標或 [!INCLUDE[tsql](../../includes/tsql-md.md)] KEYSET 資料指標，它會自動成為 STATIC 資料指標。  
  
如果 *select_statement* 包含 ORDER BY 子句，且該子句中的資料行不是唯一資料列識別碼，就會將 DYNAMIC 資料指標轉換成 KEYSET 資料指標。如果無法開啟 KEYSET 資料指標，便會轉換成 STATIC 資料指標。 使用 ISO 語法但不含 STATIC 關鍵字時所定義的資料指標，也會進行這項處理。  
  
READ ONLY  
防止利用這個資料指標來更新。 UPDATE 或 DELETE 陳述式中的 WHERE CURRENT OF 子句無法參考這個資料指標。 這個選項會覆寫要更新之資料指標的預設功能。 這個關鍵字有別於先前的 READ_ONLY，READ 和 ONLY 之間是空格，而不是底線。  
  
`UPDATE [OF column_name[ ,... n ] ]`  
在資料指標內定義可更新的資料行。 如果提供 OF *column_name* [**,**...*n*]，則只允許修改所列出的資料行。 未提供任何清單時，除非資料指標已定義為 READ_ONLY，否則可以更新所有資料行。  
  
## <a name="remarks"></a>備註  
在宣告變數之後，會將它初始化成 NULL。 請使用 SET 陳述式，將非 NULL 值指派給宣告的變數。 將值指派給變數的 SET 陳述式會傳回單一值。 當您初始化多個變數時，每個區域變數都要使用個別的 SET 陳述式。  
  
您只能在運算式中使用變數，而不能使用物件名稱或關鍵字。 若要建構動態 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，請使用 EXECUTE。  
  
SET **@** _cursor_variable_ 的語法規則不包括 LOCAL 和 GLOBAL 關鍵字。 當您使用 SET **@** _cursor_variable_ = CURSOR... 語法時，會將資料指標建立成 GLOBAL 或 LOCAL，這會視 [預設為本機資料指標資料庫] 選項的設定而不同。  
  
資料指標變數一律是區域變數，即使它們參考了全域資料指標也是如此。 當資料指標變數參考全域資料指標時，資料指標會同時有全域和本機資料指標參考。 如需詳細資訊，請參閱「範例 C」一節。  
  
如需詳細資訊，請參閱 [DECLARE CURSOR &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-cursor-transact-sql.md)。  
  
您可以將複合指派運算子用於您在運算子右手邊有指派運算式的任何地方，其中包括變數以及 UPDATE、SELECT 和 RECEIVE 陳述式中的 SET。  
  
請勿在 SELECT 陳述式中使用變數來串連值 (也就是計算彙總值)。 可能會發生非預期的查詢結果。 因為 SELECT 清單中的所有運算式 (包括指派) 都不一定剛好針對每個輸出資料列執行一次。 如需詳細資訊，請參閱[這篇知識庫文章](https://support.microsoft.com/kb/287515)。  
  
## <a name="permissions"></a>權限  
需要 public 角色中的成員資格。 所有使用者都可以使用 SET **@** _local_variable_。  
  
## <a name="examples"></a>範例  
  
### <a name="a-printing-the-value-of-a-variable-initialized-by-using-set"></a>A. 列印利用 SET 來初始化的變數值  
下列範例會建立 `@myvar` 變數，將字串值放入這個變數中，再列印 `@myvar` 變數的值。  
  
```sql  
DECLARE @myvar CHAR(20);  
SET @myvar = 'This is a test';  
SELECT @myvar;  
GO  
```  
  
### <a name="b-using-a-local-variable-assigned-a-value-by-using-set-in-a-select-statement"></a>B. 使用以 SELECT 陳述式中的 SET 指派值的區域變數  
下列範例會建立一個名稱為 `@state` 的區域變數，且會在 `SELECT` 陳述式中使用這個區域變數來尋找在 `Oregon` 州的所有員工姓名。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @state CHAR(25);  
SET @state = N'Oregon';  
SELECT RTRIM(FirstName) + ' ' + RTRIM(LastName) AS Name, City  
FROM HumanResources.vEmployee  
WHERE StateProvinceName = @state;  
```  
  
### <a name="c-using-a-compound-assignment-for-a-local-variable"></a>C. 針對區域變數使用複合指派  
下列兩個範例會產生相同的結果。 它們會建立一個名為 `@NewBalance` 的區域變數，並將它乘以 10，然後將區域變數的新值顯示在 `SELECT` 陳述式中。 第二個範例會使用複合指派運算子。  
  
```sql  
/* Example one */  
DECLARE  @NewBalance  INT ;  
SET  @NewBalance  =  10;  
SET  @NewBalance  =  @NewBalance  *  10;  
SELECT  @NewBalance;  
  
/* Example Two */  
DECLARE @NewBalance INT = 10;  
SET @NewBalance *= 10;  
SELECT @NewBalance;  
```  
  
### <a name="d-using-set-with-a-global-cursor"></a>D. 搭配全域資料指標來使用 SET  
下列範例會建立一個區域變數，再將資料指標變數設成全域資料指標名稱。  
  
```sql  
DECLARE my_cursor CURSOR GLOBAL   
FOR SELECT * FROM Purchasing.ShipMethod  
DECLARE @my_variable CURSOR ;  
SET @my_variable = my_cursor ;   
--There is a GLOBAL cursor declared(my_cursor) and a LOCAL variable  
--(@my_variable) set to the my_cursor cursor.  
DEALLOCATE my_cursor;   
--There is now only a LOCAL variable reference  
--(@my_variable) to the my_cursor cursor.  
```  
  
### <a name="e-defining-a-cursor-by-using-set"></a>E. 利用 SET 來定義資料指標  
下列範例會利用 `SET` 陳述式來定義資料指標。  
  
```sql  
DECLARE @CursorVar CURSOR;  
  
SET @CursorVar = CURSOR SCROLL DYNAMIC  
FOR  
SELECT LastName, FirstName  
FROM AdventureWorks2012.HumanResources.vEmployee  
WHERE LastName like 'B%';  
  
OPEN @CursorVar;  
  
FETCH NEXT FROM @CursorVar;  
WHILE @@FETCH_STATUS = 0  
BEGIN  
    FETCH NEXT FROM @CursorVar  
END;  
  
CLOSE @CursorVar;  
DEALLOCATE @CursorVar;  
```  
  
### <a name="f-assigning-a-value-from-a-query"></a>F. 從查詢中指派值  
下列範例會利用查詢來指派變數值。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @rows INT;  
SET @rows = (SELECT COUNT(*) FROM Sales.Customer);  
SELECT @rows;  
```  
  
### <a name="g-assigning-a-value-to-a-user-defined-type-variable-by-modifying-a-property-of-the-type"></a>G. 修改此型別的屬性來指派使用者定義型別的變數值  
下列範例會修改類型之 `Point` 屬性的值來設定使用者定義型別 `X` 的值。  
  
```sql  
DECLARE @p Point;  
SET @p.X = @p.X + 1.1;  
SELECT @p;  
GO  
```  
  
### <a name="h-assigning-a-value-to-a-user-defined-type-variable-by-invoking-a-method-of-the-type"></a>H. 叫用此型別的方法來指派使用者定義型別的變數值  
下列範例會叫用使用者定義型別 **point** 的 `SetXY` 方法來設定該型別的值。  
  
```sql  
DECLARE @p Point;  
SET @p=point.SetXY(23.5, 23.5);  
```  
  
### <a name="i-creating-a-variable-for-a-clr-type-and-calling-a-mutator-method"></a>I. 建立 CLR 型別的變數，並呼叫 mutator 方法  
下列範例會建立 `Point` 類型的變數，然後在 `Point` 中執行 mutator 方法。  
  
```sql  
CREATE ASSEMBLY mytest FROM 'c:\test.dll' WITH PERMISSION_SET = SAFE  
CREATE TYPE Point EXTERNAL NAME mytest.Point  
GO  
DECLARE @p Point = CONVERT(Point, '')  
SET @p.SetXY(22, 23);  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="j-printing-the-value-of-a-variable-initialized-by-using-set"></a>J. 列印利用 SET 來初始化的變數值  
下列範例會建立 `@myvar` 變數，將字串值放入這個變數中，再列印 `@myvar` 變數的值。  
  
```sql  
DECLARE @myvar CHAR(20);  
SET @myvar = 'This is a test';  
SELECT TOP 1 @myvar FROM sys.databases;
```  
  
### <a name="k-using-a-local-variable-assigned-a-value-by-using-set-in-a-select-statement"></a>K. 使用以 SELECT 陳述式中的 SET 指派值的區域變數  
下列範例會建立一個名稱為 `@dept` 的區域變數，且會在 `SELECT` 陳述式中利用這個區域變數來尋找在 `Marketing` 部門中工作的所有員工的姓名。  
  
```sql  
-- Uses AdventureWorks  
  
DECLARE @dept CHAR(25);  
SET @dept = N'Marketing';  
SELECT RTRIM(FirstName) + ' ' + RTRIM(LastName) AS Name  
FROM DimEmployee   
WHERE DepartmentName = @dept;  
```  
  
### <a name="l-using-a-compound-assignment-for-a-local-variable"></a>L. 針對區域變數使用複合指派  
下列兩個範例會產生相同的結果。 它們會建立一個名為 `@NewBalance` 的區域變數，並將它乘以 `10`，然後將區域變數的新值顯示在 `SELECT` 陳述式中。 第二個範例會使用複合指派運算子。  
  
```sql  
/* Example one */  
DECLARE  @NewBalance INT;  
SET  @NewBalance  =  10;  
SET  @NewBalance  =  @NewBalance  *  10;  
SELECT TOP 1 @NewBalance FROM sys.tables;  
  
/* Example Two */  
DECLARE @NewBalance INT = 10;  
SET @NewBalance *= 10;  
SELECT TOP 1 @NewBalance FROM sys.tables;  
```  
  
### <a name="m-assigning-a-value-from-a-query"></a>M. 從查詢中指派值  
下列範例會利用查詢來指派變數值。  
  
```sql  
-- Uses AdventureWorks  
  
DECLARE @rows INT;  
SET @rows = (SELECT COUNT(*) FROM dbo.DimCustomer);  
SELECT TOP 1 @rows FROM sys.tables;  
```  
  
## <a name="see-also"></a>另請參閱  
[複合運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/compound-operators-transact-sql.md)   
[DECLARE @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-local-variable-transact-sql.md)   
[EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)   
[SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
[SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)  
  
  

