---
description: LEAD (Transact-SQL)
title: LEAD (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/09/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- LEAD_TSQL
- LEAD
dev_langs:
- TSQL
helpviewer_keywords:
- LEAD function
- analytic functions, LEAD
ms.assetid: 21f66bbf-d1ea-4f75-a3c4-20dc7fc1c69e
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: dc37f93573a6629522c9bab7771ab80c257f8a4c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096031"
---
# <a name="lead-transact-sql"></a>LEAD (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  在不使用開頭為 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 的自我聯結下，於相同的資料集中，從後續的資料列存取資料。 LEAD 在跟隨目前資料列的已指定實體位移中，提供存取資料列。 在 SELECT 陳述式中使用這個分析函數，比較目前資料列中的值與下列資料列中的值。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例 &#40;Transact-SQL&#41;](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
LEAD ( scalar_expression [ ,offset ] , [ default ] )   
    OVER ( [ partition_by_clause ] order_by_clause )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *scalar_expression*  
 根據指定的位移傳回數值。 它是傳回單一 (純量) 值的任一類型運算式。 *scalar_expression* 不能是分析函數。  
  
 *offset*  
 從取得數值的目前資料列轉寄資料列的數目。 若未加以指定，預設為 1。 *offset* 可以是資料行、子查詢或其他運算式，能算出正整數或可以明確地轉換為 **bigint**。 *offset* 不能是負值或分析函數。  
  
 *預設值*  
 當 *offset* 超過資料分割範圍時會傳回的值。 如果未指定預設值，會傳回 NULL。 *default* 可以是資料行、子查詢或其他運算式，但不能是分析函數。 *default* 的類型必須與 *scalar_expression* 相容。
  
 OVER **(** [ _partition\_by\_clause_ ] _order\_by\_clause_ **)**  
 *partition_by_clause* 會將 FROM 子句產生的結果集分割成函數所要套用的分割區。 如未指定，此函數會將查詢結果集的所有資料列視為單一群組。 在套用函數之前，*order_by_clause* 可指定資料順序。 當指定 *partition_by_clause* 時，即決定每一個分割區的次序。 *order_by_clause* 為必要項目。 如需詳細資訊，請參閱 [OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)。  
  
## <a name="return-types"></a>傳回型別  
 已指定的 *scalar_expression* 的資料類型。 如果 *scalar_expression* 可以為 Null 或 *default* 設為 NULL，則會傳回 NULL。  
  
 LEAD 不具決定性。 如需詳細資訊，請參閱 [決定性與非決定性函數](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)。  
  
## <a name="examples"></a>範例  
  
### <a name="a-compare-values-between-years"></a>A. 比較不同年份的值  
 此查詢使用 LEAD 函數傳回特定員工於下列年份的銷售額差異。 請注意，因為最後一列沒有可用的前置值，所以傳回預設值零 (0)。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT BusinessEntityID, YEAR(QuotaDate) AS SalesYear, SalesQuota AS CurrentQuota,   
    LEAD(SalesQuota, 1,0) OVER (ORDER BY YEAR(QuotaDate)) AS NextQuota  
FROM Sales.SalesPersonQuotaHistory  
WHERE BusinessEntityID = 275 AND YEAR(QuotaDate) IN ('2005','2006');  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
BusinessEntityID SalesYear   CurrentQuota          NextQuota  
---------------- ----------- --------------------- ---------------------  
275              2005        367000.00             556000.00  
275              2005        556000.00             502000.00  
275              2006        502000.00             550000.00  
275              2006        550000.00             1429000.00  
275              2006        1429000.00            1324000.00  
275              2006        1324000.00            0.00  
```  
  
### <a name="b-compare-values-within-partitions"></a>B. 比較分割區中的值  
 下列範例使用 LEAD 函數比較年初至今各員工的銷售額。 指定 PARTITION BY 子句可依照銷售領域分割結果集中的資料列。 LEAD 函數會分別套用至每個分割區，並會針對每個分割區重新開始計算。 OVER 子句中指定的 ORDER BY 子句，會在套用函數之前排序每個分割區中的資料列。 SELECT 陳述式中的 ORDER BY 子句，會排序整個結果集中的資料列。 請注意，因為每個分割區的最後一列沒有可用的前置值，所以傳回預設值零 (0)。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT TerritoryName, BusinessEntityID, SalesYTD,   
       LEAD (SalesYTD, 1, 0) OVER (PARTITION BY TerritoryName ORDER BY SalesYTD DESC) AS NextRepSales  
FROM Sales.vSalesPerson  
WHERE TerritoryName IN (N'Northwest', N'Canada')   
ORDER BY TerritoryName;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```   
TerritoryName            BusinessEntityID SalesYTD              NextRepSales  
-----------------------  ---------------- --------------------- ---------------------  
Canada                   282              2604540.7172          1453719.4653  
Canada                   278              1453719.4653          0.00  
Northwest                284              1576562.1966          1573012.9383  
Northwest                283              1573012.9383          1352577.1325  
Northwest                280              1352577.1325          0.00  
  
```  
  
### <a name="c-specifying-arbitrary-expressions"></a>C. 指定任意的運算式  
 下列範例將示範如何在 LEAD 函數語法中指定多種任意的運算式。  
  
```sql  
CREATE TABLE T (a INT, b INT, c INT);   
GO  
INSERT INTO T VALUES (1, 1, -3), (2, 2, 4), (3, 1, NULL), (4, 3, 1), (5, 2, NULL), (6, 1, 5);   
  
SELECT b, c,   
    LEAD(2*c, b*(SELECT MIN(b) FROM T), -c/2.0) OVER (ORDER BY a) AS i  
FROM T;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
b           c           i  
----------- ----------- -----------  
1           -3          8  
2           4           2  
1           NULL        2  
3           1           0  
2           NULL        NULL  
1           5           -2  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-compare-values-between-quarters"></a>D：比較各季之間的值  
 下列範例示範 LEAD 函數。 查詢會獲得指定員工在後續日曆季的銷售配額值中的差異。 請注意，因為最後一列之後沒有可用的前置值，所以會使用預設值零 (0)。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT CalendarYear AS Year, CalendarQuarter AS Quarter, SalesAmountQuota AS SalesQuota,  
       LEAD(SalesAmountQuota,1,0) OVER (ORDER BY CalendarYear, CalendarQuarter) AS NextQuota,  
   SalesAmountQuota - LEAD(SalesAmountQuota,1,0) OVER (ORDER BY CalendarYear, CalendarQuarter) AS Diff  
FROM dbo.FactSalesQuota  
WHERE EmployeeKey = 272 AND CalendarYear IN (2001,2002)  
ORDER BY CalendarYear, CalendarQuarter;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
Year Quarter  SalesQuota  NextQuota  Diff  
---- -------  ----------  ---------  -------------  
2001 3        28000.0000   7000.0000   21000.0000 
2001 4         7000.0000  91000.0000  -84000.0000  
2001 1        91000.0000 140000.0000  -49000.0000  
2002 2       140000.0000   7000.0000    7000.0000  
2002 3         7000.0000 154000.0000   84000.0000  
2002 4       154000.0000      0.0000  154000.0000
```  
  
## <a name="see-also"></a>另請參閱  
 [LAG &#40;Transact-SQL&#41;](../../t-sql/functions/lag-transact-sql.md)  
  
  


