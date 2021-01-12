---
title: sys.dm_exec_describe_first_result_set (Transact-SQL)
description: sys.dm_exec_describe_first_result_set (Transact-SQL)
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_exec_describe_first_result_set
- sys.dm_exec_describe_first_result_set_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_describe_first_result_set catalog view
ms.assetid: 6ea88346-0bdb-4f0e-9f1f-4d85e3487d23
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.custom: ''
ms.date: 06/10/2016
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 00bb311a4b1faad0e85a7f3b450db2f1057dcb4e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094098"
---
# <a name="sysdm_exec_describe_first_result_set-transact-sql"></a>sys.dm_exec_describe_first_result_set (Transact-SQL)

[!INCLUDE [SQL Server Azure SQL Database ](../../includes/applies-to-version/sql-asdb.md)]

這個動態管理函數會採用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語句做為參數，並描述語句第一個結果集的中繼資料。  
  
 **sys.dm_exec_describe_first_result_set** 與 [sys.dm_exec_describe_first_result_set_for_object &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-describe-first-result-set-for-object-transact-sql.md) 具有相同的結果集定義，類似于 sp_describe_first_result_set [&#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md)。  
  

 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.dm_exec_describe_first_result_set(@tsql, @params, @include_browse_information)  
```  
  
## <a name="arguments"></a>引數  
 *\@tsql*  
 一個或多個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 *交易 SQL_batch* 可能是 **Nvarchar (** _n_ *_)_* 或 **Nvarchar (max)**。  
  
 *\@params*  
 \@params 提供批次參數的宣告字串 [!INCLUDE[tsql](../../includes/tsql-md.md)] ，類似于 sp_executesql。 參數可以是 **Nvarchar (n)** 或 **Nvarchar (max)**。  
  
 這是一個字串，其中包含已內嵌在 _batch 中之所有參數的定義 [!INCLUDE[tsql](../../includes/tsql-md.md)] **。 此字串必須是 Unicode 常數或 Unicode 變數。 每個參數定義都由參數名稱和資料類型組成。 *n* 是指出其他參數定義的預留位置。 在 stmt 中指定的每個參數都必須在 params 中定義 \@ 。 如果 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語句中的語句或批次不包含參數， \@ 則不需要 params。 這個參數的預設值是 NULL。  
  
 *\@include_browse_information*  
 如果設定為 1，就會分析每個查詢，如同查詢上有 FOR BROWSE 選項一樣。 會傳回其他索引鍵資料行和來源資料表資訊。  
  
## <a name="table-returned"></a>傳回的資料表  
 將這個通用中繼資料當做結果集傳回。 結果中繼資料中的每個資料行都會有一個資料列，每個資料列都會使用下表所示的格式來描述資料行的類型和 Null 屬性。 如果每個控制項路徑都沒有第一個陳述式，就會傳回具有零個資料列的結果集。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**is_hidden**|**bit**|指定資料行是為了瀏覽和參考用途而加入的額外資料行，而不會實際顯示在結果集中。|  
|**column_ordinal**|**int**|包含資料行在結果集中的序數位置。 第一個資料行的位置將會指定為 1。|  
|**name**|**sysname**|如果可以判別名稱，則包含資料行名稱。 否則包含 NULL。|  
|**is_nullable**|**bit**|包含下列值：<br /><br /> 如果資料行允許 NULL，則為 1 值。<br /><br /> 如果資料行不允許 NULL，則為 0 值。<br /><br /> 如果無法判別資料行是否允許 NULL，則為 1 值。|  
|**system_type_id**|**int**|包含 sys. 類型中所指定之資料行資料類型的 system_type_id。 針對 CLR 類型，即使 system_type_name 資料行將傳回 NULL，這個資料行將會傳回值 240。|  
|**system_type_name**|**nvarchar(256)**|包含名稱和引數 (例如長度、有效位數、小數位數)，已指定給資料行的資料類型。<br /><br /> 如果資料類型是使用者定義的別名類型，這裡就會指定基礎系統類型。<br /><br /> 如果資料類型是 CLR 使用者定義類型，這個資料行就會傳回 NULL。|  
|**max_length**|**smallint**|資料行的最大長度 (以位元組為單位)。<br /><br /> -1 = 資料行資料類型是 **Varchar (max)**、 **Nvarchar (max)**、 **Varbinary (max)** 或 **xml**。<br /><br /> 若為 **文字** 資料行， **max_length** 值會是16或 **sp_tableoption ' text in row '** 所設定的值。|  
|**有效位數**|**tinyint**|如果以數值為基礎，就是資料行的有效位數。 否則傳回 0。|  
|**scale**|**tinyint**|如果是以數值為基礎，便是資料行的小數位數。 否則傳回 0。|  
|**collation_name**|**sysname**|如果是以字元為基礎，便是資料行的定序名稱。 否則，便傳回 NULL。|  
|**user_type_id**|**int**|針對 CLR 和別名類型，會如同 sys.types 中所指定，包含資料行資料類型的 user_type_id。 否則，便為 NULL。|  
|**user_type_database**|**sysname**|針對 CLR 和別名類型，會包含定義類型之資料庫的名稱。 否則，便為 NULL。|  
|**user_type_schema**|**sysname**|針對 CLR 和別名類型，會包含定義類型之結構描述的名稱。 否則，便為 NULL。|  
|**user_type_name**|**sysname**|針對 CLR 和別名類型，會包含類型的名稱。 否則，便為 NULL。|  
|**assembly_qualified_type_name**|**nvarchar(4000)**|針對 CLR 類型，會傳回定義類型之組件與類別的名稱。 否則，便為 NULL。|  
|**xml_collection_id**|**int**|包含 sys.columns 中所指定資料行之資料類型的 xml_collection_id。 如果傳回的類型沒有與 XML 結構描述集合相關聯，這個資料行會傳回 NULL。|  
|**xml_collection_database**|**sysname**|包含定義與這個類型相關聯之 XML 結構描述集合的資料庫。 如果傳回的類型沒有與 XML 結構描述集合相關聯，這個資料行會傳回 NULL。|  
|**xml_collection_schema**|**sysname**|包含定義與這個類型相關聯之 XML 結構描述集合的結構描述。 如果傳回的類型沒有與 XML 結構描述集合相關聯，這個資料行會傳回 NULL。|  
|**xml_collection_name**|**sysname**|包含與這個類型相關聯之 XML 結構描述集合的名稱。 如果傳回的類型沒有與 XML 結構描述集合相關聯，這個資料行會傳回 NULL。|  
|**is_xml_document**|**bit**|如果正要傳回的資料類型是 XML，而且該類型保證是完整 XML 文件 (包含根節點)，而不是 XML 片段，則傳回 1， 否則傳回 0。|  
|**is_case_sensitive**|**bit**|如果資料行是區分大小寫的字串類型，則傳回 1。 如果不是則傳回 0。|  
|**is_fixed_length_clr_type**|**bit**|如果資料行是固定長度的 CLR 類型，則傳回 1。 如果不是則傳回 0。|  
|**source_server**|**sysname**|原始伺服器名稱 (如果它來自遠端伺服器)。 系統會在 sys. 伺服器中提供名稱。 如果資料行來自本機伺服器，或是如果無法判別其原始伺服器，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**source_database**|**sysname**|這個結果中的資料行所傳回之原始資料庫名稱。 如果無法判別資料庫，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**source_schema**|**sysname**|這個結果中的資料行所傳回之原始結構描述名稱。 如果無法判別結構描述，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**source_table**|**sysname**|這個結果的資料行所傳回之原始資料表名稱。 如果無法判別資料表，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**source_column**|**sysname**|結果資料行所傳回之原始資料行名稱。 如果無法判別資料行，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**is_identity_column**|**bit**|如果資料行是識別欄位，則傳回 1；如果不是，則傳回 0。 如果它無法判別資料行是否為識別欄位，則傳回 NULL。|  
|**is_part_of_unique_key**|**bit**|如果資料行是唯一索引 (包括唯一和主要的條件約束) 的一部分，則傳回 1，否則傳回 0。 如果它無法判別資料行是否為唯一索引的一部分，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**is_updateable**|**bit**|如果資料行是可更新的，則傳回 1；如果不是，則傳回 0。 如果它無法判別資料行是否可更新，則傳回 NULL。|  
|**is_computed_column**|**bit**|如果資料行是計算資料行，則傳回 1；如果不是，則傳回 0。 如果無法判別資料行是否為計算資料行，則傳回 NULL。|  
|**is_sparse_column_set**|**bit**|如果資料行是疏鬆資料行，則傳回 1，否則傳回 0。 如果無法判別資料行是否為疏鬆資料行集的一部分，則傳回 NULL。|  
|**ordinal_in_order_by_list**|**smallint**|這個資料行在 ORDER BY 清單中的位置。 如果資料行不會顯示在 ORDER BY 清單中，或如果無法唯一判別 ORDER BY 清單，則傳回 NULL。|  
|**order_by_list_length**|**smallint**|ORDER BY 清單的長度。 如果沒有 ORDER BY 清單，或如果無法唯一判別 ORDER BY 清單，則傳回 NULL。 請注意，對於由 sp_describe_first_result_set 傳回的所有資料列來說，這個值都是一樣的。|  
|**order_by_is_descending**|**Smallint Null**|如果 ordinal_in_order_by_list 不是 NULL，**order_by_is_descending** 資料行會回報此資料行的 ORDER BY 子句方向。 否則，它會回報 NULL。|  
|**error_number**|**int**|包含函數傳回的錯誤號碼。 如果未發生錯誤，則資料行會包含 NULL。|  
|**error_severity**|**int**|包含函數傳回的嚴重性。 如果未發生錯誤，則資料行會包含 NULL。|  
|**error_state**|**int**|包含函數傳回的 狀態訊息。 如果未發生錯誤，則資料行會包含 NULL。|  
|**error_message**|**Nvarchar (4096)**|包含函數傳回的訊息。 如果未發生錯誤，則資料行會包含 NULL。|  
|**error_type**|**int**|包含整數，代表要傳回的錯誤。 對應到 error_type_desc。 請參閱備註下的清單。|  
|**error_type_desc**|**nvarchar(60)**|包含簡短大寫字串，表示要傳回的錯誤。 對應到 error_type。 請參閱備註下的清單。|  
  
## <a name="remarks"></a>備註  
 這個函數與 **sp_describe_first_result_set** 使用相同的演算法。 如需詳細資訊，請參閱 [sp_describe_first_result_set &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md)。  
  
 下表列出錯誤類型及其說明。  
  
|error_type|error_type|描述|  
|-----------------|-----------------|-----------------|  
|1|MISC|未說明的所有錯誤。|  
|2|SYNTAX|批次發生語法錯誤。|  
|3|CONFLICTING_RESULTS|因為兩個可能的第一個陳述式之間的衝突，無法判定結果。|  
|4|DYNAMIC_SQL|因為動態 SQL 可能傳回第一個結果，無法判定結果。|  
|5|CLR_PROCEDURE|因為 CLR 預存程序可能傳回第一個結果，無法判定結果。|  
|6|CLR_TRIGGER|因為 CLR 觸發程序可能傳回第一個結果，無法判定結果。|  
|7|EXTENDED_PROCEDURE|因為擴充預存程序可能傳回第一個結果，無法判定結果。|  
|8|UNDECLARED_PARAMETER|無法判斷結果，因為一或多個結果集資料行的資料類型可能相依于未宣告的參數。|  
|9|RECURSION|因為批次包含遞迴陳述式，無法判定結果。|  
|10|TEMPORARY_TABLE|因為批次包含暫存資料表且不受 **sp_describe_first_result_set** 支援，無法判定結果。|  
|11|UNSUPPORTED_STATEMENT|因為批次包含 **sp_describe_first_result_set** 不支援的陳述式 (例如，FETCH、REVERT 等)，無法判定結果。|  
|12|OBJECT_TYPE_NOT_SUPPORTED|\@不支援傳遞至函數的 object_id (亦即不是預存程式) |  
|13|OBJECT_DOES_NOT_EXIST|在 \@ 系統目錄中找不到傳遞至函數的 object_id。|  
  
## <a name="permissions"></a>權限  
 需要執行 \@ tsql 引數的許可權。  
  
## <a name="examples"></a>範例  
 您可以調整 sp_describe_first_result_set 主題中 [&#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md) 的其他範例，以使用 **sys.dm_exec_describe_first_result_set**。  
  
### <a name="a-returning-information-about-a-single-transact-sql-statement"></a>A. 傳回單一 Transact-SQL 陳述式的相關資訊  
 下列程式碼會傳回 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式結果的相關資訊。  
  
```  
USE AdventureWorks2012;  
GO  
SELECT * FROM sys.dm_exec_describe_first_result_set  
(N'SELECT object_id, name, type_desc FROM sys.indexes', null, 0) ;  
```  
  
### <a name="b-returning-information-about-a-procedure"></a>B. 傳回程序的相關資訊  
 下列範例會建立名為 pr_TestProc 的預存程式，該預存程式會傳回兩個結果集。 然後此範例示範 **sys.dm_exec_describe_first_result_set** 傳回此預存程序的第一個結果集相關資訊。  
  
```  
USE AdventureWorks2012;  
GO  
  
CREATE PROC Production.TestProc  
AS  
SELECT Name, ProductID, Color FROM Production.Product ;  
SELECT Name, SafetyStockLevel, SellStartDate FROM Production.Product ;  
GO  
  
SELECT * FROM sys.dm_exec_describe_first_result_set  
('Production.TestProc', NULL, 0) ;  
```  
  
### <a name="c-returning-metadata-from-a-batch-that-contains-multiple-statements"></a>C. 從包含多個陳述式的批次傳回中繼資料  
 下列範例會評估包含兩個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的批次。 結果集描述第一個傳回的結果集。  
  
```  
USE AdventureWorks2012;  
GO  
  
SELECT * FROM sys.dm_exec_describe_first_result_set(  
N'SELECT CustomerID, TerritoryID, AccountNumber FROM Sales.Customer WHERE CustomerID = @CustomerID;  
SELECT * FROM Sales.SalesOrderHeader;',  
N'@CustomerID int', 0) AS a;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [sp_describe_first_result_set &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md)   
 [sp_describe_undeclared_parameters &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql.md)   
 [sys.dm_exec_describe_first_result_set_for_object &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-describe-first-result-set-for-object-transact-sql.md)  
  
  
