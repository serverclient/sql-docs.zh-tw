---
description: FREETEXT (Transact-SQL)
title: FREETEXT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 10/23/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FREETEXT
- FREETEXT_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- full-text search [SQL Server], meaning matches
- meaning matches [full-text search]
- FREETEXT predicate (Transact-SQL)
- words in predicate [full-text search]
- column searches [full-text search]
ms.assetid: 2f199d3c-440e-4bcf-bdb5-82bb3994005d
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: c4ed81c783dcd8f3fc850432f89b3f7498c16664
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596234"
---
# <a name="freetext-transact-sql"></a>FREETEXT (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  這是在 [!INCLUDE[tsql](../../includes/tsql-md.md)] SELECT 陳述式的 [!INCLUDE[tsql](../../includes/tsql-md.md)] [WHERE 子句](../../t-sql/queries/where-transact-sql.md)中使用的述詞，可在包含字元型資料類型的全文檢索索引資料行上執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 全文檢索搜尋。 這個述詞會搜尋意義與搜尋條件的文字符合但用字不完全相同的值。 使用 FREETEXT 時，全文檢索查詢引擎會在內部對 *freetext_string* 執行下列動作、為每個詞彙指派加權，然後找出相符項目：  
  
-   依據文字界限將字串分隔成單字 (斷詞)。  
  
-   產生字組的字形變化 (詞幹分析)。  
  
-   依據同義字中的相符項目識別詞彙的展開或取代清單。  
  
> [!NOTE]  
>  如需有關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所支援全文檢索搜尋形式的資訊，請參閱[使用全文檢索搜尋進行查詢](../../relational-databases/search/query-with-full-text-search.md)。  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
FREETEXT ( { column_name | (column_list) | * }   
          , 'freetext_string' [ , LANGUAGE language_term ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *column_name*  
 這是在 FROM 子句中指定之資料表一個或多個全文檢索索引資料行的名稱。 資料行可為以下類型：**char**、**varchar**、**nchar**、**nvarchar**、**text**、**ntext**、**image**、**xml**、**varbinary** 或 **varbinary(max)** 。  
  
 *column_list*  
 指出您可以指定多個資料行，各資料行用逗號分隔。 *column_list* 必須括在括號中。 除非已指定 *language_term*，否則 *column_list* 之所有資料行的語言都必須相同。  
  
 \*  
 指定所有已登錄全文檢索搜尋的資料行都應該用來搜尋指定的 *freetext_string*。 如果 FROM 子句中有多個資料表，就必須以資料表名稱限定 \*。 除非已指定 *language_term*，否則資料表之所有資料行的語言都必須相同。  
  
 *freetext_string*  
 這是要在 *column_name* 中搜尋的文字。 您可以輸入任何文字，其中包括單字、片語或句子。 在全文檢索索引中找到任何詞彙或任何詞彙的各種形式，都會產生相符項目。  
  
 與在 CONTAINS 和 CONTAINSTABLE 搜尋條件中 AND 是作為關鍵字的情況不同，用於 *freetext_string* 中時，'and' 一字會被視為非搜尋字或 [停用字詞](../../relational-databases/search/configure-and-manage-stopwords-and-stoplists-for-full-text-search.md)，而會被捨棄。  
  
 不允許使用 WEIGHT、FORMSOF、萬用字元、NEAR 和其他語法。 *freetext_string* 會經過斷字、進行詞幹分析，然後進行同義字檢查。  
  
 *freetext_string* 是 **nvarchar**。 當使用另一個字元資料類型當做輸入時，會發生隱含轉換。 不能使用大型字串資料類型 nvarchar(max) 和 varchar(max)。 在下列範例中，定義為 `@SearchWord` 的 `varchar(30)` 變數會造成 `FREETEXT` 述詞中的隱含轉換。  
  
```sql
USE AdventureWorks2012;  
GO  
DECLARE @SearchWord VARCHAR(30)  
SET @SearchWord ='performance'  
SELECT Description   
FROM Production.ProductDescription   
WHERE FREETEXT(Description, @SearchWord);  
  
```  
  
 由於「參數探測」不適用於轉換，因此請使用 **nvarchar** 來獲得更好的效能。 在此範例中，請將 `@SearchWord` 宣告為 `nvarchar(30)`。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @SearchWord NVARCHAR(30)  
SET @SearchWord = N'performance'  
SELECT Description   
FROM Production.ProductDescription   
WHERE FREETEXT(Description, @SearchWord);  
  
```  
  
 當產生的並非最佳計畫時，也可以使用 OPTIMIZE FOR 查詢提示。  
  
 LANGUAGE *language_term*  
 這是查詢利用其資源來斷詞、分析詞幹，以及移除同義字和停用字詞的語言。 這個參數是選擇性的，可以指定成對應於語言地區設定識別碼 (LCID) 的字串、整數或十六進位值。 如果指定 *language_term*，系統就會將它所代表的語言套用至搜尋條件的所有項。 如果未指定任何值，就會使用資料行全文檢索語言。  
  
 如果不同語言的文件當做二進位大型物件 (BLOB) 一起儲存在單一資料行中，給定文件的地區設定識別碼 (LCID) 會判斷要建立其內容索引所使用的語言。 查詢此種資料行時，指定 LANGUAGE *language_term* 可以增加完全相符的機率。  
  
 當指定為字串時，*language_term* 會對應到 [sys.syslanguages &#40;Transact-SQL&#41;](../../relational-databases/system-compatibility-views/sys-syslanguages-transact-sql.md) 相容性檢視表中的 **alias** 資料行值。  字串必須以單引號括住，如 '*language_term*'。 當指定為整數時，*language_term* 是用於識別語言的實際 LCID。 當指定為十六進位值時，*language_term* 是 0x，後面接著 LCID 的十六進位值。 十六進位值不能超出 8 位數，開頭的零也包括在內。  
  
 如果這個值是雙位元組字集 (DBCS) 格式，[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將其轉換成 Unicode。  
  
 如果指定的語言無效，或尚未安裝對應於這個語言的資源，[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 就會傳回錯誤。 若要使用中性語言資源，請將 0x0 指定為 *language_term*。  
  
## <a name="general-remarks"></a>一般備註  
 全文檢索述詞與函數會在 FROM 述詞中隱含的單一資料表上處理。 若要在多個資料表上進行搜尋，請使用 FROM 子句中聯結的資料表，在兩個或多個資料表之產品的結果集上進行搜尋。  
  
使用 FREETEXT 來進行全文檢索查詢比使用 CONTAINS 來進行全文檢索查詢較為不精確。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 全文檢索搜尋引擎可找出重要的單字和片語。 針對在 CONTAINS 述詞的 \<contains_search_condition> 參數中指定時通常已具有意義的任何保留關鍵字或萬用字元，不會賦予任何特殊意義。
  
 當資料庫相容性層級設定為 100 時，[OUTPUT 子句](../../t-sql/queries/output-clause-transact-sql.md) 中不允許使用全文檢索述詞。  
  
> [!NOTE]  
>  FREETEXTTABLE 函數適用的比對種類與 FREETEXT 述詞相同。 您可以在 SELECT 陳述式的 [FROM 子句](../../t-sql/queries/from-transact-sql.md)中參考此函數，就像它是一般資料表名稱一樣。 如需詳細資訊，請參閱 [FREETEXTTABLE &#40;Transact-SQL&#41;](../../relational-databases/system-functions/freetexttable-transact-sql.md)。  
  
## <a name="querying-remote-servers"></a>查詢遠端伺服器  
 您可以在 [CONTAINS](../../t-sql/queries/contains-transact-sql.md) 或 FREETEXT 述詞中使用四部分名稱，以查詢所連結伺服器上目標資料表的全文檢索索引資料行。 若要準備遠端伺服器來接收全文檢索查詢，請針對遠端伺服器上的目標資料表與資料行建立全文檢索索引，然後加入遠端伺服器成為連結的伺服器。  
  
## <a name="comparison-of-like-to-full-text-search"></a>LIKE 與全文檢索搜尋的比較  
 相較於全文檢索搜尋， [LIKE](../../t-sql/language-elements/like-transact-sql.md)[!INCLUDE[tsql](../../includes/tsql-md.md)] 述詞只能針對字元模式運作。 您也無法使用 LIKE 述詞來查詢格式化的二進位資料。 此外，針對大量非結構化文字資料執行 LIKE 查詢的速度會比針對相同資料執行對等全文檢索查詢的速度要慢很多。 對於數百萬列的資料，使用 LIKE 查詢時可能要好幾分鐘才能傳回搜尋結果，但是使用全文檢索查詢時可能只要幾秒鐘的時間 (視傳回的資料列數目而定)。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-freetext-to-search-for-words-containing-specified-character-values"></a>A. 使用 FREETEXT 搜尋含有所指定字元值的單字  
 下列範例會搜尋包含 vital、safety 和 components 相關單字的所有文件。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT Title  
FROM Production.Document  
WHERE FREETEXT (Document, 'vital safety components' );  
GO  
```  
  
### <a name="b-using-freetext-with-variables"></a>B. 搭配變數來使用 FREETEXT  
 下列範例會利用變數來取代特定搜尋詞彙。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @SearchWord NVARCHAR(30);  
SET @SearchWord = N'high-performance';  
SELECT Description   
FROM Production.ProductDescription   
WHERE FREETEXT(Description, @SearchWord);  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [全文檢索搜尋使用者入門](../../relational-databases/search/get-started-with-full-text-search.md)   
 [建立及管理全文檢索目錄](../../relational-databases/search/create-and-manage-full-text-catalogs.md)   
 [CREATE FULLTEXT CATALOG &#40;Transact-SQL&#41;](../../t-sql/statements/create-fulltext-catalog-transact-sql.md)   
 [CREATE FULLTEXT INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-fulltext-index-transact-sql.md)   
 [建立及管理全文檢索索引](../../relational-databases/search/create-and-manage-full-text-indexes.md)   
 [使用全文檢索搜尋進行查詢](../../relational-databases/search/query-with-full-text-search.md)   
 [建立全文檢索搜尋查詢 &#40;Visual Database Tools&#41;](../../ssms/visual-db-tools/create-full-text-search-queries-visual-database-tools.md)   
 [CONTAINS &#40;Transact-SQL&#41;](../../t-sql/queries/contains-transact-sql.md)   
 [CONTAINSTABLE &#40;Transact-SQL&#41;](../../relational-databases/system-functions/containstable-transact-sql.md)   
 [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [FREETEXTTABLE &#40;Transact-SQL&#41;](../../relational-databases/system-functions/freetexttable-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)  
  
