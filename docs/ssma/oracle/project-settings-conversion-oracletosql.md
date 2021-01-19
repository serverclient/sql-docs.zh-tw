---
title: 專案設定 (轉換)  (OracleToSQL) |Microsoft Docs
description: 瞭解如何使用 [專案設定] 對話方塊的 [轉換] 頁面，自訂 SSMA 如何將 Oracle 語法轉換成 SQL Server 語法。
author: nahk-ivanov
ms.prod: sql
ms.custom: ''
ms.date: 12/17/2020
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: a98a5e07-eb5e-47b9-a6f2-e2cb3a18309c
ms.author: alexiva
ms.openlocfilehash: a999bcde42029b50772f18925a1f24432d558a3f
ms.sourcegitcommit: 8fcee1bc2503bea81a0c9d02827193ab71d8ca32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98564227"
---
# <a name="project-settings-conversion-oracletosql"></a>專案設定 (轉換) (OracleToSQL)

[**專案設定**] 對話方塊的 [**轉換**] 頁面包含自訂 SSMA 如何將 Oracle 語法轉換成語法的設定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。

您可以在 [**專案設定**] 和 [**預設專案設定**] 對話方塊中使用 [**轉換**] 窗格：

- 若要指定所有 SSMA 專案的設定，請在 [ **工具** ] 功能表上，按一下 [ **預設專案設定**]，選取 [遷移專案類型]，需要從 [ **遷移目標版本** ] 下拉式清單中查看或變更設定，然後按一下左窗格底部的 **[一般** ]，然後按一下 [ **轉換**]。

- 若要指定目前專案的設定，請按一下 [ **工具** ] 功能表上的 [ **專案設定**]，然後按一下左窗格底部的 **[一般** ]，再按一下 [ **轉換**]。

## <a name="built-in-functions-and-supplied-packages"></a>內建函數和提供的封裝

|詞彙|定義|
|-|-|
|**將計數函數轉換成 COUNT_BIG**|如果您的函式 `COUNT` 可能會傳回大於2147483647的值，也就是 2<sup>31</sup>-1，您應該將函數轉換成 `COUNT_BIG` 。<br /><br />如果您選取 **[是]**，SSMA 會將的所有用途轉換成 `COUNT` `COUNT_BIG` 。<br /><br />如果您選取 [ **否**]，則函式會保持原狀 `COUNT` 。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 如果函數傳回大於 2<sup>31</sup>-1 的值，則會傳回錯誤。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/完整模式：** 是的<br />**開放式模式：** 不|
|**將 SUBSTR 函式呼叫轉換為 SUBSTRING 函式呼叫**|SSMA 可以 `SUBSTR` 根據參數的數目，將 Oracle 函式呼叫轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] `substring` 函式呼叫。 如果 SSMA 無法轉換 `SUBSTR` 函式呼叫，或不支援參數數目，SSMA 會將 `SUBSTR` 函式呼叫轉換為自訂的 SSMA 函式呼叫。<br /><br />如果您選取 **[是]**，SSMA 會將 `SUBSTR` 使用三個參數的函式呼叫轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] `substring` 。 其他函式 `SUBSTR` 將會轉換成呼叫自訂 SSMA 函式。<br /><br />如果您選取 [ **否**]，SSMA 會將 `SUBSTR` 函式呼叫轉換為自訂的 SSMA 函式呼叫。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式模式：** 是的<br />**完整模式：** 不|
|**轉換 TO_CHAR (日期、格式) 函式呼叫**|SSMA 可以將 Oracle 轉換 `TO_CHAR(date, format)` 成架構中的程式 `ssma_oracle` 。<br /><br />如果您選取 [ **使用 TO_CHAR_DATE** 函式]，SSMA 會 `TO_CHAR(date, format)` `TO_CHAR_DATE` 使用英文的轉換轉換成函式。<br /><br />如果您選取 [使用 TO_CHAR_DATE_LS 函式] **(NLS 護理)**，SSMA 會 `TO_CHAR(date, format)` `TO_CHAR_DATE_LS` 使用會話語言轉換成函式以進行轉換。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式模式：** 使用 TO_CHAR_DATE 函數<br />**完整模式：** 使用 TO_CHAR_DATE_LS 函數 (NLS 護理) |
|**產生 DBMS_SQL 的錯誤。解析**|如果您選取 [ **錯誤**]，SSMA 會在轉換時產生錯誤 `DBMS_SQL.PARSE` 。<br /><br />如果您選取 [ **警告**]，SSMA 會在轉換時產生警告 `DBMS_SQL.PARSE` 。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br />**預設/開放式/完整模式：** 錯誤|
|**在 CONCAT 函式呼叫中使用 ISNull**|`ISNULL` 語句用於 `CONCAT` 函式呼叫中，以模擬 Oracle 行為。 這項設定有下列選項：<br /><br />YES<br /><br />否<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式模式：** 不<br />**完整模式：** 是的|
|**在 REPLACE 函式呼叫中使用 ISNull**|`ISNULL` 語句用於 `REPLACE` 函式呼叫中，以模擬 Oracle 行為。 這項設定有下列選項：<br /><br />YES<br /><br />否<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式模式：** 不<br />**完整模式：** 是的|
|**盡可能使用原生轉換函數**|如果您選取 **[是]**，SSMA 會盡可能將轉換 `TO_CHAR(date, format)` 成原生轉換函數。<br /><br />如果您選取 [ **否**]，則 SSMA 會將轉換 `TO_CHAR(date, format)` 成 `TO_CHAR_DATE` 或 `TO_CHAR_DATE_LS` (由 **轉換 TO_CHAR (日期、格式化)** 選項) 來定義。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式模式：** 是的<br />**完整模式：** 不|

## <a name="conversion-messages"></a>轉換訊息

|詞彙|定義|
|-|-|
|**產生關於問題的訊息**|指定 SSMA 是否會在轉換期間產生參考用訊息、將它們顯示在 [輸出] 窗格中，然後將它們加入至已轉換的程式碼。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式模式：** 不<br />**完整模式：** 不|

## <a name="miscellaneous-options"></a>其他選項

|詞彙|定義|
|-|-|
|**將 ROWNUM 運算式轉換為整數**|當 SSMA 轉換 `ROWNUM` 運算式時，會將運算式轉換成 `TOP` 子句，後面接著運算式。 下列範例顯示 `ROWNUM` 在 Oracle 語句中 `DELETE` ：<br /><br />`DELETE FROM Table1`<br />`WHERE ROWNUM < expression and Field1 >= 2`<br /><br />下列範例顯示產生的 [!INCLUDE[tsql](../../includes/tsql-md.md)] ：<br /><br />`DELETE TOP (expression-1)`<br />`FROM Table1`<br />`WHERE Field1>=2`<br /><br />`TOP`需要 `TOP` 子句運算式評估為整數。 如果整數是負數，語句將會產生錯誤。<br /><br />如果您選取 **[是]**，SSMA 會將運算式轉換成整數。<br /><br />如果您選取 [ **否**]，SSMA 會將所有非整數運算式標示為已轉換之程式碼中的錯誤。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/完整模式：** 不<br />**開放式模式：** 是的|  
|**預設架構對應**|此設定會指定 Oracle 架構如何對應至 SQL Server 架構。 這項設定有兩個選項可供使用：<br /><br />**架構到資料庫：** 在此模式下，Oracle 架構 `sch1` 預設會對應到 `dbo` SQL Server 資料庫中 SQL Server 架構 `sch1` 。<br /><br />架構 **架構：** 在此模式下，Oracle 架構 `sch1` 預設會對應到 `sch1` [連接] 對話方塊中所提供之預設 SQL Server 資料庫中 SQL Server 架構。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 架構到資料庫|
|**在 ORDER BY 子句中模擬 Oracle null 行為**|`NULL` 和 Oracle 中的值順序不同 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ：<br /><br />在中 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ， `NULL` 值是已排序清單中的最小值。 在遞增清單中， `NULL` 會先顯示值。<br /><br />在 Oracle 中， `NULL` 值是已排序清單中最大的值。 依預設， `NULL` 值會顯示在遞增順序清單中的最後一個。<br /><br />Oracle 具有 `NULLS FIRST` 和 `NULLS LAST` 子句，可讓您變更 Oracle 訂單的方式 `NULL` 。<br /><br />SSMA 可以藉 `ORDER BY` 由檢查值來模擬 Oracle 行為 `NULL` 。 然後，它會先依 `NULL` 指定順序的值來排序，然後依其他值排序。<br /><br />如果您選取 **[是]**，SSMA 會以模擬 oracle 行為的方式轉換 oracle 語句 `ORDER BY` 。<br /><br />如果您選取 [ **否**]，則 SSMA 將忽略 Oracle 規則，並在遇到和子句時產生錯誤訊息 `NULLS FIRST` `NULLS LAST` 。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式模式：** 不<br />**完整模式：** 是的|
|**模擬 SELECT 中的資料列計數例外狀況**|如果 `SELECT` 含有 INTO 子句的語句沒有傳回任何資料列，則 Oracle 會引發 `NO_DATA_FOUND` 例外狀況。 如果語句傳回兩個或多個資料列，則 `TOO_MANY_ROWS` 會引發例外狀況。 如果資料列計數與一個不同，則中的轉換語句 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不會引發任何例外狀況。<br /><br />如果您選取 **[是]**，SSMA 會 `db_error_exact_one_row_check` 在每個語句之後，將呼叫新增至特殊程式 `SELECT` 。 此程式會模擬 `NO_DATA_FOUND` 和 `TOO_MANY_ROWS` 例外狀況。 這是預設值，它可讓您盡可能關閉重新產生 Oracle 行為。 如果原始程式碼具有處理這些錯誤的例外狀況處理常式，您應該一律選擇 **[是]** 。 請注意，如果 `SELECT` 語句出現在使用者定義函數內，此模組將會轉換為預存程式，因為執行預存程式和引發例外狀況與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 函數內容不相容。<br /><br />如果您選取 [ **否**]，就不會產生任何例外狀況。 當 SSMA 轉換使用者自訂函式，而且您想要讓它維持函式時，這項功能就很有用。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|
|**啟用 Fix Advisor**|啟用時，SSMA 會嘗試從您在目標 T-sql 程式碼中所做的修改中學習，並在另一個位置中建議可能的程式碼修正，也就是可以套用類似的模式。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|
|**產生常數運算式資料行別名**|如果清單中的 expression `SELECT` 遺漏別名，SSMA 可以產生常數別名 (例如 `expr1` 、等等 `expr2` ) 或使用運算式本身做為別名。 由於運算式的長度很長，且資料行名稱長度有限，因此針對這類別名使用常數基底名稱比較安全。 雖然這是較安全的選項，但有時不可能，因為產生的資料集會有外部相依性。 在這些情況下，您可能會想要根據其值運算式來命名資料行，類似于 Oracle 的行為。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式模式：** 是的<br />**完整模式：** 不|
|**省略擴充屬性**|啟用時，SSMA 不會將擴充屬性加入至它在目標資料庫中建立的物件。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 不|
|**轉譯錯誤碼**|啟用時，如果找到對應，目標 SQL Server 端的錯誤號碼將會轉譯為 Oracle 錯誤碼。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/完整模式：** 是的<br />**開放式模式：** 不|
|**使用類型參考的完整類型規格**|啟用時，SSMA 會遵守完整的類型規格 (包括常式參數和傳回值的小數位數和精確度) 。 Oracle 不允許常式參數的資料類型引數，但在某些情況下，它們可以隱含地衍生，例如 `%TYPE` 使用和 `%ROWTYPE` 屬性時。 在這種情況下，SSMA 可以使用完整類型規格 (包括精確度和小數位數) 轉換成 SQL Server。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式模式：** 是的<br />**完整模式：** 不|
|**在字串串連中使用 ISNull**|當字串串連包含值時，Oracle 和會傳回 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不同的結果 `NULL` 。 Oracle 會將 `NULL` 值視為空白字元集。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回 `NULL`。<br /><br />如果您選取 **[是]**，SSMA 會將 Oracle 串連字號 ( # A2) 取代為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 串連字號 (+) 。 SSMA 也會檢查串連兩邊的運算式是否有 `NULL` 值。<br /><br />如果您選取 [ **否**]，SSMA 會取代串連字號，但不會檢查 `NULL` 值。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|

## <a name="objects-conversion"></a>物件轉換

|詞彙|定義|
|-|-|
|**在非 Null 的資料行上，使用 SET Null 參考動作來轉換外鍵**|Oracle 允許建立外鍵條件約束， `SET NULL` 因為參考的資料行不允許 null，所以無法執行動作。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不允許這類外鍵設定。<br /><br />如果您選取 **[是]**，則 SSMA 將會在 Oracle 中產生參考動作，但您必須先進行手動變更，才能將條件約束載入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 例如，您可以選擇 `NO ACTION` 而不是 `SET NULL` 。<br /><br />如果您選取 [ **否**]，則會將條件約束標示為錯誤。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 不|
|**轉換子類型**|SSMA 可以透過兩種方式來轉換 PL/SQL 子類型：<br /><br />如果您選取 **[是]**，SSMA 會 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 從子類型建立使用者定義型別，並將它用於此子類型的每個變數。<br /><br />如果您選取 [ **否**]，SSMA 將會以基礎類型取代子類型的所有來源宣告，並照常轉換結果。 在此情況下，不會在中建立其他類型 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 不|
|**轉換同義字**|下列 Oracle 物件的同義字可遷移至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ：<br /><br />資料表和物件資料表<br /><br />視圖和物件檢視<br /><br />預存程式和函數<br /><br />具體化檢視<br /><br />**下列的同義字** 您可以直接參考物件來取代 Oracle 物件：<br /><br />序列<br /><br />Packages<br /><br />JAVA 類別架構物件<br /><br />使用者定義物件類型<br /><br />無法遷移其他同義字。 SSMA 將會產生同義字的錯誤訊息，以及所有使用同義字的參考。<br /><br />如果您選取 **[是]**，SSMA 會 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 根據先前的清單來建立同義字和直接物件參考。<br /><br />如果您選取 [ **否**]，SSMA 就會為此處所列的所有同義字建立直接的物件參考。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|
|**本機模組轉換**|定義在獨立預存程式或函數) 轉換中宣告的 Oracle nested subprogram (類型。<br /><br />如果您選取 [ **內嵌**]，則會將嵌套的 subprogram 呼叫取代為其主體。<br /><br />如果您選取 **預存程式**，則會將嵌套 subprogram 轉換成 SQL Server 預存程式，而且會在此程序呼叫中取代其呼叫。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 內嵌|

## <a name="records-conversion"></a>記錄轉換

|詞彙|定義|
|-|-|
|**將記錄轉換為分隔變數的清單**|SSMA 可以將 Oracle 記錄轉換成以特定結構分隔變數和 XML 變數。<br /><br />如果您選取 **[是]**，SSMA 會將記錄轉換成可分隔變數的清單（如果可能的話）。<br /><br />如果您選取 [ **否**]，SSMA 會將記錄轉換成具有特定結構的 XML 變數。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|
|**使用 SELECT .。。轉換 SELECT 時的 FOR XML .。。INTO 記錄變數**|指定當您選取至記錄變數時，是否要產生 XML 結果集。<br /><br />如果您選取 **[是]**，select 語句會傳回 XML。<br /><br />如果您選取 [ **否**]，select 語句會傳回結果集。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 不|

## <a name="returning-clause-conversion"></a>傳回子句轉換

|詞彙|定義|
|-|-|
|**將 DELETE 子句中的傳回子句轉換成輸出**|Oracle 提供 `RETURNING` 子句作為立即取得已刪除值的方法。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用子句提供該功能 `OUTPUT` 。<br /><br />如果您選取 **[是**]，SSMA 會將 `RETURNING` 語句中的子句轉換 `DELETE` 成 `OUTPUT` 子句。 因為資料表上的觸發程式可以變更值，所以傳回的值可能會與 Oracle 中的值不同 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。<br /><br />如果您選取 [ **否**]，SSMA 會 `SELECT` 在語句之前產生語句 `DELETE` ，以取出傳回的值。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|
|**將 INSERT 語句中的傳回子句轉換成輸出**|Oracle 提供 `RETURNING` 子句作為立即取得插入值的方法。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用子句提供該功能 `OUTPUT` 。<br /><br />如果您選取 **[是]**，SSMA 會將 `RETURNING` 語句中的子句轉換 `INSERT` 成 `OUTPUT` 。 因為資料表上的觸發程式可以變更值，所以傳回的值可能會與 Oracle 中的值不同 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。<br /><br />如果您選取 [ **否**]，SSMA 會藉由插入和選取參考資料表中的值，來模擬 Oracle 功能。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|
|**將 UPDATE 語句中的傳回子句轉換成輸出**|Oracle 提供 `RETURNING` 子句作為立即取得更新值的方法。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用子句提供該功能 `OUTPUT` 。<br /><br />如果您選取 **[是**]，SSMA 會將 `RETURNING` 語句中的子句轉換 `UPDATE` 成 `OUTPUT` 子句。 因為資料表上的觸發程式可以變更值，所以傳回的值可能會與 Oracle 中的值不同 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。<br /><br />如果您選取 [ **否**]，SSMA 會在語句之後產生 select 語句 `UPDATE` ，以抓取傳回的值。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|

## <a name="rowid-generation"></a>ROWID 產生

|詞彙|定義|
|-|-|
|**產生 ROWID 資料行**|當 SSMA 在中建立資料表時 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，可以建立 ROWID 資料行。 當資料移轉時，每個資料列都會取得 `UNIQUEIDENTIFIER` 函數所產生的新值 `newid()` 。<br /><br />如果您選取 **[是]**，則 `ROWID` 會在所有資料表上建立資料行，並在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 您插入值時產生 guid。 如果您打算使用 SSMA 測試人員，請一律選擇 **[是]** 。<br /><br />如果您選取 [ **否**]，則不會將 ROWID 資料行新增至資料表。<br /><br />**針對** `ROWID` 包含觸發程式的資料表，加入包含觸發程式之資料表的 [ROWID] 資料行。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式模式：** 針對具有觸發程式的資料表新增 ROWID 資料行<br /><br />**完整模式：** 是的|
|**產生 ROWID 資料行的唯一索引**|指定 SSMA 是否在產生的資料行上產生唯一的索引資料行 `ROWID` 。 如果選項設定為 [是]，則會產生唯一索引，如果它設定為 [否]，則不會在資料行上產生唯一索引 `ROWID` 。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|

## <a name="sequence-and-identity-conversion"></a>順序和身分識別轉換

|詞彙|定義|
|-|-|
|**將身分識別轉換為**|Oracle 提供多個識別列的設定選項。 SQL Server 中的身分識別功能不支援其中某些選項。<br /><br />保留這些選項的方法是將身分識別轉換為序列。<br /><br />如果您選取 [ **順序**]，Oracle 識別欄位將不會再轉換成 SQL 識別欄位。 相反地，系統會建立序列並使用它來產生資料行的預設值。<br /><br />如果您選取 [身分 **識別**]，Oracle 識別欄位將會轉換成 SQL 識別資料行。 不支援的選項將不會轉換。 <br /><br />如果您選取 [ **最適合**]，SSMA 會根據 Oracle 識別欄位的設定，決定最適合的轉換方法 (身分識別或順序) 。|
|**轉換順序產生器**|在 Oracle 中，您可以使用序列來產生唯一識別碼。<br /><br />SSMA 可將序列轉換成下列各項。<br /><br />使用 SQL Server 序列產生器。<br /><br />使用 SSMA 序列產生器。<br /><br />使用資料行識別。<br /><br />預設選項是使用 SQL Server 序列產生器。 不過，SQL Server 不支援取得目前的序列值 (例如 Oracle sequence `CURRVAL` 方法) 。 如需遷移 Oracle sequence 方法的指引，請參閱 SSMA team blog 網站 `CURRVAL` 。<br /><br />SSMA 也提供將 Oracle sequence 轉換成 SSMA sequence 模擬器的選項。 當您轉換為2012之前的 SQL Server 時，這是預設選項<br /><br />最後，您也可以將指派給資料表中資料行的順序轉換成 SQL Server 的識別值。 您必須在 [Oracle **資料表** ] 索引標籤上指定序列與識別資料行之間的對應|
|**在觸發程式外轉換 CURRVAL**|只有當 **轉換順序** 產生器設定為使用資料 **行識別** 時，才會顯示。 由於 Oracle 序列是與資料表不同的物件，因此使用順序的許多資料表都會使用觸發程式來產生和插入新的序列值。 SSMA 會將這些語句批註化，或在批註化產生錯誤時將它們標示為錯誤。<br /><br />如果您選取 **[是]**，SSMA 會在轉換的序列上將所有外部觸發程式的參考標示為 `CURRVAL` 警告。<br /><br />如果您選取 [ **否**]，SSMA 會在轉換的序列上將所有外部觸發程式的參考標示為 `CURRVAL` 錯誤。|

## <a name="statements-conversion"></a>語句轉換

|詞彙|定義|
|-|-|
|**MERGE 語句的轉換**|如果您選取 **使用 INSERT、UPDATE、DELETE 子句**，SSMA 會將 `MERGE` 語句轉換成 `INSERT` 、 `UPDATE` 、 `DELETE` 語句。<br /><br />如果您選取 **USING MERGE 語句**，SSMA 會將 `MERGE` 語句轉換成 `MERGE` 中的語句 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 使用 MERGE 語句|
|**將呼叫轉換為使用預設引數的 subprograms**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 函數不支援在函式呼叫中省略參數。 此外，函式 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和程式不支援運算式作為預設參數值。<br /><br />如果您選取 **[是]** ，而函式呼叫省略參數，則 SSMA 會將關鍵字 **預設值** 插入函式中，並在正確的位置呼叫。 然後，它會以警告標記通話。<br /><br />如果您選取 [ **否**]，SSMA 會將函式呼叫標示為錯誤。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|
|**將 FORALL 語句轉換成 WHILE 語句**|定義 SSMA 將如何處理 `FORALL` PL/SQL 集合元素上的迴圈。<br /><br />如果您選取 **[是]**，SSMA `WHILE` 會建立一個迴圈，其中逐一取出集合元素。<br /><br />如果您選取 [ **否**]，SSMA 會使用方法從集合產生資料列集 `nodes()` ，並將它當作單一資料表使用。 這會更有效率，但讓輸出程式碼更容易讀取。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式模式：** 不<br />**完整模式：** 是的|
|**將函式呼叫轉換為程序呼叫**|某些 Oracle 函數會定義為自發交易，或包含在中不正確語句 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 在這些情況下，SSMA 會建立程式和函式，這是程式的包裝函式。 轉換的函式會呼叫實進程。<br /><br />SSMA 可以將包裝函式的呼叫轉換成程式的呼叫。 這會建立更容易閱讀的程式碼，並改善效能。 不過，內容不一定會允許它;例如，您不能以程序呼叫取代清單中的函式呼叫 `SELECT` 。 SSMA 有幾個選項可涵蓋常見的案例：<br /><br />如果您選取 [ **永遠**]，SSMA 會嘗試將包裝函式呼叫轉換成程序呼叫。 如果目前的內容不允許這項轉換，就會產生錯誤訊息。 如此一來，產生的程式碼就不會有函式呼叫。<br /><br />如果您選取 [ **可能的話**]，只有在函式有輸出參數時，SSMA 才會移動到程序呼叫。 當無法移動時，會移除參數的輸出屬性。 在所有其他情況下，SSMA 會離開函式呼叫。<br /><br />如果您選取 [ **永不**]，SSMA 會將所有函式呼叫保留為函式呼叫。 有時候，這項選擇可能會因為效能原因而無法接受。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 可能的話|
|**Convert LOCK TABLE 語句**|SSMA 可以將許多 `LOCK TABLE` 語句轉換成資料表提示。 SSMA 無法轉換任何 `LOCK TABLE` 包含 `PARTITION` 、 `SUBPARTITION` 、 `@dblink` 和子句的語句 `NOWAIT` ，而會將這類語句標記為轉換錯誤訊息。<br /><br />如果您選取 **[是]**，SSMA 會將支援的 `LOCK TABLE` 語句轉換成資料表提示。<br /><br />如果您選取 [ **否**]，SSMA 會將所有 `LOCK TABLE` 語句標示為轉換錯誤訊息。<br /><br />下表顯示 SSMA 如何轉換 Oracle 鎖定模式：<br /><br />**Oracle 鎖定模式**<br /><br />`ROW SHARE`<br />`ROW EXCLUSIVE`<br />`SHARE UPDATE = ROW SHARE`<br />`SHARE`<br />`SHARE`<br />`EXCLUSIVE`<br /><br />**SQL Server 資料表提示**<br /><br />`ROWLOCK, HOLDLOCK`<br />`ROWLOCK, XLOCK, HOLDLOCK`<br />`ROWLOCK, HOLDLOCK`<br />`TABLOCK, HOLDLOCK`<br />`TABLOCK, XLOCK, HOLDLOCK`<br />`TABLOCKX, HOLDLOCK`<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|
|**轉換 REF 資料指標輸出參數的開放式語句**|在 Oracle 中， `OPEN .. FOR` 語句可以用來將結果集傳回給 `OUT` 型別的 subprogram 參數 `REF CURSOR` 。 在中 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，預存程式會直接傳回 `SELECT` 語句的結果。<br /><br />SSMA 可以將許多 `OPEN .. FOR` 語句轉換成 `SELECT` 語句。<br /><br />如果您選取 **[是]**，SSMA 會將 `OPEN .. FOR` 語句轉換成 `SELECT` 語句，這會將結果集傳回給用戶端。<br /><br />如果您選取 [ **否**]，SSMA 會在已轉換的程式碼和 [輸出] 窗格中產生錯誤訊息。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|
|**轉換交易處理語句**|SSMA 可以轉換 Oracle 交易處理語句：<br /><br />如果您選取 **[是**]，SSMA 會將 Oracle 交易處理語句轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 語句。<br /><br />如果您選取 [ **否**]，SSMA 就會將交易處理語句標示為轉換錯誤。<br /><br />**注意：** Oracle 會隱含開啟交易。 若要在上模擬這種行為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，您必須在 `BEGIN TRANSACTION` 要啟動交易的地方手動加入語句。 或者，您可以 `SET IMPLICIT_TRANSACTIONS ON` 在會話開始時執行命令。 使用自發 `SET IMPLICIT_TRANSACTIONS ON` 交易轉換副程式時，SSMA 會自動加入。<br /><br />當您在 [ **模式]** 方塊中選取轉換模式時，SSMA 會套用下列設定：<br /><br />**預設/開放式/完整模式：** 是的|

## <a name="see-also"></a>另請參閱

[使用者介面參考 (OracleToSQL)](../../ssma/oracle/user-interface-reference-oracletosql.md)
