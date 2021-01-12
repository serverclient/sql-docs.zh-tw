---
description: DECLARE CURSOR (Transact-SQL)
title: DECLARE CURSOR (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DECLARE_CURSOR_TSQL
- CURSOR_TSQL
- DECLARE CURSOR
- CURSOR
dev_langs:
- TSQL
helpviewer_keywords:
- DECLARE CURSOR statement
- cursors [SQL Server], attributes
- local cursors [SQL Server]
- nesting cursors
- Transact-SQL cursors, attributes
- global cursors [SQL Server]
ms.assetid: 5a3a27aa-03e8-4c98-a27e-809282379b21
author: cawrites
ms.author: chadam
ms.openlocfilehash: 5fcd8a9ca3239d368b25f7444c11c311e4b1d306
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102474"
---
# <a name="declare-cursor-transact-sql"></a>DECLARE CURSOR (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  定義 [!INCLUDE[tsql](../../includes/tsql-md.md)] 伺服器資料指標的屬性，例如其捲動行為以及用來建立資料指標運作所在之結果集的查詢。 `DECLARE CURSOR` 可接受以 ISO 標準為基礎的語法，以及使用一組 [!INCLUDE[tsql](../../includes/tsql-md.md)] 延伸模組的語法。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ISO Syntax  
DECLARE cursor_name [ INSENSITIVE ] [ SCROLL ] CURSOR   
     FOR select_statement   
     [ FOR { READ ONLY | UPDATE [ OF column_name [ ,...n ] ] } ]  
[;]  
Transact-SQL Extended Syntax  
DECLARE cursor_name CURSOR [ LOCAL | GLOBAL ]   
     [ FORWARD_ONLY | SCROLL ]   
     [ STATIC | KEYSET | DYNAMIC | FAST_FORWARD ]   
     [ READ_ONLY | SCROLL_LOCKS | OPTIMISTIC ]   
     [ TYPE_WARNING ]   
     FOR select_statement   
     [ FOR UPDATE [ OF column_name [ ,...n ] ] ]  
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *cursor_name*  
 這是所定義之 [!INCLUDE[tsql](../../includes/tsql-md.md)] 伺服器資料指標的名稱。 *cursor_name* 必須符合識別碼的規則。  
  
 INSENSITIVE  
 定義一個資料指標，它會建立資料暫存複本供資料指標本身使用。 對於資料指標的所有要求都會從 **tempdb** 中的這個暫存資料表來回答；因此，對基底資料表所做的修改並不會反映在對這個資料指標所做之擷取傳回的資料中，而這個資料指標不允許修改。 使用 ISO 語法時若省略了 `INSENSITIVE`，則任何使用者對基礎資料表所做的已認可刪除及更新動作，都會反映在後續的擷取中。  
  
 SCROLL  
 指定提供所有擷取選項 (`FIRST`、`LAST`、`PRIOR`、`NEXT`、`RELATIVE`、`ABSOLUTE`)。 如果 ISO `DECLARE CURSOR` 中沒有指定 `SCROLL`，則 `NEXT` 是唯一支援的擷取選項。 如果也指定了 `FAST_FORWARD`，就無法指定 `SCROLL`。 如果未指定 `SCROLL`，則只能使用擷取選項 `NEXT` 且資料指標會變成 `FORWARD_ONLY`。
  
 *select_statement*  
 這是定義資料指標結果集的標準 `SELECT` 陳述式。 資料指標宣告的 *select_statement* 中不允許關鍵字 `FOR BROWSE` 和 `INTO`。  
  
 如果 *select_statement* 中的子句與所要求資料指標類型的功能相衝突，則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會隱含地將資料指標轉換為其他類型。  
  
 READ ONLY  
 防止利用這個資料指標進行更新。 無法在 `WHERE CURRENT OF` 子句中、在 `UPDATE` 或 `DELETE` 陳述式中參考該資料指標。 這個選項會覆寫要更新之資料指標的預設功能。  
  
 UPDATE [OF *column_name* [**,**...*n*]]  
 在資料指標內定義可更新的資料行。 如果指定了 OF <column_name> [, <... n>]，只允許修改所列出的資料行。 如果指定 `UPDATE` 時沒有同時指定資料行清單，則可更新所有的資料行。  
  
*cursor_name*  
這是所定義之 [!INCLUDE[tsql](../../includes/tsql-md.md)] 伺服器資料指標的名稱。 *cursor_name* 必須符合識別碼的規則。  
  
LOCAL  
指定已建立資料指標的批次、預存程序或觸發程序，其資料指標的範圍為本機範圍。 資料指標名稱只在這個範圍內有效。 批次、預存程序或觸發程序內的區域資料指標變數或是預存程序 `OUTPUT` 參數可以參考資料指標。 `OUTPUT` 參數是用來將本機資料指標傳回呼叫的批次、預存程序或觸發程序，它們可將參數指派到資料指標變數，以在預存程序中止後參考資料指標。 當批次、預存程序或觸發程序結束時，除非在 `OUTPUT` 參數中傳回資料指標，否則，會隱含地將資料指標取消配置。 如果在 `OUTPUT` 參數中傳回資料指標，當最後一個參考資料指標的變數取消配置或離開範圍時，系統便會將資料指標取消配置。  
  
GLOBAL  
指定連接的資料指標範圍為全域。 連接所執行的任何預存程序或批次內都可以參考資料指標名稱。 只有在中斷連接時，才會隱含地取消配置資料指標。  
  
> [!NOTE]  
>  若未指定 `GLOBAL` 或 `LOCAL`，預設值是由 **default to local cursor** 資料庫選項的設定所控制。  
  
FORWARD_ONLY  
指定資料指標只能向前移動，以及從第一個資料列捲動到最後一個資料列。 `FETCH NEXT` 是唯一支援的擷取選項。 擷取資料列時，可看見目前使用者執行 (或其他使用者認可) 的所有 INSERT、UPDATE 和 DELETE 陳述式如何影響結果集中的資料列。 不過，由於無法反向捲動資料指標，因此擷取資料列之後對資料庫中資料列所進行的變更，都無法經由資料指標看見。 順向資料指標預設是動態的，這表示當處理目前的資料列時會偵測到所有變更。 這會加速資料指標的開啟，並讓結果集顯示對基礎資料表所做的更新。 雖然順向資料指標不支援反向捲動，但應用程式可以藉由關閉並重新開啟資料指標，來返回結果集的開頭。 如果指定 `FORWARD_ONLY` 但沒有包含 `STATIC`、`KEYSET` 或 `DYNAMIC` 關鍵字，則資料指標會以動態資料指標運作。 如果 `FORWARD_ONLY`和 `SCROLL` 都未指定，則預設值是 `FORWARD_ONLY`，除非已指定關鍵字 `STATIC`、`KEYSET` 或 `DYNAMIC`。 `STATIC`、`KEYSET` 和 `DYNAMIC` 資料指標預設為 `SCROLL`。 不同於 ODBC 和 ADO 等資料庫 API，`STATIC`、`KEYSET` 和 `DYNAMIC` [!INCLUDE[tsql](../../includes/tsql-md.md)] 資料指標都支援 `FORWARD_ONLY`。  
   
 STATIC  
指定資料指標一律會以第一次開啟資料指標時的原狀顯示結果集，並建立資料的暫存複本以供資料指標使用。 對於資料指標的所有要求都會從 **tempdb** 中的這個暫存資料表來回答。 因此，對基底資料表所做的插入、更新和刪除並不會反映在對這個資料指標所做之擷取傳回的資料中，而且這個資料指標不會偵測在開啟資料指標之後對結果集之成員資格、順序或值所做的變更。 雖然不需要執行這項操作，但靜態資料指標可能會偵測自己的更新、刪除和插入。 例如，假設靜態資料指標擷取一個資料列，而其他應用程式接著更新該資料列。 如果應用程式從靜態資料指標重新擷取該資料列，它所看到的值會保持不變，即使其他應用程式已進行變更亦然。 支援所有捲動類型。 
  
KEYSET  
指定在開啟資料指標時，修正資料指標中之資料列的成員資格和順序。 用來唯一識別資料列的索引鍵集會建置於 **tempdb** 中名為 **keyset** 的資料表內。 這個資料指標會提供在靜態與動態資料指標之間偵測變更的功能。 如同靜態資料指標，它不一定會偵測結果集的成員資格和順序變更。 如同動態資料指標，它會偵測結果集中的資料列值變更。 索引鍵集驅動資料指標是由稱為索引鍵集的一組唯一識別碼 (索引鍵) 所控制。 索引鍵是從結果集中唯一識別資料列的一組資料行建立的。 索引鍵集是一組取自由查詢陳述式傳回之所有資料列的索引鍵值。 使用索引鍵集驅動資料指標，會在資料指標中為每個資料列建置和保留一個索引鍵，並儲存在用戶端工作站或伺服器上。 當您存取每個資料列時，會使用此預存金鑰從資料來源擷取目前的資料值。 在索引鍵集驅動資料指標中，當完整擴展索引鍵集時，結果集的成員資格會遭到凍結。 之後，在重新開啟之前，都不會在結果集中新增或更新受影響的成員資格。 當使用者捲動結果集時，可看見資料值的變更 (索引鍵集擁有者或其他處理序所做的變更)：
-  如果刪除某個資料列，嘗試擷取該資料列的動作會傳回值為 -2 的 `@@FETCH_STATUS`，因為已刪除的資料列會在結果集中顯示為間距。 索引鍵集中存在資料列的索引鍵，但結果集中不再有此資料列。 
-  只有在關閉並重新開啟資料指標之後，才能看見其他處理序在資料指標外部執行的插入。 在結果集結尾可看見從資料指標內部執行的插入。
-  從資料指標之外更新索引鍵值，類似於先刪除舊資料列，再插入新資料列。 含有新值的資料列為不可見，且嘗試擷取含舊值的資料列傳回值為 -2 的 `@@FETCH_STATUS`。 如果更新是藉由指定 `WHERE CURRENT OF` 子句透過資料指標完成，新值的值便為可見。 

> [!NOTE]  
> 如果查詢所參考的資料表中至少有一個沒有唯一的索引鍵，則索引鍵集資料指標會轉換為靜態資料指標。  
  
DYNAMIC  
定義資料指標，在您捲動資料指標並擷取新的記錄時，反映結果集中資料列的所有資料變更，不論這些變更發生於資料指標內部，還是其他使用者在資料指標外部所做的變更。 因此，您可以透過資料指標看到所有使用者執行的全部 UPDATE、INSERT 和 DELETE 陳述式作業。 每次提取時，資料列的資料值、順序和成員資格都有可能改變。 動態資料指標不支援 `ABSOLUTE` 擷取選項。 至於資料指標外的更新必須經過認可後才看得見 (除非資料指標交易隔離等級設定為 `UNCOMMITTED`)。 例如，假設動態資料指標擷取兩個資料列，而其他應用程式接著更新其中一個資料列並刪除另一個資料列。 如果動態資料指標之後擷取這些資料列，它會找不到已刪除的資料列，但會顯示已更新資料列的新值。 
  
FAST_FORWARD  
指定 `FORWARD_ONLY`、`READ_ONLY` 資料指標，且啟用效能最佳化。 如果也指定了 `SCROLL` 或 `FOR_UPDATE`，就無法指定 `FAST_FORWARD`。 這種資料指標類型不允許從資料指標內部修改資料。  
  
> [!NOTE]  
> `FAST_FORWARD` 和 `FORWARD_ONLY` 都可用於相同的 `DECLARE CURSOR` 陳述式。  
  
READ_ONLY  
防止利用這個資料指標進行更新。 無法在 `WHERE CURRENT OF` 子句中、在 `UPDATE` 或 `DELETE` 陳述式中參考該資料指標。 這個選項會覆寫要更新之資料指標的預設功能。  
  
SCROLL_LOCKS  
指定藉由資料指標進行的定位更新或刪除一定會成功。 當資料列讀入資料指標時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會鎖定這些資料列，以確保之後可對它們進行修改。 如果也指定了 `FAST_FORWARD` 或 `STATIC`，就無法指定 `SCROLL_LOCKS`。  
  
OPTIMISTIC  
指定如果將資料列讀入資料指標之後，又更新了這些資料列，則透過資料指標來進行的定位更新或刪除不會成功。 當資料列讀入資料指標時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不會鎖定資料列。 它會改用 **timestamp** 資料行值的比較，或者，如果資料表沒有 **timestamp** 資料行則使用總和檢查碼值，來判斷在將資料列讀入資料指標之後，該資料列是否已被修改。 如果修改了資料列，試圖執行的定位更新或刪除便會失敗。 如果也指定了 `FAST_FORWARD`，就無法指定 `OPTIMISTIC`。  
  
 TYPE_WARNING  
 指定當資料指標從要求的類型隱含地轉換成另一個類型時，便傳送一則警告訊息給用戶端。  
  
 *select_statement*  
 這是定義資料指標結果集的標準 SELECT 陳述式。 資料指標宣告的 *select_statement* 中不允許關鍵字 `COMPUTE`、`COMPUTE BY`、`FOR BROWSE` 和 `INTO`。  
  
> [!NOTE]  
> 您可以在資料指標宣告中使用查詢提示，但如果您也使用 `FOR UPDATE OF` 子句，請在 `FOR UPDATE OF` 之後指定 `OPTION (<query_hint>)`。  
  
如果 *select_statement* 中的子句與所要求資料指標類型的功能相衝突，則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會隱含地將資料指標轉換為其他類型。 如需詳細資訊，請參閱＜隱含資料指標轉換＞。  
  
FOR UPDATE [OF *column_name* [**,**...*n*]]  
在資料指標內定義可更新的資料行。 如果提供了 `OF <column_name> [, <... n>]`，便只允許修改列出的資料行。 若指定 `UPDATE` 時未加上資料行清單，除非指定 `READ_ONLY` 這個並行選項，否則所有資料行皆可更新。  
  
## <a name="remarks"></a>備註  
`DECLARE CURSOR` 定義 [!INCLUDE[tsql](../../includes/tsql-md.md)] 伺服器資料指標的屬性，例如其捲動行為以及用來建立資料指標運作所在之結果集的查詢。 `OPEN` 陳述式可擴展結果集，而 `FETCH` 會從結果集中傳回一個資料列。 `CLOSE` 陳述式會釋放與資料指標相關聯的目前結果集。 `DEALLOCATE` 陳述式則會釋放資料指標所使用的資源。  
  
`DECLARE CURSOR` 陳述式的第一種格式是使用 ISO 語法來宣告資料指標的行為。 `DECLARE CURSOR` 的第二種格式是使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 延伸模組，讓您使用與 ODBC 或 ADO 資料庫 API 資料指標功能中相同的資料指標類型來定義資料指標。  
  
您不能混用這兩種格式。 如果您在 `CURSOR` 關鍵字之前指定 `SCROLL` 或 `INSENSITIVE` 關鍵字，就不能在 `CURSOR` 和 `FOR <select_statement>` 關鍵字之間使用任何關鍵字。 如果您在 `CURSOR` 和 `FOR <select_statement>` 關鍵字之間指定任何關鍵字，就不能在 `CURSOR` 關鍵字之前指定 `SCROLL` 或 `INSENSITIVE`。  
  
如果使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語法的 `DECLARE CURSOR` 沒有指定 `READ_ONLY`、`OPTIMISTIC` 或 `SCROLL_LOCKS`，結果會如下：  
  
-   若 `SELECT` 陳述式不支援更新 (權限不足、存取的遠端資料表不支援更新等等)，則資料指標為 `READ_ONLY`。  
  
-   `STATIC` 和 `FAST_FORWARD` 資料指標預設為 `READ_ONLY`。  
  
-   `DYNAMIC` 和 `KEYSET` 資料指標預設為 `OPTIMISTIC`。  
  
只有其他的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式可以參考資料指標名稱。 資料庫 API 函數無法參考這些名稱。 例如，在宣告資料指標後，OLE DB、ODBC 或 ADO 函數或方法都無法參考資料指標名稱。 資料指標資料列不能使用 API 的提取函數或方法提取；只有 [!INCLUDE[tsql](../../includes/tsql-md.md)] FETCH 陳述式可以提取這些資料列。  
  
在宣告資料指標後，下列系統預存程序即可用來判斷資料指標的特性。  
  
|系統預存程序|描述|  
|------------------------------|-----------------|  
|**sp_cursor_list**|傳回目前連接可見的資料指標清單及其屬性。|  
|**sp_describe_cursor**|描述某個資料指標的屬性，例如，它是一個順向資料指標或捲動資料指標。|  
|**sp_describe_cursor_columns**|描述資料指標結果集中的資料行屬性。|  
|**sp_describe_cursor_tables**|描述資料指標所存取的基底資料表。|  
  
 您可以在宣告資料指標的 *select_statement* 中使用變數。 在資料指標宣告之後，資料指標變數值便不會改變。  
  
## <a name="permissions"></a>權限  
 `DECLARE CURSOR` 的權限預設會授與在資料指標所使用的檢視、資料表和資料行上有 `SELECT` 權限的任何使用者。
 
## <a name="limitations-and-restrictions"></a>限制事項

您無法在具有叢集資料行存放區索引的資料表上使用資料指標或觸發程序。 此限制不適用於非叢集資料行存放區索引。您可以在具有非叢集資料行存放區索引的資料表上使用資料指標和觸發程序。 
  
## <a name="examples"></a>範例  
  
### <a name="a-using-simple-cursor-and-syntax"></a>A. 使用簡單資料指標和語法  

在開啟這個資料指標時所產生的結果集內，包含了資料表的所有資料列及所有資料行。 可以更新這個資料指標，而且所有更新和刪除都會在針對這個資料指標所做的提取中表示。 `FETCH NEXT` 是唯一可用的提取，因為尚未指定 `SCROLL` 選項。  
 
```sql  
DECLARE vend_cursor CURSOR  
    FOR SELECT * FROM Purchasing.Vendor  
OPEN vend_cursor  
FETCH NEXT FROM vend_cursor;  
```  
  
### <a name="b-using-nested-cursors-to-produce-report-output"></a>B. 使用巢狀資料指標產生報表輸出  
 下列範例顯示如何讓資料指標形成巢狀結構，以產生複雜報告。 內部資料指標宣告給每個供應商。  
  
```sql  
SET NOCOUNT ON;  
  
DECLARE @vendor_id INT, @vendor_name NVARCHAR(50),  
    @message VARCHAR(80), @product NVARCHAR(50);  
  
PRINT '-------- Vendor Products Report --------';  
  
DECLARE vendor_cursor CURSOR FOR   
SELECT VendorID, Name  
FROM Purchasing.Vendor  
WHERE PreferredVendorStatus = 1  
ORDER BY VendorID;  
  
OPEN vendor_cursor  
  
FETCH NEXT FROM vendor_cursor   
INTO @vendor_id, @vendor_name  
  
WHILE @@FETCH_STATUS = 0  
BEGIN  
    PRINT ' '  
    SELECT @message = '----- Products From Vendor: ' +   
        @vendor_name  
  
    PRINT @message  
  
    -- Declare an inner cursor based     
    -- on vendor_id from the outer cursor.  
  
    DECLARE product_cursor CURSOR FOR   
    SELECT v.Name  
    FROM Purchasing.ProductVendor pv, Production.Product v  
    WHERE pv.ProductID = v.ProductID AND  
    pv.VendorID = @vendor_id  -- Variable value from the outer cursor  
  
    OPEN product_cursor  
    FETCH NEXT FROM product_cursor INTO @product  
  
    IF @@FETCH_STATUS <> 0   
        PRINT '         <<None>>'       
  
    WHILE @@FETCH_STATUS = 0  
    BEGIN  
  
        SELECT @message = '         ' + @product  
        PRINT @message  
        FETCH NEXT FROM product_cursor INTO @product  
        END  
  
    CLOSE product_cursor  
    DEALLOCATE product_cursor  
        -- Get the next vendor.  
    FETCH NEXT FROM vendor_cursor   
    INTO @vendor_id, @vendor_name  
END   
CLOSE vendor_cursor;  
DEALLOCATE vendor_cursor;  
```  
  
## <a name="see-also"></a>另請參閱  
 [@@FETCH_STATUS &#40;Transact-SQL&#41;](../../t-sql/functions/fetch-status-transact-sql.md)   
 [CLOSE &#40;Transact-SQL&#41;](../../t-sql/language-elements/close-transact-sql.md)   
 [資料指標 &#40;Transact-SQL&#41;](../../t-sql/language-elements/cursors-transact-sql.md)   
 [DEALLOCATE &#40;Transact-SQL&#41;](../../t-sql/language-elements/deallocate-transact-sql.md)   
 [FETCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/fetch-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)  
  
  
