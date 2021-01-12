---
description: AVG (Transact-SQL)
title: AVG (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- AVG_TSQL
- AVG
dev_langs:
- TSQL
helpviewer_keywords:
- AVG function [Transact-SQL]
- GROUP BY clause, AVG function
- DISTINCT keyword
- values [SQL Server], average
- average values
ms.assetid: 4534b705-d946-441b-9b5d-5fbe561c9131
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5ab8591723ca23a0dea6577bd83ccf26dd21ff4a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98088688"
---
# <a name="avg-transact-sql"></a>AVG (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函數傳回群組中值的平均值。 它會忽略 Null 值。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
AVG ( [ ALL | DISTINCT ] expression )  
   [ OVER ( [ partition_by_clause ] order_by_clause ) ]
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
ALL  
將彙總函式套用至所有值。 ALL 是預設值。
  
DISTINCT  
指定 AVG 只在每個值的唯一一個執行個體上執行，不論該值出現多少次。
  
*expression*  
精確數值或近似數值資料型別類別的 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)，但 **bit** 資料型別除外。 不允許彙總函式和子查詢。
  
OVER **(** [ *partition_by_clause* ] _order\_by\_clause_ **)**  
*partition_by_clause* 會將 FROM 子句產生的結果集分割成函數所要套用的分割區。 如未指定，此函數會將查詢結果集的所有資料列視為單一群組。 *order_by_clause* 決定執行作業的邏輯順序。 *order_by_clause* 為必要項目。 如需詳細資訊，請參閱 [OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)。
  
## <a name="return-types"></a>傳回類型
*運算式* 的評估結果決定傳回型別。
  
|運算式結果|傳回類型|  
|---|---|
|**tinyint**|**int**|  
|**smallint**|**int**|  
|**int**|**int**|  
|**bigint**|**bigint**|  
|**decimal** 類別 (p, s)|**decimal(38, min(s,6))**|  
|**money** 和 **smallmoney** 類別|**money**|  
|**float** 和 **real** 類別|**float**|  
  
## <a name="remarks"></a>備註  
如果 *expression* 的資料類型是一個別名資料類型，傳回型別也會是別名資料類型。 不過，如果別名資料型別的基底資料型別升級；例如，從 **tinyint** 升級到 **int**，傳回值會採用升級的資料型別，而不是別名資料型別。
  
AVG () 會計算一組值的平均值，其方式是將這些值的總和除以非 null 值的計數。 如果總和超過傳回值資料型別的最大值，則 AVG() 會傳回錯誤。
  
AVG 未搭配 OVER 和 ORDER BY 子句使用時，是具決定性函數。 使用 OVER 和 ORDER BY 子句指定時，則不具決定性。 如需詳細資訊，請參閱 [決定性與非決定性函數](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)。
  
## <a name="examples"></a>範例  
  
### <a name="a-using-the-sum-and-avg-functions-for-calculations"></a>A. 使用 SUM 與 AVG 函數計算  
此範例計算 [!INCLUDE[ssSampleDBCoFull](../../includes/sssampledbcofull-md.md)] 的副總裁所用的平均假期時數及病假時數總和。 這些彙總函式的每一個都會產生所有擷取之資料列的單一摘要值。 此範例會使用 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫。
  
```sql
SELECT AVG(VacationHours)AS 'Average vacation hours',   
    SUM(SickLeaveHours) AS 'Total sick leave hours'  
FROM HumanResources.Employee  
WHERE JobTitle LIKE 'Vice President%';  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Average vacation hours       Total sick leave hours
 ----------------------       ----------------------
25                           97
  
(1 row(s) affected)
```
  
### <a name="b-using-the-sum-and-avg-functions-with-a-group-by-clause"></a>B. 搭配 GROUP BY 子句使用 SUM 與 AVG 函數  
當搭配 `GROUP BY` 子句使用時，每個彙總函數都會產生一個涵蓋每個群組的單一值，而不是涵蓋整個資料表的單一值。 以下範例會針對 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的每個銷售領域，產生摘要值。 摘要會列出每個領域的銷售人員所收到的平均獎金，以及每個領域年初至今的銷售總和。
  
```sql
SELECT TerritoryID, AVG(Bonus)as 'Average bonus', SUM(SalesYTD) as 'YTD sales'  
FROM Sales.SalesPerson  
GROUP BY TerritoryID;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
TerritoryID Average Bonus         YTD Sales  
----------- --------------------- ---------------------  
NULL        0.00                  1252127.9471  
1           4133.3333             4502152.2674  
2           4100.00               3763178.1787  
3           2500.00               3189418.3662  
4           2775.00               6709904.1666  
5           6700.00               2315185.611  
6           2750.00               4058260.1825  
7           985.00                3121616.3202  
8           75.00                 1827066.7118  
9           5650.00               1421810.9242  
10          5150.00               4116871.2277  
  
(11 row(s) affected)  
```  
  
### <a name="c-using-avg-with-distinct"></a>C. 搭配 DISTINCT 使用 AVG  
此陳述式傳回 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中產品的平均標價。 透過使用 DISTINCT，計算只會考慮唯一值。
  
```sql
SELECT AVG(DISTINCT ListPrice)  
FROM Production.Product;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
------------------------------
437.4042
  
(1 row(s) affected)
```
  
### <a name="d-using-avg-without-distinct"></a>D. 使用不含 DISTINCT 的 AVG  
沒有 DISTINCT 時，`AVG` 函數會在 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫的 `Product` 資料表中，尋找所有產品的平均標價，且包含任何重複的值。
  
```sql
SELECT AVG(ListPrice)  
FROM Production.Product;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
------------------------------
438.6662
  
(1 row(s) affected)
```
  
### <a name="e-using-the-over-clause"></a>E. 使用 OVER 子句  
以下範例使用 AVG 函數搭配 OVER 子句，以針對 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫 `Sales.SalesPerson` 資料表中的每個領域，提供年度銷售的移動平均。 `TerritoryID` 負責分割資料，而 `SalesYTD` 會進行邏輯性地排序。 這表示，將會根據銷售年度來針對每一個領域計算 AVG 函數。 請注意，對於 `TerritoryID` 1，銷售年度 2005 有兩個資料列，這表示兩個銷售人員在該年度的銷售額。 系統計算這兩個資料列的平均銷售額，然後將表示 2006 年度銷售額的第三個資料列納入計算。
  
```sql
SELECT BusinessEntityID, TerritoryID   
   ,DATEPART(yy,ModifiedDate) AS SalesYear  
   ,CONVERT(VARCHAR(20),SalesYTD,1) AS  SalesYTD  
   ,CONVERT(VARCHAR(20),AVG(SalesYTD) OVER (PARTITION BY TerritoryID   
                                            ORDER BY DATEPART(yy,ModifiedDate)   
                                           ),1) AS MovingAvg  
   ,CONVERT(VARCHAR(20),SUM(SalesYTD) OVER (PARTITION BY TerritoryID   
                                            ORDER BY DATEPART(yy,ModifiedDate)   
                                            ),1) AS CumulativeTotal  
FROM Sales.SalesPerson  
WHERE TerritoryID IS NULL OR TerritoryID < 5  
ORDER BY TerritoryID,SalesYear;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
BusinessEntityID TerritoryID SalesYear   SalesYTD             MovingAvg            CumulativeTotal  
---------------- ----------- ----------- -------------------- -------------------- --------------------  
274              NULL        2005        559,697.56           559,697.56           559,697.56  
287              NULL        2006        519,905.93           539,801.75           1,079,603.50  
285              NULL        2007        172,524.45           417,375.98           1,252,127.95  
283              1           2005        1,573,012.94         1,462,795.04         2,925,590.07  
280              1           2005        1,352,577.13         1,462,795.04         2,925,590.07  
284              1           2006        1,576,562.20         1,500,717.42         4,502,152.27  
275              2           2005        3,763,178.18         3,763,178.18         3,763,178.18  
277              3           2005        3,189,418.37         3,189,418.37         3,189,418.37  
276              4           2005        4,251,368.55         3,354,952.08         6,709,904.17  
281              4           2005        2,458,535.62         3,354,952.08         6,709,904.17  
  
(10 row(s) affected)  
  
```  
  
在這個範例中，OVER 子句未包含 PARTITION BY。 這表示該函數將套用到查詢所傳回的所有資料列。 OVER 子句中指定的 ORDER BY 子句決定套用 AVG 函數的邏輯順序。 此查詢會依照 WHERE 子句中指定的所有銷售領域傳回銷售量移動平均值 (依年度)。 SELECT 陳述式中指定的 ORDER BY 子句決定 SELECT 陳述式顯示所查詢資料列的順序。
  
```sql
SELECT BusinessEntityID, TerritoryID   
   ,DATEPART(yy,ModifiedDate) AS SalesYear  
   ,CONVERT(VARCHAR(20),SalesYTD,1) AS  SalesYTD  
   ,CONVERT(VARCHAR(20),AVG(SalesYTD) OVER (ORDER BY DATEPART(yy,ModifiedDate)   
                                            ),1) AS MovingAvg  
   ,CONVERT(VARCHAR(20),SUM(SalesYTD) OVER (ORDER BY DATEPART(yy,ModifiedDate)   
                                            ),1) AS CumulativeTotal  
FROM Sales.SalesPerson  
WHERE TerritoryID IS NULL OR TerritoryID < 5  
ORDER BY SalesYear;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
BusinessEntityID TerritoryID SalesYear   SalesYTD             MovingAvg            CumulativeTotal  
---------------- ----------- ----------- -------------------- -------------------- --------------------  
274              NULL        2005        559,697.56           2,449,684.05         17,147,788.35  
275              2           2005        3,763,178.18         2,449,684.05         17,147,788.35  
276              4           2005        4,251,368.55         2,449,684.05         17,147,788.35  
277              3           2005        3,189,418.37         2,449,684.05         17,147,788.35  
280              1           2005        1,352,577.13         2,449,684.05         17,147,788.35  
281              4           2005        2,458,535.62         2,449,684.05         17,147,788.35  
283              1           2005        1,573,012.94         2,449,684.05         17,147,788.35  
284              1           2006        1,576,562.20         2,138,250.72         19,244,256.47  
287              NULL        2006        519,905.93           2,138,250.72         19,244,256.47  
285              NULL        2007        172,524.45           1,941,678.09         19,416,780.93  
(10 row(s) affected)  
```  
  
## <a name="see-also"></a>另請參閱
[彙總函數 &#40;Transact-SQL&#41;](../../t-sql/functions/aggregate-functions-transact-sql.md)  
[OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)
  
  
