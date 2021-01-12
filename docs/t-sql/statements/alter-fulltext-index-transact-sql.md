---
description: ALTER FULLTEXT INDEX (Transact-SQL)
title: ALTER FULLTEXT INDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/27/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER FULLTEXT INDEX
- ALTER_FULLTEXT_INDEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- full-text search [SQL Server], search property lists
- modifying full-text indexes
- full-text indexes [SQL Server], modifying
- search property lists [SQL Server], associating with full-text indexes
- ALTER FULLTEXT INDEX statement
ms.assetid: b6fbe9e6-3033-4d1b-b6bf-1437baeefec3
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: d415680b64097abd9674fc6a68f9b2d3a80bb29b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98082604"
---
# <a name="alter-fulltext-index-transact-sql"></a>ALTER FULLTEXT INDEX (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中變更全文檢索索引的屬性。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER FULLTEXT INDEX ON table_name  
   { ENABLE   
   | DISABLE  
   | SET CHANGE_TRACKING [ = ] { MANUAL | AUTO | OFF }  
   | ADD ( column_name   
           [ TYPE COLUMN type_column_name ]   
           [ LANGUAGE language_term ]  
           [ STATISTICAL_SEMANTICS ]  
           [,...n]   
         )  
     [ WITH NO POPULATION ]  
   | ALTER COLUMN column_name  
     { ADD | DROP } STATISTICAL_SEMANTICS  
     [ WITH NO POPULATION ]  
   | DROP ( column_name [,...n] )  
     [ WITH NO POPULATION ]   
   | START { FULL | INCREMENTAL | UPDATE } POPULATION  
   | {STOP | PAUSE | RESUME } POPULATION   
   | SET STOPLIST [ = ] { OFF| SYSTEM | stoplist_name }  
     [ WITH NO POPULATION ]   
   | SET SEARCH PROPERTY LIST [ = ] { OFF | property_list_name }  
     [ WITH NO POPULATION ]   
   }  
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *table_name*  
 這是包括在全文檢索索引中之一個或多個資料行所在的資料表或索引檢視表名稱。 資料庫和資料表擁有者名稱的指定是選擇性的。  
  
 ENABLE | DISABLE  
 告知 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 是否要蒐集 *table_name* 的全文檢索索引資料。 ENABLE 會啟動全文檢索索引；DISABLE 會關閉全文檢索索引。 停用索引時，資料表不支援全文檢索查詢。  
  
 停用全文檢索索引可讓您關閉變更追蹤，但保留全文檢索索引，而且您可以隨時使用 ENABLE 來重新啟動。 停用全文檢索索引時，全文檢索索引中繼資料會保留在系統資料表中。 如果停用全文檢索索引時，CHANGE_TRACKING 處於已啟用狀態 (自動或手動更新)，索引狀態會凍結，而且任何進行中的搜耙都會停止，此時不會追蹤資料表資料的新變更，也不會將它們傳播到索引中。  
  
 SET CHANGE_TRACKING {MANUAL | AUTO | OFF}  
 指定全文檢索索引涵蓋的資料表資料行變更 (更新、刪除或插入)，是否會由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 散佈到全文檢索索引。 透過 WRITETEXT 和 UPDATETEXT 的資料變更並不會反映在全文檢索索引中，變更追蹤並不會收取這些變更。  
  
> [!NOTE]  
>  如需詳細資訊，請參閱[變更追蹤與 NO POPULATION 參數之間的互動](#change-tracking-no-population)。
  
 MANUAL  
 指定追蹤的變更會手動傳播 (藉由呼叫 ALTER FULLTEXT INDEX...START UPDATE POPULATION [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式 (「手動母體擴展」)。 您可以使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 來定期呼叫這個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。  
  
 AUTO  
 指定在修改基底資料表中的資料時，同時自動散佈追蹤變更 (「自動母體擴展」)。 雖然變更會自動傳播，但這些變更可能不會立即反映在全文檢索索引中。 預設值是 AUTO。  
  
 OFF  
 指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不保留索引資料的變更清單。  
  
 ADD | DROP *column_name*  
 指定要在全文檢索索引中新增或刪除的資料行。 資料行必須屬於以下類型：**char**、**varchar****nchar****nvarchar****text**、**ntext**、**image**、**xml**、**varbinary**，或 **varbinary(max)**。  
  
 請只在先前已啟用了全文檢索索引的資料行上，使用 DROP 子句。  
  
 使用 TYPE COLUMN 和 LANGUAGE 來搭配 ADD 子句，在 *column_name* 上設定這些屬性。 當新增資料行時，您必須重新擴展資料表的全文檢索索引，針對這個資料行的全文檢索查詢才能運作。  
  
> [!NOTE]  
>  全文檢索索引是要在加入資料行之後擴展還是從全文檢索索引卸除，將取決於是否啟用變更追蹤及是否指定 WITH NO POPULATION 而定。 如需詳細資訊，請參閱[變更追蹤與 NO POPULATION 參數之間的互動](#change-tracking-no-population)。
  
 TYPE COLUMN *type_column_name*  
 指定用來保存 **varbinary**、**varbinary(max)** 或 **image** 文件之文件類型的資料表資料行名稱 *type_column_name*。 這個資料行 (稱為類型資料行) 包含使用者提供的副檔名 (.doc、.pdf、.xls 等等)。 類型資料行必須屬於下列類型： **char**, **nchar**, **varchar** 或 **nvarchar**。  
  
 只有當 *column_name* 指定將資料儲存為二進位資料的 **varbinary**、**varbinary(max)** 或 **image** 資料行，才應指定 TYPE COLUMN *type_column_name* 否則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回錯誤。  
  
> [!NOTE]  
>  建立索引時，全文檢索引擎會使用各資料表資料列之類型資料行中的縮寫，以便識別要為 *column_name* 中的文件使用哪一種全文檢索搜尋篩選。 篩選會將文件載入為二進位資料流、移除格式資訊，並且將文件中的文字傳送到斷詞工具元件。 如需詳細資訊，請參閱 [設定及管理搜尋的篩選](../../relational-databases/search/configure-and-manage-filters-for-search.md)。  
  
 LANGUAGE *language_term*  
 為 **column_name** 所儲存之資料的語言。  
  
 *language_term* 為選擇性，可以指定成對應於語言地區設定識別碼 (LCID) 的字串、整數或十六進位值。 如果指定 *language_term*，系統就會將它所代表的語言套用至搜尋條件的所有項。 如果未指定任何值，就會使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的預設全文檢索語言。  
  
 請使用 **sp_configure** 預存程序來存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的預設全文檢索語言的相關資訊。  
  
 當指定為字串時，*language_term* 會對應至 **syslanguages** 系統資料表中的 **alias** 資料行值。 字串必須以單引號括住，如 '*language_term*'。 當指定為整數時，*language_term* 是用於識別語言的實際 LCID。 當指定為十六進位值時，*language_term* 是 0x，後面接著 LCID 的十六進位值。 十六進位值不能超出 8 位數，開頭的零也包括在內。  
  
 如果這個值是雙位元組字集 (DBCS) 格式，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將它轉換成 Unicode。  
  
 您必須針對指定為 *language_term* 的語言來啟用資源，如文字分隔和詞幹分析器。 如果這些資源不支援指定的語言， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回錯誤。  
  
 如果是包含多種語言之文字資料的非 BLOB 和非 XML 資料行，或資料行所儲存的文字語言不明，請使用中性 (0x0) 語言資源。 如果是儲存在 XML 或 BLOB 類型資料行中的文件，在建立索引時，將使用文件內的語言編碼。 例如，在 XML 資料行中，XML 文件的 xml:lang 屬性會識別語言。 在查詢時，除非在全文檢索查詢中指定 *language_term* 否則，*language_term* 先前所指定的值會成為全文檢索查詢所用的預設語言。  
  
 STATISTICAL_SEMANTICS  
 **適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 建立其他關鍵片語以及屬於統計語意索引一部分的文件相似度索引。 如需詳細資訊，請參閱[語意搜尋 &#40;SQL Server&#41;](../../relational-databases/search/semantic-search-sql-server.md)。  
  
 [ **,**_...n_]  
 指出 ADD、ALTER 或 DROP 子句可以指定多個資料行。 當指定多個資料行時，請用逗號來分開這些資料行。  
  
 WITH NO POPULATION  
 指定在 ADD 或 DROP 資料行作業或是 SET STOPLIST 作業之後，將不會擴展全文檢索索引。 只有在使用者執行 START...POPULATION 命令時，才擴展索引。  
  
 當指定 NO POPULATION 時， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 並不會擴展索引。 只有在使用者提供了 ALTER FULLTEXT INDEX...START POPULATION 命令之後，才會擴展索引。 未指定 NO POPULATION 時， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會擴展索引。  
  
 如果既啟用 CHANGE_TRACKING，又指定 WITH NO POPULATION，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回錯誤。 如果啟用 CHANGE_TRACKING，但沒有指定 WITH NO POPULATION，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會執行索引的完整母體擴展。  
  
> [!NOTE]  
>  如需詳細資訊，請參閱[變更追蹤與 NO POPULATION 參數之間的互動](#change-tracking-no-population)。
  
 {ADD | DROP } STATISTICAL_SEMANTICS  
 **適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 啟用或停用指定之資料行的統計語意索引。 如需詳細資訊，請參閱[語意搜尋 &#40;SQL Server&#41;](../../relational-databases/search/semantic-search-sql-server.md)。  
  
 START {FULL|INCREMENTAL|UPDATE} POPULATION  
 通知 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 來開始擴展 *table_name* 的全文檢索索引。 如果全文檢索索引擴展已在進行中，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回警告，且不會開始新的擴展。  
  
 FULL  
 指定全文檢索索引要擷取資料表的每個資料列，即使資料列已建立了索引也一樣。  
  
 INCREMENTAL  
 指定全文檢索索引只擷取前次擴展之後又修改過的資料列。 資料表必須有 **timestamp** 類型的資料行，INCREMENTAL 才適用。 如果全文檢索目錄中的資料表並未包含 **timestamp** 類型的資料行，資料表就會進行完整母體擴展。  
  
 UPDATE  
 指定處理上次更新變更追蹤索引之後的所有插入、更新或刪除。 資料表上必須啟用變更追蹤擴展，但不應開啟背景更新索引或自動變更追蹤。  
  
 {STOP | PAUSE | RESUME } POPULATION  
 停止或暫停任何進行中的擴展動作，或是停止或繼續任何已暫停的擴展動作。  
  
 STOP POPULATION 不會停止自動變更追蹤或背景更新索引。 若要停止變更追蹤，請使用 SET CHANGE_TRACKING OFF。  
  
 PAUSE POPULATION 和 RESUME POPULATION 只適用於完全擴展。 它們與其他擴展類型無關，因為其他擴展會從搜耙停止處繼續搜耙。  
  
 SET STOPLIST { OFF| SYSTEM | *stoplist_name* }  
 變更與此索引相關聯的全文檢索停用字詞表 (如果有的話)。  
  
 OFF  
 指定沒有任何停用字詞表要與全文檢索索引產生關聯。  
  
 系統  
 指定預設全文檢索系統 STOPLIST 應該用於這個全文檢索索引。  
  
 *stoplist_name*  
 指定要與全文檢索索引產生關聯的停用字詞表名稱。  
  
 如需詳細資訊，請參閱 [設定及管理全文檢索搜尋的停用字詞與停用字詞表](../../relational-databases/search/configure-and-manage-stopwords-and-stoplists-for-full-text-search.md)。  
  
 SET SEARCH PROPERTY LIST { OFF | *property_list_name* } [ WITH NO POPULATION ]  
 **適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 變更與此索引相關聯的搜尋屬性清單 (如果有的話)。  
  
 OFF  
 指定沒有任何屬性清單要與全文檢索索引產生關聯。 當您關閉全文檢索索引的搜尋屬性清單SET SEARCH PROPERTY LIST OFF) 時，便無法在基底資料表上進行屬性搜尋。  
  
 根據預設，當您關閉現有的搜尋屬性清單時，會自動重新擴展全文檢索索引。 如果當您在關閉搜尋屬性清單時指定 WITH NO POPULATION，則不會發生自動母體重新擴展。 不過，我們建議您在方便時最終要在這個全文檢索索引執行完整母體擴展。 重新擴展全文檢索索引，會移除每個已卸除之搜尋屬性的屬性特定中繼資料，讓全文檢索索引變得更小、更有效率。  
  
 *property_list_name*  
 指定要與全文檢索索引產生關聯的搜尋屬性清單名稱。  
  
 若要將搜尋屬性清單加入至全文檢索索引，需要重新擴展索引，以檢索已註冊用於相關聯搜尋屬性清單的搜尋屬性。 如果您在加入搜尋屬性清單時指定 WITH NO POPULATION，則必須適時在索引上執行母體擴展。  
  
> [!IMPORTANT]  
>  如果全文檢索索引先前與不同的搜尋相關聯，索引必須重建屬性清單，以便讓索引處於一致狀態。 系統會立即截斷並清空索引，直到完整母體擴展執行為止。 如需詳細資訊，請參閱[變更搜尋屬性清單導致重建索引](#change-search-property-rebuild-index)。 
  
> [!NOTE]  
>  您可以將特定的搜尋屬性清單與相同資料庫中的多個全文檢索索引相關聯。  
  
 **在目前資料庫上搜尋屬性清單**  
  
-   [sys.registered_search_property_lists](../../relational-databases/system-catalog-views/sys-registered-search-property-lists-transact-sql.md)  
  
 如需搜尋屬性清單的詳細資訊，請參閱[使用搜尋屬性清單搜尋文件屬性](../../relational-databases/search/search-document-properties-with-search-property-lists.md)。  
  
## <a name="interactions-of-change-tracking-and-no-population-parameter"></a><a name="change-tracking-no-population"></a> 變更追蹤與 NO POPULATION 參數之間的互動  
 全文檢索索引是否會擴展，將取決於是否啟用變更追蹤及是否在 ALTER FULLTEXT INDEX 陳述式中指定 WITH NO POPULATION 而定。 下表摘要列出其互動的結果。  
  
|變更追蹤|WITH NO POPULATION|結果|  
|---------------------|------------------------|------------|  
|未啟用|未指定|在索引上執行完整母體擴展。|  
|未啟用|已指定|要等到發出 ALTER FULLTEXT INDEX...START POPULATION 陳述式之後，才會進行索引的母體擴展。|  
|啟用|已指定|引發錯誤，而且索引不會改變。|  
|啟用|未指定|在索引上執行完整母體擴展。|  
  
 如需填入全文檢索索引的詳細資訊，請參閱[填入全文檢索索引](../../relational-databases/search/populate-full-text-indexes.md)。  
  
## <a name="changing-the-search-property-list-causes-rebuilding-the-index"></a><a name="change-search-property-rebuild-index"></a> 變更搜尋屬性清單導致重建索引  
 第一次全文檢索索引與搜尋屬性清單相關聯時，索引必須重新擴展，以檢索屬性特定的搜尋詞彙。 現有的索引資料不會被截斷。  
  
 但是，如果您將全文檢索索引與不同的屬性清單產生關聯，則會重建索引。 重建會立即截斷全文檢索索引，移除所有現有的資料，而且必須重新擴展索引。 當母體擴展進行時，基底資料表上的全文檢索查詢只會在已由母體擴展編製索引的資料表資料列上進行搜尋。 重新擴展的索引資料會包含新加入搜尋屬性清單已註冊之屬性的中繼資料。  
  
 導致重建的狀況包括：  
  
-   直接切換至不同的搜尋屬性清單 (請參閱本節稍後的＜狀況 A＞)。  
  
-   關閉搜尋屬性清單，而且稍後將索引與任何搜尋屬性清單產生關聯 (請參閱本節稍後的＜狀況 B＞)。  
  
> [!NOTE]  
>  如需全文檢索搜尋使用搜尋屬性清單之運作方式的詳細資訊，請參閱[使用搜尋屬性清單搜尋文件屬性](../../relational-databases/search/search-document-properties-with-search-property-lists.md)。 如需完整母體擴展的資訊，請參閱[填入全文檢索索引](../../relational-databases/search/populate-full-text-indexes.md)。  
  
### <a name="scenario-a-switching-directly-to-a-different-search-property-list"></a>狀況 A：直接切換至不同的搜尋屬性清單  
  
1.  在 `table_1` 上，建立具有搜尋屬性清單 `spl_1` 的全文檢索索引：  
  
    ```sql  
    CREATE FULLTEXT INDEX ON table_1 (column_name) KEY INDEX unique_key_index   
       WITH SEARCH PROPERTY LIST=spl_1,   
       CHANGE_TRACKING OFF, NO POPULATION;   
    ```  
  
2.  在全文檢索索引上執行完整母體擴展：  
  
    ```sql  
    ALTER FULLTEXT INDEX ON table_1 START FULL POPULATION;  
    ```  
  
3.  稍後使用下列陳述式，將全文檢索索引與不同的搜尋屬性清單 `spl_2` 產生關聯：  
  
    ```sql  
    ALTER FULLTEXT INDEX ON table_1 SET SEARCH PROPERTY LIST spl_2;  
    ```  
  
     此陳述式會導致完整母體擴展 (預設行為)。  但在此母體擴展開始之前，Full-Text Engine 會自動截斷索引。  
  
### <a name="scenario-b-turning-off-the-search-property-list-and-later-associating-the-index-with-any-search-property-list"></a>狀況 B：關閉搜尋屬性清單，而且稍後將索引與任何搜尋屬性清單產生關聯  
  
1.  在 `table_1` 上，建立具有搜尋屬性清單 `spl_1` 的全文檢索索引，接著執行自動的完整母體擴展 (預設行為)：  
  
    ```sql  
    CREATE FULLTEXT INDEX ON table_1 (column_name) KEY INDEX unique_key_index   
       WITH SEARCH PROPERTY LIST=spl_1;   
    ```  
  
2.  關閉搜尋屬性清單，如下所示：  
  
    ```sql  
    ALTER FULLTEXT INDEX ON table_1   
       SET SEARCH PROPERTY LIST OFF WITH NO POPULATION;   
    ```  
  
3.  再次將全文檢索索引與相同搜尋屬性清單或另一個搜尋屬性清單產生關聯。  
  
     例如，下列陳述式會將全文檢索索引與原始搜尋屬性清單 `spl_1` 重新建立關聯：  
  
    ```sql  
    ALTER FULLTEXT INDEX ON table_1 SET SEARCH PROPERTY LIST spl_1;  
    ```  
  
     此陳述式會啟動完整母體擴展 (預設行為)。  
  
    > [!NOTE]  
    >  針對不同的搜尋屬性清單 (例如 `spl_2`)，也需要進行重建。  
  
## <a name="permissions"></a>權限  
 使用者必須具有資料表或索引檢視表的 ALTER 權限，或必須是 **sysadmin** 固定伺服器角色的成員，或是 **db_ddladmin** 或 **db_owner** 固定資料庫角色的成員。  
  
 如果指定了 SET STOPLIST，使用者在停用字詞表上必須具有 REFERENCES 權限。 如果指定了 SET SEARCH PROPERTY LIST，使用者必須有搜尋屬性清單的 REFERENCES 權限。 如果指定之停用字詞表或搜尋屬性清單的擁有者有 ALTER FULLTEXT CATALOG 權限，該擁有者可以授與 REFERENCES 權限。  
  
> [!NOTE]  
>  一般使用者會被授與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所隨附之預設停用字詞表的 REFERENCES 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-setting-manual-change-tracking"></a>A. 設定手動變更追蹤  
 下列範例會針對 `JobCandidate` 資料表設定全文檢索索引的手動變更追蹤。  
  
```sql  
USE AdventureWorks2012;  
GO  
ALTER FULLTEXT INDEX ON HumanResources.JobCandidate  
   SET CHANGE_TRACKING MANUAL;  
GO  
```  
  
### <a name="b-associating-a-property-list-with-a-full-text-index"></a>B. 將屬性清單與全文檢索索引產生關聯  
  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 下列範例會將 `DocumentPropertyList` 屬性清單與 `Production.Document` 資料表的全文檢索索引產生關聯。 這個 ALTER FULLTEXT INDEX 陳述式會啟動完整母體擴展，這是 SET SEARCH PROPERTY LIST 子句的預設行為。  
  
> [!NOTE]  
>  如需建立 `DocumentPropertyList` 屬性清單的範例，請參閱 [CREATE SEARCH PROPERTY LIST &#40;Transact-SQL&#41;](../../t-sql/statements/create-search-property-list-transact-sql.md)。  
  
```sql  
USE AdventureWorks2012;  
GO  
ALTER FULLTEXT INDEX ON Production.Document   
   SET SEARCH PROPERTY LIST DocumentPropertyList;   
GO  
```  
  
### <a name="c-removing-a-search-property-list"></a>C. 移除搜尋屬性清單  
  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 下列範例會從 `DocumentPropertyList` 的全文檢索索引移除 `Production.Document` 屬性清單。 在這個範例中，從索引移除屬性沒有急迫性，所以指定了 WITH NO POPULATION 選項。 但是，不再允許對這個全文檢索索引進行屬性層級搜尋。  
  
```sql  
USE AdventureWorks2012;  
GO  
ALTER FULLTEXT INDEX ON Production.Document   
   SET SEARCH PROPERTY LIST OFF WITH NO POPULATION;   
GO  
```  
  
### <a name="d-starting-a-full-population"></a>D. 啟動完整母體擴展  
 下列範例會針對 `JobCandidate` 資料表啟動全文檢索索引的完整母體擴展。  
  
```sql  
USE AdventureWorks2012;  
GO  
ALTER FULLTEXT INDEX ON HumanResources.JobCandidate   
   START FULL POPULATION;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.fulltext_indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-fulltext-indexes-transact-sql.md)   
 [CREATE FULLTEXT INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-fulltext-index-transact-sql.md)   
 [DROP FULLTEXT INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/drop-fulltext-index-transact-sql.md)   
 [全文檢索搜尋](../../relational-databases/search/full-text-search.md)   
 [擴展全文檢索索引](../../relational-databases/search/populate-full-text-indexes.md)  
  
  
