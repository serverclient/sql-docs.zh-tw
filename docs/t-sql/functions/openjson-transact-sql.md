---
description: OPENJSON (Transact-SQL)
title: OPENJSON (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/03/2020
ms.prod: sql
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- OPENJSON
- OPENJSON_TSQL
helpviewer_keywords:
- OPENJSON rowset function
- JSON, importing
- JSON, converting from
ms.assetid: 233d0877-046b-4dcc-b5da-adeb22f78531
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: chadam
monikerRange: = azuresqldb-current||= azure-sqldw-latest||>= sql-server-2016||>= sql-server-linux-2017
ms.openlocfilehash: 1c7b948b1f883d1de6c6bb6556aee25c0e859695
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98085769"
---
# <a name="openjson-transact-sql"></a>OPENJSON (Transact-SQL)

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

**OPENJSON** 是剖析 JSON 文字並將來自 JSON 輸入的物件和屬性以資料列和資料行傳回的資料表值函式。 換句話說，**OPENJSON** 提供了 JSON 文件的資料列集檢視。 您可以明確指定資料列集中的資料行，以及用來填入資料行的 JSON 屬性路徑。 因為 **OPENJSON** 會傳回一組資料列，所以您可以在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的 `FROM` 子句中使用 **OPENJSON**，其與您在其他資料表、檢視或資料表值函式中的用法相同。  
  
使用 **OPENJSON** 將 JSON 資料匯入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，或者針對無法直接取用 JSON 的應用程式或服務，將 JSON 資料轉換為關聯式格式。  
  
> [!NOTE]  
>  **OPENJSON** 函式僅適用於相容性層級 130 或更高。 如果您的資料庫相容性層級低於 130，SQL Server 將找不到且無法執行 **OPENJSON** 函式。 其他 JSON 函數適用於所有的相容性層級。
> 
> 您可以在 `sys.databases` 檢視或資料庫屬性中查看相容性層級。 您可以使用下列命令變更資料庫的相容性層級：  
> 
> `ALTER DATABASE DatabaseName SET COMPATIBILITY_LEVEL = 130`
>
> 即使是新的 Azure SQL Database，其預設的相容性層級也可能會是 120。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示")[Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
OPENJSON( jsonExpression [ , path ] )  [ <with_clause> ]

<with_clause> ::= WITH ( { colName type [ column_path ] [ AS JSON ] } [ ,...n ] )
```  

**OPENJSON** 資料表值函式會剖析作為第一個引數提供的 *jsonExpression*，並傳回包含運算式中 JSON 物件資料的一或多個資料列。 *jsonExpression* 可包含巢狀子物件。 若您想要剖析 *jsonExpression* 中的子物件，您可以為 JSON 子物件指定 **path** 參數。

### <a name="openjson"></a>openjson

![OPENJSON TVF 的語法](../../relational-databases/json/media/openjson-syntax.png "OPENJSON 語法")  

根據預設，**OPENJSON** 資料表值函式會傳回三個資料行，其中包含了索引鍵名稱、值，以及在 *jsonExpression* 中找到的每個 {索引鍵/值} 組的類型。 或者，您可以透過提供 *with_clause* 來指定 **OPENJSON** 傳回之結果集的結構描述。
  
### <a name="with_clause"></a>with_clause
  
![OPENJSON TVF 中 WITH 子句的語法](../../relational-databases/json/media/openjson-shema-syntax.png "OPENJSON WITH 語法")

*with_clause* 包含資料行的清單，其中包含 **OPENJSON** 傳回每個資料行時的類型。 根據預設，**OPENJSON** 會比對 *jsonExpression* 中的索引鍵與 *with_clause* 中的資料行名稱 (在此案例中，相符的索引鍵表示其區分大小寫)。 若資料行名稱不符合索引鍵名稱，您可以選擇性的提供 *column_path*，其為參考 *jsonExpression* 中索引鍵的 [JSON 路徑運算式](../../relational-databases/json/json-path-expressions-sql-server.md)。 

## <a name="arguments"></a>引數

### <a name="jsonexpression"></a>*jsonExpression*

為包含 JSON 文字的 Unicode 字元運算式。  
  
OPENJSON 會逐一查看陣列中的項目或 JSON 運算式中物件的屬性，並為每個項目或屬性傳回一個資料列。 下列範例會傳回以 *jsonExpression* 提供之每個物件的屬性：  

```sql
DECLARE @json NVARCHAR(2048) = N'{
   "String_value": "John",
   "DoublePrecisionFloatingPoint_value": 45,
   "DoublePrecisionFloatingPoint_value": 2.3456,
   "BooleanTrue_value": true,
   "BooleanFalse_value": false,
   "Null_value": null,
   "Array_value": ["a","r","r","a","y"],
   "Object_value": {"obj":"ect"}
}';

SELECT * FROM OpenJson(@json);
```

**結果：**

| 索引鍵                                | value                 | type |
| :--                                | :----                 | :--- |
| String_value                       | John                  | 1 |
| DoublePrecisionFloatingPoint_value | 45                    | 2 |
| DoublePrecisionFloatingPoint_value | 2.3456                | 2 |
| BooleanTrue_value                  | true                  | 3 |
| BooleanFalse_value                 | false                 | 3 |
| Null_value                         | NULL                  | 0 |
| Array_value                        | ["a","r","r","a","y"] | 4 |
| Object_value                       | {"obj":"ect"}         | 5 |
| &nbsp; | &nbsp; | &nbsp; |

- DoublePrecisionFloatingPoint_value 符合 IEEE-754 標準。

### <a name="path"></a>*path*

為選擇性的 JSON 路徑運算式，會參考 *jsonExpression* 中的物件或陣列。 **OPENJSON** 會在 JSON 文字中指定的位置搜尋，並只剖析參考的片段。 如需詳細資訊，請參閱 [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)。

在 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 及 [!INCLUDE[ssSDSfull_md](../../includes/sssdsfull-md.md)] 中，您可以將變數作為 *path* 的值提供。
  
下列範例會藉由指定 *path* 來傳回巢狀物件：  

```sql  
DECLARE @json NVARCHAR(4000) = N'{  
      "path": {  
            "to":{  
                 "sub-object":["en-GB", "en-UK","de-AT","es-AR","sr-Cyrl"]  
                 }  
              }  
 }';

SELECT [key], value
FROM OPENJSON(@json,'$.path.to."sub-object"')
```  
  
 **結果**  
  
|Key|值|  
|---------|-----------|  
|0|en-GB|  
|1|en-UK|  
|2|de-AT|  
|3|es-AR|  
|4|sr-Cyrl|  

當 **OPENJSON** 剖析 JSON 陣列時，函式會將 JSON 文字中項目的索引作為索引鍵傳回。

用來比對路徑步驟與 JSON 運算式屬性的比較會區分大小寫且為非識別定序 (即 BIN2 比較)。 

### <a name="with_clause"></a>*with_clause*

明確定義 **OPENJSON** 函式要傳回的輸出結構描述。 選擇性的 *with_clause* 可包含下列項目：

*colName* 為輸出資料行的名稱。  
  
根據預設，**OPENJSON** 會使用資料行的名稱來比對 JSON 文字中的屬性。 例如，若您在結構描述中指定資料行 *name*，OPENJSON 便會嘗試使用 JSON 文字中的 "name" 屬性來填入此資料行。 您可以使用 *column_path* 引數來覆寫此預設對應。  
  
*type*  
為輸出資料行的資料類型。  

> [!NOTE]
> 若您同時使用 **AS JSON** 選項，則資料行 *type* 必須是 `NVARCHAR(MAX)`。
  
*column_path*  
為指定要在指定資料行中傳回之屬性的 JSON 路徑。 如需詳細資訊，請參閱本主題先前的 *path* 參數描述。  
  
當輸出資料行的名稱與屬性的名稱不符時，請使用 *column_path* 來覆寫預設對應規則。  
  
用來比對路徑步驟與 JSON 運算式屬性的比較會區分大小寫且為非識別定序 (即 BIN2 比較)。  
  
如需路徑的詳細資訊，請參閱 [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)。  
  
*AS JSON*  
在資料行定義中使用 **AS JSON** 選項來指定參考的屬性包含內部 JSON 物件或陣列。 若您指定 **AS JSON** 選項，則資料行的類型必須為 NVARCHAR(MAX)。

- 若您沒有為資料行指定 **AS JSON**，則函式會從指定路徑上的指定 JSON 屬性傳回純量值 (例如：int、string、true、false)。 若路徑代表物件或陣列，且在指定路徑中找不到屬性，則函式會傳回 null (lax 模式) 或錯誤 (strict 模式)。 此行為與 **JSON_VALUE** 函式的行為相似。  
  
- 若您為資料行指定 **AS JSON**，函式會從指定路徑上的指定 JSON 屬性傳回 JSON 片段。 若路徑代表純量值，且在指定路徑中找不到屬性，則函式會傳回 null (lax 模式) 或錯誤 (strict 模式)。 此行為與 **JSON_QUERY** 函式的行為相似。  
  
> [!NOTE]  
> 若您想要從 JSON 屬性傳回巢狀 JSON 片段，您必須提供 **AS_JSON** 旗標。 若沒有此選項，且找不到屬性，則 OPENJSON 會傳回 NULL 值，而非參考的 JSON 物件或陣列，或是傳回執行階段錯誤 (strict 模式下)。  
  
例如，下列查詢會傳回並格式化陣列的項目：  
  
```sql  
DECLARE @json NVARCHAR(MAX) = N'[  
  {  
    "Order": {  
      "Number":"SO43659",  
      "Date":"2011-05-31T00:00:00"  
    },  
    "AccountNumber":"AW29825",  
    "Item": {  
      "Price":2024.9940,  
      "Quantity":1  
    }  
  },  
  {  
    "Order": {  
      "Number":"SO43661",  
      "Date":"2011-06-01T00:00:00"  
    },  
    "AccountNumber":"AW73565",  
    "Item": {  
      "Price":2024.9940,  
      "Quantity":3  
    }  
  }
]'  
   
SELECT *
FROM OPENJSON ( @json )  
WITH (   
              Number   VARCHAR(200)   '$.Order.Number',  
              Date     DATETIME       '$.Order.Date',  
              Customer VARCHAR(200)   '$.AccountNumber',  
              Quantity INT            '$.Item.Quantity',  
              [Order]  NVARCHAR(MAX)  AS JSON  
 )
```  
  
**結果**
  
|Number|Date|客戶|數量|單|  
|------------|----------|--------------|--------------|-----------|  
|SO43659|2011-05-31T00:00:00|AW29825|1|{"Number":"SO43659","Date":"2011-05-31T00:00:00"}|  
|SO43661|2011-06-01T00:00:00|AW73565|3|{"Number":"SO43661","Date":"2011-06-01T00:00:00"}|  
  
## <a name="return-value"></a>傳回值
OPENJSON 函式傳回的資料行取決於 WITH 選項。  
  
1. 當您使用預設結構描述呼叫 OPENJSON 時 (即您沒有在 WITH 子句中指定明確的結構描述時)，函式會傳回具有下列資料行的資料表：  
    1.  **索引鍵**。 包含指定屬性名稱或指定陣列中項目索引的 nvarchar(4000) 值。 索引鍵資料行具有 BIN2 定序。  
    2.  **值**。 包含屬性值的 nvarchar(max) 值。 「值」資料行會從 *jsonExpression* 繼承其定序。
    3.  **類型**。 包含值類型的 int 值。 只有在您使用預設結構描述使用 OPENJSON 時，才會傳回 **類型** 資料行。 「類型」資料行有下列其中一個值：  
  
        |「類型」資料行的值|JSON 資料類型|  
        |------------------------------|--------------------|  
        |0|null|  
        |1|字串|  
        |2|number|  
        |3|true/false|  
        |4|array|  
        |5|物件 (object)|  
  
     只會傳回第一層的屬性。 若 JSON 文字的格式不正確，陳述式便會失敗。  

2. 當您呼叫 OPENJSON 且在 WITH 子句中指定明確的結構描述時，函式會傳回使用您在 WITH 子句中定義之結構描述的資料表。

> [!NOTE]  
> **Key**、**Value** 和 **Type** 資料行只會在您搭配預設結構描述使用 OPENJSON 時傳回，且無法搭配明確結構描述使用。

## <a name="remarks"></a>備註  

**OPENJSON** 的第二個引數或 *with_clause* 中的 *json_path* 開頭可為 **lax** 或 **strict** 關鍵字。

- 在 **lax** 模式中，**OPENJSON** 不會在指定路徑上找不到物件或值時引發錯誤。 若找不到路徑，**OPENJSON** 會傳回空的結果集或 NULL 值。
- 在 **strict** 模式中，**OPENJSON** 會在找不到路徑時傳回錯誤。

此頁面上的一些範例會明確指定路徑模式 (lax 或 strict)。 路徑模式為選擇性。 若您沒有明確指定路徑模式，則預設值為 lax 模式。 如需路徑模式與路徑運算式的詳細資訊，請參閱 [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)。    

*with_clause* 中的資料行名稱會與 JSON 文字中的索引鍵進行比對。 若您指定資料行名稱 `[Address.Country]`，它便會與索引鍵 `Address.Country` 進行比對。 若您想要參考 `Address` 物件中的 `Country` 巢狀索引鍵，您必須在資料行路徑中指定 `$.Address.Country` 路徑。

*json_path* 可包含使用英數字元的索引鍵。 若索引鍵中有特殊字元，請在 *json_path* 中使用雙引號來逸出索引鍵名稱。 例如，`$."my key $1".regularKey."key with . dot"` 會與下列 JSON 文字中的值 1 相符：

```json
{
  "my key $1": {
    "regularKey":{
      "key with . dot": 1
    }
  }
}
```  

## <a name="examples"></a>範例  
  
### <a name="example-1---convert-a-json-array-to-a-temporary-table"></a>範例 1 - 將 JSON 陣列轉換成暫存資料表

下列範例以一個數字 JSON 陣列提供識別碼清單。 查詢會將 JSON 陣列轉換成識別碼資料表，並使用指定的識別碼篩選所有產品。  
  
```sql  
DECLARE @pSearchOptions NVARCHAR(4000) = N'[1,2,3,4]'

SELECT *
FROM products
INNER JOIN OPENJSON(@pSearchOptions) AS productTypes
 ON product.productTypeID = productTypes.value
```  
  
這個查詢相當於下列範例。 然而，在以下的範例中，您必須在查詢中嵌入數字，而非將它們作為參數傳遞。  
  
```sql  
SELECT *
FROM products
WHERE product.productTypeID IN (1,2,3,4)
```  
  
### <a name="example-2---merge-properties-from-two-json-objects"></a>範例 2 - 合併來自兩個 JSON 物件的屬性

下列範例會選取兩個 JSON 物件的集合聯集。 兩個物件具有重複的 *name* 屬性。 範例使用索引鍵值來從結果中排除重複的資料列。  
  
```sql  
DECLARE @json1 NVARCHAR(MAX),@json2 NVARCHAR(MAX)

SET @json1=N'{"name": "John", "surname":"Doe"}'

SET @json2=N'{"name": "John", "age":45}'

SELECT *
FROM OPENJSON(@json1)
UNION ALL
SELECT *
FROM OPENJSON(@json2)
WHERE [key] NOT IN (SELECT [key] FROM OPENJSON(@json1))
```  
  
### <a name="example-3---join-rows-with-json-data-stored-in-table-cells-using-cross-apply"></a>範例 3 - 使用 CROSS APPLY 來使用儲存在表格儲存格中的 JSON 資料聯結資料列

在下列範例中，`SalesOrderHeader` 資料表有一個 `SalesReason` 文字資料行，其中包含以 JSON 格式儲存的 `SalesOrderReasons` 陣列。 `SalesOrderReasons` 物件包含如 *Quality* 和 *Manufacturer* 等屬性。 範例會建立將每個銷售訂單聯結到相關銷售原因的報表。 OPENJSON 運算子會展開銷售原因的 JSON 陣列，彷彿原因是儲存在個別的子資料表中一樣。 CROSS APPLY 運算子會聯結每個銷售訂單資料列與 OPENJSON 資料表值函式所傳回的資料列。  
  
```sql  
SELECT SalesOrderID,OrderDate,value AS Reason
FROM Sales.SalesOrderHeader
CROSS APPLY OPENJSON(SalesReasons)
```  
  
> [!TIP] 
> 當您必須展開儲存在個別欄位中的 JSON 陣列，並將它們與其父資料列聯結時，您通常會使用 [!INCLUDE[tsql](../../includes/tsql-md.md)]CROSS APPLY 運算子。 如需 CROSS APPLY 的詳細資訊，請參閱 [&#40;Transact-SQL&#41;](../../t-sql/queries/from-transact-sql.md)。  
  
相同的查詢可使用 `OPENJSON` 搭配明確定義要傳回之資料列的結構描述來重寫：  
  
```sql  
SELECT SalesOrderID, OrderDate, value AS Reason  
FROM Sales.SalesOrderHeader  
     CROSS APPLY OPENJSON (SalesReasons) WITH (value NVARCHAR(100) '$')
```  
  
在此範例中，`$` 路徑會參考陣列中的每個項目。 若您想要明確轉換傳回的值，您可以使用這種類型的查詢。  
  
### <a name="example-4---combine-relational-rows-and-json-elements-with-cross-apply"></a>範例 4 - 使用 CROSS APPLY 合併關聯式資料列和 JSON 項目

下列查詢會將關聯式資料列和 JSON 項目合併為在下列表格中顯示的結果。  
  
```sql  
SELECT store.title, location.street, location.lat, location.long  
FROM store  
CROSS APPLY OPENJSON(store.jsonCol, 'lax $.location')   
     WITH (street VARCHAR(500) ,  postcode VARCHAR(500) '$.postcode' ,  
     lon int '$.geo.longitude', lat int '$.geo.latitude')  
     AS location
```  
  
**結果**
  
|title|街道|postcode|lon|lat|  
|-----------|------------|--------------|---------|---------|  
|Whole Food Markets|17991 Redmond Way|WA  98052|47.666124|-122.10155|  
|Sears|148th Ave NE|WA  98052|47.63024|-122.141246,17|  
  
### <a name="example-5---import-json-data-into-sql-server"></a>範例 5 - 將 JSON 資料匯入 SQL Server

以下範例會將整個 JSON 物件載入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料表。  
  
```sql  
DECLARE @json NVARCHAR(max)  = N'{  
  "id" : 2,  
  "firstName": "John",  
  "lastName": "Smith",  
  "isAlive": true,  
  "age": 25,  
  "dateOfBirth": "2015-03-25T12:00:00",  
  "spouse": null  
  }';  
   
  INSERT INTO Person  
  SELECT *   
  FROM OPENJSON(@json)  
  WITH (id INT,  
        firstName NVARCHAR(50), lastName NVARCHAR(50),   
        isAlive BIT, age INT,  
        dateOfBirth DATETIME, spouse NVARCHAR(50))
```  

### <a name="example-6---simple-example-with-json-content"></a>範例 6 - 使用 JSON 內容的簡單範例

```sql
--simple cross apply example
DECLARE @JSON NVARCHAR(MAX) = N'[
{
"OrderNumber":"SO43659",
"OrderDate":"2011-05-31T00:00:00",
"AccountNumber":"AW29825",
"ItemPrice":2024.9940,
"ItemQuantity":1
},
{
"OrderNumber":"SO43661",
"OrderDate":"2011-06-01T00:00:00",
"AccountNumber":"AW73565",
"ItemPrice":2024.9940,
"ItemQuantity":3
}
]'

SELECT root.[key] AS [Order],TheValues.[key], TheValues.[value]
FROM OPENJSON ( @JSON ) AS root
CROSS APPLY OPENJSON ( root.value) AS TheValues
```

## <a name="see-also"></a>另請參閱

- [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)   
- [使用 OPENJSON 將 JSON 資料轉換成資料列和資料行 &#40;SQL Server&#41;](../../relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server.md)   
- [搭配使用 OPENJSON 與預設結構描述 &#40;SQL Server&#41;](../../relational-databases/json/use-openjson-with-the-default-schema-sql-server.md)   
- [搭配使用明確結構描述與 OPENJSON &#40;SQL Server&#41;](../../relational-databases/json/use-openjson-with-an-explicit-schema-sql-server.md)  
  
