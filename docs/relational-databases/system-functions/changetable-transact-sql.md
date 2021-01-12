---
description: CHANGETABLE (Transact-SQL)
title: CHANGETABLE (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/08/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- CHANGETABLE_TSQL
- CHANGETABLE
dev_langs:
- TSQL
helpviewer_keywords:
- CHANGETABLE
- change tracking [SQL Server], CHANGETABLE
ms.assetid: d405fb8d-3b02-4327-8d45-f643df7f501a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 6ffbd16a2a62b9adcc5ff4e2f31dcee9f133538a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098719"
---
# <a name="changetable-transact-sql"></a>CHANGETABLE (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回資料表的變更追蹤資訊。您可以使用這個陳述式來傳回資料表的所有變更，或特定資料列的變更追蹤資訊。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```sql
CHANGETABLE (  
    { CHANGES table , last_sync_version  
    | VERSION table , <primary_key_values> } )  
[AS] table_alias [ ( column_alias [ ,...n ] )  
  
<primary_key_values> ::=  
( column_name [ , ...n ] ) , ( value [ , ...n ] )  
```  
  
## <a name="arguments"></a>引數  
 變更 *資料表* 、 *last_sync_version*  
 傳回資料表的所有變更的追蹤資訊，這些變更是在 *last_sync_version* 指定的版本之後發生的。  
  
 *table*  
 可取得其追蹤變更的使用者定義資料表。 您必須在資料表上啟用變更追蹤。 可以使用一部分、兩部分、三部分或四部分資料表名稱。 資料表名稱可以是資料表的同義字。  
  
 *last_sync_version*  
 當它取得變更時，呼叫的應用程式就必須指定需要變更的時間點。 last_sync_version 會指定該時間點。 此函數會傳回自從該版本以來已經變更之所有資料列的資訊。 此應用程式會進行查詢，以便接收版本大於 last_sync_version 的變更。  
  
 一般來說，在取得變更之前，應用程式會呼叫 **CHANGE_TRACKING_CURRENT_VERSION ( # B1** 來取得下次需要變更時將使用的版本。 因此，這個應用程式不需要解譯或了解實際值。  
  
 由於 last_sync_version 是由呼叫的應用程式所取得，所以此應用程式必須保存這個值。 如果此應用程式遺失這個值，它就必須重新初始化資料。  
  
 *last_sync_version* 為 **Bigint**。 值必須是純量。 運算式會引起語法錯誤。  
  
 如果值為 NULL，則會傳回所有追蹤變更。  
  
 *last_sync_version* 應進行驗證，以確保它不會太舊，因為部分或所有變更資訊可能已根據針對資料庫所設定的保留週期進行清除。 如需詳細資訊，請參閱 [CHANGE_TRACKING_MIN_VALID_VERSION &#40;transact-sql&#41;](../../relational-databases/system-functions/change-tracking-min-valid-version-transact-sql.md) 和 [ALTER DATABASE SET 選項 &#40;transact-sql&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)。  
  
 版本 *資料表*、{<primary_key_values>}  
 針對指定的資料列傳回最新的變更追蹤資訊。 主索引鍵值必須識別資料列。 <primary_key_values> 會識別主鍵資料行並指定值。 您可以用任何順序指定主索引鍵資料行名稱。  
  
 *資料表*  
 可取得其變更追蹤資訊的使用者定義資料表。 您必須在資料表上啟用變更追蹤。 可以使用一部分、兩部分、三部分或四部分資料表名稱。 資料表名稱可以是資料表的同義字。  
  
 *column_name*  
 指定主索引鍵資料行的名稱。 您可以用任何順序指定多個資料行名稱。  
  
 *值*  
 主索引鍵的值。 如果有多個主鍵資料行，則這些值的指定順序必須和資料行出現在 *column_name* 清單中的順序相同。  
  
 按照 *table_alias* [ (*column_alias* [,.。。*n* ] ) ]  
 提供 CHANGETABLE 所傳回之結果的名稱。  
  
 *table_alias*  
 這是 CHANGETABLE 傳回的資料表的別名名稱。 *table_alias* 是必要的，而且必須是有效的 [識別碼](../../relational-databases/databases/database-identifiers.md)。  
  
 *column_alias*  
 CHANGETABLE 所傳回之資料行的資料行選用別名或資料行別名的清單。 這可在結果中有多個重複名稱時，自訂資料行名稱。  
  
## <a name="return-types"></a>傳回型別  
 **table**  
  
## <a name="return-values"></a>傳回值  
  
### <a name="changetable-changes"></a>CHANGETABLE CHANGES  
 指定 CHANGES 時，會傳回具有下列資料行的零個或多個資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|SYS_CHANGE_VERSION|**bigint**|與資料列最後變更相關聯的版本值|  
|SYS_CHANGE_CREATION_VERSION|**bigint**|與最後插入作業相關聯的版本值。|  
|SYS_CHANGE_OPERATION|**nchar(1)**|指定變更的類型：<br /><br /> **U** = 更新<br /><br /> **I** = 插入<br /><br /> **D** = 刪除|  
|SYS_CHANGE_COLUMNS|**varbinary(4100)**|列出 last_sync_version (基準版本) 以來變更的資料行。 請注意，計算資料行絕不會列為已變更。<br /><br /> 當下列任一條件成立時，該值為 NULL：<br /><br /> 未啟用資料行變更追蹤。<br /><br /> 作業為插入或刪除作業。<br /><br /> 已經在一個作業中更新所有非主索引鍵資料行。 這個二進位值不得直接解譯。 相反地，若要解讀，請使用 [CHANGE_TRACKING_IS_COLUMN_IN_MASK ( # B1 ](../../relational-databases/system-functions/change-tracking-is-column-in-mask-transact-sql.md)。|  
|SYS_CHANGE_CONTEXT|**varbinary(128)**|您可以使用 [WITH](../../relational-databases/system-functions/with-change-tracking-context-transact-sql.md) 子句做為 INSERT、UPDATE 或 DELETE 子句的一部分，藉此變更您可以選擇性地指定的內容資訊。|  
|\<primary key column value>|與使用者資料表資料行相同|追蹤資料表的主索引鍵值。 這些值會唯一識別使用者資料表中的每個資料列。|  
  
### <a name="changetable-version"></a>CHANGETABLE VERSION  
 指定 VERSION 時，會傳回具有下列資料行的一個資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|SYS_CHANGE_VERSION|**bigint**|與資料列相關聯的目前變更版本值。<br /><br /> 如果尚未針對比變更追蹤保留週期長的任何週期進行變更，或者資料列在啟用變更追蹤以來尚未經過變更，則值為 NULL。|  
|SYS_CHANGE_CONTEXT|**varbinary(128)**|您可以使用 WITH 子句當做 INSERT、UPDATE 或 DELETE 陳述式一部分選擇性指定的變更內容資訊。|  
|\<primary key column value>|與使用者資料表資料行相同|追蹤資料表的主索引鍵值。 這些值會唯一識別使用者資料表中的每個資料列。|  
  
## <a name="remarks"></a>備註  
 CHANGETABLE 函數通常會以資料表的方式，在查詢的 FROM 子句中使用。  
  
## <a name="changetablechanges"></a>CHANGETABLE(CHANGES...)  
 若要針對新的資料列或修改的資料列取得資料列資料，請使用主索引鍵資料行，將結果集加入到使用者資料表中。 針對已變更之使用者資料表中的每個資料列，只會傳回一個資料列，即使在 *last_sync_version* 值之後，相同的資料列已經有多個變更。  
  
 主索引鍵資料行變更絕不會被標示為更新。 如果主索引鍵值變更，就會被視為刪除舊的值並插入新的值。  
  
 如果您刪除一個資料列，然後插入一個包含舊的主索引鍵的資料列，這個變更在資料列的所有資料行中，就會被視為變更。  
  
 針對 SYS_CHANGE_OPERATION 和 SYS_CHANGE_COLUMNS 資料行所傳回的值會相對於所指定的基準 (last_sync_version) 。 例如，如果在第10版進行插入作業，並在第15版進行更新作業，而且如果基準 *last_sync_version* 為12，將會報告更新。 如果 *last_sync_version* 值為8，則會報告插入。 SYS_CHANGE_COLUMNS 絕不會將計算資料行報告為已經過更新。  
  
 一般來說，會追蹤在使用者資料表中插入、更新或刪除資料的所有作業，包括 MERGE 陳述式在內。  
  
 但是下列會影響使用者資料表資料的作業則不會追蹤：  
  
-   執行 UPDATETEXT 陳述式  
  
     此陳述式已被取代，而且將在未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中移除。 但是，使用 UPDATE 陳述式的 .WRITE 子句所做的變更會受到追蹤。  
  
-   使用 TRUNCATE TABLE 刪除資料列  
  
     當資料表遭到截斷時，會重設與此資料表有關的變更追蹤版本資訊，就像是此資料表上已啟用變更追蹤一樣。 用戶端應用程式應該要驗證它上一次同步處理的版本。 如果資料表已遭到截斷，驗證就會失敗。  
  
## <a name="changetableversion"></a>CHANGETABLE(VERSION...)  
 如果指定了不存在的主索引鍵，就會傳回空的結果集。  
  
 如果尚未針對比保留週期長的任何週期進行變更 (例如，清除作業已經移除了變更資訊)，或者資料列在資料表啟用變更追蹤以來尚未經過變更，則 SYS_CHANGE_VERSION 的值可能就是 NULL。  
  
## <a name="permissions"></a>權限  
 需要 *資料表值所指定之資料表的* 下列許可權，才能取得變更追蹤資訊：  
  
-   主索引鍵資料行的 SELECT 權限  
  
-   VIEW CHANGE TRACKING  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-rows-for-an-initial-synchronization-of-data"></a>A. 針對資料的首次同步處理傳回資料列  
 下列範例會顯示如何針對資料表資料的首次同步處理取得資料。 該查詢會傳回所有資料列資料及其相關聯的版本。 然後您可以將該資料插入或加入將包含已同步處理之資料的系統。  
  
```sql  
-- Get all current rows with associated version  
SELECT e.[Emp ID], e.SSN, e.FirstName, e.LastName,  
    c.SYS_CHANGE_VERSION, c.SYS_CHANGE_CONTEXT  
FROM Employees AS e  
CROSS APPLY CHANGETABLE   
    (VERSION Employees, ([Emp ID], SSN), (e.[Emp ID], e.SSN)) AS c;  
```  
  
### <a name="b-listing-all-changes-that-were-made-since-a-specific-version"></a>B. 列出自特定版本以來進行的所有變更  
 下列範例會列出自指定之版本 (`@last_sync_version)` 以來，在資料表中所進行的全部變更。 [Emp ID] 和 SSN 是複合主索引鍵中的資料行。  
  
```sql  
DECLARE @last_sync_version bigint;  
SET @last_sync_version = <value obtained from query>;  
SELECT [Emp ID], SSN,  
    SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION,  
    SYS_CHANGE_COLUMNS, SYS_CHANGE_CONTEXT   
FROM CHANGETABLE (CHANGES Employees, @last_sync_version) AS C;  
```  
  
### <a name="c-obtaining-all-changed-data-for-a-synchronization"></a>C. 取得同步處理的所有變更資料  
 下列範例會顯示您如何取得已經變更的所有資料。 此查詢會加入包含使用者資料表的變更追蹤資訊，以便傳回使用者資料表資訊。 使用 `LEFT OUTER JOIN` 以便針對刪除的資料列傳回資料列。  
  
```sql  
-- Get all changes (inserts, updates, deletes)  
DECLARE @last_sync_version bigint;  
SET @last_sync_version = <value obtained from query>;  
SELECT e.FirstName, e.LastName, c.[Emp ID], c.SSN,  
    c.SYS_CHANGE_VERSION, c.SYS_CHANGE_OPERATION,  
    c.SYS_CHANGE_COLUMNS, c.SYS_CHANGE_CONTEXT   
FROM CHANGETABLE (CHANGES Employees, @last_sync_version) AS c  
    LEFT OUTER JOIN Employees AS e  
        ON e.[Emp ID] = c.[Emp ID] AND e.SSN = c.SSN;  
```  
  
### <a name="d-detecting-conflicts-by-using-changetableversion"></a>D. 使用 CHANGETABLE(VERSION...) 偵測衝突  
 下列範例會顯示如何僅在資料列上次同步處理後尚未變更的情況下更新資料列。 特定資料列的版本號碼可利用 `CHANGETABLE` 取得。 如果資料列已經經過更新，則不會進行變更，而且查詢會傳回有關資料列最近更新的資訊。  
  
```sql  
-- @last_sync_version must be set to a valid value  
UPDATE  
    SalesLT.Product  
SET  
    ListPrice = @new_listprice  
FROM  
    SalesLT.Product AS P  
WHERE  
    ProductID = @product_id AND  
    @last_sync_version >= ISNULL (  
        (SELECT CT.SYS_CHANGE_VERSION FROM   
            CHANGETABLE(VERSION SalesLT.Product,  
            (ProductID), (P.ProductID)) AS CT),  
        0);  
```  
  
## <a name="see-also"></a>另請參閱  
 [變更追蹤函數 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/change-tracking-functions-transact-sql.md)   
 [追蹤資料變更 &#40;SQL Server&#41;](../../relational-databases/track-changes/track-data-changes-sql-server.md)   
 [CHANGE_TRACKING_IS_COLUMN_IN_MASK &#40;Transact-sql&#41;](../../relational-databases/system-functions/change-tracking-is-column-in-mask-transact-sql.md)   
 [CHANGE_TRACKING_CURRENT_VERSION &#40;Transact-SQL&#41;](../../relational-databases/system-functions/change-tracking-current-version-transact-sql.md)   
 [CHANGE_TRACKING_MIN_VALID_VERSION &#40;Transact-SQL&#41;](../../relational-databases/system-functions/change-tracking-min-valid-version-transact-sql.md)  
  
  
