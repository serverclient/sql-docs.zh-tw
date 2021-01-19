---
description: ALTER TABLE column_definition (Transact-SQL)
title: column_definition (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/24/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- column_definition
- column_definition_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- column_definition
- ALTER TABLE statement
- column properties [SQL Server]
- column definitions [SQL Server]
ms.assetid: a1742649-ca29-4d9b-9975-661cdbf18f78
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 001417f19035ab9a304f2af3ae42207bd7600fb1
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170830"
---
# <a name="alter-table-column_definition-transact-sql"></a>ALTER TABLE column_definition (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi](../../includes/applies-to-version/sql-asdb-asdbmi.md)]

  使用 [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md) 指定新增至資料表之資料行的屬性。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
column_name <data_type>  
[ FILESTREAM ]  
[ COLLATE collation_name ]   
[ NULL | NOT NULL ]  
[   
    [ CONSTRAINT constraint_name ] DEFAULT constant_expression [ WITH VALUES ]   
    | IDENTITY [ ( seed , increment ) ] [ NOT FOR REPLICATION ]   
]  
[ ROWGUIDCOL ]   
[ SPARSE ]   
[ ENCRYPTED WITH  
  ( COLUMN_ENCRYPTION_KEY = key_name ,  
      ENCRYPTION_TYPE = { DETERMINISTIC | RANDOMIZED } ,   
      ALGORITHM =  'AEAD_AES_256_CBC_HMAC_SHA_256'   
  ) ]  
[ MASKED WITH ( FUNCTION = ' mask_function ') ]  
[ <column_constraint> [ ...n ] ]  
  
<data type> ::=   
[ type_schema_name . ] type_name   
    [ ( precision [ , scale ] | max |   
        [ { CONTENT | DOCUMENT } ] xml_schema_collection ) ]   
  
<column_constraint> ::=   
[ CONSTRAINT constraint_name ]   
{     { PRIMARY KEY | UNIQUE }   
        [ CLUSTERED | NONCLUSTERED ]   
        [   
            WITH FILLFACTOR = fillfactor    
          | WITH ( < index_option > [ , ...n ] )   
        ]   
        [ ON { partition_scheme_name ( partition_column_name )   
            | filegroup | "default" } ]  
  | [ FOREIGN KEY ]   
        REFERENCES [ schema_name . ] referenced_table_name [ ( ref_column ) ]   
        [ ON DELETE { NO ACTION | CASCADE | SET NULL | SET DEFAULT } ]   
        [ ON UPDATE { NO ACTION | CASCADE | SET NULL | SET DEFAULT } ]   
        [ NOT FOR REPLICATION ]   
  | CHECK [ NOT FOR REPLICATION ] ( logical_expression )   
}  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *column_name*  
 要改變、加入或卸除的資料欄名稱。 *column_name* 可由 1 到 128 個字元組成。 如果是以 timestamp 資料類型建立的新資料行，可以省略 *column_name*。 如果沒有為 **timestamp** 資料類型資料行指定任何 *column_name*，則會使用 **timestamp** 這個名稱。  
  
 [ _type_schema_name_ **.** ] *type_name*  
 這是加入的資料行之資料類型及它所屬的結構描述。  
  
 *type_name* 可以是：  
  
-   [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統資料類型。  
  
-   基於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統資料類型的別名資料類型。 別名資料類型必須先利用 CREATE TYPE 建立，之後，才能在資料表定義中使用它們。  
  
-   [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 使用者定義型別及其所屬的結構描述。 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 使用者自訂類型必須先利用 CREATE TYPE 建立，之後，才能在資料表定義中使用它。  
  
 若未指定 *type_schema_name*，則 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會以下列順序來參考 *type_name*：  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統資料類型。  
  
-   目前資料庫中之目前使用者的預設結構描述。  
  
-   目前資料庫中的 **dbo** 結構描述。  
  
*有效位數*  
 這是指定之資料類型的有效位數。 如需有關有效位數值的詳細資訊，請參閱[有效位數、小數位數和長度 &#40;Transact-SQL&#41;](../../t-sql/data-types/precision-scale-and-length-transact-sql.md)。  
  
*scale*  
 這是指定資料類型的小數位數。 如需有關有效小數位數值的詳細資訊，請參閱[有效位數、小數位數和長度 &#40;Transact-SQL&#41;](../../t-sql/data-types/precision-scale-and-length-transact-sql.md)。  
  
**max**  
 僅適用於 **varchar**、**nvarchar**，和 **varbinary** 資料類型。 這些資料類型用來儲存 2^31 位元組的字元和二進位資料，以及 2^30 位元組的 Unicode 資料。  
  
**CONTENT**  
 指定 *column_name* 中 **xml** 資料類型的每個執行個體都可以由多個最上層項目組成。 CONTENT 只適用於 **xml** 資料類型，而且只有在同時指定 *xml_schema_collection* 時，才能指定。 如果未指定這個項目，CONTENT 便是預設行為。  
  
DOCUMENT  
 指定 *column_name* 中 **xml** 資料類型的每個執行個體都只能由一個最上層項目組成。 DOCUMENT 只適用於 **xml** 資料類型，而且只有在同時指定 *xml_schema_collection* 時，才能指定。  
  
 *xml_schema_collection*  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 只適用於 **xml** 資料類型，以便將 XML 結構描述集合與類型產生關聯。 在結構描述中鍵入 **xml** 資料行之前，必須先使用 [CREATE XML SCHEMA COLLECTION](../../t-sql/statements/create-xml-schema-collection-transact-sql.md)，在資料庫中建立結構描述。  
  
FILESTREAM  
 選擇性的針對具有 **varbinary(max)** 之 *type_name* 的資料行指定 FILESTREAM 儲存屬性。  
  
 當針對資料行指定 FILESTREAM 時，資料表也必須要有 **uniqueidentifier** 資料類型的資料行 (此類型具有 ROWGUIDCOL 屬性)。 這個資料行不能允許 null 值，且必須具有 UNIQUE 或 PRIMARY KEY 單一資料行條件約束。 資料行的 GUID 值必須在插入資料時由應用程式提供，或是由使用 NEWID () 函數的 DEFAULT 條件約束所提供。  
  
 ROWGUIDCOL 資料行無法卸除，而且當資料表有定義 FILESTREAM 資料行時，無法變更相關的條件約束。 只有當最後一個 FILESTREAM 資料行卸除之後，才可卸除 ROWGUIDCOL 資料行。  
  
 當有針對資料行指定 FILESTREAM 儲存屬性時，該資料行的所有值都會儲存在檔案系統的 FILESTREAM 資料容器內。  
  
 如需示範如何使用資料行定義的範例，請參閱 [FILESTREAM &#40;SQL Server&#41;](../../relational-databases/blob/filestream-sql-server.md)。  
  
COLLATE *collation_name*  
 指定資料行的定序。 若未指定，就會將資料庫的預設定序指派給資料行。 定序名稱可以是 Windows 定序名稱，也可以是 SQL 定序名稱。 如需清單和詳細資訊，請參閱 [Windows 定序名稱 &#40;Transact-SQL&#41;](../../t-sql/statements/windows-collation-name-transact-sql.md) 和 [SQL Server 定序名稱 &#40;Transact-SQL&#41;](../../t-sql/statements/sql-server-collation-name-transact-sql.md)。  
  
 COLLATE 子句只能用來指定 **char**、**varchar**、**nchar** 與 **nvarchar** 資料類型之資料行的定序。  
  
 如需有關 COLLATE 子句的詳細資訊，請參閱 [COLLATE &#40;Transact-SQL&#41;](~/t-sql/statements/collations.md)。  
  
 NULL | NOT NULL  
 判斷資料行中是否允許 Null 值。 嚴格來說，NULL 並不算是條件約束，但是您可以如同指定 NOT NULL 一樣加以指定。  
  
[ CONSTRAINT *constraint_name* ]  
 指定開始定義 DEFAULT 值。 若要維護與舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的相容性，您可以將條件約束名稱指派給 DEFAULT。 *constraint_name* 必須遵照 [identifiers](../../relational-databases/databases/database-identifiers.md) 的規則，不過，名稱開頭不能是數字記號 (#)。 如果未指定 *constraint_name*，便會將系統產生的名稱指派給 DEFAULT 定義。  
  
DEFAULT  
 這是指定資料行預設值的關鍵字。 您可以利用 DEFAULT 定義來提供現有資料列之新資料行的值。 **timestamp** 資料行或含 IDENTITY 屬性的資料行不能套用 DEFAULT 定義。 如果使用者定義型別資料行指定了預設值，類型必須支援將 *constant_expression* 隱含轉換成使用者定義型別。  
  
*constant_expression*  
 這是用來作為資料行預設值的常值、NULL 或系統函數。 如果結合定義為 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 使用者定義型別的資料行來使用，類型的實作必須支援將 *constant_expression* 隱含轉換成使用者定義型別。  
  
WITH VALUES   
 當新增資料行和 (AND) DEFAULT 條件約束時，如果資料行允許使用 WITH VALUES NULLS，則對於現有的資料列，會將新資料行的值設定為 DEFAULT *constant_expression* 中提供的值。 如果新增的資料行不允許 NULLS，則對於現有的資料列，會一律將該資料行的值設定為 DEFAULT *constant expression* 中提供的值。 從 SQL Server 2012 中開始，這可以是中繼資料作業 [adding-not-null-columns-as-an-online-operation](alter-table-transact-sql.md#adding-not-null-columns-as-an-online-operation)。
如果在相關資料行沒有一起新增的情況下使用此方法，則它沒有作用。
 
 指定將 DEFAULT *constant_expression* 中所提供的值，儲存在要新增至現有資料列的新資料行。 如果加入的資料行允許 Null 值，且指定了 WITH VALUES，就會將預設值儲存在加入現有資料列的新資料行中。 如果允許 Null 的資料行沒有指定 WITH VALUES，就會將 NULL 值儲存在現有資料列的新資料行中。 如果新資料行不允許 NULL，就會將預設值儲存在新資料列中，不論是否指定了 WITH VALUES，都是如此。  
  
IDENTITY  
 指定新資料行是識別欄位。 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會提供資料行的唯一累加值。 將識別碼資料行加入現有資料表時，識別碼便會加入至資料表中含有初始值和遞增值的現有資料列。 但是，無法保證資料列的更新順序。 任何加入的新資料列也都會產生識別碼。  
  
 識別欄位通常用來結合 PRIMARY KEY 條件約束一起使用，當做資料表的唯一資料列識別碼。 可以將 IDENTITY 屬性指派給 **tinyint**、**smallint** **int** **bigint** **decimal(p,0)** 或 **numeric(p,0)** 資料行。 每份資料表都只能建立一個識別欄位。 DEFAULT 關鍵字和繫結的預設值無法搭配識別欄位來使用。 您必須同時指定種子和遞增，或同時不指定這兩者。 如果同時不指定這兩者，預設值便是 (1,1)。  
  
> [!NOTE]  
>  您不能修改現有的資料表資料行來加入 IDENTITY 屬性。  
  
 不支援將識別欄位加入已發行的資料表中，因為當資料行複寫到訂閱者時，它可能造成非聚合。 簽發者端識別欄位中的值，隨著受影響的資料表之資料列的實際儲存順序而不同。 訂閱者端可能會用不同的方式來儲存這些資料列；因此，相同資料列可能會有不同的識別欄位值。  
  
 若要允許明確插入值來停用資料行的 IDENTITY 屬性，請使用 [SET IDENTITY_INSERT](../../t-sql/statements/set-identity-insert-transact-sql.md)。  
  
*seed*  
 這是載入資料表的第一個資料列所用的值。  
  
*increment*  
 這是加入先前載入的資料列之識別值的累加值。  
  
NOT FOR REPLICATION  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 可以指定給 IDENTITY 屬性。 如果 IDENTITY 屬性指定了這個子句，當複寫代理程式執行插入作業時，值不會在識別欄位中累加。  
  
ROWGUIDCOL  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定資料行是一個資料列全域唯一識別碼資料行。 ROWGUIDCOL 只能指派給 **uniqueidentifier** 資料行，且每個資料表只能有一個 **uniqueidentifier** 能指定為 ROWGUIDCOL 資料行。 ROWGUIDCOL 不能指派給使用者自訂資料類型的資料行。  
  
 ROWGUIDCOL 不會強制執行資料行所儲存之值的唯一性。 另外，ROWGUIDCOL 也不會自動產生插入資料表之新資料列的值。 若要產生每個資料行的唯一值，請在 INSERT 陳述式上使用 NEWID 函數，或將 NEWID 函數指定為資料行的預設值。 如需詳細資訊，請參閱 [NEWID &#40;TRANSACT-SQL&#41;](../../t-sql/functions/newid-transact-sql.md) 和 [INSERT &#40;TRANSACT-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)。  
  
SPARSE  
 指出此資料行是疏鬆資料行。 疏鬆資料行的儲存體會針對 Null 值最佳化。 疏鬆資料行無法指定為 NOT NULL。 如需有關疏鬆資料行的其他限制和詳細資訊，請參閱[使用疏鬆資料行](../../relational-databases/tables/use-sparse-columns.md)。  
  
\<column_constraint>  
 如需有關資料行條件約束引數的定義，請參閱 [column_constraint &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-column-constraint-transact-sql.md)。  
  
 ENCRYPTED WITH  
 指定使用 [Always Encrypted](../../relational-databases/security/encryption/always-encrypted-database-engine.md) 功能來加密資料行。  
  
 COLUMN_ENCRYPTION_KEY = *key_name*  
 指定資料行加密金鑰。 如需詳細資訊，請參閱 [CREATE COLUMN ENCRYPTION KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-column-encryption-key-transact-sql.md)。  
  
ENCRYPTION_TYPE = { DETERMINISTIC | RANDOMIZED }  
 **確定性加密** 使用的方法一律會針對任何指定純文字值產生相同加密值。 使用確定性加密時，可允許使用相等比較來進行搜尋、使用以加密值為基礎的相等聯結來群組和聯結表格，但也可讓未經授權的使用者檢查加密資料行中的模式，以此猜測加密值的相關資訊。 只有這兩個資料行都是使用相同的資料行加密金鑰時，才有可能將確定性加密資料行上的兩個資料表聯結起來。 確定性加密必須針對字元資料行使用 binary2 排序次序的資料行定序。  
  
 **隨機加密** 使用的方法會以更難預測的方式來加密資料。 隨機化加密更為安全，但會防止任何對加密資料行的計算和編製索引，除非您的 SQL Server 執行個體支援[具有安全記憶體保護區的 Always Encrypted](../../relational-databases/security/encryption/always-encrypted-enclaves.md)。
  
 如果您使用 Always Encrypted (無安全記憶體保護區)，則請使用要使用參數或分組參數所搜尋的決定性加密 (例如政府識別碼)。 針對像是信用卡號碼這類未與其他記錄組成群組或被用來聯結資料表，也不會被搜尋 (因為您是使用其他資料行，例如交易號碼，來尋找哪一個資料列包含想找的加密資料行) 的資料，請使用隨機加密。  

 如果您使用具有安全記憶體保護區的 Always Encrypted，則隨機化加密是建議的加密類型。  
  
 資料行必須為合格的資料類型。  
  
ALGORITHM  
**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本、[!INCLUDE[ssSDS](../../includes/sssds-md.md)]。  
必須為 **'AEAD_AES_256_CBC_HMAC_SHA_256'** 。  
  
 如需包括功能條件約束的詳細資訊，請參閱[永遠加密 &#40;Database Engine&#41;](../../relational-databases/security/encryption/always-encrypted-database-engine.md)。  
  
   
ADD MASKED WITH ( FUNCTION = ' *mask_function* ')  
 **適用於**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。  
  
 指定動態資料遮罩。 *mask_function* 是遮罩函式的名稱並具備適當的參數。 下列是可以使用的函式：  
  
-   default()  
  
-   email()  
  
-   partial()  
  
-   random()  
  
 如需函式參數，請參閱[動態資料遮罩](../../relational-databases/security/dynamic-data-masking.md)。  
  
## <a name="remarks"></a>備註  
 如果新增的資料行有 **uniqueidentifier** 資料類型，您可以使用預設值來定義它。預設值會使用 NEWID() 函式，在資料表中每個現有資料列的新資料行內提供唯一識別碼值。  
  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 不會強制在資料行定義中指定 DEFAULT、IDENTITY、ROWGUIDCOL 或資料行條件約束的順序。  
  
 如果加入資料行會造成資料列大小超過 8060 位元組，則 ALTER TABLE 陳述式會失敗。  
  
## <a name="examples"></a>範例  
 如需範例，請參閱 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)。  
  
## <a name="see-also"></a>另請參閱  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)  
  
