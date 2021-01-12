---
description: JSON_MODIFY (Transact-SQL)
title: JSON_MODIFY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/03/2020
ms.prod: sql
ms.technology: t-sql
ms.topic: language-reference
ms.assetid: 96bc8255-a037-4907-aec4-1a9c30814651
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: chadam
monikerRange: = azuresqldb-current||= azure-sqldw-latest||>= sql-server-2016||>= sql-server-linux-2017
ms.openlocfilehash: 547a304b4f5a9b6069d023a751f73d99e8456bbc
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092223"
---
# <a name="json_modify-transact-sql"></a>JSON_MODIFY (Transact-SQL)

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

  更新 JSON 字串中的屬性值，並傳回更新後的 JSON 字串。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
JSON_MODIFY ( expression , path , newValue )  
```  
  
## <a name="arguments"></a>引數

 *expression*  
 運算式。 通常為變數的名稱或包含 JSON 文字的資料行。  
  
 **JSON_MODIFY** 會在 *expression* 未包含有效的 JSON 時傳回錯誤。  
  
 *path*  
 指定要更新之屬性的 JSON 路徑運算式。

 *path* 的語法如下：  
  
 `[append] [ lax | strict ] $.<json path>`  
  
- *append*  
    選擇性的修飾詞，能指定新的值應附加到 *\<json path>* 所參考的陣列。  
  
- *lax*  
    指定 *\<json path>* 所參考的屬性不一定要存在。 若屬性不存在，JSON_MODIFY 便會嘗試在指定的路徑插入新值。 若屬性無法在路徑上插入，插入可能會失敗。 若您未指定 *lax* 或 *strict*，則預設模式為 *lax*。  
  
- *strict*  
    指定 *\<json path>* 所參考的屬性必須存在於 JSON 運算式中。 若屬性不存在，JSON_MODIFY 會傳回錯誤。  
  
- *\<json path>*  
    指定要更新之屬性的路徑。 如需詳細資訊，請參閱 [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)。  
  
在 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 及 [!INCLUDE[ssSDSfull_md](../../includes/sssdsfull-md.md)] 中，您可以將變數作為 *path* 的值提供。

若 **path** 的格式無效，*JSON_MODIFY* 便會傳回錯誤。  
  
 *newValue*  
 *path* 指定之屬性的新值。  
 新的值必須是 [n]varchar 或 text。
  
 在 lax 模式中，若新值為 NULL，則 JSON_MODIFY 會刪除指定的索引鍵。  
  
JSON_MODIFY 會逸出所有類型為 NVARCHAR 或 VARCHAR 新值中的特殊字元。 若文字值為 FOR JSON、JSON_QUERY 或 JSON_MODIFY 所產生之格式正確的 JSON，則文字值便不會逸出。  
  
## <a name="return-value"></a>傳回值

 將 *expression* 的更新值以格式正確的 JSON 文字傳回。  
  
## <a name="remarks"></a>備註

 JSON_MODIFY 函式讓您可以更新現有屬性的值、插入新的索引鍵/值組，或根據模式與提供值的組合來刪除索引鍵。  
  
 下列表格會比較 lax 模式與 strict 模式中 **JSON_MODIFY** 的行為。 如需選擇性路徑模式規格 (lax 或 strict) 的詳細資訊，請參閱 [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)。  
  
|現有的值|路徑存在|Lax 模式|Strict 模式|  
|--------------------|-----------------|--------------|-----------------|  
|非 NULL|是|更新現有值。|更新現有值。|  
|非 NULL|否|嘗試在指定的路徑上建立新的索引鍵/值組。<br /><br /> 這可能會失敗。 例如，若您指定路徑為 `$.user.setting.theme`，若 `$.user` 或 `$.user.settings` 物件不存在，或是設定為陣列或純量值，則 JSON_MODIFY 便不會建立 `theme` 索引鍵。|錯誤 - INVALID_PROPERTY|  
|NULL|是|刪除現有屬性。|將現有值設定為 null。|  
|NULL|否|不進行動作。 第一個引數會作為結果傳回。|錯誤 - INVALID_PROPERTY|  
  
 在 lax 模式中，JSON_MODIFY 會嘗試建立新的索引鍵/值組，但在某些案例下可能會失敗。  
  
## <a name="examples"></a>範例  
  
### <a name="example---basic-operations"></a>範例 - 基本作業

 下列範例示範可使用 JSON 文字進行的基本作業。  
  
 **查詢**
  
```sql

DECLARE @info NVARCHAR(100)='{"name":"John","skills":["C#","SQL"]}'

PRINT @info

-- Update name  

SET @info=JSON_MODIFY(@info,'$.name','Mike')

PRINT @info

-- Insert surname  

SET @info=JSON_MODIFY(@info,'$.surname','Smith')

PRINT @info

-- Delete name  

SET @info=JSON_MODIFY(@info,'$.name',NULL)

PRINT @info

-- Add skill  

SET @info=JSON_MODIFY(@info,'append $.skills','Azure')

PRINT @info
```  
  
 **結果**
  
```json  
{
    "name": "John",
    "skills": ["C#", "SQL"]
} {
    "name": "Mike",
    "skills": ["C#", "SQL"]
} {
    "name": "Mike",
    "skills": ["C#", "SQL"],
    "surname": "Smith"
} {
    "skills": ["C#", "SQL"],
    "surname": "Smith"
} {
    "skills": ["C#", "SQL", "Azure"],
    "surname": "Smith"
}
```  
  
### <a name="example---multiple-updates"></a>範例 - 多個更新

 使用 JSON_MODIFY，您可以僅更新一個屬性。 若您需要進行多個更新，您可以使用多個 JSON_MODIFY 呼叫。  
  
 **查詢**
  
```sql
DECLARE @info NVARCHAR(100)='{"name":"John","skills":["C#","SQL"]}'

PRINT @info

-- Multiple updates  

SET @info=JSON_MODIFY(JSON_MODIFY(JSON_MODIFY(@info,'$.name','Mike'),'$.surname','Smith'),'append $.skills','Azure')

PRINT @info
```  
  
 **結果**
  
```json  
{
    "name": "John",
    "skills": ["C#", "SQL"]
} {
    "name": "Mike",
    "skills": ["C#", "SQL", "Azure"],
    "surname": "Smith"
}
```  
  
### <a name="example---rename-a-key"></a>範例 - 重新命名索引鍵  
 下列範例示範如何使用 JSON_MODIFY 函式重新命名 JSON 文字中的屬性。 首先，您可以使用現有屬性的值，並將其插入為新的索引鍵/值組。 然後您便可以透過將舊屬性的值設為 NULL 來刪除舊索引鍵。  
  
 **查詢**
  
```sql  
DECLARE @product NVARCHAR(100)='{"price":49.99}'

PRINT @product

-- Rename property  

SET @product=
 JSON_MODIFY(
  JSON_MODIFY(@product,'$.Price',CAST(JSON_VALUE(@product,'$.price') AS NUMERIC(4,2))),
  '$.price',
  NULL
 )

PRINT @product
```  
  
 **結果**
  
```json  
{
    "price": 49.99
} {
    "Price": 49.99
}
```  
  
 若您沒有將新的值轉換成數值類型，JSON_MODIFY 便會將其當作文字處理，並用雙引號括住。  
  
### <a name="example---increment-a-value"></a>範例 - 遞增值

 下列範例示範如何使用 JSON_MODIFY 函式遞增 JSON 文字中的屬性。 首先，您可以使用現有屬性的值，並將其插入為新的索引鍵/值組。 然後您便可以透過將舊屬性的值設為 NULL 來刪除舊索引鍵。  
  
 **查詢**
  
```sql  
DECLARE @stats NVARCHAR(100)='{"click_count": 173}'

PRINT @stats

-- Increment value  

SET @stats=JSON_MODIFY(@stats,'$.click_count',
 CAST(JSON_VALUE(@stats,'$.click_count') AS INT)+1)

PRINT @stats
```  
  
 **結果**
  
```json  
{
    "click_count": 173
} {
    "click_count": 174
}
```  
  
### <a name="example---modify-a-json-object"></a>範例 - 修改 JSON 物件

 JSON_MODIFY 會將 *newValue* 引數以純文字來處理，即使它包含格式正確的 JSON 文字。 因此，函式的 JSON 輸出會由雙引號括住，並且所有的特殊字元都會遭到逸出，如下列範例中所示。  
  
 **查詢**  
  
```sql
DECLARE @info NVARCHAR(100)='{"name":"John","skills":["C#","SQL"]}'

PRINT @info

-- Update skills array

SET @info=JSON_MODIFY(@info,'$.skills','["C#","T-SQL","Azure"]')

PRINT @info
```  
  
 **結果**
  
```json  
{
    "name": "John",
    "skills": ["C#", "SQL"]
} {
    "name": "John",
    "skills": "["C#","T-SQL","Azure"]"
}
```  
  
 若要避免自動逸出，請透過使用 JSON_QUERY 函式來提供 *newValue*。 JSON_MODIFY 知道由 JSON_MODIFY 傳回的值是格式正確的 JSON，因此不會逸出該值。  
  
 **查詢**  
  
```sql
DECLARE @info NVARCHAR(100)='{"name":"John","skills":["C#","SQL"]}'

PRINT @info

-- Update skills array  

SET @info=JSON_MODIFY(@info,'$.skills',JSON_QUERY('["C#","T-SQL","Azure"]'))

PRINT @info
```  
  
 **結果**
  
```json  
{
    "name": "John",
    "skills": ["C#", "SQL"]
} {
    "name": "John",
    "skills": ["C#", "T-SQL", "Azure"]
}
```  
  
### <a name="example---update-a-json-column"></a>範例 - 更新 JSON 資料行

 下列範例會更新包含 JSON 之資料表資料行中屬性的值。  
  
```sql  
UPDATE Employee
SET jsonCol=JSON_MODIFY(jsonCol,'$.info.address.town','London')
WHERE EmployeeID=17
```  
  
## <a name="see-also"></a>另請參閱

- [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)   
- [JSON 資料 &#40;SQL Server&#41;](../../relational-databases/json/json-data-sql-server.md)  
  
