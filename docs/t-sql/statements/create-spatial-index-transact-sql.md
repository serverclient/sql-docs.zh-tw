---
description: CREATE SPATIAL INDEX (Transact-SQL)
title: CREATE SPATIAL INDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/10/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SPATIAL INDEX
- CREATE SPATIAL INDEX
- CREATE_SPATIAL_INDEX_TSQL
- SPATIAL_INDEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- spatial indexes [SQL Server], creating
- index creation [SQL Server], spatial indexes
- CREATE SPATIAL INDEX statement
- CREATE INDEX statement
ms.assetid: ee6b9116-a7ff-463a-a9f0-b360804d8678
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: ce2693109669da084a67849105e859066f818213
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102368"
---
# <a name="create-spatial-index-transact-sql"></a>CREATE SPATIAL INDEX (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，於指定的資料表和資料行上建立空間索引。 可以在資料表中有資料之前建立索引。 指定限定的資料庫名稱，就可以在另一個資料庫的資料表或檢視上建立索引。 空間索引要求資料表具有叢集主索引鍵。 不能在索引檢視表上指定空間索引。 如需空間索引的資訊，請參閱[空間索引概觀](../../relational-databases/spatial/spatial-indexes-overview.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE SPATIAL INDEX index_name
  ON <object> ( spatial_column_name )  
    {  
       <geometry_tessellation> | <geography_tessellation>  
    }
  [ ON { filegroup_name | "default" } ]  
[;]
  
<object> ::=  
    { database_name.schema_name.table_name | schema_name.table_name | table_name }  
  
<geometry_tessellation> ::=  
{
  <geometry_automatic_grid_tessellation>
| <geometry_manual_grid_tessellation>
}  
  
<geometry_automatic_grid_tessellation> ::=  
{  
    [ USING GEOMETRY_AUTO_GRID ]  
          WITH  (  
        <bounding_box>  
            [ [,] <tessellation_cells_per_object> [ ,...n] ]  
            [ [,] <spatial_index_option> [ ,...n] ]  
                 )  
}  
  
<geometry_manual_grid_tessellation> ::=  
{  
       [ USING GEOMETRY_GRID ]  
         WITH (  
                    <bounding_box>  
                        [ [,]<tessellation_grid> [ ,...n] ]  
                        [ [,]<tessellation_cells_per_object> [ ,...n] ]  
                        [ [,]<spatial_index_option> [ ,...n] ]  
   )  
}
  
<geography_tessellation> ::=  
{  
      <geography_automatic_grid_tessellation> | <geography_manual_grid_tessellation>  
}  
  
<geography_automatic_grid_tessellation> ::=  
{  
    [ USING GEOGRAPHY_AUTO_GRID ]  
    [ WITH (  
        [ [,] <tessellation_cells_per_object> [ ,...n] ]  
        [ [,] <spatial_index_option> ]  
     ) ]  
}  
  
<geography_manual_grid_tessellation> ::=  
{  
    [ USING GEOGRAPHY_GRID ]  
    [ WITH (  
                [ <tessellation_grid> [ ,...n] ]  
                [ [,] <tessellation_cells_per_object> [ ,...n] ]  
                [ [,] <spatial_index_option> [ ,...n] ]  
                ) ]  
}  
  
<bounding_box> ::=  
{  
      BOUNDING_BOX = ( {  
       xmin, ymin, xmax, ymax
       | <named_bb_coordinate>, <named_bb_coordinate>, <named_bb_coordinate>, <named_bb_coordinate>
  } )  
}  
  
<named_bb_coordinate> ::= { XMIN = xmin | YMIN = ymin | XMAX = xmax | YMAX=ymax }  
  
<tessellation_grid> ::=  
{
    GRIDS = ( { <grid_level> [ ,...n ] | <grid_size>, <grid_size>, <grid_size>, <grid_size>  }
        )  
}  
<tessellation_cells_per_object> ::=  
{
   CELLS_PER_OBJECT = n
}  
  
<grid_level> ::=  
{  
     LEVEL_1 = <grid_size>
  |  LEVEL_2 = <grid_size>
  |  LEVEL_3 = <grid_size>
  |  LEVEL_4 = <grid_size>
}  
  
<grid_size> ::= { LOW | MEDIUM | HIGH }  
  
<spatial_index_option> ::=  
{  
    PAD_INDEX = { ON | OFF }  
  | FILLFACTOR = fillfactor  
  | SORT_IN_TEMPDB = { ON | OFF }  
  | IGNORE_DUP_KEY = OFF  
  | STATISTICS_NORECOMPUTE = { ON | OFF }  
  | DROP_EXISTING = { ON | OFF }  
  | ONLINE = OFF  
  | ALLOW_ROW_LOCKS = { ON | OFF }  
  | ALLOW_PAGE_LOCKS = { ON | OFF }  
  | MAXDOP = max_degree_of_parallelism  
    | DATA_COMPRESSION = { NONE | ROW | PAGE }  
}  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

 *index_name*     
 這是索引的名稱。 索引名稱在資料表中必須是唯一的，但是在資料庫中不需要是唯一的。 索引名稱必須遵照[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 ON \<object> ( *spatial_column_name* )     
 指定索引建立所在的物件 (資料庫、結構描述或資料表) 以及空間資料行的名稱。  
  
 *spatial_column_name* 會指定當做索引根據的空間資料行。 在單一空間索引定義中，只能指定一個空間資料行；但是在 **geometry** 或 **geography** 資料行上可以建立多個空間索引。  
  
 USING      
 指示空間索引的鑲嵌式配置。 這個參數會使用類型專用值，如下表所示：  
  
|資料行的資料類型|鑲嵌式配置|  
|-------------------------|-------------------------|  
|**幾何**|GEOMETRY_GRID|  
|**幾何**|GEOMETRY_AUTO_GRID|  
|**地理位置**|GEOGRAPHY_GRID|  
|**地理位置**|GEOGRAPHY_AUTO_GRID|  
  
 空間索引只能建立在 **geometry** 或 **geography** 型別的資料行上，否則會引發錯誤。 如果傳遞特定型別的無效參數，會引發錯誤。  
  
 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 如何實作鑲嵌的資訊，請參閱[空間索引概觀](../../relational-databases/spatial/spatial-indexes-overview.md)。  
  
 ON *filegroup_name*      
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 在指定的檔案群組上建立指定的索引。 如果未指定位置，且資料表未分割，則索引會使用與基礎資料表相同的檔案群組。 此檔案群組必須已存在。  
  
 ON "default"     
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 在預設的檔案群組上建立指定的索引。  
  
 在這個內容中，default 這個字不是關鍵字。 它是預設檔案群組的識別碼，必須加以分隔，例如 ON "default" 或 ON [default]。 如果指定了 "default"，目前工作階段的 QUOTED_IDENTIFIER 選項就必須是 ON。 這是預設值。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)。  
  
 **\<object>::=**     
 這是要建立索引的完整或非完整物件。  
  
 *database_name*      
 這是資料庫的名稱。  
  
 *schema_name*     
 這是資料表所屬的結構描述名稱。  
  
 *table_name*      
 這是要建立索引的資料表名稱。  
  
當 database_name 是目前的資料庫或 database_name 是 tempdb 且 object_name 的開頭為 # 時，[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 支援三部分名稱格式 database_name.[schema_name].object_name。  
  
### <a name="using-options"></a>USING 選項

 GEOMETRY_GRID      
 指定您所使用的 **geometry** 方格鑲嵌式配置。 GEOMETRY_GRID 只能在 **geometry** 資料類型的資料行上指定。  GEOMETRY_GRID 允許手動調整鑲嵌式配置。  
  
 GEOMETRY_AUTO_GRID      
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 只能在 geometry 資料類型的資料行上指定。 這是此資料類型的預設值，而且不需要加以指定。  
  
 GEOGRAPHY_GRID      
 指定地理方格鑲嵌式配置。 GEOGRAPHY_GRID 只能在 **geography** 資料類型的資料行上指定。  
  
 GEOGRAPHY_AUTO_GRID      
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 只能在 geography 資料類型的資料行上指定。  這是此資料類型的預設值，而且不需要加以指定。  
  
### <a name="with-options"></a>WITH 選項

BOUNDING_BOX      
指定定義週框方塊之四個座標的四個數值 Tuple：左下角的 x-min 和 y-min 座標及右上角的 x-max 和 y-max 座標。  
  
 *xmin*      
 指定週框方塊左下角的 X 座標。  
  
 *ymin*      
 指定週框方塊左下角的 Y 座標。  
  
 *xmax*     
 指定週框方塊右上角的 X 座標。  
  
 *ymax*     
 指定週框方塊右上角的 Y 座標。  
  
 XMIN = *xmin*     
 針對週框方塊左下角的 X 座標指定屬性名稱和值。  
  
 YMIN =*ymin*      
 針對週框方塊左下角的 Y 座標指定屬性名稱和值。  
  
 XMAX =*xmax*      
 針對週框方塊右上角的 X 座標指定屬性名稱和值。  
  
 YMAX =*ymax*     
 針對週框方塊右上角的 Y 座標指定屬性名稱和值。  
  
 > [!NOTE]
 > 週框方塊座標只適用於 USING GEOMETRY_GRID 子句內。  
 >
 > *xmax* 必須大於 *xmin*，而 *ymax* 必須大於 *ymin*。 您可以指定任何有效的 [float](../../t-sql/data-types/float-and-real-transact-sql.md) 值表示法，前提如下：*xmax* > *xmin* 且 *ymax* > *ymin*。 否則會引發適當的錯誤。  
 >
 > 沒有預設值。  
 >
 > 週框方塊屬性名稱不區分大小寫，不論資料庫定序為何。  
  
 若要指定屬性名稱，您必須一次指定一個，而且只能指定一次。 您可以依照任何順序來指定它們。 例如，下列子句是相等的：  
  
- BOUNDING_BOX =( XMIN =*xmin*, YMIN =*ymin*, XMAX =*xmax*, YMAX =*ymax* )  
  
- BOUNDING_BOX =( XMIN =*xmin*, XMAX =*xmax*, YMIN =*ymin*, YMAX =*ymax*)  
  
GRIDS      
定義鑲嵌式配置之每一個層級上的方格密度。 已選取 GEOMETRY_AUTO_GRID 和 GEOGRAPHY_AUTO_GRID 時，會停用這個選項。  
  
 如需鑲嵌的資訊，請參閱[空間索引概觀](../../relational-databases/spatial/spatial-indexes-overview.md)。  
  
 GRIDS 參數如下所示：  
  
 LEVEL_1     
 指定第一層 (上層) 方格。  
  
 LEVEL_2    
 指定第二層方格。  
  
 LEVEL_3    
 指定第三層方格。  
  
 LEVEL_4    
 指定第四層方格。  
  
 LOW    
 針對給定層級的方格指定可能的最低密度。 LOW 等於 16 個資料格 (4x4 方格)。  
  
 **MEDIUM**     
 針對給定層級的方格指定中密度。 MEDIUM 等於 64 個資料格 (8x8 方格)。  
  
 HIGH     
 針對給定層級的方格指定可能的最高密度。 HIGH 等於 256 個資料格 (16x16 方格)。  
  
> [!NOTE]
> 使用層級名稱可讓您依照任何順序指定層級以及省略層級。 如果您使用任何層級的名稱，您就必須使用您指定之任何其他層級的名稱。 如果您省略層級，它的密度預設為 MEDIUM。  

> [!WARNING]
> 如果指定了無效的密度，將會引發錯誤。  
  
CELLS_PER_OBJECT =*n*     
指定可供鑲嵌式程序用於索引內單一空間物件之每一物件的鑲嵌式資料格數目。 *n* 可以是 1 與 8192 (含) 之間的任何整數。 如果傳遞了無效的數目，或是此數目大於指定之鑲嵌的最大資料格數目，就會引發錯誤。  
  
 CELLS_PER_OBJECT 的預設值如下：  
  
|USING 選項|每一物件的預設資料格數目|  
|------------------|------------------------------|  
|GEOMETRY_GRID|**16**|  
|GEOMETRY_AUTO_GRID|**8**|  
|GEOGRAPHY_GRID|**16**|  
|GEOGRAPHY_AUTO_GRID|**12**|  
  
在最上層，如果物件涵蓋的資料格數目要比 *n* 指定的數目還要多，則索引會盡量使用所需的資料格數目來提供完整的最上層鑲嵌。 在這類情況下，物件可能會收到比指定之資料格數目還要多的資料格。 在此情況下，最大數目就是最上層方格產生的資料格數目，該數目取決於密度。  
  
CELLS_PER_OBJECT 值會由每一物件的資料格鑲嵌式規則所使用。 如需鑲嵌規則的資訊，請參閱[空間索引概觀](../../relational-databases/spatial/spatial-indexes-overview.md)。  
  
PAD_INDEX = { ON | **OFF** }     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
指定索引填補。 預設值為 OFF。  
  
開啟     
指出 *fillfactor* 指定的可用空間百分比會套用到索引的中繼層級頁面上。  
  
OFF 或未指定 *fillfactor*     
指出中繼層級頁面會幾乎填滿整個容量，但會考量中繼頁面上的索引鍵集，而保留至少可供索引所能擁有之大小上限的一個資料列使用的足夠空間。  
  
只有在指定 FILLFACTOR 時，才能使用 PAD_INDEX 選項，因為 PAD_INDEX 會使用 FILLFACTOR 所指定的百分比。 如果 FILLFACTOR 所指定的百分比不夠，無法允許一個資料列，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會在內部覆寫該百分比以允許最小值。 不論 *fillfactor* 的值設得多低，中繼索引頁面上的資料列數目絕對不能少於兩個。  
  
FILLFACTOR =*fillfactor*     
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 指定用以指出建立或重建索引時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 填滿各索引頁面分葉層級之程度的百分比。 *fillfactor* 必須是 1 到 100 之間的整數值。 預設值是 0。 如果 *fillfactor* 是 100 或 0， [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會利用已填滿容量的分葉頁面來建立索引。  
  
> [!NOTE]  
> 填滿因數值 0 和 100 在各方面都是一樣的。
  
 只有在建立或重建索引時才會套用 FILLFACTOR 設定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 不會動態保留頁面中空白空間的指定百分比。 若要檢視填滿因數設定，請使用 [sys.indexes](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) 目錄檢視表。  
  
> [!IMPORTANT]  
> 利用小於 100 的 FILLFACTOR 來建立叢集索引，會影響資料所佔用的儲存空間數量，因為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 在建立叢集索引時會轉散發資料。  
  
 如需詳細資訊，請參閱 [指定索引的填滿因素](../../relational-databases/indexes/specify-fill-factor-for-an-index.md)。  
  
SORT_IN_TEMPDB = { ON | **OFF** }       
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 
  
 指定是否要將暫時排序結果儲存在 tempdb 中。 預設值為 OFF。  
  
 開啟     
 用來建立索引的中繼排序結果會儲存在 tempdb 中。 如果 tempdb 位於使用者資料庫以外的另一組磁碟上，這種儲存方式可以減少建立索引所需的時間。 不過，這會增加建立索引時所使用的磁碟空間量。  
  
 OFF     
 中繼排序結果會儲存在與用來儲存索引相同的資料庫中。  
  
 除了在建立索引時，使用者資料庫中所需的空間以外，tempdb 還需要大約相同數量的額外空間來容納中繼排序結果。 如需詳細資訊，請參閱[索引的 SORT_IN_TEMPDB 選項](../../relational-databases/indexes/sort-in-tempdb-option-for-indexes.md)。  
  
IGNORE_DUP_KEY =**OFF**     
對於空間索引沒有任何作用，因為索引類型絕對不是唯一的。 請勿將這個選項設定為 ON，否則會引發錯誤。  
  
STATISTICS_NORECOMPUTE = { ON | **OFF**}     
指定是否要重新計算散發統計資料。 預設值為 OFF。  
  
 開啟    
 不會自動重新計算過期的統計資料。  
  
 OFF    
 啟用自動統計資料更新。  
  
 若要還原自動統計資料更新，請將 STATISTICS_NORECOMPUTE 設為 OFF，或執行不含 NORECOMPUTE 子句的 UPDATE STATISTICS。  
  
> [!IMPORTANT]  
> 停用散發統計資料的自動重新計算，可防止查詢最佳化工具取得與資料表有關之查詢的最佳執行計畫。  
  
DROP_EXISTING = { ON | **OFF** }     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 
  
 指定要卸除及重建預先存在的具名空間索引。 預設值為 OFF。  
  
 開啟     
 卸除及重建現有的索引。 所指定的索引名稱必須與目前現有的索引相同；不過，索引定義可以修改。 例如，您可以指定不同的資料行、排序次序、分割區配置或索引選項。  
  
 OFF     
 如果所指定的索引名稱已存在，畫面上會出現錯誤。  
  
 您無法利用 DROP_EXISTING 來變更索引類型。  
  
ONLINE =**OFF**      
指定在索引作業期間，基礎資料表和相關聯的索引無法供查詢和資料修改使用。 在這一版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，空間索引不支援線上索引建立。 如果此選項針對空間索引設定為 ON，就會引發錯誤。 請省略 ONLINE 選項，或是將 ONLINE 設定為 OFF。  
  
 建立、重建或卸除空間索引的離線索引作業會取得資料表的結構描述修改 (Sch-M) 鎖定。 這可防止所有使用者在作業持續期間存取基礎資料表。  
  
> [!NOTE]  
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的所有版本都無法使用線上索引作業。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本支援的功能清單，請參閱 [SQL Server 2016 版本支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)。  
  
ALLOW_ROW_LOCKS = { **ON** | OFF }     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
 指定是否允許資料列鎖定。 預設值是 ON。  
  
 開啟     
 當存取索引時，允許資料列鎖定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會決定使用資料列鎖定的時機。  
  
 OFF     
 不使用資料列鎖定。  
  
ALLOW_PAGE_LOCKS = { **ON** | OFF }     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 
  
 指定是否允許頁面鎖定。 預設值是 ON。  
  
 開啟    
 當存取索引時，允許頁面鎖定。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會決定使用頁面鎖定的時機。  
  
 OFF     
 不使用頁面鎖定。  
  
MAXDOP =*max_degree_of_parallelism*      
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 針對索引作業持續時間覆寫 `max degree of parallelism` 組態選項。 請利用 MAXDOP 來限制執行平行計畫所用的處理器數目。 最大值是 64 個處理器。  
  
> [!IMPORTANT]  
> 雖然 MAXDOP 選項在語法上有受到支援，但是 CREATE SPATIAL INDEX 目前一定只會使用單一處理器。  
  
 *max_degree_of_parallelism* 可以是：  
  
 1     
 隱藏平行計畫的產生。  
  
 \>1    
 根據目前的系統工作負載，將平行索引作業所使用的處理器數目上限，限制為所指定的數目或更少的數目。  
  
 0 (預設值)    
 根據目前的系統工作負載，使用實際數目或比實際數目更少的處理器。  
  
 如需詳細資訊，請參閱 [設定平行索引作業](../../relational-databases/indexes/configure-parallel-index-operations.md)。  
  
> [!NOTE]
> [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的每個版本都無法使用平行索引作業。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本支援的功能清單，請參閱 [SQL Server 2016 版本支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)。  
  
DATA_COMPRESSION = {NONE | ROW | PAGE}     
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]
  
 決定索引使用的資料壓縮層級。  
  
 無     
 索引不會將壓縮用於資料上。  
  
 ROW    
 索引會將資料列壓縮用於資料上。  
  
 PAGE    
 索引會將頁面壓縮用於資料上。  
  
## <a name="remarks"></a>備註
每一個 CREATE SPATIAL INDEX 陳述式只能指定每一個選項一次。 指定重複的任何選項都會引發錯誤。  
  
在資料表的每一個空間資料行上最多可以建立 249 個空間索引。 例如，要針對單一資料行中的不同鑲嵌式參數建立索引時，在特定空間資料行上建立一個以上的空間索引可能會很有用處。  
  
> [!IMPORTANT]  
> 建立空間索引時，有許多其他限制。 如需詳細資訊，請參閱[空間索引概觀](../../relational-databases/spatial/spatial-indexes-overview.md)。  
  
索引建立無法利用可用的處理序平行處理原則。  
  
## <a name="methods-supported-on-spatial-indexes"></a>空間索引上支援的方法
在某些條件下，空間索引可支援一些集合導向的幾何方法。 如需詳細資訊，請參閱[空間索引概觀](../../relational-databases/spatial/spatial-indexes-overview.md)。  
  
## <a name="spatial-indexes-and-partitioning"></a>空間索引和資料分割
根據預設，如果空間索引在資料分割資料表上建立，則會根據資料表的分割區配置來分割索引。 這會確保索引資料和相關的資料列會儲存在相同的分割區中。  
  
在此情況下，若要更改基底資料表的分割區配置，您必須先卸除此空間索引，然後才可以重新分割此基底資料表。 為了避免這項限制，當您正在建立空間索引時，可以指定 "ON filegroup" 選項。 如需詳細資訊，請參閱本主題稍後的「空間索引和檔案群組」。  
  
## <a name="spatial-indexes-and-filegroups"></a>空間索引和檔案群組
根據預設，空間索引會分割到與指定索引的資料表相同的檔案群組。 可以藉由檔案群組的指定來覆寫此選項：  
  
[ ON { *filegroup_name* | "default" } ]     
如果您針對空間索引指定檔案群組，此索引會放在該檔案群組中，不論資料表的分割區配置為何。  
  
## <a name="catalog-views-for-spatial-indexes"></a>空間索引的目錄檢視

 下列目錄檢視是空間索引所特有：  
  
 [sys.spatial_indexes](../../relational-databases/system-catalog-views/sys-spatial-indexes-transact-sql.md)  
 表示空間索引的主要索引資訊。  
  
 [sys.spatial_index_tessellations](../../relational-databases/system-catalog-views/sys-spatial-index-tessellations-transact-sql.md)  
 表示有關鑲嵌式配置和每一個空間索引之參數的資訊。  
  
## <a name="additional-remarks-about-creating-indexes"></a>有關建立索引的其他備註

 如需索引建立的詳細資訊，請參閱 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md) 中的＜備註＞一節。  
  
## <a name="permissions"></a>權限
使用者必須擁有資料表或檢視表的 `ALTER` 權限，或者使用者必須是 sysadmin 固定伺服器角色的成員或 `db_ddladmin` 和 `db_owner` 固定資料庫角色成員。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-a-spatial-index-on-a-geometry-column"></a>A. 在幾何資料行上建立空間索引
下列範例會建立包含 **geometry** 類型資料行 `geometry_col` 且名稱為 `SpatialTable` 的資料表。 然後，此範例會在 `SIndx_SpatialTable_geometry_col1` 上建立空間索引 `geometry_col`。 此範例會使用預設鑲嵌式配置，並指定週框方塊。  
  
```sql  
CREATE TABLE SpatialTable(id int primary key, geometry_col geometry);  
CREATE SPATIAL INDEX SIndx_SpatialTable_geometry_col1
   ON SpatialTable(geometry_col)  
   WITH ( BOUNDING_BOX = ( 0, 0, 500, 200 ) );  
```  
  
### <a name="b-creating-a-spatial-index-on-a-geometry-column"></a>B. 在幾何資料行上建立空間索引
下列範例會在 `SIndx_SpatialTable_geometry_col2` 資料表的 `geometry_col` 上建立第二個空間索引 `SpatialTable`。 此範例會指定 `GEOMETRY_GRID` 做為鑲嵌式配置。 此範例也會指定週框方塊、不同方格層級上的不同密度，以及每一物件 64 個資料格。 此範例也會將索引填補設定為 `ON`。  
  
```sql  
CREATE SPATIAL INDEX SIndx_SpatialTable_geometry_col2  
   ON SpatialTable(geometry_col)  
   USING GEOMETRY_GRID  
   WITH (  
    BOUNDING_BOX = ( xmin=0, ymin=0, xmax=500, ymax=200 ),  
    GRIDS = (LOW, LOW, MEDIUM, HIGH),  
    CELLS_PER_OBJECT = 64,  
    PAD_INDEX  = ON );  
```  
  
### <a name="c-creating-a-spatial-index-on-a-geometry-column"></a>C. 在幾何資料行上建立空間索引
下列範例會在 `SIndx_SpatialTable_geometry_col3` 資料表的 `geometry_col` 中建立第三個空間索引 `SpatialTable`。 此範例會使用預設鑲嵌式配置。 此範例會指定週框方塊，並在第三和第四層上使用不同的資料格密度，同時使用每一物件的預設資料格數目。  
  
```sql  
CREATE SPATIAL INDEX SIndx_SpatialTable_geometry_col3  
   ON SpatialTable(geometry_col)  
   WITH (  
    BOUNDING_BOX = ( 0, 0, 500, 200 ),  
    GRIDS = ( LEVEL_4 = HIGH, LEVEL_3 = MEDIUM ) );  
```  
  
### <a name="d-changing-an-option-that-is-specific-to-spatial-indexes"></a>D. 變更空間索引所特有的選項
下列範例會使用 DROP_EXISTING = ON 來指定新的 `SIndx_SpatialTable_geography_col3` 密度，藉以重建上述範例中所建立的空間索引 `LEVEL_3`。  
  
```sql  
CREATE SPATIAL INDEX SIndx_SpatialTable_geography_col3  
   ON SpatialTable(geography_col)  
   WITH ( BOUNDING_BOX = ( 0, 0, 500, 200 ),  
        GRIDS = ( LEVEL_3 = LOW ),  
        DROP_EXISTING = ON );  
```  
  
### <a name="e-creating-a-spatial-index-on-a-geography-column"></a>E. 在地理資料行上建立空間索引
下列範例會建立包含 **geography** 類型資料行 `geography_col` 且名稱為 `SpatialTable2` 的資料表。 然後，此範例會在 `SIndx_SpatialTable_geography_col1` 上建立空間索引 `geography_col`。 此範例會使用 GEOGRAPHY_AUTO_GRID 鑲嵌式配置的預設參數值。  
  
```sql  
CREATE TABLE SpatialTable2(id int primary key, object GEOGRAPHY);  
CREATE SPATIAL INDEX SIndx_SpatialTable_geography_col1
   ON SpatialTable2(object);  
```  
  
> [!NOTE]  
> 如果是地理方格索引，將無法指定週框方塊。
  
### <a name="f-creating-a-spatial-index-on-a-geography-column"></a>F. 在地理資料行上建立空間索引
下列範例會在 `SIndx_SpatialTable_geography_col2` 資料表的 `geography_col` 上建立第二個空間索引 `SpatialTable2`。 此範例會指定 `GEOGRAPHY_GRID` 做為鑲嵌式配置。 此範例也會在不同層級上指定不同方格密度，並指定每一物件 64 個資料格。 此範例也會將索引填補設定為 `ON`。  
  
```sql  
CREATE SPATIAL INDEX SIndx_SpatialTable_geography_col2  
   ON SpatialTable2(object)  
   USING GEOGRAPHY_GRID  
   WITH (  
    GRIDS = (MEDIUM, LOW, MEDIUM, HIGH ),  
    CELLS_PER_OBJECT = 64,  
    PAD_INDEX  = ON );  
```  
  
### <a name="g-creating-a-spatial-index-on-a-geography-column"></a>G. 在地理資料行上建立空間索引
然後此範例會在 `SIndx_SpatialTable_geography_col3` 資料表的 `geography_col` 中建立第三個空間索引 `SpatialTable2`。 此範例會使用預設鑲嵌式配置 GEOGRAPHY_GRID 及預設 CELLS_PER_OBJECT 值 (16)。  
  
```sql  
CREATE SPATIAL INDEX SIndx_SpatialTable_geography_col3  
   ON SpatialTable2(object)  
   WITH ( GRIDS = ( LEVEL_3 = HIGH, LEVEL_2 = HIGH ) );  
```  
  
## <a name="see-also"></a>另請參閱
[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)       
[CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)       
[CREATE PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-function-transact-sql.md)       
[CREATE PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-scheme-transact-sql.md)       
[CREATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/create-statistics-transact-sql.md)       
[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)       
[資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)       
[DBCC SHOW_STATISTICS &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-show-statistics-transact-sql.md)       
[DROP INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/drop-index-transact-sql.md)       
[EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)       
[sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)       
[sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)       
[sys.spatial_index_tessellations &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-spatial-index-tessellations-transact-sql.md)       
[sys.spatial_indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-spatial-indexes-transact-sql.md)       
[空間索引概觀](../../relational-databases/spatial/spatial-indexes-overview.md)       
