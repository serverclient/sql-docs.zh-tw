---
description: JSON_VALUE (Transact-SQL)
title: JSON_VALUE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/03/2020
ms.prod: sql
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- JSON_VALUE
- JSON_VALUE_TSQL
helpviewer_keywords:
- JSON_VALUE function
- JSON, extracting
- JSON, querying
ms.assetid: cd016e14-11eb-4eaf-bf05-c7cfcc820a10
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: chadam
monikerRange: = azuresqldb-current||= azure-sqldw-latest||>= sql-server-2016||>= sql-server-linux-2017
ms.openlocfilehash: 9c6571a5938c503b99378003323168a695de58a9
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98086805"
---
# <a name="json_value-transact-sql"></a>JSON_VALUE (Transact-SQL)

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

 從 JSON 字串擷取純量值。  
  
 若要從 JSON 字串而非純量值擷取物件或陣列，請參閱 [JSON_QUERY &#40;Transact-SQL&#41;](../../t-sql/functions/json-query-transact-sql.md)。 如需 **JSON_VALUE** 及 **JSON_QUERY** 之間的差異資訊，請參閱 [比較 JSON_VALUE 與 JSON_QUERY](../../relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server.md#JSONCompare)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
JSON_VALUE ( expression , path )  
```  
  
## <a name="arguments"></a>引數

 *expression*  
 運算式。 通常為變數的名稱或包含 JSON 文字的資料行。  

 若 **JSON_VALUE** 在找到 *path* 識別的值之前，先找到 *expression* 中無效的 JSON，函式便會傳回錯誤。 如果 **JSON_VALUE** 找不到 *path* 所識別的值，則會掃描整個文字，並在 *expression* 中任何一處找到無效的 JSON 時傳回錯誤。
  
 *path*  
 指定要擷取之屬性的 JSON 路徑。 如需詳細資訊，請參閱 [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)。  

在 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 及 [!INCLUDE[ssSDSfull_md](../../includes/sssdsfull-md.md)] 中，您可以將變數作為 *path* 的值提供。
  
 若 *path* 的格式無效，**JSON_VALUE** 便會傳回錯誤。  
  
## <a name="return-value"></a>傳回值

 傳回 nvarchar(4000) 類型的單一文字值。 傳回值的定序與輸入運算式的定序相同。  
  
 如果值大於 4000 個字元：  
  
- 在 lax 模式中，**JSON_VALUE** 會傳回 Null。  
  
- 在 strict 模式中，**JSON_VALUE** 會傳回錯誤。  
  
 如果您必須傳回大於 4000 個字元的純量值，請使用 **OPENJSON** 而不是 **JSON_VALUE**。 如需詳細資訊，請參閱 [OPENJSON &#40;Transact-SQL&#41;](../../t-sql/functions/openjson-transact-sql.md)。  
  
## <a name="remarks"></a>備註

### <a name="lax-mode-and-strict-mode"></a>lax 模式和 strict 模式

 請參考下列 JSON 文字：  
  
```json  
DECLARE @jsonInfo NVARCHAR(MAX)

SET @jsonInfo=N'{  
     "info":{    
       "type":1,  
       "address":{    
         "town":"Bristol",  
         "county":"Avon",  
         "country":"England"  
       },  
       "tags":["Sport", "Water polo"]  
    },  
    "type":"Basic"  
 }'  
```  
  
 下列表格會比較 lax 模式與 strict 模式中 **JSON_VALUE** 的行為。 如需選擇性路徑模式規格 (lax 或 strict) 的詳細資訊，請參閱 [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)。  
  
|Path|lax 模式中的傳回值|strict 模式中的傳回值|其他資訊|  
|----------|------------------------------|---------------------------------|---------------|  
|$|NULL|錯誤|非純量值。<br /><br /> 請改用 **JSON_QUERY**。|  
|$.info.type|N'1'|N'1'|N/a|  
|$.info.address.town|N'Bristol'|N'Bristol'|N/a|  
|$.info."address"|NULL|錯誤|非純量值。<br /><br /> 請改用 **JSON_QUERY**。|  
|$.info.tags|NULL|錯誤|非純量值。<br /><br /> 請改用 **JSON_QUERY**。|  
|$.info.type[0]|NULL|錯誤|非陣列。|  
|$.info.none|NULL|錯誤|屬性不存在。|  
| &nbsp; | &nbsp; | &nbsp; | &nbsp; |
  
## <a name="examples"></a>範例  
  
### <a name="example-1"></a>範例 1
 下列範例會使用查詢結果中 `town` 和 `state` 的 JSON 屬性值。 由於 **JSON_VALUE** 會保留來源的定序，因此結果的排序次序取決於 `jsonInfo` 資料行定序而定  

> [!NOTE]
> (這個範例假設一個名為 `Person.Person` 的資料表，其中包含 JSON 文字的 `jsonInfo` 資料行，且該資料行的結構如先前 lax 模式和 strict 模式的說明中所示。 在 AdventureWorks 範例資料庫中，`Person` 資料表實際上不包含 `jsonInfo` 資料行)。
  
```sql  
SELECT FirstName, LastName,
 JSON_VALUE(jsonInfo,'$.info.address[0].town') AS Town
FROM Person.Person
WHERE JSON_VALUE(jsonInfo,'$.info.address[0].state') LIKE 'US%'
ORDER BY JSON_VALUE(jsonInfo,'$.info.address[0].town')
```  
  
### <a name="example-2"></a>範例 2
 下列範例會將 JSON 屬性 `town` 的值擷取到區域變數中。  
  
```sql
DECLARE @jsonInfo NVARCHAR(MAX)
DECLARE @town NVARCHAR(32)

SET @jsonInfo=N'{"info":{"address":[{"town":"Paris"},{"town":"London"}]}}';

SET @town=JSON_VALUE(@jsonInfo,'$.info.address[0].town'); -- Paris
SET @town=JSON_VALUE(@jsonInfo,'$.info.address[1].town'); -- London
```  
  
### <a name="example-3"></a>範例 3
 下列範例會建立以 JSON 屬性值為基礎的計算資料行。  
  
```sql  
CREATE TABLE dbo.Store
 (
  StoreID INT IDENTITY(1,1) NOT NULL,
  Address VARCHAR(500),
  jsonContent NVARCHAR(4000),
  Longitude AS JSON_VALUE(jsonContent, '$.address[0].longitude'),
  Latitude AS JSON_VALUE(jsonContent, '$.address[0].latitude')
 )
```  
  
## <a name="see-also"></a>請參閱
 [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)   
 [JSON 資料 &#40;SQL Server&#41;](../../relational-databases/json/json-data-sql-server.md)  
  
