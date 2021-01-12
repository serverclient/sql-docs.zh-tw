---
description: CREATE TYPE (Transact-SQL)
title: CREATE TYPE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/05/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- sql13.swb.sysdatatype.properties.f1
- CREATE TYPE
- TYPE_TSQL
- TYPE
- CREATE_TYPE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- UDTs [SQL Server], creating
- CLR user-defined types [SQL Server]
- user-defined table types [SQL Server]
- user-defined types [SQL Server], creating
- CREATE TYPE statement
- alias data types [SQL Server], creating
- data types [SQL Server], creating
ms.assetid: 2202236b-e09f-40a1-bbc7-b8cff7488905
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: a2a3cafce24295670839dbce25f67301900ce57b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099496"
---
# <a name="create-type-transact-sql"></a>CREATE TYPE (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 的目前資料庫中建立別名資料類型或使用者定義型別。 別名資料類型的實作是以 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 原生系統類型為基礎。 使用者定義型別是使用 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 通用語言執行平台 (CLR) 中之組件的類別來實作的。 若要將使用者定義型別繫結到它的實作，必須先在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中使用 [CREATE ASSEMBLY](../../t-sql/statements/create-assembly-transact-sql.md) 來註冊內含該型別之實作的 CLR 組件。  
  
 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，依預設，執行 CLR 程式碼的功能是關閉。 您可以建立、修改及卸除參考受控碼模組的資料庫物件，但除非您使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) 來啟用 [clr enabled 選項](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md)；否則，這些參考就不會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中執行。  
 
> [!NOTE]  
>  本主題將討論如何將 .NET Framework CLR 整合至 SQL Server。 CLR 整合不適用於 Azure [!INCLUDE[ssSDS](../../includes/sssds-md.md)]。
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- User-defined Data Type Syntax    
CREATE TYPE [ schema_name. ] type_name  
{   
    [
      FROM base_type   
      [ ( precision [ , scale ] ) ]  
      [ NULL | NOT NULL ]
    ]
    | EXTERNAL NAME assembly_name [ .class_name ]   
    | AS TABLE ( { <column_definition> | <computed_column_definition> [ ,... n ] }
      [ <table_constraint> ] [ ,... n ]    
      [ <table_index> ] [ ,... n ] } )
 
} [ ; ]  
  
<column_definition> ::=  
column_name <data_type>  
    [ COLLATE collation_name ]   
    [ NULL | NOT NULL ]  
    [   
        DEFAULT constant_expression ]   
      | [ IDENTITY [ ( seed ,increment ) ]   
    ]  
    [ ROWGUIDCOL ] [ <column_constraint> [ ...n ] ]   
  
<data type> ::=   
[ type_schema_name . ] type_name   
    [ ( precision [ , scale ] | max |   
                [ { CONTENT | DOCUMENT } ] xml_schema_collection ) ]   
  
<column_constraint> ::=   
{     { PRIMARY KEY | UNIQUE }   
        [ CLUSTERED | NONCLUSTERED ]   
        [   
            WITH ( <index_option> [ ,...n ] )   
        ]  
  | CHECK ( logical_expression )   
}   
  
<computed_column_definition> ::=  
  
column_name AS computed_column_expression   
[ PERSISTED [ NOT NULL ] ]  
[   
    { PRIMARY KEY | UNIQUE }  
        [ CLUSTERED | NONCLUSTERED ]  
        [   
            WITH ( <index_option> [ ,...n ] )  
        ]  
    | CHECK ( logical_expression )   
]   
  
<table_constraint> ::=  
{   
    { PRIMARY KEY | UNIQUE }   
        [ CLUSTERED | NONCLUSTERED ]   
    ( column [ ASC | DESC ] [ ,...n ] )   
        [   
    WITH ( <index_option> [ ,...n ] )   
        ]  
    | CHECK ( logical_expression )   
}   
  
<index_option> ::=  
{  
    IGNORE_DUP_KEY = { ON | OFF }  
}  

< table_index > ::=  
  INDEX constraint_name  
     [ CLUSTERED | NONCLUSTERED ]   (column [ ASC | DESC ] [ ,... n ] )} }  
```  
  
```syntaxsql
-- User-defined Memory Optimized Table Types Syntax  
CREATE TYPE [schema_name. ] type_name  
AS TABLE ( { <column_definition> [ ,... n ] }  
    | [ <table_constraint> ] [ ,... n ]    
    | [ <table_index> ] [ ,... n ] } )
    [ WITH ( <table_option> [ ,... n ] ) ]  
 [ ; ]  
  
<column_definition> ::=  
column_name <data_type>  
    [ COLLATE collation_name ]   [ NULL | NOT NULL ]    [  
      [ IDENTITY [ (1 , 1) ]  
    ]  
    [ <column_constraint> [ ... n ] ]    [ <column_index> ]  
  
<data type> ::=  
 [type_schema_name . ] type_name [ (precision [ , scale ]) ]  
  
<column_constraint> ::=  
{ PRIMARY KEY {   NONCLUSTERED HASH WITH (BUCKET_COUNT = bucket_count) 
                | NONCLUSTERED } }  
  
< table_constraint > ::=  
{ PRIMARY KEY { NONCLUSTERED HASH (column [ ,... n ] ) 
                   WITH (BUCKET_COUNT = bucket_count) 
               | NONCLUSTERED  (column [ ASC | DESC ] [ ,... n ] )  } }  
  
<column_index> ::=  
  INDEX index_name  
{ { [ NONCLUSTERED ] HASH WITH (BUCKET_COUNT = bucket_count) 
     | NONCLUSTERED } }  
  
< table_index > ::=  
  INDEX constraint_name  
{ { [ NONCLUSTERED ] HASH (column [ ,... n ] ) WITH (BUCKET_COUNT = bucket_count) 
 |  [NONCLUSTERED]  (column [ ASC | DESC ] [ ,... n ] )} }  
  
<table_option> ::=  
{  
    [MEMORY_OPTIMIZED = {ON | OFF}]  
}  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *schema_name*  
 這是別名資料類型或使用者自訂類型所屬的結構描述名稱。  
  
 *type_name*  
 這是別名資料類型或使用者自訂類型的名稱。 類型名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 *base_type*  
 為別名資料類型依據的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 提供的資料類型。 *base_type* 為沒有預設值的 **sysname**，且可以為下列值之一：  
  
|||||  
|-|-|-|-|  
|**bigint**|**binary(** *n* **)**|**bit**|**char(** *n* **)**|  
|**date**|**datetime**|**datetime2**|**datetimeoffset**|  
|**decimal**|**float**|**image**|**int**|  
|**money**|**nchar(** *n* **)**|**ntext**|**numeric**|  
|**nvarchar(** *n* &#124; **max)**|**real**|**smalldatetime**|**smallint**|  
|**smallmoney**|**sql_variant**|**text**|**time**|  
|**tinyint**|**uniqueidentifier**|**varbinary(** *n* &#124; **max)**|**varchar(** *n* &#124; **max)**|  
  
 *base_type* 也可以是對應至這些系統資料類型之一的任何資料類型同義字。  
  
 *有效位數*  
 如果是 **decimal** 或 **numeric**，這會是一個非負數整數，它指出可儲存的最大十進位數總數，小數點左右兩側都包括在內。 如需詳細資訊，請參閱 [decimal 和 numeric &#40;TRANSACT-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md)。  
  
 *scale*  
 如果是 **decimal** 或 **numeric**，這會是一個非負數整數，它指出小數點右側所能儲存的最大十進位數，且它必須小於或等於有效位數。 如需詳細資訊，請參閱 [decimal 和 numeric &#40;TRANSACT-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md)。  
  
 **NULL** | NOT NULL  
 指定類型可否保留 Null 值。 若未指定，NULL 是預設值。  
  
 *assembly_name*  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定在通用語言執行平台中參考使用者定義型別實作的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組件。 *assembly_name* 必須符合目前資料庫中 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的現有組件。  
  
> [!NOTE]  
>  自主資料庫無法使用 EXTERNAL_NAME。  
  
 **[.** *class_name*  **]**  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定組件內實作使用者自訂類型的類別。 *class_name* 必須是有效的識別碼，且必須以類別的形式存在於可以顯示的組件中。 不論資料庫定序為何，*class_name* 都要區分大小寫，且必須完全符合相對應組件中的類別名稱。 如果用來撰寫類別的程式設計語言使用命名空間概念 (如 C#)，類別名稱可以是一個以方括弧 ( **[ ]** ) 括住之符合命名空間資格的名稱。 如果未指定 *class_name*，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會假設它和 *type_name* 一樣。  
  
 \<column_definition>  
 定義使用者定義資料表類型的資料行。  
  
 \<data type>  
 針對使用者定義資料表類型定義資料行中的資料類型。 如需資料類型的詳細資訊，請參閱[資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)。 如需資料表的詳細資訊，請參閱 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)。  
  
 \<column_constraint>  
 定義使用者定義資料表類型的資料行條件約束。 支援的條件約束包括 PRIMARY KEY、UNIQUE 和 CHECK。 如需資料表的詳細資訊，請參閱 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)。  
  
 \<computed_column_definition>  
 將計算資料行運算式定義為使用者定義資料表類型中的資料行。 如需資料表的詳細資訊，請參閱 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)。  
  
 \<table_constraint>  
 定義使用者定義資料表類型上的資料表條件約束。 支援的條件約束包括 PRIMARY KEY、UNIQUE 和 CHECK。  
  
 \<index_option>  
 指定在唯一叢集或唯一非叢集索引的多資料列插入作業中，對於索引鍵值重複的錯誤回應。 如需索引選項的詳細資訊，請參閱 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)。  
 
  `INDEX *index_name* [ CLUSTERED | NONCLUSTERED ] (*column_name* [ ASC | DESC ] [ ,... *n* ] )`  
     
**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

指定要在資料表上建立索引。 這可以是叢集的索引或非叢集索引。 索引將包含列示的資料行，並會以遞增或遞減順序來排序資料。
  
 INDEX  
 您必須指定資料行和資料表索引，做為 CREATE TABLE 陳述式的一部分。 記憶體最佳化資料表不支援 CREATE INDEX 和 DROP INDEX。  
  
 MEMORY_OPTIMIZED  
 **適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 指出資料表類型是否為記憶體最佳化。 此選項預設關閉；資料表 (類型) 不是記憶體最佳化的資料表 (類型)。 記憶體最佳化的資料表類型是記憶體最佳化的使用者資料表，其結構描述保存在磁碟上，類似於其他使用者資料表。  
  
 BUCKET_COUNT  
 **適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 指出應該在雜湊索引中建立的貯體數目。 雜湊索引中 BUCKET_COUNT 的最大值是 1,073,741,824。 如需貯體計數的詳細資訊，請參閱[經記憶體最佳化之資料表上的索引](../../relational-databases/in-memory-oltp/indexes-for-memory-optimized-tables.md)。 *bucket_count* 為必要的引數。  
  
 HASH  
 **適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 表示已建立雜湊索引。 只有記憶體最佳化的資料表才支援雜湊索引。  
  
## <a name="remarks"></a>備註  
 *assembly_name* 中參考的組件類別及其方法，應符合在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中實作使用者定義型別的所有需求。 如需這些需求的詳細資訊，請參閱 [CLR 使用者定義型別](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)。  
  
 其他考量包括以下各項：  
  
-   類別可以有多載的方法，但只能從 Managed 程式碼內 (不能從 [!INCLUDE[tsql](../../includes/tsql-md.md)] 內) 呼叫這些方法。  
  
-   如果 *assembly_name* 是 SAFE 或 EXTERNAL_ACCESS，任何靜態成員都必須宣告為 **const** 或 **readonly**。  
  
 在資料庫內，只能有一個依照已從 CLR 上傳至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中之任何指定類型來註冊的使用者自訂類型。 如果使用者自訂類型建立在某 CLR 類型上，而針對該 CLR 類型，在資料庫中已存在使用者自訂類型，則 CREATE TYPE 會因錯誤而失敗。 如果 CLR 類型可以對應至多個使用者自訂類型，就需要利用這項限制來避免 SQL 類型解析期間的模稜兩可。  
  
 如果類型中有任何 mutator 方法沒有傳回 *void*，CREATE TYPE 陳述式就不會執行。  
  
 若要修改使用者自訂類型，您必須利用 DROP TYPE 陳述式卸除該類型，然後重新建立它。  
  
 **public** 資料庫角色與使用 **sp_addtype** 建立的使用者定義型別不同，前者不會在使用 CREATE TYPE 建立的類型上自動被授與 REFERENCES 權限。 這個權限必須另外授與。  
  
 在使用者定義的資料表類型中，*column_name* \<data type> 所使用結構化使用者定義類型屬於資料表類型定義所在資料庫結構描述範圍的一部分。 若要在資料庫中存取不同範圍內的結構化使用者定義型別，請使用兩部分的名稱。  
  
 在使用者定義的資料表類型中，計算資料行的主索引鍵必須是 PERSISTED 和 NOT NULL。  
  
## <a name="memory-optimized-table-types"></a>記憶體最佳化的資料表類型  
 從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始，資料表類型的資料處理可以在主要記憶體中執行，而不是在磁碟上。 如需詳細資訊，請參閱[記憶體內部 OLTP &#40;記憶體內部最佳化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)。 如需如何建立經記憶體最佳化的資料表類型的範例程式碼，請參閱[建立經記憶體最佳化的資料表和原生編譯的預存程序](../../relational-databases/in-memory-oltp/creating-a-memory-optimized-table-and-a-natively-compiled-stored-procedure.md)。  
  
## <a name="permissions"></a>權限  
 需要目前資料庫的 CREATE TYPE 權限，以及 *schema_name* 的 ALTER 權限。 如果未指定 *schema_name* ，則套用用來判斷目前使用者之結構描述的預設名稱解析規則。 如果指定 *assembly_name*，則使用者必須擁有該組件，或必須有在該組件上的 REFERENCES 權限。  

 如果將 CREATE TABLE 陳述式中的任何資料行定義成使用者定義型別，則需要使用者定義型別的 REFERENCES 權限。
 
   >[!NOTE]
  > 一位使用者建立一張資料表，裡面有一個資料行採用使用者定義的型別，這位使用者需要 REFERENCES 權限。
  > 如果此資料表必須建立在 TempDB 中，則每次建立資料表 **之前**，必須必須明確授與 REFERENCES 權限，或者此資料類型和 REFERENCES 權限需要加入至模型資料庫。 如果這樣做，則 TempDB 便永遠能取得此資料類型和權限。 否則，當 SQL Server 重新啟動時，使用者定義的資料類型和權限就會消失。 如需詳細資訊，請參閱 [CREATE TABLE](./create-table-transact-sql.md#permissions-1)。
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-an-alias-type-based-on-the-varchar-data-type"></a>A. 根據 varchar 資料類型建立別名類型  
 下列範例根據系統提供的 `varchar` 資料類型建立別名資料類型。  
  
```sql  
CREATE TYPE SSN  
FROM varchar(11) NOT NULL ;  
```  
  
### <a name="b-creating-a-user-defined-type"></a>B. 建立使用者自訂類型  
 下列範例建立 `Utf8String` 類型，它參考 `utf8string` 組件中的 `utf8string` 類別。 建立該類型之前，必須先在本機資料庫中註冊 `utf8string` 組件。 以有效的描述取代 CREATE ASSEMBLY 陳述式的二進位部分。  
  
**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
```sql  
CREATE ASSEMBLY utf8string  
AUTHORIZATION [dbi]   
FROM 0x4D... ;  
GO  
CREATE TYPE Utf8String   
EXTERNAL NAME utf8string.[Microsoft.Samples.SqlServer.utf8string] ;  
GO  
```  
  
### <a name="c-creating-a-user-defined-table-type"></a>C. 建立使用者自訂資料表類型  
 下列範例會建立有兩個資料行的使用者定義資料表類型。 如需如何建立及使用資料表值參數的詳細資訊，請參閱[使用資料表值參數 &#40;Database Engine&#41;](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)。  
  
```sql  
CREATE TYPE LocationTableType AS TABLE   
    ( LocationName VARCHAR(50)  
    , CostRate INT );  
GO  
```  

### <a name="d-creating-a-user-defined-table-type-with-primary-key-and-index"></a>D. 使用主索引鍵和索引來建立使用者定義的資料表類型
以下範例會建立有三個資料行的使用者定義資料表類型，其中一個 (`Name`) 是主索引鍵，另一個 (`Price`) 則是非叢集索引。  如需如何建立及使用資料表值參數的詳細資訊，請參閱[使用資料表值參數 &#40;Database Engine&#41;](../../relational-databases/tables/use-table-valued-parameters-database-engine.md)。

```sql
CREATE TYPE InventoryItem AS TABLE
(
    [Name] NVARCHAR(50) NOT NULL,
    SupplierId BIGINT NOT NULL,
    Price DECIMAL (18, 4) NULL,
    PRIMARY KEY (
        Name
    ),
    INDEX IX_InventoryItem_Price (
        Price
    )
)
GO
```
  
## <a name="see-also"></a>另請參閱  
 [CREATE ASSEMBLY &#40;Transact-SQL&#41;](../../t-sql/statements/create-assembly-transact-sql.md)   
 [DROP TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-type-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)    
 [CLR 使用者定義類型](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)     
 [在 SQL Server 中使用使用者定義型別](../../relational-databases/clr-integration-database-objects-user-defined-types/working-with-user-defined-types-in-sql-server.md)     
