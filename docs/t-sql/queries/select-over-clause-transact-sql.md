---
title: OVER 子句 (Transact-SQL) | Microsoft Docs
description: OVER 子句的 Transact-SQL 參考會在查詢結果集內定義使用者所指定資料列集。
ms.date: 08/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- OVER_TSQL
- OVER
dev_langs:
- TSQL
helpviewer_keywords:
- order of rowsets [SQL Server]
- rowsets [SQL Server], windowing
- window function
- partitions [SQL Server], rowsets
- clauses [SQL Server], OVER
- rowsets [SQL Server], partitioning
- rowsets [SQL Server], ordering
- OVER clause
ms.assetid: ddcef3a6-0341-43e0-ae73-630484b7b398
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5dd2c6c6f33f9a115196d9a2afc3ca700b3a4631
ms.sourcegitcommit: a81823f20262227454c0b5ce9c8ac607aaf537e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97684202"
---
# <a name="select---over-clause-transact-sql"></a>SELECT - OVER 子句 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  在套用相關的視窗函數之前，決定資料列集的資料分割和排序。 也就是說，OVER 子句會定義查詢結果集內的視窗或使用者指定的資料列集。 然後視窗函數會針對視窗中的每個資料列來計算值。 您可以搭配函數使用 OVER 子句，以便計算彙總值，例如移動平均值、累計彙總、累加值或是每組前 N 個結果。  
  
-   [次序函數](../../t-sql/functions/ranking-functions-transact-sql.md)  
  
-   [彙總函數](../../t-sql/functions/aggregate-functions-transact-sql.md)  
  
-   [分析函數](../../t-sql/functions/analytic-functions-transact-sql.md)  
  
-   [NEXT VALUE FOR 函數](../../t-sql/functions/next-value-for-transact-sql.md)  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server, Azure SQL Database, and Azure Synapse Analytics  
  
OVER (   
       [ <PARTITION BY clause> ]  
       [ <ORDER BY clause> ]   
       [ <ROW or RANGE clause> ]  
      )  
  
<PARTITION BY clause> ::=  
PARTITION BY value_expression , ... [ n ]  
  
<ORDER BY clause> ::=  
ORDER BY order_by_expression  
    [ COLLATE collation_name ]   
    [ ASC | DESC ]   
    [ ,...n ]  
  
<ROW or RANGE clause> ::=  
{ ROWS | RANGE } <window frame extent>  
  
<window frame extent> ::=   
{   <window frame preceding>  
  | <window frame between>  
}  
<window frame between> ::=   
  BETWEEN <window frame bound> AND <window frame bound>  
  
<window frame bound> ::=   
{   <window frame preceding>  
  | <window frame following>  
}  
  
<window frame preceding> ::=   
{  
    UNBOUNDED PRECEDING  
  | <unsigned_value_specification> PRECEDING  
  | CURRENT ROW  
}  
  
<window frame following> ::=   
{  
    UNBOUNDED FOLLOWING  
  | <unsigned_value_specification> FOLLOWING  
  | CURRENT ROW  
}  
  
<unsigned value specification> ::=   
{  <unsigned integer literal> }  
  
```  
  
```syntaxsql
-- Syntax for Parallel Data Warehouse  
  
OVER ( [ PARTITION BY value_expression ] [ order_by_clause ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

視窗函數的 `OVER` 子句中可能有下列引數：
- [PARTITION BY](#partition-by) 可將查詢結果集分成幾個資料分割。
- [ORDER BY](#order-by) 可定義結果集的每個資料分割內資料列的邏輯順序。 
- [ROWS/RANGE](#rows-or-range) 可透過指定資料分割中的起點和終點來限制資料分割內的資料列。 其需要 `ORDER BY` 引數，且在指定 `ORDER BY` 引數的情況下，其預設值為資料分割的開頭至目前的元素。

如果您未指定任何引數，則視窗函數會套用到整個結果集。
```sql
select 
      object_id
    , [min] = min(object_id) over()
    , [max] = max(object_id) over()
from sys.objects
```
 
|object_id | 分鐘 | max |
|---|---|---|
| 3 | 3 | 2139154666 |
| 5 | 3 | 2139154666 |
| ... | ... | ... |
| 2123154609 |  3 | 2139154666 |
| 2139154666 |  3 | 2139154666 |

### <a name="partition-by"></a>PARTITION BY  
 將查詢結果集分成幾個資料分割。 視窗函數會分別套用至每個資料分割，並且針對每個資料分割重新開始計算。  

```sqlsyntax
PARTITION BY *value_expression* 
```
 
 如未指定 PARTITION BY，則函式會將查詢結果集的所有資料列視為單一資料分割。
如未指定 `ORDER BY` 子句，則函式會套用至資料分割中的所有資料列。
  
#### <a name="partition-by-value_expression"></a>PARTITION BY *value_expression*  
 指定分割資料列集所根據的資料行。 *value_expression* 只能參考 FROM 子句所提供的資料行。 *value_expression* 無法參考選取清單中的運算式或別名。 *value_expression* 可以是資料行運算式、純量子查詢、純量函數或使用者定義的變數。 
 
 ```sql
 select 
      object_id, type
    , [min] = min(object_id) over(partition by type)
    , [max] = max(object_id) over(partition by type)
from sys.objects
```

|object_id | 類型 | 分鐘 | max |
|---|---|---|---|
| 68195293  | PK    | 68195293  | 711673583 |
| 631673298 | PK    | 68195293  | 711673583 |
| 711673583 | PK    | 68195293  | 711673583 |
| ... | ... | ... |
| 3 | S | 3 | 98 |
| 5 | S |   3   | 98 |
| ... | ... | ... |
| 98    | S |   3   | 98 |
| ... | ... | ... |
  
### <a name="order-by"></a>排序依據  

```sqlsyntax
ORDER BY *order_by_expression* [COLLATE *collation_name*] [ASC|DESC]  
```

 定義結果集的每個資料分割內資料列的邏輯順序。 也就是說，其會指定執行視窗函數計算的邏輯順序。 
 - 如未指定，則預設的順序為 `ASC`，而且視窗函數會使用資料分割中的所有資料列。
 - 如已加以指定，且在 ROWS/RANGE 中未指定，則可接受選擇性 ROWS/RANGE 規格的函式 (例如 `min` 或 `max`) 會使用預設的 `RANGE UNBOUNDED PRECEDING AND CURRENT ROW` 作為視窗框架的預設值。 
 
```sql
select 
      object_id, type
    , [min] = min(object_id) over(partition by type order by object_id)
    , [max] = max(object_id) over(partition by type order by object_id)
from sys.objects
```

|object_id | 類型 | 分鐘 | max |
|---|---|---|---|
| 68195293  | PK    | 68195293  | 68195293 |
| 631673298 | PK    | 68195293  | 631673298 |
| 711673583 | PK    | 68195293  | 711673583 |
| ... | ... | ... |
| 3 | S | 3 | 3 |
| 5 | S |   3 | 5 |
| 6 | S |   3 | 6 |
| ... | ... | ... |
| 97    | S |   3 | 97 |
| 98    | S |   3 | 98 |
| ... | ... | ... |


 *order_by_expression*  
 指定排序的資料行或運算式。 *order_by_expression* 只能參考 FROM 子句所提供的資料行。 不能指定整數來代表資料行名稱或別名。  
  
 COLLATE *collation_name*  
 指定應該根據 *collation_name* 中指定的定序來執行 ORDER BY 作業。 *collation_name* 可以是 Windows 定序名稱或 SQL 定序名稱。 如需詳細資訊，請參閱 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)。 COLLATE 只適用於下列類型的資料行：**char**、**varchar**、**nchar** 及 **nvarchar**。  
  
 **ASC** | DESC  
 指定指定之資料行的值應該以遞增或遞減順序排序。 ASC 是預設排序次序。 Null 值會當做最低的可能值來處理。  
  
### <a name="rows-or-range"></a>ROWS 或 RANGE  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。 
  
 指定資料分割內的起始點和結束點，以進一步限制資料分割中的資料列。 這可以藉由指定與目前資料列有關的資料列範圍 (透過邏輯關聯或實體關聯) 來完成。 可以使用 ROWS 子句來達成實體關聯。  
  
 ROWS 子句會限制資料分割內的資料列，方法是指定目前資料列之前或之後的固定資料列數。 另外，RANGE 子句會以邏輯方式限制資料分割內的資料列，方法是指定與目前資料列的值相關的值範圍。 前後的資料列是根據 ORDER BY 子句的順序定義。 視窗框架 "RANGE ...CURRENT ROW ..." 包含 ORDER BY 運算式中，與目前資料列相同值的所有資料列。 例如，ROWS BETWEEN 2 PRECEDING AND CURRENT ROW 表示此函數操作所在的資料列視窗大小為三個資料列，從之前的 2 個資料列直到目前的資料列。  
 
```sql
select
      object_id
    , [preceding]   = count(*) over(order by object_id ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW )
    , [central] = count(*) over(order by object_id ROWS BETWEEN 2 PRECEDING AND 2 FOLLOWING )
    , [following]   = count(*) over(order by object_id ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING)
from sys.objects
order by object_id asc
```

|object_id | preceding | central | following |
|---|---|---|---|
| 3 | 1 | 3 | 156 |
| 5 | 2 | 4 | 155 |
| 6 | 3 | 5 | 154 |
| 7 | 4 | 5 | 153 |
| 8 | 5 | 5 | 152 |
| ...   | ...   | ...   | ... |
| 2112726579    | 153   | 5 | 4 |
| 2119678599    | 154   | 5 | 3 |
| 2123154609    | 155   | 4 | 2 |
| 2139154666    | 156   | 3 | 1 | 
  
> [!NOTE]  
>  ROWS 或 RANGE 要求必須指定 ORDER BY 子句。 如果 ORDER BY 包含多個順序運算式，則 CURRENT ROW FOR RANGE 會在判斷目前資料列時，考量 ORDER BY 清單中的所有資料列。  
  
#### <a name="unbounded-preceding"></a>UNBOUNDED PRECEDING  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 指定視窗從資料分割的第一個資料列開始。 只能將 UNBOUNDED PRECEDING 指定為視窗起點。  
  
 \<unsigned value specification> PRECEDING  
 與 \<unsigned value specification> 一起指定，以指出要置於目前資料列前面的資料列或值數目。 RANGE 不允許這項指定。  
  
#### <a name="current-row"></a>CURRENT ROW  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。 
  
 指定在與 ROWS 一起使用時，視窗在目前的資料列開始或結束，或者在與 RANGE 一起使用時則為目前值。 CURRENT ROW 可以指定為開始點和結束點。  
  
#### <a name="between-and"></a>BETWEEN AND  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。 
  
```sqlsyntax
BETWEEN <window frame bound > AND <window frame bound >  
```
 與 ROWS 或 RANGE 一起使用，以指定視窗的下 (開始) 邊界點和上 (結束) 邊界點。 \<window frame bound> 會定義界限開始點，而 \<window frame bound> 則定義界限結束點。 上限不能小於下限。  
  
#### <a name="unbounded-following"></a>UNBOUNDED FOLLOWING  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。 
  
 指定視窗在資料分割的最後一個資料列結束。 只能將 UNBOUNDED FOLLOWING 指定為視窗結束點。 例如，RANGE BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING 會定義一個視窗，此視窗從資料分割的目前資料列開始，並結束於資料分割的最後一個資料列。  
  
 \<unsigned value specification> FOLLOWING  
 與 \<unsigned value specification> 一起指定，可指出要置於目前資料列後面的資料列或值數目。 當 \<unsigned value specification> FOLLOWING 指定為視窗開始點時，結束點必須是 \<unsigned value specification> FOLLOWING。 例如，ROWS BETWEEN 2 FOLLOWING AND 10 FOLLOWING 會定義一個視窗，此視窗從目前資料列後面的第二個資料列開始，並結束於目前資料列後面的第十個資料列。 RANGE 不允許這項指定。  
  
 不帶正負號的整數常值  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 一個正整數常值 (包括 0)，可指定要置於目前資料列或值前面或後面的資料列或值的數目。 這項指定只對 ROWS 有效。  
  
## <a name="general-remarks"></a>一般備註  
 在含有一個 FROM 子句的單一查詢中，可以使用一個以上的視窗函數。 每個函數的 OVER 子句在進行資料分割和進行排序時，都不一樣。  
  
 如未指定 PARTITION BY，此函數會將查詢結果集的所有資料列視為單一群組。 
 
### <a name="important"></a>重要！

如果指定了 ROWS/RANGE，且 \<window frame preceding> 用於 \<window frame extent> (簡短語法)，則這個指定會用於視窗框架界限開始點，而 CURRENT ROW 則用於界限結束點。 例如，"ROWS 5 PRECEDING" 等於 "ROWS BETWEEN 5 PRECEDING AND CURRENT ROW"。  
  
> [!NOTE]
> 如果未指定 ORDER BY，則將整個資料分割用於視窗框架。 這只適用於不需要 ORDER BY 子句的函數。 如果未指定 ROWS/RANGE，但指定了 ORDER BY，則將 RANGE UNBOUNDED PRECEDING AND CURRENT ROW 當做視窗框架的預設值。 這只適用於可以接受選擇性 ROWS/RANGE 指定的函數。 例如，排名函數不能接受 ROWS/RANGE，因此，即使存在 ORDER BY 而不存在 ROWS/RANGE，這個視窗框架依然不適用。  
    
## <a name="limitations-and-restrictions"></a>限制事項  
 OVER 子句不能搭配 CHECKSUM 彙總函式使用。  
  
 RANGE 不能與 \<unsigned value specification> PRECEDING 或 \<unsigned value specification> FOLLOWING 一起使用。  
  
 視與 OVER 子句搭配使用的次序、彙總或分析函數而定，可能不支援 \<ORDER BY clause> 及/或 \<ROWS and RANGE clause>。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-the-over-clause-with-the-row_number-function"></a>A. 搭配 ROW_NUMBER 函數來使用 OVER 子句  
 下列範例示範如何搭配 ROW_NUMBER 函數使用 OVER 子句，以針對資料分割內的每一個資料列顯示資料列號碼。 OVER 子句中指定的 ORDER BY 子句會依照 `SalesYTD` 資料行來排序每一個資料分割中的資料列。 SELECT 陳述式中的 ORDER BY 子句會決定傳回整個查詢結果集的順序。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT ROW_NUMBER() OVER(PARTITION BY PostalCode ORDER BY SalesYTD DESC) AS "Row Number",   
    p.LastName, s.SalesYTD, a.PostalCode  
FROM Sales.SalesPerson AS s   
    INNER JOIN Person.Person AS p   
        ON s.BusinessEntityID = p.BusinessEntityID  
    INNER JOIN Person.Address AS a   
        ON a.AddressID = p.BusinessEntityID  
WHERE TerritoryID IS NOT NULL   
    AND SalesYTD <> 0  
ORDER BY PostalCode;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Row Number      LastName                SalesYTD              PostalCode 
 --------------- ----------------------- --------------------- ---------- 
 1               Mitchell                4251368.5497          98027 
 2               Blythe                  3763178.1787          98027 
 3               Carson                  3189418.3662          98027 
 4               Reiter                  2315185.611           98027 
 5               Vargas                  1453719.4653          98027  
 6               Ansman-Wolfe            1352577.1325          98027  
 1               Pak                     4116871.2277          98055  
 2               Varkey Chudukatil       3121616.3202          98055  
 3               Saraiva                 2604540.7172          98055  
 4               Ito                     2458535.6169          98055  
 5               Valdez                  1827066.7118          98055  
 6               Mensa-Annan             1576562.1966          98055  
 7               Campbell                1573012.9383          98055  
 8               Tsoflias                1421810.9242          98055
 ```  
  
### <a name="b-using-the-over-clause-with-aggregate-functions"></a>B. 搭配彙總函式來使用 OVER 子句  
 下列範例會針對查詢傳回的所有資料列來搭配彙總函式使用 `OVER` 子句。 在這個範例中，使用 `OVER` 子句比使用子查詢來衍生彙總值更有效率。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT SalesOrderID, ProductID, OrderQty  
    ,SUM(OrderQty) OVER(PARTITION BY SalesOrderID) AS Total  
    ,AVG(OrderQty) OVER(PARTITION BY SalesOrderID) AS "Avg"  
    ,COUNT(OrderQty) OVER(PARTITION BY SalesOrderID) AS "Count"  
    ,MIN(OrderQty) OVER(PARTITION BY SalesOrderID) AS "Min"  
    ,MAX(OrderQty) OVER(PARTITION BY SalesOrderID) AS "Max"  
FROM Sales.SalesOrderDetail   
WHERE SalesOrderID IN(43659,43664);  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
SalesOrderID ProductID   OrderQty Total       Avg         Count       Min    Max  
------------ ----------- -------- ----------- ----------- ----------- ------ ------  
43659        776         1        26          2           12          1      6  
43659        777         3        26          2           12          1      6  
43659        778         1        26          2           12          1      6  
43659        771         1        26          2           12          1      6  
43659        772         1        26          2           12          1      6  
43659        773         2        26          2           12          1      6  
43659        774         1        26          2           12          1      6  
43659        714         3        26          2           12          1      6  
43659        716         1        26          2           12          1      6  
43659        709         6        26          2           12          1      6  
43659        712         2        26          2           12          1      6  
43659        711         4        26          2           12          1      6  
43664        772         1        14          1           8           1      4  
43664        775         4        14          1           8           1      4  
43664        714         1        14          1           8           1      4  
43664        716         1        14          1           8           1      4  
43664        777         2        14          1           8           1      4  
43664        771         3        14          1           8           1      4  
43664        773         1        14          1           8           1      4  
43664        778         1        14          1           8           1      4  
```  
  
 下列範例顯示在計算值中搭配彙總函式來使用 `OVER` 子句。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT SalesOrderID, ProductID, OrderQty  
    ,SUM(OrderQty) OVER(PARTITION BY SalesOrderID) AS Total  
    ,CAST(1. * OrderQty / SUM(OrderQty) OVER(PARTITION BY SalesOrderID)   
        *100 AS DECIMAL(5,2))AS "Percent by ProductID"  
FROM Sales.SalesOrderDetail   
WHERE SalesOrderID IN(43659,43664);  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)] 請注意，彙總是以 `SalesOrderID` 來計算，而且每個 `Percent by ProductID` 的每一行都會計算出 `SalesOrderID`。  
  
```  
SalesOrderID ProductID   OrderQty Total       Percent by ProductID  
------------ ----------- -------- ----------- ---------------------------------------  
43659        776         1        26          3.85  
43659        777         3        26          11.54  
43659        778         1        26          3.85  
43659        771         1        26          3.85  
43659        772         1        26          3.85  
43659        773         2        26          7.69  
43659        774         1        26          3.85  
43659        714         3        26          11.54  
43659        716         1        26          3.85  
43659        709         6        26          23.08  
43659        712         2        26          7.69  
43659        711         4        26          15.38  
43664        772         1        14          7.14  
43664        775         4        14          28.57  
43664        714         1        14          7.14  
43664        716         1        14          7.14  
43664        777         2        14          14.29  
43664        771         3        14          21.4  
43664        773         1        14          7.14  
43664        778         1        14          7.14  
  
 (20 row(s) affected)  
```  
  
### <a name="c-producing-a-moving-average-and-cumulative-total"></a>C. 產生移動平均值和累計總和  
 下列範例搭配 OVER 子句來使用 AVG 與 SUM 函數，為 `Sales.SalesPerson` 資料表中各領域的年度銷售提供移動平均值和累計總和。 `TerritoryID` 負責分割資料，而 `SalesYTD` 會進行邏輯性地排序。 這表示，將會根據銷售年度來針對每一個領域計算 AVG 函數。 請注意，在 `TerritoryID` 1 中，2005 銷售年度有兩個資料列，分別表示在該年度有銷售業績的兩個銷售人員。 計算這兩個資料列的平均銷售額，然後將表示 2006 年度銷售額的第三個資料列納入計算。  
  
```sql  
USE AdventureWorks2012;  
GO  
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
  
 在這個範例中，OVER 子句未包含 PARTITION BY。 這表示，該函數將套用到查詢所傳回的所有資料列。 OVER 子句中指定的 ORDER BY 子句會決定套用 AVG 函數的邏輯順序。 此查詢會依照 WHERE 子句中指定的所有銷售領域傳回依年度的銷售量移動平均值。 SELECT 陳述式中指定的 ORDER BY 子句會決定查詢的資料列顯示的順序。  
  
```sql  
SELECT BusinessEntityID, TerritoryID   
   ,DATEPART(yy,ModifiedDate) AS SalesYear  
   ,CONVERT(VARCHAR(20),SalesYTD,1) AS SalesYTD  
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
  
### <a name="d-specifying-the-rows-clause"></a>D. 指定 ROWS 子句  
  
**適用對象**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本。  
  
 下列範例會使用 ROWS 子句來定義一個視窗，其上的資料列會計算為目前資料列與隨後的 *N* 個資料列 (在此範例中為 1 個資料列)。  
  
```sql  
SELECT BusinessEntityID, TerritoryID   
    ,CONVERT(VARCHAR(20),SalesYTD,1) AS SalesYTD  
    ,DATEPART(yy,ModifiedDate) AS SalesYear  
    ,CONVERT(VARCHAR(20),SUM(SalesYTD) OVER (PARTITION BY TerritoryID   
                                             ORDER BY DATEPART(yy,ModifiedDate)   
                                             ROWS BETWEEN CURRENT ROW AND 1 FOLLOWING ),1) AS CumulativeTotal  
FROM Sales.SalesPerson  
WHERE TerritoryID IS NULL OR TerritoryID < 5;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
BusinessEntityID TerritoryID SalesYTD             SalesYear   CumulativeTotal  
---------------- ----------- -------------------- ----------- --------------------  
274              NULL        559,697.56           2005        1,079,603.50  
287              NULL        519,905.93           2006        692,430.38  
285              NULL        172,524.45           2007        172,524.45  
283              1           1,573,012.94         2005        2,925,590.07  
280              1           1,352,577.13         2005        2,929,139.33  
284              1           1,576,562.20         2006        1,576,562.20  
275              2           3,763,178.18         2005        3,763,178.18  
277              3           3,189,418.37         2005        3,189,418.37  
276              4           4,251,368.55         2005        6,709,904.17  
281              4           2,458,535.62         2005        2,458,535.62  
```  
  
 在下列範例中，會使用 UNBOUNDED PRECEDING 來指定 ROWS 子句。 結果是視窗從資料分割的第一個資料列開始。  
  
```sql  
SELECT BusinessEntityID, TerritoryID   
    ,CONVERT(VARCHAR(20),SalesYTD,1) AS SalesYTD  
    ,DATEPART(yy,ModifiedDate) AS SalesYear  
    ,CONVERT(VARCHAR(20),SUM(SalesYTD) OVER (PARTITION BY TerritoryID   
                                             ORDER BY DATEPART(yy,ModifiedDate)   
                                             ROWS UNBOUNDED PRECEDING),1) AS CumulativeTotal  
FROM Sales.SalesPerson  
WHERE TerritoryID IS NULL OR TerritoryID < 5;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
BusinessEntityID TerritoryID SalesYTD             SalesYear   CumulativeTotal  
---------------- ----------- -------------------- ----------- --------------------  
274              NULL        559,697.56           2005        559,697.56  
287              NULL        519,905.93           2006        1,079,603.50  
285              NULL        172,524.45           2007        1,252,127.95  
283              1           1,573,012.94         2005        1,573,012.94  
280              1           1,352,577.13         2005        2,925,590.07  
284              1           1,576,562.20         2006        4,502,152.27  
275              2           3,763,178.18         2005        3,763,178.18  
277              3           3,189,418.37         2005        3,189,418.37  
276              4           4,251,368.55         2005        4,251,368.55  
281              4           2,458,535.62         2005        6,709,904.17  
  
```  
  
## <a name="examples-sspdw"></a>範例：[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-the-over-clause-with-the-row_number-function"></a>E. 搭配 ROW_NUMBER 函數來使用 OVER 子句  
 下列範例會根據指派給業務代表的銷售配額，傳回業務代表的 ROW_NUMBER。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT ROW_NUMBER() OVER(ORDER BY SUM(SalesAmountQuota) DESC) AS RowNumber,  
    FirstName, LastName,   
CONVERT(VARCHAR(13), SUM(SalesAmountQuota),1) AS SalesQuota   
FROM dbo.DimEmployee AS e  
INNER JOIN dbo.FactSalesQuota AS sq  
    ON e.EmployeeKey = sq.EmployeeKey  
WHERE e.SalesPersonFlag = 1  
GROUP BY LastName, FirstName;  
```  
  
 以下為部分結果集。  
  
 ```
 RowNumber  FirstName  LastName            SalesQuota  
 ---------  ---------  ------------------  -------------  
 1          Jillian    Carson              12,198,000.00  
 2          Linda      Mitchell            11,786,000.00  
 3          Michael    Blythe              11,162,000.00  
 4          Jae        Pak                 10,514,000.00  
 ```
 
### <a name="f-using-the-over-clause-with-aggregate-functions"></a>F. 搭配彙總函式來使用 OVER 子句  
 下列範例示範如何搭配彙總函數使用 OVER 子句。 在這個範例中，使用 OVER 子句比使用子查詢更有效率。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT SalesOrderNumber AS OrderNumber, ProductKey,   
       OrderQuantity AS Qty,   
       SUM(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Total,  
       AVG(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Avg,  
       COUNT(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Count,  
       MIN(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Min,  
       MAX(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Max  
FROM dbo.FactResellerSales   
WHERE SalesOrderNumber IN(N'SO43659',N'SO43664') AND  
      ProductKey LIKE '2%'  
ORDER BY SalesOrderNumber,ProductKey;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 OrderNumber  Product  Qty  Total  Avg  Count  Min  Max  
 -----------  -------  ---  -----  ---  -----  ---  ---  
 SO43659      218      6    16     3    5      1    6  
 SO43659      220      4    16     3    5      1    6  
 SO43659      223      2    16     3    5      1    6  
 SO43659      229      3    16     3    5      1    6  
 SO43659      235      1    16     3    5      1    6  
 SO43664      229      1     2     1    2      1    1  
 SO43664      235      1     2     1    2      1    1  
 ```
 
 下列範例示範如何在計算值中搭配彙總函數使用 OVER 子句。 請注意，會依 `SalesOrderNumber` 計算彙總，並且會為每個 `SalesOrderNumber` 的每一行計算出銷售訂單總計百分比。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT SalesOrderNumber AS OrderNumber, ProductKey AS Product,   
       OrderQuantity AS Qty,   
       SUM(OrderQuantity) OVER(PARTITION BY SalesOrderNumber) AS Total,  
       CAST(1. * OrderQuantity / SUM(OrderQuantity)   
        OVER(PARTITION BY SalesOrderNumber)   
            *100 AS DECIMAL(5,2)) AS PctByProduct  
FROM dbo.FactResellerSales   
WHERE SalesOrderNumber IN(N'SO43659',N'SO43664') AND  
      ProductKey LIKE '2%'  
ORDER BY SalesOrderNumber,ProductKey;  
```  
  
 此結果集的第一個開始為：  
  
 ```
 OrderNumber  Product  Qty  Total  PctByProduct  
 -----------  -------  ---  -----  ------------  
 SO43659      218      6    16     37.50  
 SO43659      220      4    16     25.00  
 SO43659      223      2    16     12.50  
 SO43659      229      2    16     18.75  
 ```
 
## <a name="see-also"></a>另請參閱  
 [彙總函數 &#40;Transact-SQL&#41;](../../t-sql/functions/aggregate-functions-transact-sql.md)   
 [分析函數 &#40;Transact-SQL&#41;](../../t-sql/functions/analytic-functions-transact-sql.md)   
 [Itzik Ben-Gan 在 sqlmag.com 上所發表有關視窗函數和 OVER 的精彩部落格文章](https://www.itprotoday.com/sql-server/how-use-microsoft-sql-server-2012s-window-functions-part-1) \(英文\)  
  
  
