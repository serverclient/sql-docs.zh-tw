---
description: DROP INDEX (Transact-SQL)
title: DROP INDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP_INDEX_TSQL
- DROP INDEX
dev_langs:
- TSQL
helpviewer_keywords:
- nonclustered indexes [SQL Server], removing
- MAXDOP index option, DROP INDEX statement
- index removal [SQL Server]
- spatial indexes [SQL Server], dropping
- removing indexes
- deleting indexes
- dropping indexes
- MOVE TO clause
- clustered indexes, removing
- indexes [SQL Server], dropping
- filtered indexes [SQL Server], dropping
- ONLINE option
- indexes [SQL Server], moving
- XML indexes [SQL Server], dropping
- DROP INDEX statement
ms.assetid: 2b1464c8-934c-405f-8ef7-2949346b5372
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ba18cecec3f473f0ec4fe0cc7c606a49b74ecd98
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172450"
---
# <a name="drop-index-transact-sql"></a>DROP INDEX (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  從目前資料庫中移除一或多個關聯式索引、空間索引、已篩選的索引或 XML 索引。 您可以卸除叢集索引，再藉由指定 MOVE TO 選項，於單一交易中將結果資料表移到另一個檔案群組或資料分割配置。  
  
 DROP INDEX 陳述式不會套用在定義 PRIMARY KEY 或 UNIQUE 條件約束所建立的索引上。 若要移除條件約束和對應的索引，請搭配 DROP CONSTRAINT 子句來使用 [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)。  
  
> [!IMPORTANT]
>  在未來的 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中，將移除 `<drop_backward_compatible_index>` 所定義的語法。 請避免在新的開發工作中使用這個語法，並規劃修改目前在使用這個語法的應用程式。 請改用 `<drop_relational_or_xml_index>` 下所指定的語法。 您無法利用與舊版相容的語法來卸除 XML 索引。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server (All options except filegroup and filestream apply to Azure SQL Database.)  
  
DROP INDEX [ IF EXISTS ]   
{ <drop_relational_or_xml_or_spatial_index> [ ,...n ]   
| <drop_backward_compatible_index> [ ,...n ]  
}  
  
<drop_relational_or_xml_or_spatial_index> ::=  
    index_name ON <object>   
    [ WITH ( <drop_clustered_index_option> [ ,...n ] ) ]  
  
<drop_backward_compatible_index> ::=  
    [ owner_name. ] table_or_view_name.index_name  
  
<object> ::=  
{ database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }  
  
<drop_clustered_index_option> ::=  
{  
    MAXDOP = max_degree_of_parallelism  
  | ONLINE = { ON | OFF }  
  | MOVE TO { partition_scheme_name ( column_name )   
            | filegroup_name  
            | "default"   
            }  
  [ FILESTREAM_ON { partition_scheme_name   
            | filestream_filegroup_name   
            | "default" } ]  
}  
```  
  
```syntaxsql
-- Syntax for Azure SQL Database  
  
DROP INDEX  
{ <drop_relational_or_xml_or_spatial_index> [ ,...n ]   
}  
  
<drop_relational_or_xml_or_spatial_index> ::=   
    index_name ON <object>  
  
<object> ::=   
{ database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }  
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
DROP INDEX index_name ON { database_name.schema_name.table_name | schema_name.table_name | table_name }  
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *IF EXISTS*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](https://go.microsoft.com/fwlink/p/?LinkId=299658))。  
  
 只有在索引已存在時，才能有條件地將其卸除。  
  
 *index_name*  
 這是要卸除的索引名稱。  
  
 *database_name*  
 這是資料庫的名稱。  
  
 *schema_name*  
 這是資料表或檢視表所屬的結構描述名稱。  
  
 *table_or_view_name*  
 這是與索引相關聯的資料表或檢視表的名稱。 只有資料表上才支援空間索引。  
  
 若要顯示物件的索引報表，請使用 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) 目錄檢視。  
  
 當 database_name 是目前的資料庫或 database_name 是 tempdb，而且 object_name 開頭為 # 時，Azure SQL Database 支援三部分名稱格式 database_name.[schema_name].object_name。  
  
 \<drop_clustered_index_option>  
 **適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 控制叢集索引選項。 這些選項無法搭配其他索引類型使用。  
  
 MAXDOP = *max_degree_of_parallelism*  
 **適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] (僅限效能等級 P2 和 P3)。  
  
 在索引作業期間，覆寫 **max degree of parallelism** 設定選項。 如需詳細資訊，請參閱 [設定 max degree of parallelism 伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。 請利用 MAXDOP 來限制執行平行計畫所用的處理器數目。 最大值是 64 個處理器。  
  
> [!IMPORTANT]  
>  空間索引或 XML 索引不允許 MAXDOP。  
  
 *max_degree_of_parallelism* 可以是：  
  
 1  
 隱藏平行計畫的產生。  
  
 \>1  
 將平行索引作業所用的最大處理器數目限制為指定的數目。  
  
 0 (預設值)  
 根據目前的系統工作負載，使用實際數目或比實際數目更少的處理器。  
  
 如需詳細資訊，請參閱 [設定平行索引作業](../../relational-databases/indexes/configure-parallel-index-operations.md)。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的所有版本都無法使用平行索引作業。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本支援的功能清單，請參閱 [SQL Server 2016 版本和支援的功能](../../sql-server/editions-and-supported-features-for-sql-server-2016.md)。  
  
 ONLINE = ON | **OFF**  
 **適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 指定在索引作業期間，查詢和資料修改是否能夠使用基礎資料表和相關聯的索引。 預設值為 OFF。  
  
 開啟  
 不保留長期資料表鎖定。 這使得基礎資料表的查詢或更新能夠繼續運作。  
  
 OFF  
 在索引作業期間，會套用資料表鎖定，無法使用資料表。  
  
 只有在卸除叢集索引時，才能指定 ONLINE 選項。 如需詳細資訊，請參閱＜備註＞一節。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的所有版本都無法使用線上索引作業。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本支援的功能清單，請參閱 [SQL Server 2016 版本和支援的功能](../../sql-server/editions-and-supported-features-for-sql-server-2016.md)。  
  
 MOVE TO { _partition\_scheme\_name_ **(** _column\_name_ **)**  | _filegroup\_name_ |  **"** default **"**  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 支援以 "default" 為檔案群組名稱。  
  
 指定目前在叢集索引分葉層級之資料列所要移往的位置。 資料會以堆積的形式移至新位置。 您可以指定資料分割配置或檔案群組來作為新位置，但是這個資料分割配置或檔案群組必須已經存在。 MOVE TO 對於索引檢視表或非叢集索引無效。 如果未指定資料分割結構描述或檔案群組，結果資料表會放在定義給叢集索引的相同資料分割結構描述或檔案群組中。  
  
 如果利用 MOVE TO 卸除叢集索引，便會重建基底資料表的任何非叢集索引，不過，它們會保留在原始檔案群組或資料分割結構描述中。 如果將基底資料表移到不同的檔案群組或資料分割結構描述中，則不會移動非叢集索引來符合基底資料表 (堆積) 的新位置。 因此，即使非叢集索引先前與叢集索引對齊，它們也可能不再與堆積對齊。 如需資料分割索引對齊的詳細資訊，請參閱[資料分割資料表與索引](../../relational-databases/partitions/partitioned-tables-and-indexes.md)。  
  
 _partition_scheme_name_ **(** _column_name_ **)**  
 **適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 指定一個資料分割結構描述來做為結果資料表的位置。 您必須已執行 [CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md) 或 [ALTER PARTITION SCHEME](../../t-sql/statements/alter-partition-scheme-transact-sql.md) 來建立資料分割結構描述。 如果未指定位置，且資料表已進行資料分割，便會將資料表併入與現有叢集索引相同的資料分割配置中。  
  
 配置中的資料行名稱不限定為索引定義中的資料行。 您可以指定基底資料表中的任何資料行。  
  
 *filegroup_name*  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定一個檔案群組來做為結果資料表的位置。 如果未指定位置，且資料表未進行資料分割，便會將結果資料表包括在叢集索引的相同檔案群組中。 此檔案群組必須已存在。  
  
 **"** default **"**  
 指定產生資料表的預設位置。  
  
> [!NOTE]
>  在此內容中，default 不是關鍵字。 它是預設檔案群組的識別碼，必須加以分隔，如 MOVE TO **"** default **"** 或 MOVE TO **[** default **]** 。 如果指定了 **"** default **"** ，則目前工作階段的 QUOTED_IDENTIFIER 選項就必須是 ON。 這是預設值。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)。  
  
 FILESTREAM_ON { *partition_scheme_name* | *filestream_filegroup_name* |  **"** default **"** }  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定目前在叢集索引分葉層級之 FILESTREAM 資料表所要移往的位置。 資料會以堆積的形式移至新位置。 您可以指定資料分割配置或檔案群組來作為新位置，但是這個資料分割配置或檔案群組必須已經存在。 FILESTREAM ON 對於索引檢視表或非叢集索引是無效的。 如果未指定資料分割配置，資料將會放在針對叢集索引所定義的相同資料分割配置中。  
  
 *partition_scheme_name*  
 為 FILESTREAM 資料指定資料分割配置。 您必須已執行 [CREATE PARTITION SCHEME](../../t-sql/statements/create-partition-scheme-transact-sql.md) 或 [ALTER PARTITION SCHEME](../../t-sql/statements/alter-partition-scheme-transact-sql.md) 來建立資料分割結構描述。 如果未指定位置，且資料表已進行資料分割，便會將資料表併入與現有叢集索引相同的資料分割配置中。  
  
 如果您為 MOVE TO 指定資料分割配置，您必須針對 FILESTREAM ON 使用相同的資料分割配置。  
  
 *filestream_filegroup_name*  
 為 FILESTREAM 資料指定 FILESTREAM 檔案群組。 如果未指定位置，且資料表未分割，則資料會併入預設的 FILESTREAM 檔案群組中。  
  
 **"** default **"**  
 為 FILESTREAM 資料指定預設位置。  
  
> [!NOTE]
>  在此內容中，default 不是關鍵字。 它是預設檔案群組的識別碼，必須加以分隔，如 MOVE TO **"** default **"** 或 MOVE TO **[** default **]** 。 如果指定了 "default"，目前工作階段的 QUOTED_IDENTIFIER 選項就必須是 ON。 這是預設值。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)。  
  
## <a name="remarks"></a>備註  
 當卸除非叢集索引時，會從中繼資料移除索引定義，從資料庫檔案中移除索引資料頁面 (B 型樹狀目錄)。 當卸除叢集索引時，會從中繼資料移除索引定義，且會將叢集索引分葉層級所儲存的資料列儲存在未排序的結果資料表 (堆積) 中。 索引先前所佔用的所有空間都會重新取得。 之後，任何資料庫物件都可以使用這個空間。  
  
 如果索引所在的檔案群組離線或設為唯讀，便無法卸除索引。  
  
 當卸除索引檢視的叢集索引時，會自動卸除相同檢視的所有非叢集索引和自動建立的統計資料。 不會卸除手動建立的統計資料。  
  
 語法 _table\_or\_view\_name_ **.** _index\_name_ 的目的是要與舊版相容。 您無法利用與舊版相容的語法來卸除 XML 索引或空間索引。  
  
 當卸除含有 128 個 (含) 以上之範圍的索引時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會延遲取消配置實際的頁面及其相關聯的鎖定，直到認可交易之後。  
  
 有時候，會卸除再重新建立索引來重新組織或重建索引，例如套用新的填滿因數值，或在大量載入之後重新組織資料。 若要做到這一點，[ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md) 會比較有效，對於叢集索引而言，尤其如此。 ALTER INDEX REBUILD 已最佳化，可防止重建非叢集索引所帶來的負擔。  
  
## <a name="using-options-with-drop-index"></a>搭配 DROP INDEX 使用選項  
 您可以在卸除叢集索引時，設定下列索引選項：MAXDOP、ONLINE 和 MOVE TO。  
  
 請利用 MOVE TO 來卸除叢集索引，再利用單一交易，將結果資料表移到另一個檔案群組或資料分割結構描述。  
  
 當您指定 ONLINE = ON 時，DROP INDEX 交易不會封鎖基礎資料和相關聯非叢集索引的查詢和修改。 您只能每次在線上卸除一個叢集索引。 如需 ONLINE 選項的完整描述，請參閱 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)。  
  
 如果在檢視上停用了叢集索引，或叢集索引包含分葉層級資料列中的 **text**、**ntext**、**image**、**varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** 或 **xml** 資料行，您便無法在線上卸除這個叢集索引。  
  
 利用 ONLINE = ON 和 MOVE TO 選項需要其他暫存磁碟空間。  
  
 在卸除索引之後，產生的堆積會出現在 **sys.indexes** 目錄檢視中，**name** 資料行會出現 NULL。 若要檢視資料表名稱，請在 **object_id** 上，聯結 **sys.indexes** 和 **sys.tables**。 如需範例查詢，請參閱 D 範例。  
  
 在執行 [!INCLUDE[ssEnterpriseEd2005](../../includes/ssenterpriseed2005-md.md)] 或更新版本的多重處理器電腦上，DROP INDEX 可能會如同其他查詢一樣，使用更多的處理器來執行與卸除叢集索引相關的掃描和排序作業。 您可以藉由指定 MAXDOP 索引選項，手動設定用來執行 DROP INDEX 陳述式的處理器數目。 如需詳細資訊，請參閱 [設定平行索引作業](../../relational-databases/indexes/configure-parallel-index-operations.md)。  
  
 當卸除叢集索引時，除非修改了資料分割配置，否則對應的堆積資料分割會保留其資料壓縮設定。 如果資料分割配置有所變更，所有資料分割都會重建為未壓縮的狀態 (DATA_COMPRESSION = NONE)。 若要卸除叢集索引及變更資料分割配置，您需要執行以下兩個步驟：  
  
1.  卸除叢集索引。  
  
2.  使用指定壓縮選項的 ALTER TABLE ...REBUILD ... 選項來修改資料表。  
  
如果在離線狀態卸除叢集索引，則只會移除叢集索引的上層；因此，此作業的速度相當快。 線上卸除叢集索引時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會重建堆積兩次，一次在步驟 1，另一次在步驟 2。 如需資料壓縮的詳細資訊，請參閱[資料壓縮](../../relational-databases/data-compression/data-compression.md)。  
  
## <a name="xml-indexes"></a>XML 索引  
 當您卸除 XML 索引時，無法指定選項。 此外，您無法使用 _table\_or\_view\_name_ **.** _index\_name_ 語法。 當卸除主要 XML 索引時，也會自動卸除所有相關聯的次要 XML 索引。 如需詳細資訊，請參閱 [XML 索引 &#40;SQL Server&#41;](../../relational-databases/xml/xml-indexes-sql-server.md)。  
  
## <a name="spatial-indexes"></a>空間索引  
 只有資料表上才支援空間索引。 當您卸除空間索引時，不能指定任何選項或使用 **.** _index\_name_。 正確的語法如下：  
  
 DROP INDEX *spatial_index_name* ON *spatial_table_name*;  
  
 如需空間索引的詳細資訊，請參閱[空間索引概觀](../../relational-databases/spatial/spatial-indexes-overview.md)。  
  
## <a name="permissions"></a>權限  
 若要執行 DROP INDEX，至少需要擁有對資料表或檢視的 ALTER 權限。 依預設，這個權限會授與 **系統管理員** 固定伺服器角色以及 **db_ddladmin** 和 **db_owner** 固定資料庫角色。  
  
## <a name="examples"></a>範例  
  
### <a name="a-dropping-an-index"></a>A. 卸除一個索引  
 下列範例會刪除 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `ProductVendor` 資料表的 `IX_ProductVendor_VendorID` 索引。  
  
```sql  
DROP INDEX IX_ProductVendor_BusinessEntityID   
    ON Purchasing.ProductVendor;  
GO  
```  
  
### <a name="b-dropping-multiple-indexes"></a>B. 卸除多個索引  
 下列範例會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的單一交易中刪除兩個索引。  
  
```sql  
DROP INDEX  
    IX_PurchaseOrderHeader_EmployeeID ON Purchasing.PurchaseOrderHeader,  
    IX_Address_StateProvinceID ON Person.Address;  
GO  
```  
  
### <a name="c-dropping-a-clustered-index-online-and-setting-the-maxdop-option"></a>C. 在線上卸除叢集索引或設定 MAXDOP 選項  
 下列範例將 `ONLINE` 選項設為 `ON`，將 `MAXDOP` 設為 `8` 來刪除叢集索引。 由於未指定 MOVE TO 選項，因此產生的資料表會當做索引儲存在相同的檔案群組中。 這個範例會使用 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫  
  
**適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
```sql  
DROP INDEX AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate   
    ON Production.BillOfMaterials WITH (ONLINE = ON, MAXDOP = 2);  
GO  
```  
  
### <a name="d-dropping-a-clustered-index-online-and-moving-the-table-to-a-new-filegroup"></a>D. 在線上卸除叢集索引或將資料表移到新的檔案群組  
 下列範例會在線上刪除叢集索引，並利用 `NewGroup` 子句，將產生的資料表 (堆積) 移到 `MOVE TO` 檔案群組。 它會查詢 `sys.indexes`、 `sys.tables`和 `sys.filegroups` 目錄檢視來確認在移動之前和之後，索引和資料表在檔案群組中的位置。 (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，您可以使用 DROP INDEX IF EXISTS 語法。)  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
```sql  
--Create a clustered index on the PRIMARY filegroup if the index does not exist.  
CREATE UNIQUE CLUSTERED INDEX  
    AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate   
        ON Production.BillOfMaterials (ProductAssemblyID, ComponentID,   
        StartDate)  
    ON 'PRIMARY';  
GO  
-- Verify filegroup location of the clustered index.  
SELECT t.name AS [Table Name], i.name AS [Index Name], i.type_desc,  
    i.data_space_id, f.name AS [Filegroup Name]  
FROM sys.indexes AS i  
    JOIN sys.filegroups AS f ON i.data_space_id = f.data_space_id  
    JOIN sys.tables as t ON i.object_id = t.object_id  
        AND i.object_id = OBJECT_ID(N'Production.BillOfMaterials','U')  
GO  
--Create filegroup NewGroup if it does not exist.  
IF NOT EXISTS (SELECT name FROM sys.filegroups  
                WHERE name = N'NewGroup')  
    BEGIN  
    ALTER DATABASE AdventureWorks2012  
        ADD FILEGROUP NewGroup;  
    ALTER DATABASE AdventureWorks2012  
        ADD FILE (NAME = File1,  
            FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\File1.ndf')  
        TO FILEGROUP NewGroup;  
    END  
GO  
--Verify new filegroup  
SELECT * from sys.filegroups;  
GO  
-- Drop the clustered index and move the BillOfMaterials table to  
-- the Newgroup filegroup.  
-- Set ONLINE = OFF to execute this example on editions other than Enterprise Edition.  
DROP INDEX AK_BillOfMaterials_ProductAssemblyID_ComponentID_StartDate   
    ON Production.BillOfMaterials   
    WITH (ONLINE = ON, MOVE TO NewGroup);  
GO  
-- Verify filegroup location of the moved table.  
SELECT t.name AS [Table Name], i.name AS [Index Name], i.type_desc,  
    i.data_space_id, f.name AS [Filegroup Name]  
FROM sys.indexes AS i  
    JOIN sys.filegroups AS f ON i.data_space_id = f.data_space_id  
    JOIN sys.tables as t ON i.object_id = t.object_id  
        AND i.object_id = OBJECT_ID(N'Production.BillOfMaterials','U');  
GO  
```  
  
### <a name="e-dropping-a-primary-key-constraint-online"></a>E. 在線上卸除 PRIMARY KEY 條件約束  
 因建立 PRIMARY KEY 或 UNIQUE 條件約束而建立的索引，無法利用 DROP INDEX 來卸除。 它們是利用 ALTER TABLE DROP CONSTRAINT 陳述式來卸除。 如需詳細資訊，請參閱 [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)。  
  
 下列範例會卸除條件約束來刪除含 PRIMARY KEY 條件約束的叢集索引。 `ProductCostHistory` 資料表沒有 FOREIGN KEY 條件約束。 如果有的話，您必須先移除這些條件約束。  
  
```sql  
-- Set ONLINE = OFF to execute this example on editions other than Enterprise Edition.  
ALTER TABLE Production.TransactionHistoryArchive  
DROP CONSTRAINT PK_TransactionHistoryArchive_TransactionID  
WITH (ONLINE = ON);  
```  
  
### <a name="f-dropping-an-xml-index"></a>F. 卸除 XML 索引  
 下列範例會卸除 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `ProductModel` 資料表的 XML 索引。  
  
```sql  
DROP INDEX PXML_ProductModel_CatalogDescription   
    ON Production.ProductModel;  
```  
  
### <a name="g-dropping-a-clustered-index-on-a-filestream-table"></a>G. 卸除 FILESTREAM 資料表上的叢集索引  
 下列範例會在線上刪除叢集索引，並將結果資料表 (堆積) 和 FILESTREAM 資料移到 `MyPartitionScheme` 資料分割配置，其方式是同時使用 `MOVE TO` 子句和 `FILESTREAM ON` 子句。  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
```sql  
DROP INDEX PK_MyClusteredIndex   
    ON dbo.MyTable   
    WITH (MOVE TO MyPartitionScheme,  
          FILESTREAM_ON MyPartitionScheme);  
GO  
```  

  
## <a name="see-also"></a>另請參閱  
 [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)   
 [ALTER PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/alter-partition-scheme-transact-sql.md)   
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)   
 [CREATE PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-scheme-transact-sql.md)   
 [CREATE SPATIAL INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-spatial-index-transact-sql.md)   
 [CREATE XML INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-xml-index-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.tables &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-tables-transact-sql.md)   
 [sys.filegroups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-filegroups-transact-sql.md)   
 [sp_spaceused &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md)  
  
  


