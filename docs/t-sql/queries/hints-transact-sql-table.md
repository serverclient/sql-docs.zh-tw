---
description: 提示 (Transact-SQL) - 資料表
title: 資料表提示 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/21/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- TABLE_HINT_TSQL
- Table Hint
dev_langs:
- TSQL
helpviewer_keywords:
- SERIALIZABLE table hint
- UPDLOCK table hint
- HOLDLOCK table hint
- TABLOCKX table hint
- READUNCOMMITTED table hint
- hints [SQL Server], tables
- READCOMMITTEDLOCK table hint
- FORCESCAN table hint
- ROWLOCK table hint
- XLOCK table hint
- NOLOCK table hint
- READPAST table hint
- NOWAIT table hint
- FORCESEEK table hint
- table hints [SQL Server]
- TABLOCK table hint
- REPEATABLEREAD table hint
- READ COMMITTED table hint
- NOEXPAND table hint
- PAGLOCK table hint
ms.assetid: 8bf1316f-c0ef-49d0-90a7-3946bc8e7a89
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: 2043bf4c60a1154b719d81b583d055b60b85c6ec
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172330"
---
# <a name="hints-transact-sql---table"></a>提示 (Transact-SQL) - 資料表
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  資料表提示會在資料操作語言 (DML) 陳述式持續時間覆寫查詢最佳化工具的預設行為，其方式是指定鎖定方法、一或多個索引、查詢處理作業 (例如資料表掃描或索引搜尋) 或是其他選項。 資料表提示會在 DML 陳述式的 FROM 子句中指定，並只會影響該子句中參考的資料表或檢視表。  
  
> [!CAUTION]  
>  由於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢最佳化工具通常會選取最好的查詢執行計畫，因此我們建議只有資深的開發人員和資料庫管理員才應該使用提示，並將其當作最後的解決辦法。  
  
 **適用範圍：**  
  
 [DELETE](../../t-sql/statements/delete-transact-sql.md)  
  
 [INSERT](../../t-sql/statements/insert-transact-sql.md)  
  
 [SELECT](../../t-sql/queries/select-transact-sql.md)  
  
 [UPDATE](../../t-sql/queries/update-transact-sql.md)  
  
 [MERGE](../../t-sql/statements/merge-transact-sql.md)  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
WITH  ( <table_hint> [ [, ]...n ] )  
  
<table_hint> ::=   
{ NOEXPAND [ , INDEX ( <index_value> [ ,...n ] ) | INDEX = ( <index_value> ) ]  
  | INDEX ( <index_value> [ ,...n ] ) | INDEX = ( <index_value> )
  | FORCESEEK [ ( <index_value> ( <index_column_name> [,... ] ) ) ] 
  | FORCESCAN  
  | FORCESEEK  
  | HOLDLOCK   
  | NOLOCK   
  | NOWAIT  
  | PAGLOCK   
  | READCOMMITTED   
  | READCOMMITTEDLOCK   
  | READPAST   
  | READUNCOMMITTED   
  | REPEATABLEREAD   
  | ROWLOCK   
  | SERIALIZABLE   
  | SNAPSHOT   
  | SPATIAL_WINDOW_MAX_CELLS = <integer_value>  
  | TABLOCK   
  | TABLOCKX   
  | UPDLOCK   
  | XLOCK   
}   
  
<table_hint_limited> ::=  
{  
    KEEPIDENTITY   
  | KEEPDEFAULTS   
  | HOLDLOCK   
  | IGNORE_CONSTRAINTS   
  | IGNORE_TRIGGERS   
  | NOLOCK   
  | NOWAIT  
  | PAGLOCK   
  | READCOMMITTED   
  | READCOMMITTEDLOCK   
  | READPAST   
  | REPEATABLEREAD   
  | ROWLOCK   
  | SERIALIZABLE   
  | SNAPSHOT   
  | TABLOCK   
  | TABLOCKX   
  | UPDLOCK   
  | XLOCK   
}   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
WITH **(** \<table_hint> **)** [ [ **,** ]...*n* ]  
但有某些例外，僅當利用 WITH 關鍵字指定提示時，FROM 子句才會支援資料表提示。 資料表提示也必須用括號來指定。  
  
> [!IMPORTANT]  
> 省略 WITH 關鍵字是一項已被取代的功能：[!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
不論是否有 `WITH` 關鍵字，都允許使用下列資料表提示：`NOLOCK`、`READUNCOMMITTED`、`UPDLOCK`、`REPEATABLEREAD`、`SERIALIZABLE`、`READCOMMITTED`、`TABLOCK`、`TABLOCKX`、`PAGLOCK`、`ROWLOCK`、`NOWAIT`、`READPAST`、`XLOCK`、`SNAPSHOT` 和 `NOEXPAND`。 在沒有 WITH 關鍵字的情況下指定這些資料表提示時，應該單獨指定這些提示。 例如：  
  
```sql  
FROM t (TABLOCK)  
```  
  
如果要使用另一個選項指定提示，您必須利用 WITH 關鍵字來指定提示：  
  
```sql  
FROM t WITH (TABLOCK, INDEX(myindex))  
```  
  
我們建議您在資料表提示之間使用逗號。  
  
> [!IMPORTANT]  
> 用空格而不用逗號來分隔提示是一項已被取代的功能：[!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)]  
  
NOEXPAND  
指定當查詢最佳化工具處理查詢時，不展開任何索引檢視表來存取基礎資料表。 查詢最佳化工具在處理檢視表時，會將它視為具有叢集索引的資料表。 NOEXPAND 只適用於索引檢視表。 如需詳細資訊，請參閱[使用 NOEXPAND](#using-noexpand)。  
  
INDEX  **(** _<index\_value>_ [ **,** ... _n_ ] ) | INDEX =  ( _<index\_value>_ **)**  
INDEX() 語法會指定要由查詢最佳化工具在其處理陳述式時使用之一或多個索引的名稱或識別碼。 替代的 `INDEX =` 語法會指定單一索引值。 每份資料表只能指定一個索引提示。  
  
如果叢集索引存在，`INDEX(0)` 會強制執行叢集索引掃描，而 `INDEX(1)` 會強制執行叢集索引掃描或搜尋。 如果沒有叢集索引存在，`INDEX(0)` 會強制執行資料表掃描，並將 `INDEX(1)` 解譯為錯誤。  
  
 如果在單一提示清單中使用多個索引，便會忽略重複項目，且會利用其餘列出的索引來擷取資料表的資料列。 索引提示中的索引順序非常重要。 另外，多個索引提示也會強制執行索引的 AND 作業，而且查詢最佳化工具會在所存取的每個索引上盡量套用多一點的條件。 如果提示索引的集合不含此查詢所參考的所有資料行，則當 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 擷取所有索引資料行之後，將會執行提取作業來擷取其餘的資料行。  
  
> [!NOTE]  
> 當星狀聯結的事實資料表使用參考多個索引的索引提示時，最佳化工具會忽略索引提示，且會傳回一則警告訊息。 另外，指定了索引提示的資料表不接受索引的 OR 作業。  
  
 資料表提示中的最大索引數目是 250 個非叢集索引。  
  
KEEPIDENTITY  
只有在搭配 [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) 使用 BULK 選項時，才適用於 INSERT 陳述式。  
  
 指定識別欄位要使用匯入之資料檔案中的一個或多個識別值。 如果未指定 KEEPIDENTITY，就會驗證這個資料行的識別值，但不會匯入它，而且查詢最佳化工具會根據建立資料表期間所指定的種子值和遞增值來自動指派唯一值。  
  
> [!IMPORTANT]  
> 如果資料檔案中沒有資料表或檢視表中之識別欄位的值，而且識別欄位不是資料表的最後一個資料行，您就必須略過此識別欄位。 如需詳細資訊，請參閱[使用格式檔案略過資料欄位 &#40;SQL Server&#41;](../../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)。 如果順利跳過識別欄位，查詢最佳化工具會自動在匯入的資料表資料列中，指派識別欄位的唯一值。  
  
如需在 `INSERT ... SELECT * FROM OPENROWSET(BULK...)` 陳述式中使用此提示的範例，請參閱[大量匯入資料時保留識別值 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-identity-values-when-bulk-importing-data-sql-server.md)。  
  
如需有關檢查資料表識別值的詳細資訊，請參閱 [DBCC CHECKIDENT &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-checkident-transact-sql.md)。  
  
KEEPDEFAULTS  
只有在搭配 [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) 使用 BULK 選項時，才適用於 INSERT 陳述式。  
  
指定在資料記錄缺少資料行的值時，如果資料表資料行有預設值，便插入這個預設值，而不是 NULL。  
  
如需在 INSERT ...SELECT * FROM OPENROWSET(BULK...) 陳述式中使用此提示的範例，請參閱[大量匯入期間保留 Null 或使用預設值 &#40;SQL Server&#41;](../../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)。  
  
FORCESEEK [ **(** _<index\_value>_ **(** _<index\_column\_name>_ [ **,** ... _n_ ] **))** ]  
指定查詢最佳化工具只使用索引搜尋作業做為資料表或檢視表資料的存取路徑。 

> [!NOTE]
> 從 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] SP1 開始，您也可以指定索引參數。 如此一來，查詢最佳化工具只會考慮至少使用指定索引資料行之指定索引的索引搜尋作業。  
  
 *index_value*  
 索引名稱或索引識別碼值。 您無法指定索引識別碼 0 (堆積)。 如果要傳回索引名稱或識別碼，請查詢 **sys.indexes** 目錄檢視。  
  
 *index_column_name*  
 要加入搜尋作業的索引資料行名稱。 指定 FORCESEEK 搭配索引參數，類似於使用 FORCESEEK 與 INDEX 提示。 但您可以同時指定要搜尋的索引，以及搜尋作業所要考慮的索引資料行，以更有效地控制查詢最佳化工具所使用的存取路徑。 最佳化工具可能會視需要考慮其他資料行。 例如，如果指定了非叢集索引，最佳化工具可能會選擇使用叢集索引鍵資料行及指定的資料行。  
  
您可以使用下列方式指定 FORCESEEK 提示。  
  
|語法|範例|描述|  
|------------|-------------|-----------------|  
|沒有索引或 INDEX 提示|`FROM dbo.MyTable WITH (FORCESEEK)`|查詢最佳化工具只會考慮透過任何相關索引的索引搜尋作業存取資料表或檢視表。|  
|結合 INDEX 提示|`FROM dbo.MyTable WITH (FORCESEEK, INDEX (MyIndex))`|查詢最佳化工具只會考慮利用指定索引的索引搜尋作業存取資料表或檢視表。|  
|透過指定索引和索引資料行參數化|`FROM dbo.MyTable WITH (FORCESEEK (MyIndex (col1, col2, col3)))`|如此一來，查詢最佳化工具只會考慮至少使用指定索引資料行之指定索引的索引搜尋作業存取資料表或檢視。|  
  
使用 FORCESEEK 提示 (搭配或不搭配索引參數) 時，請考慮下列指導方針：  
-   您可以將提示指定為資料表提示或查詢提示。 如需有關查詢提示的詳細資訊，請參閱[查詢提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)。  
-   如果要將 FORCESEEK 套用至索引檢視，也必須指定 NOEXPAND 提示。  
-   每個資料表或檢視表最多可套用提示一次。  
-   您無法指定遠端資料來源的提示。 指定 FORCESEEK 與索引提示會傳回錯誤 7377；而只使用 FORCESEEK 而不使用索引提示則會傳回錯誤 8180。  
-   如果 FORCESEEK 造成找不到任何計畫，將會傳回錯誤 8622。  
  
使用索引參數指定 FORCESEEK 時，請注意下列指導方針和限制：  
-   您不可為 INSERT、UPDATE 或 DELETE 陳述式的目標資料表指定此提示。  
-   此提示不可與 INDEX 提示或其他 FORCESEEK 提示並用。  
-   至少必須指定一個資料行，且該資料行必須是前端索引鍵資料行。  
-   您可以指定其他索引資料行，但無法略過索引鍵資料行。 例如，指定的索引如有包含索引鍵資料行 `a`、`b` 和 `c`，有效的語法是 `FORCESEEK (MyIndex (a))` 和 `FORCESEEK (MyIndex (a, b)`。 無效的語法則是 `FORCESEEK (MyIndex (c))` 和 `FORCESEEK (MyIndex (a, c)`。  
-   在提示中指定的資料行名稱順序，必須符合參考索引中的資料行順序。  
-   您不可指定索引鍵所未定義的資料行。 例如，在非叢集索引中，您只可指定經過定義的索引鍵資料行。 您無法指定自動包含在索引中的叢集索引鍵資料行，但最佳化工具則可加以使用。  
-   您不可指定 xVelocity 記憶體最佳化的資料行存放區索引做為索引參數。 系統會傳回錯誤 366。  
-   修改索引定義 (例如加入或移除資料行) 可能需要修改參考該索引的查詢。  
-   此提示會讓最佳化工具無法使用資料表的任何空間或 XML 索引。  
-   此提示不可與 FORCESCAN 提示並用。  
-   如果是資料分割索引，即無法在 FORCESEEK 提示中指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 隱含加入的資料分割資料行。  
  
> [!CAUTION]  
> 使用參數指定 FORCESEEK，會限制最佳化工具所能使用的計畫數目，其限制幅度將大於指定 FORCESEEK 而不指定參數時的限制。 這可能會導致在許多狀況下發生 `Plan cannot be generated` 錯誤。 在後續版本中，將會從內部修改查詢最佳化工具，以擴大可使用的計畫範圍。  
  
FORCESCAN **適用於**：[!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] SP1 及更新版本。
指定查詢最佳化工具只使用索引掃描作業做為參考資料表或檢視表的存取路徑。 當最佳化工具低估受影響的資料列數，而這些查詢又選擇使用搜尋作業，而不使用掃描作業時，即可使用 FORCESCAN 提示。 發生此狀況時，將只會授與作業少量的記憶體，導致查詢效能受到影響。  
  
您可以使用或不使用 INDEX 提示指定 FORCESCAN。 當與索引提示 (`INDEX = index_name, FORCESCAN`) 並用時，查詢最佳化工具只會利用指定的索引的掃描存取路徑存取參考資料表。 您可以指定 FORCESCAN 與索引提示 INDEX(0)，對基底資料表強制執行資料表掃描作業。  
  
如果是分割區資料表和索引，將會在透過查詢述詞評估刪除分割區之後才套用 FORCESCAN。 這表示掃描只會套用至其餘分割區，而不會套用到整份資料表。  
  
FORCESCAN 提示具有下列限制：  
-   您不可為 INSERT、UPDATE 或 DELETE 陳述式的目標資料表指定此提示。  
-   此提示無法搭配多個索引提示使用。  
-   此提示會讓查詢最佳化工具無法將資料表上的任何空間或 XML 索引納入考量。  
-   您無法指定遠端資料來源的提示。  
-   此提示不可與 FORCESCAN 提示同時指定。  
  
HOLDLOCK  
這相當於 SERIALIZABLE。 如需詳細資訊，請參閱這個主題稍後的 SERIALIZABLE。 HOLDLOCK 只適用於指定了 HOLDLOCK 的資料表或檢視表，且只在其使用所在之陳述式所定義的交易期間內有效。 HOLDLOCK 不可在包含 FOR BROWSE 選項的 SELECT 陳述式中使用。  
  
IGNORE_CONSTRAINTS  
只有在搭配 [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) 使用 BULK 選項時，才適用於 INSERT 陳述式。  
  
指定大量匯入作業忽略資料表的任何條件約束。 根據預設，INSERT 會檢查[唯一條件約束與檢查條件約束](../../relational-databases/tables/unique-constraints-and-check-constraints.md)及[主要與外部索引鍵條件約束](../../relational-databases/tables/primary-and-foreign-key-constraints.md)。 當大量匯入作業指定 IGNORE_CONSTRAINTS 時，INSERT 必須在目標資料表上忽略這些條件約束。 請注意，您不能停用 UNIQUE、PRIMARY KEY 或 NOT NULL 條件約束。  
  
如果輸入資料包含違反條件約束的資料列，您可能會想停用 CHECK 和 FOREIGN KEY 條件約束。 當停用 CHECK 和 FOREIGN KEY 條件約束時，您可以先匯入資料，再利用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式來清理資料。  
  
不過，在忽略 CHECK 和 FOREIGN KEY 條件約束的情況下，系統於作業完成之後，會將資料表上被忽略的每個條件約束於 [sys.check_constraints](../../relational-databases/system-catalog-views/sys-check-constraints-transact-sql.md) 或 [sys.foreign_keys](../../relational-databases/system-catalog-views/sys-foreign-keys-transact-sql.md) 目錄檢視中標示為 **is_not_trusted**。 您應該在某個點上，檢查整份資料表的條件約束。 如果在大量匯入作業之前，資料表不是空的，重新驗證條件約束的成本，可能會超出在累加資料上套用 CHECK 和 FOREIGN KEY 條件約束的成本。  
  
IGNORE_TRIGGERS  
只有在搭配 [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) 使用 BULK 選項時，才適用於 INSERT 陳述式。  
  
指定大量匯入作業忽略資料表所定義的任何觸發程序。 依預設，INSERT 會套用觸發程序。  
  
請只在應用程式不相依於任何觸發程序，且發揮最大效能非常重要時，才使用 IGNORE_TRIGGERS。  
  
NOLOCK  
這相當於 READUNCOMMITTED。 如需詳細資訊，請參閱這個主題稍後的 READUNCOMMITTED。  
  
> [!NOTE]  
> 如果是 UPDATE 或 DELETE 陳述式：[!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
NOWAIT  
指示 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 一旦發現資料表的鎖定時，便立刻傳回訊息。 NOWAIT 相當於針對特定資料表指定 `SET LOCK_TIMEOUT 0`。 同時包含 TABLOCK 提示時，NOWAIT 提示無效。 如果在使用 TABLOCK 提示時要終止查詢而不等候，請改為在查詢前面加上 `SETLOCK_TIMEOUT 0;`。  
  
PAGLOCK  
在通常會採用資料列或索引鍵的個別鎖定，或通常會採用單一資料表鎖定的情況下，頁面會鎖定。 依預設，會使用作業所適用的鎖定模式。 如果是在以 SNAPSHOT 隔離等級操作的交易中指定時，不會採用頁面鎖定，除非 PAGLOCK 是與其他需要鎖定的資料表提示相結合，例如 UPDLOCK 和 HOLDLOCK。  
  
READCOMMITTED  
指定讀取作業使用鎖定或資料列版本設定，以遵守 READ COMMITTED 隔離等級的規則。 如果資料庫選項 READ_COMMITTED_SNAPSHOT 是 OFF，則 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會在讀取資料時取得共用鎖定，並在讀取作業完成時釋放這些鎖定。 如果資料庫選項 READ_COMMITTED_SNAPSHOT 是 ON，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 便不需要鎖定，而會使用資料列版本設定。 如需隔離等級的詳細資訊，請參閱 [SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)。  
  
> [!NOTE]  
> 如果是 UPDATE 或 DELETE 陳述式：[!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
READCOMMITTEDLOCK  
指定讀取作業使用鎖定，以遵守 READ COMMITTED 隔離等級的規則。 不論資料庫選項 READ_COMMITTED_SNAPSHOT 的設定為何，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 一律在讀取資料時取得共用鎖定，並在讀取作業完成時釋放這些鎖定。 如需隔離等級的詳細資訊，請參閱 [SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)。 您不可為 INSERT 陳述式的目標資料表指定此提示，否則會傳回錯誤 4140。  
  
READPAST  
指定 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 不讀取其他交易已鎖定的資料列。 當指定 READPAST 時，系統會略過資料列層級鎖定，但不會略過頁面層級的鎖定。 亦即，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會略過資料列，而不會封鎖目前的交易，直到釋放鎖定為止。 例如，假設資料表 `T1` 包含了值為 1、2、3、4、5 的單一整數資料行。 如果交易 A 將 3 的值變更為 8，但是尚未認可，則 SELECT * FROM T1 (READPAST) 會產生 1、2、4、5 的值。 READPAST 主要是在實作使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料表的工作佇列時用來減少鎖定競爭。 使用 READPAST 的佇列讀取器會略過其他交易已鎖定的佇列項目，直接到下一個可用的佇列項目，不需要等待其他交易釋放鎖定。  
  
UPDATE 或 DELETE 陳述式所參考的任何資料表，以及 FROM 子句所參考的任何資料表，都可以指定 READPAST。 當在 UPDATE 陳述式中指定 READPAST 時，只有在讀取資料來識別要更新的記錄時，才會套用 READPAST，不論是在陳述式的哪個位置指定，都是如此。 INSERT 陳述式 INTO 子句中的資料表不能指定 READPAST。 當讀取外部索引鍵或索引檢視表時，或當修改次要索引時，使用 READPAST 的更新或刪除作業可能會進行封鎖。  
  
您只能在執行 READ COMMITTED 或 REPEATABLE READ 隔離等級的交易中指定 READPAST。 如果是在以 SNAPSHOT 隔離等級操作的交易中指定時，READPAST 必須與其他需要鎖定的資料表提示相結合，例如 UPDLOCK 和 HOLDLOCK。  
  
當 READ_COMMITTED_SNAPSHOT 資料庫選項設為 ON，且下列其中一個條件成立時，無法指定 READPAST 資料表提示：  
-   此工作階段的交易隔離等級為 READ COMMITTED。  
-   READCOMMITTED 資料表提示也會指定於查詢中。  
  
若要在這些情況下指定 READPAST 提示，請移除 READCOMMITTED 資料表提示 (如果有的話)，並在查詢中包含 READCOMMITTEDLOCK 資料表提示。  
  
READUNCOMMITTED  
指定允許中途讀取。 不會發出任何共用鎖定來防止其他交易修改目前交易所讀取的資料，其他交易所設定的獨佔鎖定也不會封鎖目前交易，使它無法讀取鎖定的資料。 允許中途讀取可以提高並行程度，但代價是所讀取的資料修改後來會被其他交易回復。 這可能會使您的交易發生錯誤、為使用者提供永遠不被認可的資料，或是讓使用者看到記錄兩次 (或是根本看不到)。  
  
READUNCOMMITTED 和 NOLOCK 提示只適用於資料鎖定。 所有的查詢 (包括具有 READUNCOMMITTED 和 NOLOCK 提示的查詢)，都會在編譯和執行期間取得 Sch-S (結構描述穩定性) 鎖定。 因此，當並行交易在資料表上保有 Sch-M (結構描述修改) 鎖定時，查詢將會遭到封鎖。 例如，資料定義語言 (DDL) 作業會在修改資料表的結構描述資訊之前先取得 Sch-M 鎖定。 任何並行查詢，包括以 READUNCOMMITTED 或 NOLOCK 提示執行的查詢，會在嘗試取得 Sch-S 鎖定時遭到封鎖。 相反地，保有 Sch-S 鎖定的查詢將會封鎖嘗試取得 Sch-M 鎖定的並行交易。  
  
無法針對插入、更新或刪除作業修改的資料表指定 READUNCOMMITTED 和 NOLOCK。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢最佳化工具會忽略套用在 UPDATE 或 DELETE 陳述式目標資料表的 FROM 子句中的 READUNCOMMITTED 和 NOLOCK 提示。  
  
> [!NOTE]  
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的未來版本將移除套用到 UPDATE 或 DELETE 陳述式目標資料表的 FROM 子句中 READUNCOMMITTED 和 NOLOCK 提示的使用支援。 請避免在新的開發工作中使用此內容中的這些提示，並規劃修改目前在使用這些提示的應用程式。  
  
您可以使用下列其中一個選項，防止交易讀到尚未認可的資料修改 (中途讀取)，同時也將鎖定競爭的情況減到最低：  
-   READ_COMMITTED_SNAPSHOT 資料庫選項設為 ON 的 READ COMMITTED 隔離等級。  
-   SNAPSHOT 隔離等級。  
  
如需隔離等級的詳細資訊，請參閱 [SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)。  
  
> [!NOTE]  
> 若在指定 READUNCOMMITTED 時收到[錯誤訊息 601](../../relational-databases/errors-events/database-engine-events-and-errors.md#errors--2-to-999)，請採用解決鎖死錯誤 ([錯誤訊息 1205](../../relational-databases/errors-events/mssqlserver-1205-database-engine-error.md)) 時相同的方式來解決，然後重試陳述式。  
  
REPEATABLEREAD  
指定利用與執行 REPEATABLE READ 隔離等級之交易相同的鎖定語意來執行掃描。 如需隔離等級的詳細資訊，請參閱 [SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)。  
  
ROWLOCK  
指定通常在採用頁面或資料表鎖定時，採用資料列鎖定。 如果是在以 SNAPSHOT 隔離等級操作的交易中指定時，不會採用資料列鎖定，除非 ROWLOCK 是與其他需要鎖定的資料表提示相結合，例如 UPDLOCK 和 HOLDLOCK。 ROWLOCK 無法搭配具有叢集資料行存放區索引的資料表使用。 下列範例會將[錯誤 651](../../relational-databases/errors-events/database-engine-events-and-errors.md#errors--2-to-999) 傳回應用程式。  

```sql 
UPDATE [dbo].[FactResellerSalesXL_CCI] WITH (ROWLOCK)
SET UnitPrice = 50
WHERE ProductKey = 150;
```  
  
SERIALIZABLE  
這相當於 HOLDLOCK。 使共用鎖定更具限制性的方法是將共用鎖定持續保留到交易完成為止，而不是在不再需要所要求的資料表或資料頁面時，便立即釋放共用鎖定 (不論交易是否完成)。 利用與在 SERIALIZABLE 隔離等級執行之交易相同的語意來執行掃描。 如需隔離等級的詳細資訊，請參閱 [SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)。  
  
SNAPSHOT  
**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。 
  
記憶體最佳化的資料表是在 SNAPSHOT 隔離下存取。 SNAPSHOT 只能搭配經記憶體最佳化的資料表使用 (無法搭配磁碟式資料表使用)，如下列範例所示。 如需詳細資訊，請參閱[記憶體最佳化的資料表簡介](../../relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables.md)。  
  
```sql 
SELECT * 
FROM dbo.Customers AS c WITH (SNAPSHOT)   
LEFT JOIN dbo.[Order History] AS oh   
    ON c.customer_id=oh.customer_id;  
```  
  
SPATIAL_WINDOW_MAX_CELLS = *<integer\_value>*  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
指定要用於鑲嵌幾何或地理物件的資料格數上限。 *<integer\_value>* 是介於 1 到 8192 之間的值。  
  
此選項允許藉由調整主要和次要篩選執行時間之間的取捨，微調查詢執行時間。 較大的數字會降低次要篩選執行時間，但增加主要篩選執行時間，較小的數字會減少主要篩選執行時間，但增加次要篩選器執行時間。 對於較密集的空間資料，較高的數字應該會藉由使用主要篩選提供更好的近似值，並減少次要篩選執行時間，產生更快速的執行時間。 對於較疏鬆的資料，較低的數目會減少主要篩選執行時間。  
  
 這個選項適用於手動和自動方格鑲嵌。  
  
TABLOCK  
指定在資料表層級套用取得的鎖定。 取得的鎖定類型取決於執行的陳述式。 例如，SELECT 陳述式可能會取得共用鎖定。 指定 TABLOCK 可以將共用鎖定套用至整份資料表，而不會在資料列或頁面層級進行套用。 如果同時指定了 HOLDLOCK，就會將資料表鎖定保留到交易結束為止。  
  
當您使用 `INSERT INTO <target_table> SELECT <columns> FROM <source_table>` 陳述式將資料匯入堆積時，可以透過為目標資料表指定 TABLOCK 提示來針對陳述式啟用最低限度記錄和最佳化鎖定。 此外，資料庫的復原模式必須設定為簡單或大量記錄。 TABLOCK 提示也可啟用平行插入至堆積或叢集資料行存放區索引。 如需詳細資訊，請參閱 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)。  
  
當搭配 [OPENROWSET](../../t-sql/functions/openrowset-transact-sql.md) 大量資料列集提供者使用，以將資料匯入資料表時，TABLOCK 會使多個用戶端能夠以最佳化的記錄和鎖定，同時將資料載入目標資料表中。 如需詳細資訊，請參閱[在大量匯入中採用最低限度記錄的必要條件](../../relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import.md)。  
  
TABLOCKX  
指定獨佔鎖定是在資料表上取得。  
  
UPDLOCK  
指定採用更新鎖定，且保留到交易完成為止。 UPDLOCK 只會在資料列層級或頁面層級，為讀取作業採用更新鎖定。 如果並用 UPDLOCK 與 TABLOCK，或因故採用了資料表層級鎖定，將會改採獨佔 (X) 鎖定。  
  
如有指定 UPDLOCK 時，將會忽略 READCOMMITTED 和 READCOMMITTEDLOCK 隔離等級提示。 例如，如果工作階段的隔離等級設定為 SERIALIZABLE，而查詢指定了 (UPDLOCK, READCOMMITTED)，將會忽略 READCOMMITTED 提示，並使用 SERIALIZABLE 隔離等級執行交易。  
  
XLOCK  
指定採用獨佔鎖定，且保留到交易完成為止。 如果指定了 ROWLOCK、PAGLOCK 或 TABLOCK，就會將獨佔鎖定套用在適當的資料粒度層級上。  
  
## <a name="remarks"></a>備註  
如果查詢計劃並未存取資料表，就會忽略資料表提示。 這可能是最佳化工具選擇完全不存取資料表所造成的，也可能是因為改成存取索引檢視表。 在後面一種情況中，您可以利用 OPTION (EXPAND VIEWS) 查詢提示來防止存取索引檢視表。  
  
所有鎖定提示都會傳播到查詢計劃所存取的所有資料表和檢視表，包括檢視表中所參考的資料表和檢視表。 另外，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也會執行對應的鎖定一致性檢查。  
  
取得資料列層級鎖定的 ROWLOCK、UPDLOCK、AND XLOCK 等鎖定提示，會將鎖定放在索引鍵上，而不是實際的資料列上。 例如，如果資料表有非叢集索引，且使用鎖定提示的 SELECT 陳述式是由涵蓋索引來處理，就會取得此涵蓋索引之索引鍵的鎖定，而不是基底資料表之資料列的鎖定。  
  
如果資料表包含計算資料行，且計算資料行是由存取其他資料表之資料行的運算式或函數來計算，資料表提示就不會在這些資料表上使用，也不會傳播。 例如，在查詢中指定資料表的 NOLOCK 資料表提示。 這份資料表擁有多個計算資料行，這些計算資料行會利用存取另一資料表中之資料行的運算式和函數的組合來進行計算。 當存取運算式和函數所參考的資料表時，它們不會使用 NOLOCK 資料表提示。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不允許 FROM 子句中每份資料表的下列每個群組，都有一個以上的資料表提示：  
-   資料粒度提示：PAGLOCK、NOLOCK、READCOMMITTEDLOCK、ROWLOCK、TABLOCK 或 TABLOCKX。  
-   隔離等級提示：HOLDLOCK、NOLOCK、READCOMMITTED、REPEATABLEREAD、SERIALIZABLE。  
  
## <a name="filtered-index-hints"></a>已篩選的索引提示  
 已篩選的索引可以當做資料表提示使用，但是當它未涵蓋此查詢所選取的所有資料列時，會造成查詢最佳化工具產生錯誤 8622。 下列是無效之已篩選的索引提示範例。 此範例會建立已篩選的索引 `FIBillOfMaterialsWithComponentID`，然後將它當做 SELECT 陳述式的索引提示使用。 已篩選的索引述詞包括 ComponentID 533、324 和 753 的資料列。 此查詢述詞也包含 ComponentID 533、324 和 753 的資料列，但是會擴充結果集，使其包含 ComponentID 855 和 924 (這兩者不在已篩選的索引中)。 因此，查詢最佳化工具無法使用已篩選的索引提示，而且會產生錯誤 8622。 如需詳細資訊，請參閱 [Create Filtered Indexes](../../relational-databases/indexes/create-filtered-indexes.md)。  
  
```sql  
IF EXISTS (SELECT name FROM sys.indexes  
    WHERE name = N'FIBillOfMaterialsWithComponentID'   
    AND object_id = OBJECT_ID(N'Production.BillOfMaterials'))  
DROP INDEX FIBillOfMaterialsWithComponentID  
    ON Production.BillOfMaterials;  
GO  
CREATE NONCLUSTERED INDEX "FIBillOfMaterialsWithComponentID"  
    ON Production.BillOfMaterials (ComponentID, StartDate, EndDate)  
    WHERE ComponentID IN (533, 324, 753);  
GO  
SELECT StartDate, ComponentID FROM Production.BillOfMaterials  
    WITH( INDEX (FIBillOfMaterialsWithComponentID) )  
    WHERE ComponentID in (533, 324, 753, 855, 924);  
GO  
```  
  
如果 SET 選項不具已篩選之索引的必要值，查詢最佳化工具將不會考慮索引提示。 如需詳細資訊，請參閱 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)。  
  
## <a name="using-noexpand"></a>使用 NOEXPAND  
NOEXPAND 只適用於 *索引檢視表*。 索引檢視表是建立了唯一叢集索引的檢視表。 如果查詢包含同時在索引檢視表和基底資料表中的資料行參考，而且查詢最佳化工具判斷使用索引檢視表能夠提供最好的查詢執行方法，則查詢最佳化工具會使用檢視表的索引。 此功能稱為 *索引檢視表比對*。 在 [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] SP1 之前，只有特定版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援由查詢最佳化工具自動使用索引檢視表。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本所支援的功能清單，請參閱 [SQL Server 2016 版本所支援的功能](../../sql-server/editions-and-components-of-sql-server-2016.md)、[SQL Server 2017 版本所支援的功能](../../SQL-server/editions-and-components-of-SQL-server-2017.md)與 [SQL Server 2019 版本所支援的功能](../../sql-server/editions-and-components-of-sql-server-version-15.md)。  
  
不過，若要讓查詢最佳化工具考慮比對索引檢視表，或使用由 NOEXPAND 提示所參考的索引檢視表，就必須將下列 SET 選項設為 ON。  

> [!NOTE]
> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 支援在不指定 NOEXPAND 提示的情況下，自動使用索引檢視表。

- ANSI_NULLS
- ANSI_PADDING
- ANSI_WARNINGS
- ARITHABORT<sup>1</sup>
- CONCAT_NULL_YIELDS_NULL
- QUOTED_IDENTIFIER

<sup>1</sup> 當 ANSI_WARNINGS 設為 ON 時，ARITHABORT 也會隱含地設為 ON。 因此，您不需要手動調整這個設定。  

另外，NUMERIC_ROUNDABORT 選項必須設成 OFF。  
  
 若要強制查詢最佳化工具使用索引檢視表的索引，請指定 NOEXPAND 選項。 僅當查詢中指定了檢視的名稱時，才可使用此提示。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不會提供提示以強制於未在 FROM 子句中直接為檢視命名的查詢中使用特定的索引檢視表，但是，即使查詢中未直接參考索引檢視表，查詢最佳化工具仍會考慮使用索引檢視表。 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 只有在使用 NOEXPAND 資料表提示時，才會自動建立索引檢視表的相關統計資料。 省略此提示可能會導致遺漏統計資料的相關執行計畫警告，該警告無法透過手動建立統計資料來解決。 在查詢最佳化期間，若查詢會直接參考檢視並使用了 NOEXPAND 提示，[!INCLUDE[ssde_md](../../includes/ssde_md.md)] 將會使用自動或手動建立的檢視統計資料。    
  
## <a name="using-a-table-hint-as-a-query-hint"></a>將資料表提示當做查詢提示使用  
 也可以使用 OPTION (TABLE HINT) 子句將 *資料表提示* 指定為查詢提示。 我們建議您只在 [計劃指南](../../relational-databases/performance/plan-guides.md)的內容中，才將資料表提示當做查詢提示使用。 如果是特定的查詢，只將這些提示指定為資料表提示。 如需詳細資訊，請參閱[查詢提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)。  
  
## <a name="permissions"></a>權限  
 KEEPIDENTITY、IGNORE_CONSTRAINTS 與 IGNORE_TRIGGERS 提示需要資料表的 `ALTER` 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-the-tablock-hint-to-specify-a-locking-method"></a>A. 使用 TABLOCK 提示指定鎖定方法  
 以下範例會指定在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的 `Production.Product` 資料表上採用共用鎖定，並且將鎖定保留到 UPDATE 陳述式結束為止。  
  
```sql  
UPDATE Production.Product  
WITH (TABLOCK)  
SET ListPrice = ListPrice * 1.10  
WHERE ProductNumber LIKE 'BK-%';  
GO  
```  
  
### <a name="b-using-the-forceseek-hint-to-specify-an-index-seek-operation"></a>B. 使用 FORCESEEK 提示指定索引搜尋作業  
 以下範例會使用 FORCESEEK 提示但不指定索引，強制查詢最佳化工具對 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的 `Sales.SalesOrderDetail` 資料表執行索引搜尋作業。  
  
```sql
SELECT *  
FROM Sales.SalesOrderHeader AS h  
INNER JOIN Sales.SalesOrderDetail AS d WITH (FORCESEEK)  
    ON h.SalesOrderID = d.SalesOrderID   
WHERE h.TotalDue > 100  
AND (d.OrderQty > 5 OR d.LineTotal < 1000.00);  
GO  
  
```  
  
 下列範例會使用 FORCESEEK 提示搭配索引，強制查詢最佳化工具對指定的索引和索引資料行執行索引搜尋作業。  
  
```sql  
SELECT h.SalesOrderID, h.TotalDue, d.OrderQty  
FROM Sales.SalesOrderHeader AS h  
    INNER JOIN Sales.SalesOrderDetail AS d   
    WITH (FORCESEEK (PK_SalesOrderDetail_SalesOrderID_SalesOrderDetailID (SalesOrderID)))   
    ON h.SalesOrderID = d.SalesOrderID   
WHERE h.TotalDue > 100  
AND (d.OrderQty > 5 OR d.LineTotal < 1000.00);   
GO  
  
```  
  
### <a name="c-using-the-forcescan-hint-to-specify-an-index-scan-operation"></a>C. 使用 FORCESCAN 提示指定索引掃描作業  
 以下範例會使用 FORCESCAN 提示，強制查詢最佳化工具對 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的 `Sales.SalesOrderDetail` 資料表執行掃描作業。  
  
```sql  
SELECT h.SalesOrderID, h.TotalDue, d.OrderQty  
FROM Sales.SalesOrderHeader AS h  
    INNER JOIN Sales.SalesOrderDetail AS d   
    WITH (FORCESCAN)   
    ON h.SalesOrderID = d.SalesOrderID   
WHERE h.TotalDue > 100  
AND (d.OrderQty > 5 OR d.LineTotal < 1000.00);  
```  
  
## <a name="see-also"></a>另請參閱  
 [OPENROWSET &#40;Transact-SQL&#41;](../../t-sql/functions/openrowset-transact-sql.md)   
 [提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql.md)   
 [查詢提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)  
  
