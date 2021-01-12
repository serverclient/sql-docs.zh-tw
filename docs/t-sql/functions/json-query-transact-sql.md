---
description: JSON_QUERY (Transact-SQL)
title: JSON_QUERY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/03/2020
ms.prod: sql
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- JSON_QUERY
- JSON_QUERY_TSQL
helpviewer_keywords:
- JSON, extracting
- JSON, querying
- JSON_QUERY function
ms.assetid: 1ab0d90f-19b6-4988-ab4f-22fdf28b7c79
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: chadam
monikerRange: = azuresqldb-current||= azure-sqldw-latest||>= sql-server-2016||>= sql-server-linux-2017
ms.openlocfilehash: ec281dac44455369bc1d7e8f53b2b5b2a7ff5bb0
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102551"
---
# <a name="json_query-transact-sql"></a>JSON_QUERY (Transact-SQL)

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

 從 JSON 字串擷取物件或陣列。  
  
 若要從 JSON 字串而非物件或陣列擷取純量值，請參閱 [JSON_VALUE &#40;Transact-SQL&#41;](../../t-sql/functions/json-value-transact-sql.md)。 如需 **JSON_VALUE** 及 **JSON_QUERY** 之間的差異資訊，請參閱 [比較 JSON_VALUE 與 JSON_QUERY](../../relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server.md#JSONCompare)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
JSON_QUERY ( expression [ , path ] )  
```  
  
## <a name="arguments"></a>引數

 *expression*  
 運算式。 通常為變數的名稱或包含 JSON 文字的資料行。  
  
 若 **JSON_QUERY** 在找到 *path* 識別的值之前找到在 *expression* 中無效的 JSON，函式便會傳回錯誤。 若 **JSON_QUERY** 找不到 *path* 識別的值，它會掃描整個文字，並在 *expression* 中任何一處找到無效的 JSON 時傳回錯誤。  
  
 *path*  
 指定要擷取之物件或陣列的 JSON 路徑。

在 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 及 [!INCLUDE[ssSDSfull_md](../../includes/sssdsfull-md.md)] 中，您可以將變數作為 *path* 的值提供。

JSON 路徑可為剖析指定 lax 或 strict 模式。 若您未指定剖析模式，預設會使用 lax 模式。 如需詳細資訊，請參閱 [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)。  

*path* 的預設值為 '$'。 如此一來，若您未提供 *path* 的值，**JSON_QUERY** 會傳回輸入的 *expression*。

若 *path* 的格式無效，**JSON_QUERY** 便會傳回錯誤。  
  
## <a name="return-value"></a>傳回值

 傳回型別為 nvarchar(max) 的 JSON 片段。 傳回值的定序與輸入運算式的定序相同。  
  
 若值並非物件或陣列：  
  
- 在 lax 模式中，**JSON_QUERY** 會傳回 Null。  
  
- 在 strict 模式中，**JSON_QUERY** 會傳回錯誤。  
  
## <a name="remarks"></a>備註  

### <a name="lax-mode-and-strict-mode"></a>lax 模式和 strict 模式

 請參考下列 JSON 文字：  
  
```json  
{
   "info": {
      "type": 1,
      "address": {
         "town": "Bristol",
         "county": "Avon",
         "country": "England"
      },
      "tags": ["Sport", "Water polo"]
   },
   "type": "Basic"
} 
```  
  
 下列表格會比較 lax 模式與 strict 模式中 **JSON_QUERY** 的行為。 如需選擇性路徑模式規格 (lax 或 strict) 的詳細資訊，請參閱 [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)。  
  
|Path|lax 模式中的傳回值|strict 模式中的傳回值|其他資訊|  
|----------|------------------------------|---------------------------------|---------------|  
|$|傳回完整的 JSON 文字。|傳回完整的 JSON 文字。|N/a|  
|$.info.type|NULL|錯誤|並非物件或陣列。<br /><br /> 請改為使用 **JSON_VALUE**。|  
|$.info.address.town|NULL|錯誤|並非物件或陣列。<br /><br /> 請改為使用 **JSON_VALUE**。|  
|$.info."address"|N'{ "town":"Bristol", "county":"Avon", "country":"England" }'|N'{ "town":"Bristol", "county":"Avon", "country":"England" }'|N/a|  
|$.info.tags|N'[ "Sport", "Water polo"]'|N'[ "Sport", "Water polo"]'|N/a|  
|$.info.type[0]|NULL|錯誤|非陣列。|  
|$.info.none|NULL|錯誤|屬性不存在。|  

### <a name="using-json_query-with-for-json"></a>搭配 FOR JSON 使用 JSON_QUERY

**JSON_QUERY** 會傳回有效的 JSON 片段。 因此，**FOR JSON** 不會在 **JSON_QUERY** 的傳回值中逸出特殊字元。

若您使用 FOR JSON 傳回結果，並且在其中包含已經是 JSON 格式的資料 (在資料行中或運算式的結果)，請使用不具有 *path* 參數的 **JSON_QUERY** 來包裝 JSON 資料。

## <a name="examples"></a>範例  
  
### <a name="example-1"></a>範例 1

 下列範例示範如何在查詢結果中傳回來自 `CustomFields` 資料行的 JSON 片段。  
  
```sql  
SELECT PersonID,FullName,
  JSON_QUERY(CustomFields,'$.OtherLanguages') AS Languages
FROM Application.People
```  
  
### <a name="example-2"></a>範例 2

下列範例示範如何在 FOR JSON 子句的輸出中包含 JSON 片段。  
  
```sql  
SELECT StockItemID, StockItemName,
         JSON_QUERY(Tags) as Tags,
         JSON_QUERY(CONCAT('["',ValidFrom,'","',ValidTo,'"]')) ValidityPeriod
FROM Warehouse.StockItems
FOR JSON PATH
```  
  
## <a name="see-also"></a>另請參閱

- [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)   
- [JSON 資料 &#40;SQL Server&#41;](../../relational-databases/json/json-data-sql-server.md)  
