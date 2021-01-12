---
description: sys.dm_exec_describe_first_result_set_for_object (Transact-SQL)
title: sys.dm_exec_describe_first_result_set_for_object (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_exec_describe_first_result_set_for_object_TSQL
- sys.dm_exec_describe_first_result_set_for_object
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_describe_first_result_set_for_object catalog view
ms.assetid: 63b0fde7-95d7-4ad7-a219-a9feacf1bd89
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 490f2197f1399bd89b83a920085829c96a4ed52c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101634"
---
# <a name="sysdm_exec_describe_first_result_set_for_object-transact-sql"></a>sys.dm_exec_describe_first_result_set_for_object (Transact-SQL)
[!INCLUDE [SQL Server Azure SQL Database ](../../includes/applies-to-version/sql-asdb.md)]

  這個動態管理函式會採用 @object_id 做為參數，並描述具有該識別碼之模組的第一個結果中繼資料。 @object_id指定的可以是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 預存程式或觸發程式的識別碼 [!INCLUDE[tsql](../../includes/tsql-md.md)] 。 如果是任何其他物件 (例如檢視表、資料表、函數或 CLR 程序) 的識別碼，結果的錯誤資料行中會指定錯誤。  
  
 **sys.dm_exec_describe_first_result_set_for_object** 與 [sys.dm_exec_describe_first_result_set &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-describe-first-result-set-transact-sql.md) 具有相同的結果集定義，類似于 sp_describe_first_result_set [&#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.dm_exec_describe_first_result_set_for_object   
    ( @object_id , @include_browse_information )  
```  
  
## <a name="arguments"></a>引數  
 *\@object_id*  
 @object_id [!INCLUDE[tsql](../../includes/tsql-md.md)] 預存程式或觸發程式的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 。 @object_id 的類型為 **int**。  
  
 *\@include_browse_information*  
 @include_browse_information 是類型 **位**。 如果設定為 1，就會分析每個查詢，如同查詢上有 FOR BROWSE 選項一樣。 會傳回其他索引鍵資料行和來源資料表資訊。  
  
## <a name="table-returned"></a>傳回的資料表  
 這個通用的中繼資料會當做結果集來傳回，而結果中繼資料中的每個資料行都會有一個資料列。 每個資料列都會使用下一節所描述的格式來描述資料行的類型和 Null 屬性。 如果每個控制項路徑都沒有第一個陳述式，就會傳回具有零個資料列的結果集。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**is_hidden**|**bit**|指定資料行是否為了用來瀏覽資訊而加入的額外資料行，而不會實際顯示在結果集中。|  
|**column_ordinal**|**int**|包含資料行在結果集中的序數位置。 第一個資料行的位置將會指定為 1。|  
|**name**|**sysname**|如果可以判別名稱，則包含資料行名稱。 否則，便為 NULL。|  
|**is_nullable**|**bit**|如果資料行允許 NULL 則包含值 1，如果資料行不允許 NULL 則包含 0，此外，如果無法判別資料行是否允許 NULL，則為 1。|  
|**system_type_id**|**int**|包含 sys. 類型中所指定資料行之資料類型的 system_type_id。 針對 CLR 類型，即使 system_type_name 資料行將傳回 NULL，這個資料行將會傳回值 240。|  
|**system_type_name**|**nvarchar(256)**|包含資料類型名稱。 包含指定給資料行之資料類型的引數 (例如長度、有效位數、小數位數)。 如果資料類型是使用者定義的別名類型，這裡就會指定基礎系統類型。 如果它是 CLR 使用者定義類型，這個資料行就會傳回 NULL。|  
|**max_length**|**smallint**|資料行的最大長度 (以位元組為單位)。<br /><br /> -1 = 資料行資料類型是 **Varchar (max)**、 **Nvarchar (max)**、 **Varbinary (max)** 或 **xml**。<br /><br /> 若為 **文字** 資料行， **max_length** 值會是16或 **sp_tableoption ' text in row '** 所設定的值。|  
|**有效位數**|**tinyint**|如果以數值為基礎，就是資料行的有效位數。 否則傳回 0。|  
|**scale**|**tinyint**|如果是以數值為基礎，便是資料行的小數位數。 否則傳回 0。|  
|**collation_name**|**sysname**|如果是以字元為基礎，便是資料行的定序名稱。 否則，便傳回 NULL。|  
|**user_type_id**|**int**|針對 CLR 和別名類型，會如同 sys.types 中所指定，包含資料行資料類型的 user_type_id。 否則，便為 NULL。|  
|**user_type_database**|**sysname**|針對 CLR 和別名類型，會包含定義類型之資料庫的名稱。 否則，便為 NULL。|  
|**user_type_schema**|**sysname**|針對 CLR 和別名類型，會包含定義類型之結構描述的名稱。 否則，便為 NULL。|  
|**user_type_name**|**sysname**|針對 CLR 和別名類型，會包含類型的名稱。 否則，便為 NULL。|  
|**assembly_qualified_type_name**|**nvarchar(4000)**|針對 CLR 類型，會傳回定義類型之組件與類別的名稱。 否則，便為 NULL。|  
|**xml_collection_id**|**int**|包含 sys.columns 中所指定資料行之資料類型的 xml_collection_id。 如果傳回的類型沒有與 XML 結構描述集合相關聯，這個資料行將傳回 NULL。|  
|**xml_collection_database**|**sysname**|包含定義與這個類型相關聯之 XML 結構描述集合的資料庫。 如果傳回的類型沒有與 XML 結構描述集合相關聯，這個資料行將傳回 NULL。|  
|**xml_collection_schema**|**sysname**|包含定義與這個類型相關聯之 XML 結構描述集合的結構描述。 如果傳回的類型沒有與 XML 結構描述集合相關聯，這個資料行將傳回 NULL。|  
|**xml_collection_name**|**sysname**|包含與這個類型相關聯之 XML 結構描述集合的名稱。 如果傳回的類型沒有與 XML 結構描述集合相關聯，這個資料行將傳回 NULL。|  
|**is_xml_document**|**bit**|如果正要傳回的資料類型是 XML，而且該類型保證是完整 XML 文件 (包含根節點)，而不是 XML 片段，則傳回 1， 否則傳回 0。|  
|**is_case_sensitive**|**bit**|如果資料行是區分大小寫的字串類型，則傳回 1，否則傳回 0。|  
|**is_fixed_length_clr_type**|**bit**|如果資料行是固定長度的 CLR 類型，則傳回 1，否則傳回 0。|  
|**source_server**|**sysname**|在這個結果中的資料行所傳回的原始伺服器名稱 (如果它來自遠端伺服器)。 系統會在 sys. 伺服器中提供名稱。  如果資料行來自本機伺服器，或是如果無法判別其原始伺服器，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**source_database**|**sysname**|這個結果中的資料行所傳回之原始資料庫名稱。 如果無法判別資料庫，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**source_schema**|**sysname**|這個結果中的資料行所傳回之原始結構描述名稱。 如果無法判別結構描述，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**source_table**|**sysname**|這個結果的資料行所傳回之原始資料表名稱。 如果無法判別資料表，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**source_column**|**sysname**|這個結果中的資料行所傳回之原始資料行名稱。 如果無法判別資料行，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**is_identity_column**|**bit**|如果資料行是識別欄位，則傳回 1；如果不是，則傳回 0。 如果它無法判別資料行是否為識別欄位，則傳回 NULL。|  
|**is_part_of_unique_key**|**bit**|如果資料行是唯一索引 (包括唯一和主要的條件約束) 的一部分，則傳回 1；如果不是，則傳回 0。 如果它無法判別資料行是否為唯一索引的一部分，則傳回 NULL。 只會在要求瀏覽資訊時填入。|  
|**is_updateable**|**bit**|如果資料行是可更新的，則傳回 1；如果不是，則傳回 0。 如果它無法判別資料行是否可更新，則傳回 NULL。|  
|**is_computed_column**|**bit**|如果資料行是計算資料行，則傳回 1；如果不是，則傳回 0。 如果它無法判別資料行是否為計算資料行，則傳回 NULL。|  
|**is_sparse_column_set**|**bit**|如果資料行是疏鬆資料行，則傳回 1，否則傳回 0。 如果無法判別資料行是否為疏鬆資料行集的一部分，則傳回 NULL。|  
|**ordinal_in_order_by_list**|**smallint**|這個資料行在 ORDER BY 清單中的位置。如果資料行不會顯示在 ORDER BY 清單中，或如果無法唯一判別 ORDER BY 清單，則傳回 NULL。|  
|**order_by_list_length**|**smallint**|ORDER BY 清單的長度。 如果沒有 ORDER BY 清單，或如果無法唯一判別 ORDER BY 清單，則傳回 NULL。 請注意，對於由 sp_describe_first_result_set 傳回的所有資料列來說，這個值都是一樣的。|  
|**order_by_is_descending**|**Smallint Null**|如果 ordinal_in_order_by_list 不是 NULL，**order_by_is_descending** 資料行會回報此資料行的 ORDER BY 子句方向。 否則，它會回報 NULL。|  
|**error_number**|**int**|包含函數傳回的錯誤號碼。 如果資料行未發生錯誤，則包含 NULL。|  
|**error_severity**|**int**|包含函數傳回的嚴重性。 如果資料行未發生錯誤，則包含 NULL。|  
|**error_state**|**int**|包含函數傳回的狀態訊息。 如果未發生錯誤， 則資料行會包含 NULL。|  
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
|12|OBJECT_ID_NOT_SUPPORTED|@object_id不支援傳遞至函數的， (亦即不是預存程式) |  
|13|OBJECT_ID_DOES_NOT_EXIST|在 @object_id 系統目錄中找不到傳遞給函式的。|  
  
## <a name="permissions"></a>權限  
 需要執行 @tsql 引數的許可權。  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-metadata-with-and-without-browse-information"></a>A. 傳回具有及不具有瀏覽資訊的中繼資料  
 下列範例會建立一個名為 TestProc2 的預存程式，它會傳回兩個結果集。 然後此範例示範 **sys.dm_exec_describe_first_result_set** 傳回此預存程序的第一個結果集相關資訊 (具有瀏覽資訊以及不具瀏覽資訊)。  
  
```  
CREATE PROC TestProc2  
AS  
SELECT object_id, name FROM sys.objects ;  
SELECT name, schema_id, create_date FROM sys.objects ;  
GO  
  
SELECT * FROM sys.dm_exec_describe_first_result_set_for_object(OBJECT_ID('TestProc2'), 0) ;  
SELECT * FROM sys.dm_exec_describe_first_result_set_for_object(OBJECT_ID('TestProc2'), 1) ;  
GO  
```  
  
### <a name="b-combining-the-sysdm_exec_describe_first_result_set_for_object-function-and-a-table-or-view"></a>B. 結合 sys.dm_exec_describe_first_result_set_for_object 函數與資料表或檢視表  
 下列範例會使用 sys. 程式系統目錄檢視和 **sys.dm_exec_describe_first_result_set_for_object** 函式來顯示資料庫中所有預存程式之結果集的中繼資料 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 。  
  
```  
USE AdventureWorks2012;  
GO  
  
SELECT p.name, r.*   
FROM sys.procedures AS p  
CROSS APPLY sys.dm_exec_describe_first_result_set_for_object(p.object_id, 0) AS r;  
GO  
  
```  
  
## <a name="see-also"></a>另請參閱  
 [sp_describe_first_result_set &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md)   
 [sp_describe_undeclared_parameters &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql.md)   
 [sys.dm_exec_describe_first_result_set &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-describe-first-result-set-transact-sql.md)  
  
  
