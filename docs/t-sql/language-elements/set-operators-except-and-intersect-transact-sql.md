---
description: 設定運算子 - EXCEPT 和 INTERSECT (Transact-SQL)
title: EXCEPT 和 INTERSECT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- INTERSECT_TSQL
- EXCEPT_TSQL
- INTERSECT
- EXCEPT
dev_langs:
- TSQL
helpviewer_keywords:
- EXCEPT operator [Transact-SQL]
- queries [SQL Server], comparing
- comparing queries
- INTERSECT operator
ms.assetid: b1019300-171a-4a1a-854f-e1e751de3565
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f0a7ddf707ec6ed5bea3ff32fee215e29497767a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100308"
---
# <a name="set-operators---except-and-intersect-transact-sql"></a>設定運算子 - EXCEPT 和 INTERSECT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

比較兩個查詢的結果來傳回相異的資料列。  
  
EXCEPT 會從左側的輸入查詢傳回相異資料列，而不會從右側輸入查詢的輸出傳回。  
 
INTERSECT 會傳回左右兩側輸入查詢運算子所輸出的相異資料列。  
  
若要結合使用 EXCEPT 或 INTERSECT 兩個查詢的結果集，基本規則如下：  
  
-   在所有查詢中，資料行的數目和順序都必須相同。  
  
-   資料類型必須相容。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
{ <query_specification> | ( <query_expression> ) }   
{ EXCEPT | INTERSECT }  
{ <query_specification> | ( <query_expression> ) }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
\<_query\_specification_> | ( \<_query\_expression_> )  
這是一個查詢規格或查詢運算式，它會傳回要與另一個查詢規格或查詢運算式資料比較的資料。 EXCEPT 或 INTERSECT 作業中的資料行定義不必相同， 但必須能夠透過隱含的轉換來比較。 當資料類型不同時，[資料類型優先順序](../../t-sql/data-types/data-type-precedence-transact-sql.md)規則可決定要執行比較的資料類型。  
  
當類型相同，但有效位數、小數位數或長度不同時，結果取決於相同的運算式組合規則。 如需詳細資訊，請參閱[有效位數、小數位數和長度 &#40;Transact-SQL&#41;](../../t-sql/data-types/precision-scale-and-length-transact-sql.md)。  
  
查詢規格或運算式無法傳回 **xml**、**text**、**ntext**、**image** 或非二進位 CLR 使用者定義的類型資料行，因為這些資料類型無法比較。  
  
EXCEPT  
會從 EXCEPT 運算子左側查詢傳回任何相異值。 這些值傳回的前提是：右側查詢未傳回其中的任何值。  
  
INTERSECT  
傳回 INTERSECT 運算子左右兩側之查詢所傳回的任何相異值。  
  
## <a name="remarks"></a>備註  
EXCEPT 或 INTERSECT 運算子左側和右側查詢會傳回可比較資料行的資料類型。 這些資料類型可能包括含不同定序的字元資料類型。 如果是這種情形，則會根據[定序優先順序](../../t-sql/statements/collation-precedence-transact-sql.md)規則來執行必要的比較。 如果您無法執行這項轉換，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會傳回錯誤。  
  
當您比較資料行值來判斷相異資料列時，會將兩個 NULL 值視為相等。  
  
EXCEPT 和 INTERSECT 傳回的結果集資料行名稱，都與運算子左側查詢傳回的資料行名稱相同。  
  
ORDER BY 子句中的資料行名稱或別名必須參考左側查詢傳回的資料行名稱。  
  
EXCEPT 或 INTERSECT 所傳回結果集中任何資料行的 Null 屬性，都與運算子左側查詢所傳回對應資料行的 Null 屬性相同。  
  
如果 EXCEPT 或 INTERSECT 與運算式中的其他運算子搭配使用，就會依照下列優先順序內容來進行評估：  
  
1.  括弧內的運算式  
  
2.  INTERSECT 運算子  
  
3.  根據在運算式中的位置，由左至右評估 EXCEPT 和 UNION  
  
您可以使用 EXCEPT 或 INTERSECT 來比較兩組以上的查詢。 當您進行上述作業時，系統會一次比較兩個查詢，並遵循先前所提及的運算式評估規則，以決定資料類型的轉換。  
  
EXCEPT 和 INTERSECT 無法用在分散式資料分割檢視定義和查詢通知中。  
 
EXCEPT 和 INTERSECT 可用在分散式查詢中，但只能執行於本機伺服器，不會發送到連結伺服器。 因此，在分散式查詢中使用 EXCEPT 和 INTERSECT 可能會影響效能。  
  
當快速順向資料指標和靜態資料指標與 EXCEPT 或 INTERSECT 作業搭配使用時，您即可在結果集中使用這些資料指標。 您也可以將索引鍵集驅動資料指標或動態資料指標與 EXCEPT 或 INTERSECT 作業搭配使用。 當您進行上述作業時，系統會將作業結果集的資料指標轉換成靜態資料指標。  
  
利用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 的圖形化執行程序表功能來顯示 EXCEPT 作業時，此作業會顯示為[左方反半聯結](../../relational-databases/showplan-logical-and-physical-operators-reference.md)，而 INTERSECT 作業會顯示為[左方半聯結](../../relational-databases/showplan-logical-and-physical-operators-reference.md)。  
  
## <a name="examples"></a>範例  
下列範例示範如何使用 `INTERSECT` 和 `EXCEPT` 運算子。 第一個查詢會傳回 `Production.Product` 資料表的所有值，以便與 `INTERSECT` 和 `EXCEPT` 的結果進行比較。  
  
```sql
-- Uses AdventureWorks  
  
SELECT ProductID   
FROM Production.Product ;  
--Result: 504 Rows  
```  
  
下列查詢會傳回 `INTERSECT` 運算子左右兩側之查詢所傳回的任何相異值。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT ProductID   
FROM Production.Product  
INTERSECT  
SELECT ProductID   
FROM Production.WorkOrder ;  
--Result: 238 Rows (products that have work orders)  
```  
  
下列查詢會從 `EXCEPT` 運算子左側查詢傳回在右側查詢中找不到的任何相異值。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT ProductID   
FROM Production.Product  
EXCEPT  
SELECT ProductID   
FROM Production.WorkOrder ;  
--Result: 266 Rows (products without work orders)  
```  
  
下列查詢會從 `EXCEPT` 運算子左側查詢傳回在右側查詢中找不到的任何相異值。 資料表是由先前範例反轉所得。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT ProductID   
FROM Production.WorkOrder  
EXCEPT  
SELECT ProductID   
FROM Production.Product ;  
--Result: 0 Rows (work orders without products)  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
下列範例示範如何使用 `INTERSECT` 及 `EXCEPT` 運算子。 第一個查詢會傳回 `FactInternetSales` 資料表的所有值，以便與 `INTERSECT` 和 `EXCEPT` 的結果進行比較。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT CustomerKey   
FROM FactInternetSales;  
--Result: 60398 Rows  
```  
  
下列查詢會傳回 `INTERSECT` 運算子左右兩側之查詢所傳回的任何相異值。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT CustomerKey   
FROM FactInternetSales    
INTERSECT   
SELECT CustomerKey   
FROM DimCustomer   
WHERE DimCustomer.Gender = 'F'  
ORDER BY CustomerKey;  
--Result: 9133 Rows (Sales to customers that are female.)  
```  
  
下列查詢會從 `EXCEPT` 運算子左側查詢傳回在右側查詢中找不到的任何相異值。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT CustomerKey   
FROM FactInternetSales    
EXCEPT   
SELECT CustomerKey   
FROM DimCustomer   
WHERE DimCustomer.Gender = 'F'  
ORDER BY CustomerKey;  
--Result: 9351 Rows (Sales to customers that are not female.)  
```  
