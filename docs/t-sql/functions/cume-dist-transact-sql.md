---
description: CUME_DIST (Transact-SQL)
title: CUME_DIST (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: sql-data-warehouse, database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CUME_DIST
- CUME_DIST_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CUME_DIST function
- analytic functions, CUME_DIST
ms.assetid: 491b07f3-9ffd-4cdd-93e5-5abb636fc5ef
author: cawrites
ms.author: chadam
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 4252c237e234fdc380ca0f4b18cfcf55f6bf9ae4
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093718"
---
# <a name="cume_dist-transact-sql"></a>CUME_DIST (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，此函數會計算值群組中，某值的累加分配。 換言之，`CUME_DIST` 會計算值群組中，某一指定值的相對位置。 假設採取遞增排序，則資料列 _r_ 中，某值的 `CUME_DIST` 是定義為有多少個資料列的值是小於或等於資料列 _r_ 中的值，除以資料分割或查詢結果集中計算得出的資料列數。 `CUME_DIST` 類似於 `PERCENT_RANK` 函數。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CUME_DIST( )  
    OVER ( [ partition_by_clause ] order_by_clause )  
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
OVER **(** [ _partition\_by\_clause_ ] _order\_by\_clause_)  

_partition\_by\_clause_ 會將 FROM 子句結果集分割成函式所要套用的資料分割。 如未指定 _partition\_by\_clause_ 引數，則 `CUME_DIST` 會將所有查詢結果集資料列視為單一群組。 _order\_by\_clause_ 會決定作業的執行邏輯順序。 `CUME_DIST` 需要 _order\_by\_clause_。 `CUME_DIST` 不接受 OVER 語法的 \<rows or range clause>。 如需詳細資訊，請參閱 [OVER 子句 &#40;Transact-SQL&#41;](../../t-sql/queries/select-over-clause-transact-sql.md)。
  
## <a name="return-types"></a>傳回類型
**float(53)**
  
## <a name="remarks"></a>備註  
`CUME_DIST` 傳回某一範圍的值，且這些值大於 0 且小於或等於 1。 繫結值的計算所得一律會是相同的累計分佈值。 `CUME_DIST` 預設會包含 NULL 值，並將其視為最低值。
  
`CUME_DIST` 不具決定性。 如需詳細資訊，請參閱 [決定性與非決定性函數](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)。
  
## <a name="examples"></a>範例  
此範例使用 `CUME_DIST` 函數來計算指定部門各員工的薪資百分位數。 `CUME_DIST` 會傳回一個值，它代表有多少百分比的員工，其薪資要比同部門當前的員工要來得低或持平。 `PERCENT_RANK` 函數會計算該員工薪資在部門中的排名百分比。 為了按部門分割結果集資料列，此範例會指定 _partition\_by\_clause_ 值。 OVER 子句中的 ORDER BY 子句會將每個資料分割中的資料列，以邏輯方式排序。 SELECT 陳述式的 ORDER BY 子句會決定結果集的顯示排序。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT Department, LastName, Rate,   
       CUME_DIST () OVER (PARTITION BY Department ORDER BY Rate) AS CumeDist,   
       PERCENT_RANK() OVER (PARTITION BY Department ORDER BY Rate ) AS PctRank  
FROM HumanResources.vEmployeeDepartmentHistory AS edh  
    INNER JOIN HumanResources.EmployeePayHistory AS e    
    ON e.BusinessEntityID = edh.BusinessEntityID  
WHERE Department IN (N'Information Services',N'Document Control')   
ORDER BY Department, Rate DESC;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Department             LastName               Rate                  CumeDist               PctRank  
---------------------- ---------------------- --------------------- ---------------------- ----------------------  
Document Control       Arifin                 17.7885               1                      1  
Document Control       Norred                 16.8269               0.8                    0.5  
Document Control       Kharatishvili          16.8269               0.8                    0.5  
Document Control       Chai                   10.25                 0.4                    0  
Document Control       Berge                  10.25                 0.4                    0  
Information Services   Trenary                50.4808               1                      1  
Information Services   Conroy                 39.6635               0.9                    0.888888888888889  
Information Services   Ajenstat               38.4615               0.8                    0.666666666666667  
Information Services   Wilson                 38.4615               0.8                    0.666666666666667  
Information Services   Sharma                 32.4519               0.6                    0.444444444444444  
Information Services   Connelly               32.4519               0.6                    0.444444444444444  
Information Services   Berg                   27.4038               0.4                    0  
Information Services   Meyyappan              27.4038               0.4                    0  
Information Services   Bacon                  27.4038               0.4                    0  
Information Services   Bueno                  27.4038               0.4                    0  
(15 row(s) affected)  
```  
  
## <a name="see-also"></a>另請參閱
[PERCENT_RANK &#40;Transact-SQL&#41;](../../t-sql/functions/percent-rank-transact-sql.md)
  
  
