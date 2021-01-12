---
title: CREATE XML INDEX (Transact-SQL)
description: CREATE XML INDEX (Transact-SQL)
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE_XML_INDEX_TSQL
- XML INDEX
- CREATE_XML_TSQL
- XML
- CREATE XML
- CREATE XML INDEX
- XML_INDEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE XML INDEX statement
- CREATE INDEX statement
- index creation [SQL Server], XML indexes
- XML indexes [SQL Server], creating
ms.assetid: c510cfbc-68be-4736-b3cc-dc5b7aa51f14
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.custom: ''
ms.date: 08/10/2017
ms.openlocfilehash: 03295249061e1ed6210c8dfbc086d0326137ad26
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98082201"
---
# <a name="create-xml-index-transact-sql"></a>CREATE XML INDEX (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  在指定的資料表上建立 XML 索引。 可以在資料表中有資料之前建立索引。 指定限定的資料庫名稱，就可以在另一個資料庫的資料表上建立 XML 索引。  
  
> [!NOTE]  
>  若要建立關聯式索引，請參閱 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)。 如需有關如何建立空間索引的詳細資訊，請參閱 [CREATE SPATIAL INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-spatial-index-transact-sql.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
--Create XML Index   
CREATE [ PRIMARY ] XML INDEX index_name   
    ON <object> ( xml_column_name )  
    [ USING XML INDEX xml_index_name   
        [ FOR { VALUE | PATH | PROPERTY } ] ]  
    [ WITH ( <xml_index_option> [ ,...n ] ) ]  
[ ; ]  
  
<object> ::=  
{ database_name.schema_name.table_name | schema_name.table_name | table_name }
  
<xml_index_option> ::=  
{   
    PAD_INDEX  = { ON | OFF }  
  | FILLFACTOR = fillfactor  
  | SORT_IN_TEMPDB = { ON | OFF }  
  | IGNORE_DUP_KEY = OFF  
  | DROP_EXISTING = { ON | OFF }  
  | ONLINE = OFF  
  | ALLOW_ROW_LOCKS = { ON | OFF }  
  | ALLOW_PAGE_LOCKS = { ON | OFF }  
  | MAXDOP = max_degree_of_parallelism  
}  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 [PRIMARY] XML  
 在指定的 **xml** 資料行上建立 XML 索引。 當指定 PRIMARY 時，會利用使用者資料表和 XML 節點識別碼所構成的叢集索引鍵來建立叢集索引。 每一份資料表最多可以有 249 個 XML 索引。 建立 XML 索引時，請注意下列事項：  
  
-   叢集索引必須存在於使用者資料表的主索引鍵上。  
  
-   使用者資料表的叢集索引鍵限定為 15 個資料行。  
  
-   資料表中的每一個 **xml** 資料行都可以有一個主要 XML 索引和多個次要 XML 索引。  
  
-   主要 XML 索引必須先存在於 **xml** 資料行上，才能在該資料行上建立次要 XML 索引。  
  
-   XML 索引只能在單一 **xml** 資料行上建立。 您無法在非 **xml** 資料行上建立 XML 索引，也無法在 **xml** 資料行上建立關聯式索引。  
  
-   在檢視表的 **xml** 資料行上、在含有 **xml** 資料行之資料表值的變數上，或在 **xml** 類型變數上，您都無法建立 XML 索引 (不論是主要還是次要)。  
  
-   您無法在計算的 **xml** 資料行上建立主要 XML 索引。  
  
-   SET 選項設定必須與索引檢視表和計算資料行索引所需的設定相同。 具體而言，在建立 XML 索引時，以及在插入、刪除或更新 **xml** 資料行中的值時，必須將 ARITHABORT 選項設為 ON。  
  
 如需詳細資訊，請參閱 [XML 索引 &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)。  
  
 *index_name*  
 這是索引的名稱。 索引名稱在資料表中必須是唯一的，但是在資料庫中不需要是唯一的。 索引名稱必須遵照[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 主要 XML 索引名稱的開頭不能是下列字元： **#** 、 **##** 、 **@** 或 **@@** 。  
  
 *xml_column_name*  
 這是當做索引根據的 **xml** 資料行。 在單一 XML 索引定義中，只能指定一個 **xml** 資料行；但是在 **xml** 資料行上則可以建立多個次要 XML 索引。  
  
 USING XML INDEX *xml_index_name*  
 指定主要 XML 索引，以便用來建立次要 XML 索引。  
  
 FOR { VALUE | PATH | PROPERTY }  
 指定次要 XML 索引的類型。  
  
 值  
 在索引鍵資料行屬於主要 XML 索引 (屬於主要 XML 索引的節點值和路徑) 的資料行上建立次要 XML 索引。  
  
 PATH  
 在建立於主要 XML 索引中之路徑值和節點值上的資料行上建立次要 XML 索引。 在 PATH 次要索引中，路徑值和節點值是指在搜尋路徑時允許有效搜尋的索引鍵資料行。  
  
 PROPERTY  
 在主要 XML 索引的資料行 (PK、路徑和節點值) 上建立次要 XML 索引 (此主要 XML 索引的 PK 是基底資料表的主索引鍵)。  
  
 **\<object>::=**  
  
 這是要建立索引的完整或非完整物件。  
  
 *database_name*  
 這是資料庫的名稱。  
  
 *schema_name*  
 這是資料表所屬的結構描述名稱。  
  
 *table_name*  
 這是要建立索引的資料表名稱。  
  
 **\<xml_index_option> ::=** 
  
 指定當您建立索引時所需使用的選項。  
  
 PAD_INDEX **=** { ON | **OFF** }  
 指定索引填補。 預設值為 OFF。  
  
 開啟  
 *fillfactor* 指定的可用空間百分比會套用到索引的中繼層級頁面。  
  
 OFF 或未指定 *fillfactor*  
 中繼層級頁面會幾乎填滿整個容量，但會考量中繼頁面上的索引鍵集，而保留至少可供索引所能擁有之大小上限的一個資料列使用的足夠空間。  
  
 只有在指定 FILLFACTOR 時，才能使用 PAD_INDEX 選項，因為 PAD_INDEX 會使用 FILLFACTOR 所指定的百分比。 如果 FILLFACTOR 所指定的百分比不夠，無法允許一個資料列，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會在內部覆寫該百分比以允許最小值。 不論 *fillfactor* 的值設得多低，中繼索引頁面上的資料列數目絕對不能少於兩個。  
  
 FILLFACTOR **=** _fillfactor_  
 指定用以指出建立或重建索引時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 填滿各索引頁面分葉層級之程度的百分比。 *fillfactor* 必須是 1 到 100 之間的整數值。 預設值是 0。 如果 *fillfactor* 是 100 或 0， [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會利用已填滿容量的分葉頁面來建立索引。  
  
> [!NOTE]  
>  填滿因數值 0 和 100 在各方面都是一樣的。  
  
 只有在建立或重建索引時才會套用 FILLFACTOR 設定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 不會動態保留頁面中空白空間的指定百分比。 若要檢視填滿因數設定，請使用 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) 目錄檢視表。  
  
> [!IMPORTANT]  
>  利用小於 100 的 FILLFACTOR 來建立叢集索引，會影響資料所佔用的儲存空間數量，因為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 在建立叢集索引時會轉散發資料。  
  
 如需詳細資訊，請參閱 [指定索引的填滿因素](../../relational-databases/indexes/specify-fill-factor-for-an-index.md)。  
  
 SORT_IN_TEMPDB **=** { ON | **OFF** }  
 指定是否要將暫時排序結果儲存在 **tempdb** 中。 預設值為 OFF。  
  
 開啟  
 用來建置索引的中繼排序結果會儲存在 **tempdb** 中。 如果 **tempdb** 位於與使用者資料庫所在磁碟不同的磁碟上，這可能會減少建立索引所需的時間。 不過，這會增加建立索引時所使用的磁碟空間量。  
  
 OFF  
 中繼排序結果會儲存在與用來儲存索引相同的資料庫中。  
  
 除了建立索引時使用者資料庫中所需的空間以外，**tempdb** 還需要大約相同數量的額外空間來容納中繼排序結果。 如需詳細資訊，請參閱[索引的 SORT_IN_TEMPDB 選項](../../relational-databases/indexes/sort-in-tempdb-option-for-indexes.md)。  
  
 IGNORE_DUP_KEY **=OFF**  
 對於 XML 索引沒有任何作用，因為索引類型絕對不是唯一的。 請勿將這個選項設定為 ON，否則會引發錯誤。  
  
 DROP_EXISTING **=** { ON | **OFF** }  
 指定要卸除及重建預先存在的具名 XML 索引。 預設值為 OFF。  
  
 開啟  
 卸除及重建現有的索引。 所指定的索引名稱必須與目前現有的索引相同；不過，索引定義可以修改。 例如，您可以指定不同的資料行、排序次序、分割區配置或索引選項。  
  
 OFF  
 如果所指定的索引名稱已存在，畫面上會出現錯誤。  
  
 您無法利用 DROP_EXISTING 來變更索引類型。 另外，主要 XML 索引無法重新定義為次要 XML 索引，反之亦然。  
  
 ONLINE **=OFF**  
 指定在索引作業期間，基礎資料表和相關聯的索引無法供查詢和資料修改使用。 在這一版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，XML 索引不支援線上索引建立。 如果此選項針對 XML 索引設定為 ON，就會引發錯誤。 請省略 ONLINE 選項，或是將 ONLINE 設定為 OFF。  
  
 建立、重建或卸除 XML 索引的離線索引作業會取得資料表的結構描述修改 (Sch-M) 鎖定。 這可防止所有使用者在作業持續期間存取基礎資料表。  
  
> [!NOTE]
>  [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的所有版本都無法使用線上索引作業。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本支援的功能清單，請參閱 [SQL Server 2016 版本和支援的功能](../../sql-server/editions-and-components-of-sql-server-2016.md)。  
  
 ALLOW_ROW_LOCKS **=** { **ON** | OFF }  
 指定是否允許資料列鎖定。 預設值是 ON。  
  
 開啟  
 當存取索引時，允許資料列鎖定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會決定使用資料列鎖定的時機。  
  
 OFF  
 不使用資料列鎖定。  
  
 ALLOW_PAGE_LOCKS **=** { **ON** | OFF }  
 指定是否允許頁面鎖定。 預設值是 ON。  
  
 開啟  
 當存取索引時，允許頁面鎖定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會決定使用頁面鎖定的時機。  
  
 OFF  
 不使用頁面鎖定。  
  
 MAXDOP **=** _max_degree_of_parallelism_  
 針對索引作業期間，覆寫[設定平行處理原則的最大程度伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)組態選項。 請利用 MAXDOP 來限制執行平行計畫所用的處理器數目。 最大值是 64 個處理器。  
  
> [!IMPORTANT]  
>  雖然所有 XML 索引在語法上都支援 MAXDOP 選項，但是對於主要 XML 索引而言，CREATE XML INDEX 只會使用單一處理器。  
  
 *max_degree_of_parallelism* 可以是：  
  
 1  
 隱藏平行計畫的產生。  
  
 \>1  
 根據目前的系統工作負載，將平行索引作業所使用的處理器數目上限，限制為所指定的數目或更少的數目。  
  
 0 (預設值)  
 根據目前的系統工作負載，使用實際數目或比實際數目更少的處理器。  
  
 如需詳細資訊，請參閱 [設定平行索引作業](../../relational-databases/indexes/configure-parallel-index-operations.md)。  
  
> [!NOTE]
>  [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的每個版本都無法使用平行索引作業。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本支援的功能清單，請參閱 [SQL Server 2016 版本和支援的功能](../../sql-server/editions-and-components-of-sql-server-2016.md)。  
  
## <a name="remarks"></a>備註  
 只要計算資料行資料類型可當做索引鍵資料行或非索引鍵資料行，衍生自 **xml** 資料類型的計算資料行都可以當做索引鍵資料行或內含非索引鍵資料行來建立索引。 您無法在計算的 **xml** 資料行上建立主要 XML 索引。  
  
 若要檢視關於 XML 索引的資訊，請使用 [sys.xml_indexes](../../relational-databases/system-catalog-views/sys-xml-indexes-transact-sql.md) 目錄檢視。  
  
 如需 XML 索引的詳細資訊，請參閱 [XML 索引 &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)。  
  
## <a name="additional-remarks-on-index-creation"></a>有關索引建立的其他備註  
 如需有關索引建立的詳細資訊，請參閱 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md) 中的＜備註＞一節。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-a-primary-xml-index"></a>A. 建立主要 XML 索引  
 下列範例會在 `CatalogDescription` 資料表的 `Production.ProductModel` 資料行上建立主要 XML 索引。  
  
```sql  
USE AdventureWorks2012;  
GO  
IF EXISTS (SELECT * FROM sys.indexes  
            WHERE name = N'PXML_ProductModel_CatalogDescription')  
    DROP INDEX PXML_ProductModel_CatalogDescription   
        ON Production.ProductModel;  
GO  
CREATE PRIMARY XML INDEX PXML_ProductModel_CatalogDescription  
    ON Production.ProductModel (CatalogDescription);  
GO  
```  
  
### <a name="b-creating-a-secondary-xml-index"></a>B. 建立次要 XML 索引  
 下列範例會在 `CatalogDescription` 資料表的 `Production.ProductModel` 資料行上建立次要 XML 索引。  
  
```sql  
USE AdventureWorks2012;  
GO  
IF EXISTS (SELECT name FROM sys.indexes  
            WHERE name = N'IXML_ProductModel_CatalogDescription_Path')  
    DROP INDEX IXML_ProductModel_CatalogDescription_Path  
        ON Production.ProductModel;  
GO  
CREATE XML INDEX IXML_ProductModel_CatalogDescription_Path   
    ON Production.ProductModel (CatalogDescription)  
    USING XML INDEX PXML_ProductModel_CatalogDescription FOR PATH ;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)   
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)   
 [CREATE PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-function-transact-sql.md)   
 [CREATE PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-scheme-transact-sql.md)   
 [CREATE SPATIAL INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-spatial-index-transact-sql.md)   
 [CREATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/create-statistics-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [DBCC SHOW_STATISTICS &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-show-statistics-transact-sql.md)   
 [DROP INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/drop-index-transact-sql.md)   
 [XML 索引 &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)   
 [sys.xml_indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-xml-indexes-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [XML 索引 &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)  
  
