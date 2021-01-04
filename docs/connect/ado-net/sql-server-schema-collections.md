---
title: SQL Server 結構描述集合
description: 說明 Microsoft SqlClient Data Provider for SQL Server 所支援的其他結構描述集合。
ms.date: 11/30/2020
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: e5a90fa57c4fa7478a965250fded7ceb92bf326c
ms.sourcegitcommit: 2add15a99df7b85d271adb261523689984dfd134
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051279"
---
# <a name="sql-server-schema-collections"></a>SQL Server 結構描述集合

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

除了一般結構描述集合之外，Microsoft SqlClient Data Provider for SQL Server 還支援其他結構描述集合。 這些結構描述集合會因您目前使用的 SQL Server 版本而稍微不同。 若要判斷支援的結構描述集合清單，請呼叫 **GetSchema** 方法但不搭配任何引數或搭配結構描述集合名稱 "MetaDataCollections"。 這會傳回 <xref:System.Data.DataTable>，包括支援的結構描述集合清單、每個集合所支援的限制數目，以及集合所使用之識別項部分的數目。  

## <a name="databases"></a>資料庫  
  
|ColumnName|DataType|Description|  
|----------------|--------------|-----------------|  
|database_name|String|資料庫的名稱。|  
|dbid|Int16|資料庫識別碼。|  
|create_date|DateTime|資料庫的建立日期。|  

## <a name="foreign-keys"></a>外部索引鍵  
  
|ColumnName|DataType|描述|  
|----------------|--------------|-----------------|  
|CONSTRAINT_CATALOG|String|條件約束所屬的目錄。|  
|CONSTRAINT_SCHEMA|String|包含條件約束的結構描述。|  
|CONSTRAINT_NAME|String|Name：|  
|TABLE_CATALOG|String|包含條件約束的資料表名稱。|  
|TABLE_SCHEMA|String|包含資料表的結構描述。|  
|TABLE_NAME|String|資料表名稱|  
|CONSTRAINT_TYPE|String|條件約束的類型。 僅允許 FOREIGN KEY。|  
|IS_DEFERRABLE|String|指定條件約束是否可以延遲。 傳回 NO。|  
|INITIALLY_DEFERRED|String|指定條件約束一開始是否可以延遲。 傳回 NO。|  

## <a name="indexes"></a>索引  
  
|ColumnName|DataType|Description|  
|----------------|--------------|-----------------|  
|constraint_catalog|String|索引所屬的目錄。|  
|constraint_schema|String|包含索引的結構描述。|  
|constraint_name|String|索引的名稱。|  
|table_catalog|String|與索引相關的資料表名稱。|  
|table_schema|String|包含與索引相關之資料表的結構描述。|  
|table_name|String|資料表名稱。|  
|index_name|String|索引名稱。|  
|type_desc|String|索引的類型將屬於下列其中一種類型：<br /><br /> -   HEAP<br />-   CLUSTERED<br />-   NONCLUSTERED<br />-   XML<br />-   SPATIAL|  

## <a name="indexcolumns"></a>IndexColumns  
  
|ColumnName|DataType|Description|  
|----------------|--------------|-----------------|  
|constraint_catalog|String|索引所屬的目錄。|  
|constraint_schema|String|包含索引的結構描述。|  
|constraint_name|String|索引的名稱。|  
|table_catalog|String|與索引相關的資料表名稱。|  
|table_schema|String|包含與索引相關之資料表的結構描述。|  
|table_name|String|資料表名稱。|  
|column_name|String|與索引相關的資料行名稱。|  
|ordinal_position|Int32|資料行序數位置。|  
|KeyType|Byte|物件的型別。|  
|index_name|String|索引名稱。| 

## <a name="procedures"></a>程序  
  
|ColumnName|DataType|Description|  
|----------------|--------------|-----------------|  
|SPECIFIC_CATALOG|String|目錄的特定名稱。|  
|SPECIFIC_SCHEMA|String|結構描述的特定名稱。|  
|SPECIFIC_NAME|String|目錄的特定名稱。|  
|ROUTINE_CATALOG|String|預存程序所屬的目錄。|  
|ROUTINE_SCHEMA|String|包含預存程序的結構描述。|  
|ROUTINE_NAME|String|預存程序的名稱。|  
|ROUTINE_TYPE|String|針對預存程序傳回 PROCEDURE，並針對函式傳回 FUNCTION。|  
|CREATED|DateTime|建立程序的時間。|  
|LAST_ALTERED|DateTime|上次修改程序的時間。| 

## <a name="procedure-parameters"></a>程序參數  
  
|ColumnName|DataType|Description|  
|----------------|--------------|-----------------|  
|SPECIFIC_CATALOG|String|此為其參數之程序的目錄名稱。|  
|SPECIFIC_SCHEMA|String|包含此參數所屬程序的結構描述。|  
|SPECIFIC_NAME|String|此參數所屬程序的名稱。|  
|ORDINAL_POSITION|Int32|參數的序數位置從 1 開始。 若為程序的傳回值，則此項為 0。|  
|PARAMETER_MODE|String|如果是輸入參數，便傳回 IN；如果是輸出參數，便傳回 OUT，如果是輸入/輸出參數，便傳回 INOUT。|  
|IS_RESULT|String|若表示做為函式之程序的結果，傳回 YES。 否則，便傳回 NO。|  
|AS_LOCATOR|String|如果宣告成定位器，便傳回 YES。 否則，便傳回 NO。|  
|PARAMETER_NAME|String|參數的名稱。 如果這對應於函數的傳回值，便是 NULL。|  
|DATA_TYPE|String|系統提供的資料類型。|  
|CHARACTER_MAXIMUM_LENGTH|Int32|二進位或字元資料類型的最大長度 (以字元為單位)。 否則，便傳回 NULL。|  
|CHARACTER_OCTET_LENGTH|Int32|二進位或字元資料類型的最大長度 (以位元組為單位)。 否則，便傳回 NULL。|  
|COLLATION_CATALOG|String|參數集合的目錄名稱。 如果不是字元類型之一，便傳回 NULL。|  
|COLLATION_SCHEMA|String|一律傳回 NULL。|  
|COLLATION_NAME|String|參數的定序名稱。 如果不是字元類型之一，便傳回 NULL。|  
|CHARACTER_SET_CATALOG|String|參數字元集的目錄名稱。 如果不是字元類型之一，便傳回 NULL。|  
|CHARACTER_SET_SCHEMA|String|一律傳回 NULL。|  
|CHARACTER_SET_NAME|String|參數字元集的名稱。 如果不是字元類型之一，便傳回 NULL。|  
|NUMERIC_PRECISION|Byte|近似數值資料、精確數值資料、整數資料或貨幣資料的有效位數。 否則，便傳回 NULL。|  
|NUMERIC_PRECISION_RADIX|Int16|近似數值資料、精確數值資料、整數資料或貨幣資料的有效位數基數。 否則，便傳回 NULL。|  
|NUMERIC_SCALE|Int32|近似數值資料、精確數值資料、整數資料或貨幣資料的小數位數。 否則，便傳回 NULL。|  
|DATETIME_PRECISION|Int16|若參數型別為 datetime 或 smalldatetime，則為以分數秒為單位的精確度。 否則，便傳回 NULL。|  
|INTERVAL_TYPE|String|NULL。 保留供 SQL Server 日後使用。|  
|INTERVAL_PRECISION|Int16|NULL。 保留供 SQL Server 日後使用。| 

## <a name="tables"></a>資料表  
  
|ColumnName|DataType|描述|  
|----------------|--------------|-----------------|  
|TABLE_CATALOG|String|資料表的目錄。|  
|TABLE_SCHEMA|String|包含資料表的結構描述。|  
|TABLE_NAME|String|資料表名稱。|  
|TABLE_TYPE|String|資料表的類型。 可以是 VIEW 或 BASE TABLE。|  

## <a name="columns"></a>資料行  
  
|ColumnName|DataType|描述|  
|----------------|--------------|-----------------|  
|TABLE_CATALOG|String|資料表的目錄。|  
|TABLE_SCHEMA|String|包含資料表的結構描述。|  
|TABLE_NAME|String|資料表名稱。|  
|COLUMN_NAME|String|資料行名稱。|  
|ORDINAL_POSITION|Int32|資料行識別碼。|  
|COLUMN_DEFAULT|String|資料行的預設值。|  
|IS_NULLABLE|String|資料行的 Null 屬性。 若此資料行允許 NULL，其會傳回 YES。 否則，傳回 NO。|  
|DATA_TYPE|String|系統提供的資料類型。|  
|CHARACTER_MAXIMUM_LENGTH|Int32 – Sql8, Int16 – Sql7|二進位資料、字元資料，或文字和影像資料的最大長度 (以字元為單位)。 否則，就傳回 NULL。|  
|CHARACTER_OCTET_LENGTH|Int32 – SQL8, Int16 – Sql7|二進位資料、字元資料，或文字和影像資料的最大長度 (以位元組為單位)。 否則，就傳回 NULL。|  
|NUMERIC_PRECISION|Unsigned Byte|近似數值資料、精確數值資料、整數資料或貨幣資料的有效位數。 否則，就傳回 NULL。|  
|NUMERIC_PRECISION_RADIX|Int16|近似數值資料、精確數值資料、整數資料或貨幣資料的有效位數基數。 否則，就傳回 NULL。|  
|NUMERIC_SCALE|Int32|近似數值資料、精確數值資料、整數資料或貨幣資料的小數位數。 否則，就傳回 NULL。|  
|DATETIME_PRECISION|Int16|datetime 和 SQL-92 interval 資料類型的子類型代碼。 如果是其他資料類型，就傳回 NULL。|  
|CHARACTER_SET_CATALOG|String|若資料行為字元資料或文字資料型別，則傳回 master，表示字元集所在的資料庫。 否則，就傳回 NULL。|  
|CHARACTER_SET_SCHEMA|String|一律傳回 NULL。|  
|CHARACTER_SET_NAME|String|若此資料行為字元資料或文字資料型別，則傳回字元集的唯一名稱。 否則，就傳回 NULL。|  
|COLLATION_CATALOG|String|若資料行為字元資料或文字資料型別，則傳回 master，表示定義定序的資料庫。 否則，這個資料行就是 NULL。|  
|IS_FILESTREAM|String|YES (如果資料行具有 FILESTREAM 屬性)。<br /><br /> NO (如果資料行沒有 FILESTREAM 屬性)。|  
|IS_SPARSE|String|YES (如果資料行是疏鬆資料行)。<br /><br /> NO (如果資料行不是疏鬆資料行)。|  
|IS_COLUMN_SET|String|YES (如果資料行是資料行集資料行)。<br /><br /> NO (如果資料行不是資料行集資料行)。|  

## <a name="allcolumns"></a>AllColumns  

 AllColumns 結構描述集合會用於支援疏鬆資料行。 AllColumns 與 Columns 結構描述集合具有相同的限制和產生的 DataTable 結構描述。 唯一的差異是 AllColumns 包含了 Columns 結構描述集合並未包含的資料行集資料行。 下表將描述這些資料行。  
  
|ColumnName|DataType|描述|  
|----------------|--------------|-----------------|  
|TABLE_CATALOG|String|資料表的目錄。|  
|TABLE_SCHEMA|String|包含資料表的結構描述。|  
|TABLE_NAME|String|資料表名稱。|  
|COLUMN_NAME|String|資料行名稱。|  
|ORDINAL_POSITION|Int32|資料行識別碼。|  
|COLUMN_DEFAULT|String|資料行的預設值。|  
|IS_NULLABLE|String|資料行的 Null 屬性。 若此資料行允許 NULL，其會傳回 YES。 否則，它就會傳回 NO。|  
|DATA_TYPE|String|系統提供的資料類型。|  
|CHARACTER_MAXIMUM_LENGTH|Int32|二進位資料、字元資料，或文字和影像資料的最大長度 (以字元為單位)。 否則，就傳回 NULL。|  
|CHARACTER_OCTET_LENGTH|Int32|二進位資料、字元資料，或文字和影像資料的最大長度 (以位元組為單位)。 否則，就傳回 NULL。|  
|NUMERIC_PRECISION|Unsigned Byte|近似數值資料、精確數值資料、整數資料或貨幣資料的有效位數。 否則，就傳回 NULL。|  
|NUMERIC_PRECISION_RADIX|Int16|近似數值資料、精確數值資料、整數資料或貨幣資料的有效位數基數。 否則，就傳回 NULL。|  
|NUMERIC_SCALE|Int32|近似數值資料、精確數值資料、整數資料或貨幣資料的小數位數。 否則，就傳回 NULL。|  
|DATETIME_PRECISION|Int16|datetime 和 SQL-92 interval 資料類型的子類型代碼。 如果是其他資料類型，就傳回 NULL。|  
|CHARACTER_SET_CATALOG|String|若資料行為字元資料或文字資料型別，則傳回 master，表示字元集所在的資料庫。 否則，就傳回 NULL。|  
|CHARACTER_SET_SCHEMA|String|一律傳回 NULL。|  
|CHARACTER_SET_NAME|String|若此資料行為字元資料或文字資料型別，則傳回字元集的唯一名稱。 否則，就傳回 NULL。|  
|COLLATION_CATALOG|String|若資料行為字元資料或文字資料型別，則傳回 master，表示定義定序的資料庫。 否則，這個資料行就是 NULL。|  
|IS_FILESTREAM|String|YES (如果資料行具有 FILESTREAM 屬性)。<br /><br /> NO (如果資料行沒有 FILESTREAM 屬性)。|  
|IS_SPARSE|String|YES (如果資料行是疏鬆資料行)。<br /><br /> NO (如果資料行不是疏鬆資料行)。|  
|IS_COLUMN_SET|String|YES (如果資料行是資料行集資料行)。<br /><br /> NO (如果資料行不是資料行集資料行)。|  

## <a name="columnsetcolumns"></a>ColumnSetColumns

ColumnSetColumns 結構描述集合會用於支援疏鬆資料行。 ColumnSetColumns 結構描述集合會針對資料行集中的所有資料行傳回結構描述。 下表將描述這些資料行。  
  
|ColumnName|DataType|描述|  
|----------------|--------------|-----------------|  
|TABLE_CATALOG|String|資料表的目錄。|  
|TABLE_SCHEMA|String|包含資料表的結構描述。|  
|TABLE_NAME|String|資料表名稱。|  
|COLUMN_NAME|String|資料行名稱。|  
|ORDINAL_POSITION|Int32|資料行識別碼。|  
|COLUMN_DEFAULT|String|資料行的預設值。|  
|IS_NULLABLE|String|資料行的 Null 屬性。 若此資料行允許 NULL，其會傳回 YES。 否則，它就會傳回 NO。|  
|DATA_TYPE|String|系統提供的資料類型。|  
|CHARACTER_MAXIMUM_LENGTH|Int32|二進位資料、字元資料，或文字和影像資料的最大長度 (以字元為單位)。 否則，就傳回 NULL。|  
|CHARACTER_OCTET_LENGTH|Int32|二進位資料、字元資料，或文字和影像資料的最大長度 (以位元組為單位)。 否則，就傳回 NULL。|  
|NUMERIC_PRECISION|Unsigned Byte|近似數值資料、精確數值資料、整數資料或貨幣資料的有效位數。 否則，就傳回 NULL。|  
|NUMERIC_PRECISION_RADIX|Int16|近似數值資料、精確數值資料、整數資料或貨幣資料的有效位數基數。 否則，就傳回 NULL。|  
|NUMERIC_SCALE|Int32|近似數值資料、精確數值資料、整數資料或貨幣資料的小數位數。 否則，就傳回 NULL。|  
|DATETIME_PRECISION|Int16|datetime 和 SQL-92 interval 資料類型的子類型代碼。 如果是其他資料類型，就傳回 NULL。|  
|CHARACTER_SET_CATALOG|String|若資料行為字元資料或文字資料型別，則傳回 master，表示字元集所在的資料庫。 否則，就傳回 NULL。|  
|CHARACTER_SET_SCHEMA|String|一律傳回 NULL。|  
|CHARACTER_SET_NAME|String|若此資料行為字元資料或文字資料型別，則傳回字元集的唯一名稱。 否則，就傳回 NULL。|  
|COLLATION_CATALOG|String|若資料行為字元資料或文字資料型別，則傳回 master，表示定義定序的資料庫。 否則，這個資料行就是 NULL。|  
|IS_FILESTREAM|String|YES (如果資料行具有 FILESTREAM 屬性)。<br /><br /> NO (如果資料行沒有 FILESTREAM 屬性)。|  
|IS_SPARSE|String|YES (如果資料行是疏鬆資料行)。<br /><br /> NO (如果資料行不是疏鬆資料行)。|  
|IS_COLUMN_SET|String|YES (如果資料行是資料行集資料行)。<br /><br /> NO (如果資料行不是資料行集資料行)。|  

## <a name="users"></a>使用者  
  
|ColumnName|DataType|Description|  
|----------------|--------------|-----------------|  
|uid|Int16|使用者識別碼，在這個資料庫中是唯一的。 1 是資料庫擁有人。|  
|使用者名稱|String|使用者名稱或群組名稱，在此資料庫中是唯一的。|  
|createdate|DateTime|加入帳戶的日期。|  
|updatedate|DateTime|上次變更帳戶的日期。| 

## <a name="views"></a>檢視  
  
|ColumnName|DataType|描述|  
|----------------|--------------|-----------------|  
|TABLE_CATALOG|String|檢視表的目錄。|  
|TABLE_SCHEMA|String|包含檢視表的結構描述。|  
|TABLE_NAME|String|檢視名稱。|  
|CHECK_OPTION|String|WITH CHECK OPTION 的類型。 若原始檢視表是使用 WITH CHECK OPTION 所建立的，則為 CASCADE。 否則，就傳回 NONE。|  
|IS_UPDATABLE|String|指定檢視是否可以更新。 一律傳回 NO。|  

## <a name="viewcolumns"></a>ViewColumns  
  
|ColumnName|DataType|描述|  
|----------------|--------------|-----------------|  
|VIEW_CATALOG|String|檢視表的目錄。|  
|VIEW_SCHEMA|String|包含檢視表的結構描述。|  
|VIEW_NAME|String|檢視名稱。|  
|TABLE_CATALOG|String|與此檢視表相關之資料表的目錄。|  
|TABLE_SCHEMA|String|包含與此檢視表相關之資料表的結構描述。|  
|TABLE_NAME|String|與檢視表相關之資料表的名稱。 基底資料表。|  
|COLUMN_NAME|String|資料行名稱。|  

## <a name="userdefinedtypes"></a>UserDefinedTypes  
  
|ColumnName|DataType|Description|  
|----------------|--------------|-----------------|  
|assembly_name|String|組件檔案的名稱。|  
|udt_name|String|組件的類別名稱。|  
|version_major|Object|主要版本號碼。|  
|version_minor|Object|次要版本號碼。|  
|version_build|Object|組建編號。|  
|version_revision|Object|修訂編號。|  
|culture_info|Object|與此 UDT 相關的文化特性資訊。|  
|public_key|Object|此組件使用的公開金鑰。|  
|is_fixed_length|Boolean|指定型別的長度是否永遠與 max_length 相同。|  
|max_length|Int16|型別的最大長度 (以位元組為單位)。|  
|Create_Date|DateTime|建立/登錄組件的日期。|  
|Permission_set_desc|String|組件使用權限集合/安全性層級的易記名稱。|  

## <a name="see-also"></a>請參閱

- [擷取資料庫結構描述資訊](retrieving-database-schema-information.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
