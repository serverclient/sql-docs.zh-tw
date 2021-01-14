---
description: '專案設定 (轉換)  (DB2ToSQL) '
title: 專案設定 (轉換)  (DB2ToSQL) |Microsoft Docs
ms.prod: sql
ms.custom: ''
ms.date: 01/19/2017
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: 538c93cf-c5bb-43d5-b758-186d9fb00c19
author: nahk-ivanov
ms.author: alexiva
ms.openlocfilehash: 2f39965e87911f7fef8d50c0b0888d552a451a90
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172650"
---
# <a name="project-settings-conversion-db2tosql"></a>專案設定 (轉換)  (DB2ToSQL) 
[ **專案設定** ] 對話方塊的 [轉換] 頁面包含自訂 SSMA 如何將 DB2 語法轉換成語法的設定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
您可以在 [ **專案設定** ] 和 [ **預設專案設定** ] 對話方塊中使用 [轉換] 窗格：  
  
-   若要指定所有 SSMA 專案的設定，請在 [ **工具** ] 功能表上，按一下 [ **預設專案設定**]，選取 [遷移專案類型]，需要從 [ **遷移目標版本** ] 下拉式清單中查看或變更設定，然後按一下左窗格底部的 **[一般** ]，然後按一下 [ **轉換**]。  
  
-   若要指定目前專案的設定，請按一下 [ **工具** ] 功能表上的 [ **專案設定**]，然後按一下左窗格底部的 **[一般** ]，再按一下 [ **轉換**]。  
  
## <a name="conversion-messages"></a>轉換訊息  
  
### <a name="generate-messages-about-issues-applied"></a>產生已套用問題的訊息  
指定 SSMA 是否會在轉換期間產生參考用訊息、將它們顯示在 [輸出] 窗格中，然後將它們加入至已轉換的程式碼。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式模式：** 不  
  
**完整模式：** 不  
  
## <a name="miscellaneous-options"></a>其他選項  
  
### <a name="cast-rownum-expressions-as-integers"></a>將 ROWNUM 運算式轉換為整數  
當 SSMA 轉換 ROWNUM 運算式時，它會將運算式轉換成 TOP 子句，後面接著運算式。 下列範例顯示 DB2 DELETE 子句中的 ROWNUM：  
  
`DELETE FROM Table1`  
  
`WHERE ROWNUM < expression and Field1 >= 2`  
  
下列範例顯示產生的 [!INCLUDE[tsql](../../includes/tsql-md.md)] ：  
  
`DELETE TOP (expression-1)`  
  
`FROM Table1`  
  
`WHERE Field1>=2`  
  
頂端的子句運算式必須評估為整數。 如果整數是負數，語句將會產生錯誤。  
  
-   如果您選取 **[是]**，SSMA 會將運算式轉換成整數。  
  
-   如果您選取 [ **否**]，SSMA 會將所有非整數運算式標示為已轉換之程式碼中的錯誤。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/完整模式：** 不  
  
**開放式模式：** 是的  
  
### <a name="default-schema-mapping"></a>預設架構對應  
此設定會指定 DB2 架構如何對應至 SQL Server 架構。 這項設定有兩個選項可供使用：  
  
1.  **架構到資料庫：** 在此模式中，DB2 架構 ' sch1 ' 在 SQL Server 資料庫 ' sch1 ' 中預設會對應至 ' dbo ' SQL Server 架構。  
  
2.  架構 **架構：** 在此模式中，DB2 架構 ' sch1 ' 預設會對應到 ' sch1 ' SQL Server 在 [連接] 對話方塊中提供的預設 SQL Server 資料庫中的架構。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 架構到資料庫  
  
### <a name="conversion-ways-of-merge-statement"></a>MERGE 語句的轉換方式  
  
-   如果您選取 **使用 insert、update、delete 語句**，SSMA 會將合併語句轉換成 INSERT、UPDATE、delete 語句。  
  
-   如果您選取 **使用 MERGE 語句**，SSMA 會在中將 merge 語句轉換成 MERGE 語句 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
> [!WARNING]  
> 此專案設定選項僅適用于 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2008、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2012、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2014。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 使用 MERGE 語句  
  
### <a name="convert-calls-to-subprograms-that-use-default-arguments"></a>將呼叫轉換為使用預設引數的 subprograms  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 函數不支援在函式呼叫中省略參數。 此外，函式 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和程式不支援運算式作為預設參數值。  
  
-   如果您選取 **[是]** ，而函式呼叫省略參數，則 SSMA 會將關鍵字 **預設值** 插入函式中，並在正確的位置呼叫。 然後，它會以警告標記通話。  
  
-   如果您選取 [ **否**]，SSMA 會將函式呼叫標示為錯誤。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 是的  
  
### <a name="convert-count-function-to-count_big"></a>將計數函數轉換成 COUNT_BIG  
如果您的計數函式可能會傳回大於2147483647的值，也就是 2<sup>31</sup>-1，您應該將函數轉換成 COUNT_BIG。  
  
-   如果您選取 **[是]**，SSMA 會將 COUNT 的所有使用轉換成 COUNT_BIG。  
  
-   如果您選取 [ **否**]，則函式會維持為計數。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 如果函數傳回大於 2<sup>31</sup>-1 的值，則會傳回錯誤。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/完整模式：** 是的  
  
**開放式模式：** 不  
  
### <a name="convert-forall-statement-to-while-statement"></a>將 FORALL 語句轉換成 WHILE 語句  
定義 SSMA 將如何處理 PL/SQL 集合元素上的 FORALL 迴圈。  
  
-   如果您選取 **[是]**，SSMA 會建立一個 WHILE 迴圈，其中逐一取出集合元素。  
  
-   如果您選取 [ **否**]，SSMA 會使用 ( ) 方法的節點，從集合產生資料列集，並將它當作單一資料表使用。 這會更有效率，但讓輸出程式碼更容易讀取。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式模式：** 不  
  
**完整模式：** 是的  
  
### <a name="convert-foreign-keys-with-set-null-referential-action-on-column-that-is-not-null"></a>在非 Null 的資料行上，使用 SET Null 參考動作來轉換外鍵  
DB2 允許建立外鍵條件約束，其中無法執行 SET Null 動作，因為參考的資料行中不允許 Null。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不允許這類外鍵設定。  
  
-   如果您選取 **[是]**，SSMA 會產生與 DB2 中的參考動作，但您必須先進行手動變更，才能將條件約束載入至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 例如，您可以選擇 [沒有任何動作]，而不是設定為 Null。  
  
-   如果您選取 [ **否**]，則會將條件約束標示為錯誤。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 不  
  
### <a name="convert-function-calls-to-procedure-calls"></a>將函式呼叫轉換為程序呼叫  
某些 DB2 函數會定義為自發交易，或包含在中不正確語句 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 在這些情況下，SSMA 會建立程式和函式，這是程式的包裝函式。 轉換的函式會呼叫實進程。  
  
SSMA 可以將包裝函式的呼叫轉換成程式的呼叫。 這會建立更容易閱讀的程式碼，並改善效能。 不過，內容不一定會允許它;例如，您不能使用程序呼叫來取代選取清單中的函式呼叫。 SSMA 有幾個選項可涵蓋常見的案例：  
  
-   如果您選取 [ **永遠**]，SSMA 會嘗試將包裝函式呼叫轉換成程序呼叫。 如果目前的內容不允許這項轉換，就會產生錯誤訊息。 如此一來，產生的程式碼就不會有函式呼叫。  
  
-   如果您選取 [ **可能的話**]，只有在函式有輸出參數時，SSMA 才會移動到程序呼叫。 當無法移動時，會移除參數的輸出屬性。 在所有其他情況下，SSMA 會離開函式呼叫。  
  
-   如果您選取 [ **永不**]，SSMA 會將所有函式呼叫保留為函式呼叫。 有時候，這項選擇可能會因為效能原因而無法接受。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 可能的話  
  
### <a name="convert-lock-table-statements"></a>Convert LOCK TABLE 語句  
SSMA 可以將許多鎖定資料表語句轉換成資料表提示。 SSMA 無法轉換包含 PARTITION、SUBPARTITION、和 NOWAIT 子句的任何鎖定資料表語句 @dblink ，並且會使用轉換錯誤訊息來標記這類語句。  
  
-   如果您選取 **[是]**，SSMA 會將支援的鎖定資料表語句轉換成資料表提示。  
  
-   如果您選取 [ **否**]，SSMA 會將所有鎖定資料表語句標示為轉換錯誤訊息。  
  
下表顯示 SSMA 如何轉換 DB2 鎖定模式：  
  
|DB2 鎖定模式|SQL Server 資料表提示|  
|-|-|  
|資料列共用|ROWLOCK、HOLDLOCK|  
|獨佔資料列|ROWLOCK、XLOCK、HOLDLOCK|  
|共用更新 = 資料列共用|ROWLOCK、HOLDLOCK|  
|共用|TABLOCK、HOLDLOCK|  
|共用資料列獨佔|TABLOCK、XLOCK、HOLDLOCK|  
|獨家|TABLOCKX、HOLDLOCK|  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 是的  
  
### <a name="convert-open-for-statements-for-ref-cursor-out-parameters"></a>轉換 REF 資料指標輸出參數的開放式語句  
在 DB2 中，可以使用 OPEN FOR 語句將結果集傳回至類型 REF 資料指標的 subprogram OUT 參數。 在中 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，預存程式會直接傳回 SELECT 語句的結果。  
  
SSMA 可以將許多開啟的語句轉換成 SELECT 語句。  
  
-   如果您選取 **[是]**，SSMA 會將開啟的語句轉換成 select 語句，這會將結果集傳回給用戶端。  
  
-   如果您選取 [ **否**]，SSMA 會在已轉換的程式碼和 [輸出] 窗格中產生錯誤訊息。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 是的  
  
### <a name="convert-record-as-a-list-of-separates-variables"></a>將記錄轉換為分隔變數的清單  
SSMA 可將 DB2 記錄轉換成以特定結構分隔變數和 XML 變數。  
  
-   如果您選取 **[是]**，SSMA 會將記錄轉換成可分隔變數的清單（如果可能的話）。  
  
-   如果您選取 [ **否**]，SSMA 會將記錄轉換成具有特定結構的 XML 變數。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 是的  
  
### <a name="convert-substr-function-calls-to-substring-function-calls"></a>將 SUBSTR 函式呼叫轉換為 SUBSTRING 函式呼叫  
SSMA 可以根據參數的數目，將 DB2 SUBSTR 函式呼叫轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **子字串** 函式呼叫。 如果 SSMA 無法轉換 SUBSTR 函式呼叫，或不支援參數數目，則 SSMA 會將 SUBSTR 函式呼叫轉換為自訂 SSMA 函式呼叫。  
  
-   如果您選取 **[是]**，SSMA 會將使用三個參數的 SUBSTR 函式呼叫轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **子字串**。 其他 SUBSTR 函式將會轉換成呼叫自訂 SSMA 函式。  
  
-   如果您選取 [ **否**]，SSMA 會將 SUBSTR 函式呼叫轉換為自訂 SSMA 函式呼叫。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式模式：** 是的  
  
**完整模式：** 不  
  
### <a name="convert-subtypes"></a>轉換子類型  
SSMA 可以透過兩種方式來轉換 PL/SQL 子類型：  
  
-   如果您選取 **[是]**，SSMA 會 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 從子類型建立使用者定義型別，並將它用於此子類型的每個變數。  
  
-   如果您選取 [ **否**]，SSMA 將會以基礎類型取代子類型的所有來源宣告，並照常轉換結果。 在此情況下，不會在中建立其他類型 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 不  
  
### <a name="convert-synonyms"></a>轉換同義字  
下列 DB2 物件的同義字可以遷移至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ：  
  
-   資料表和物件資料表  
  
-   視圖和物件檢視  
  
-   預存程式和函數  
  
-   具體化檢視  
  
下列 DB2 物件的同義字可由物件的直接參考取代：  
  
-   序列  
  
-   套件  
  
-   JAVA 類別架構物件  
  
-   使用者定義物件類型  
  
無法遷移其他同義字。 SSMA 將會產生同義字的錯誤訊息，以及所有使用同義字的參考。  
  
-   如果您選取 **[是]**，SSMA 會 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 根據先前的清單來建立同義字和直接物件參考。  
  
-   如果您選取 [ **否**]，SSMA 就會為此處所列的所有同義字建立直接的物件參考。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 是的  
  
### <a name="convert-to_chardate-format"></a>轉換 TO_CHAR (日期，格式)   
SSMA 可將 DB2 TO_CHAR 的 (date、format) 轉換成 sysdb 資料庫的程式。  
  
-   如果您選取 [ **使用 TO_CHAR_DATE** 函式]，SSMA 會將 TO_CHAR (日期、格式) 轉換成使用英文語言進行轉換的 TO_CHAR_DATE 函數。  
  
-   如果您選取 [使用 TO_CHAR_DATE_LS 函式] **([NLS 護理)**]，SSMA 會使用轉換的會話語言，將 TO_CHAR (日期、格式) 轉換成 TO_CHAR_DATE_LS 函數  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式模式：** 使用 TO_CHAR_DATE 函數  
  
**完整模式：** 使用 TO_CHAR_DATE_LS 函數 (NLS 護理)   
  
### <a name="convert-transaction-processing-statements"></a>轉換交易處理語句  
SSMA 可以轉換 DB2 交易處理語句：  
  
-   如果您選取 **[是**]，SSMA 會將 DB2 交易處理語句轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 語句。  
  
-   如果您選取 [ **否**]，SSMA 就會將交易處理語句標示為轉換錯誤。  
  
> [!NOTE]  
> DB2 會隱含開啟交易。 若要在上模擬這種行為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，您必須手動加入您想要啟動交易的 BEGIN TRANSACTION 語句。 或者，您可以在會話開始時，執行 SET IMPLICIT_TRANSACTIONS ON 命令。 使用自發交易轉換副程式時，SSMA 會自動將 SET IMPLICIT_TRANSACTIONS。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 是的  
  
### <a name="emulate-db2-null-behavior-in-order-by-clauses"></a>在 ORDER BY 子句中模擬 DB2 null 行為  
Null 值在和 DB2 中的排序方式不同 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ：  
  
-   在中 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，Null 值是已排序清單中的最小值。 在遞增清單中，會先出現 Null 值。  
  
-   在 DB2 中，Null 值是已排序清單中最大的值。 根據預設，Null 值會出現在遞增順序清單中的最後一個。  
  
-   DB2 具有 Null FIRST 和 NullS LAST 子句，可讓您變更 DB2 訂單的 Null。  
  
SSMA 可以藉由檢查 Null 值來模擬 DB2 ORDER BY 行為。 然後，它會先依指定順序依 Null 值排序，然後依其他值排序。  
  
-   如果您選取 **[是]**，SSMA 會以模擬 DB2 ORDER BY 行為的方式轉換 db2 語句。  
  
-   如果您選取 [ **否**]，則 SSMA 會忽略 DB2 規則，並在遇到 null FIRST 和 null LAST 子句時產生錯誤訊息。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式模式：** 不  
  
**完整模式：** 是的  
  
### <a name="emulate-row-count-exceptions-in-select"></a>模擬 SELECT 中的資料列計數例外狀況  
如果含有 INTO 子句的 SELECT 語句不會傳回任何資料列，則 DB2 會引發 NO_DATA_FOUND 例外狀況。 如果語句傳回兩個或多個資料列，則會引發 TOO_MANY_ROWS 的例外狀況。 如果資料列計數與一個不同，則中的轉換語句 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不會引發任何例外狀況。  
  
-   如果您選取 **[是]**，SSMA 會在每個 select 語句之後，將呼叫加入至 sysdb 程式 db_error_exact_one_row_check。 此程式會模擬 NO_DATA_FOUND 並 TOO_MANY_ROWS 例外狀況。 這是預設值，它可讓您盡可能關閉重現 DB2 行為。 如果原始程式碼具有處理這些錯誤的例外狀況處理常式，您應該一律選擇 **[是]** 。 請注意，如果 SELECT 語句發生在使用者自訂函數中，此模組將會轉換為預存程式，因為執行預存程式和引發例外狀況與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 函數內容不相容。  
  
-   如果您選取 [ **否**]，就不會產生任何例外狀況。 當 SSMA 轉換使用者自訂函式，而且您想要讓它維持函式時，這項功能就很有用。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 是的  
  
### <a name="generate-error-for-dbms_sqlparse"></a>產生 DBMS_SQL 的錯誤。解析  
  
-   如果您選取 [ **錯誤**]，SSMA 會在轉換 DBMS_SQL 時產生錯誤。解析。  
  
-   如果您選取 [ **警告**]，SSMA 會在轉換 DBMS_SQL 產生警告。解析。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 錯誤  
  
### <a name="generate-rowid-column"></a>產生 ROWID 資料行  
當 SSMA 在中建立資料表時 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，可以建立 ROWID 資料行。 當資料移轉時，每個資料列都會取得 newid ( # A1 函數所產生的新 UNIQUEIDENTIFIER 值。  
  
-   如果您選取 **[是]**，則會在所有資料表上建立 ROWID 資料行，並在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 您插入值時產生 guid。 如果您打算使用 SSMA 測試人員，請一律選擇 **[是]** 。  
  
-   如果您選取 [ **否**]，則不會將 ROWID 資料行新增至資料表。  
  
-   **針對** 包含觸發程式的資料表新增 rowid 資料行（含觸發程式）。  
  
> [!WARNING]  
> 在 SQL Server 2005、SQL Server 2008 和 SQL Server 2012 和2014的情況下，預設設定為 **具有觸發程式的資料表新增 ROWID 資料行**。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式模式：** 針對具有觸發程式的資料表新增 ROWID 資料行  
  
**完整模式：** 是的  
  
### <a name="generate-unique-index-on-rowid-column"></a>產生 ROWID 資料行的唯一索引  
指定 SSMA 是否在 ROWID 產生的資料行上產生唯一的索引資料行。 如果選項設定為 [是]，則會產生唯一索引，如果它設定為 [否]，則不會在 ROWID 資料行上產生唯一索引。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 是的  
  
### <a name="local-modules-conversion"></a>本機模組轉換  
定義在獨立預存程式或函數) 轉換中宣告的 DB2 nested subprogram (類型。  
  
-   如果您選取 [ **內嵌**]，則會將嵌套的 subprogram 呼叫取代為其主體。  
  
-   如果您選取 **預存程式**，則會將嵌套 subprogram 轉換成 SQL Server 預存程式，而且會在此程序呼叫中取代其呼叫。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 內嵌  
  
### <a name="use-isnull-in-string-concatenation"></a>在字串串連中使用 ISNull  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]當字串串連包含 Null 值時，DB2 和會傳回不同的結果。 DB2 會將 Null 值視為空白字元集。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回 Null。  
  
-   如果您選取 **[是]**，SSMA 會取代 (的 DB2 串連字號 | |具有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 串連字號 (+) 的) 。 SSMA 也會檢查串連兩端的運算式是否有 Null 值。  
  
-   如果您選取 [ **否**]，SSMA 會取代串連字號，但不會檢查是否有 Null 值。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
### <a name="use-isnull-in-replace-function-calls"></a>在 REPLACE 函式呼叫中使用 ISNull  
ISNull 語句是用來取代函式呼叫，以模擬 DB2 行為。 這項設定有下列選項：  
  
-   YES  
  
-   否  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式模式：** 不  
  
**完整模式：** 是的  
  
### <a name="use-isnull-in-concat-function-calls"></a>在 CONCAT 函式呼叫中使用 ISNull  
ISNull 語句會在 CONCAT 函式呼叫中用來模擬 DB2 行為。 這項設定有下列選項：  
  
-   YES  
  
-   否  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式模式：** 不  
  
**完整模式：** 是的  
  
### <a name="use-native-convert-function-when-possible"></a>盡可能使用原生轉換函數  
  
-   如果您選取 **[是]**，則 SSMA 會盡可能將 TO_CHAR (日期、格式) 轉換成原生轉換函數。  
  
-   如果您選取 [ **否**]，則 SSMA 會將 TO_CHAR 的 (日期、格式) 轉換成 TO_CHAR_DATE 或 TO_CHAR_DATE_LS (其定義方式為「轉換 TO_CHAR (日期，格式) 」選項) 。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式模式：** 是的  
  
**完整模式：** 不  
  
### <a name="use-selectfor-xml-when-converting-selectinto-for-record-variable"></a>使用 SELECT .。。轉換 SELECT 時的 FOR XML .。。INTO 記錄變數  
指定當您選取至記錄變數時，是否要產生 XML 結果集。  
  
-   如果您選取 **[是]**，select 語句會傳回 XML。  
  
-   如果您選取 [ **否**]，select 語句會傳回結果集。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 不  
  
## <a name="returning-clause-conversion"></a>傳回子句轉換  
  
### <a name="convert-returning-clause-in-delete-statement-to-output"></a>將 DELETE 子句中的傳回子句轉換成輸出  
DB2 提供一個傳回子句，做為立即取得已刪除之值的方法。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用 OUTPUT 子句提供該功能。  
  
-   如果您選取 **[是]**，SSMA 會將 DELETE 子句中的傳回子句轉換成 OUTPUT 子句。 因為資料表上的觸發程式可以變更值，所以傳回的值可能會與 DB2 中的值不同 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
-   如果您選取 [ **否**]，SSMA 會在 DELETE 子句之前產生 select 語句，以取出傳回的值。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 是的  
  
### <a name="convert-returning-clause-in-insert-statement-to-output"></a>將 INSERT 語句中的傳回子句轉換成輸出  
DB2 提供一個傳回子句，作為立即取得插入值的方法。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用 OUTPUT 子句提供該功能。  
  
-   如果您選取 **[是]**，SSMA 會將 INSERT 語句中的傳回子句轉換成輸出。 因為資料表上的觸發程式可以變更值，所以傳回的值可能會與 DB2 中的值不同 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
-   如果您選取 [ **否**]，SSMA 會藉由插入和選取參考資料表中的值，來模擬 DB2 功能。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 是的  
  
### <a name="convert-returning-clause-in-update-statement-to-output"></a>將 UPDATE 語句中的傳回子句轉換成輸出  
DB2 提供一個傳回子句來立即取得更新的值。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用 OUTPUT 子句提供該功能。  
  
-   如果您選取 **[是]**，SSMA 會將 UPDATE 語句中的傳回子句轉換成 OUTPUT 子句。 因為資料表上的觸發程式可以變更值，所以傳回的值可能會與 DB2 中的值不同 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
-   如果您選取 [ **否**]，SSMA 會在 UPDATE 語句之後產生 select 語句，以抓取傳回的值。  
  
當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：  
  
**預設/開放式/完整模式：** 是的  
  
## <a name="sequence-conversion"></a>順序轉換  
  
### <a name="convert-sequence-generator"></a>轉換順序產生器  
在 DB2 中，您可以使用序列來產生唯一識別碼。  
  
SSMA 可將序列轉換成下列各項。  
  
-   使用 SQL Server 順序產生器 (只有在轉換成 SQL Server 2012 和 SQL Server 2014) 時，才能使用此選項。  
  
-   使用 SSMA 序列產生器。  
  
-   使用資料行識別。  
  
轉換成 SQL Server 2012 或 SQL Server 2014 時，預設選項是使用 SQL Server 順序產生器。 但是，SQL Server 2012 和 SQL Server 2014 不支援取得目前的序列值 (例如 DB2 sequence currval 方法) 的值。 如需遷移 DB2 sequence currval 方法的指引，請參閱 SSMA team blog 網站。  
  
SSMA 也提供將 DB2 sequence 轉換成 SSMA sequence 模擬器的選項。 當您轉換為2012之前的 SQL Server 時，這是預設選項  
  
最後，您也可以將指派給資料表中資料行的順序轉換成 SQL Server 的識別值。 您必須在 [DB2 **資料表** ] 索引標籤上指定序列與識別資料行之間的對應。  
  
### <a name="convert-currval-outside-triggers"></a>在觸發程式外轉換 CURRVAL  
只有當轉換順序產生器設定為使用資料 **行識別** 時，才會顯示。 由於 DB2 序列是與資料表不同的物件，因此使用順序的許多資料表都會使用觸發程式來產生和插入新的序列值。 SSMA 會將這些語句批註化，或在批註化產生錯誤時將它們標示為錯誤。  
  
-   如果您選取 **[是]**，SSMA 會在轉換的序列 CURRVAL 上將所有外部觸發程式的參考標示為警告。  
  
-   如果您選取 [ **否**]，SSMA 會在轉換的序列 CURRVAL 上將所有外部觸發程式的參考標示為錯誤。  
  
## <a name="see-also"></a>另請參閱  
[消費者介面參考 &#40;DB2ToSQL&#41;](../../ssma/db2/user-interface-reference-db2tosql.md)  
  
