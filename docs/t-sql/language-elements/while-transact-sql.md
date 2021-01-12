---
description: WHILE (Transact-SQL)
title: WHILE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- WHILE_TSQL
- WHILE
dev_langs:
- TSQL
helpviewer_keywords:
- statements [SQL Server], repeated executions
- statement blocks [SQL Server]
- repeated statement executions
- nested WHILE loops
- WHILE keyword
ms.assetid: 52dd29ab-25d7-4fd3-a960-ac55c30c9ea9
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7529dc6a101b97fc89ae08e6445f8333811a9c23
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100341"
---
# <a name="while-transact-sql"></a>WHILE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]


  設定重複執行 SQL 陳述式或陳述式區塊的條件。 只要符合指定的條件，就會重複執行這些陳述式。 您可以在迴圈內，利用 BREAK 和 CONTINUE 關鍵字來控制 WHILE 迴圈陳述式的執行情況。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
WHILE Boolean_expression   
     { sql_statement | statement_block | BREAK | CONTINUE }  
  
```  
  
```syntaxsql
-- Syntax for Azure Azure Synapse Analytics and Parallel Data Warehouse  
  
WHILE Boolean_expression   
     { sql_statement | statement_block | BREAK }  
  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *Boolean_expression*  
 這是傳回 **TRUE** 或 **FALSE** 的 [運算式](../../t-sql/language-elements/expressions-transact-sql.md)。 如果布林運算式包含 SELECT 陳述式，則這個 SELECT 陳述式必須括在括號中。  
  
 {*sql_statement* | *statement_block*}  
 這是利用陳述式區塊來定義的任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式或陳述式分組。 若要定義陳述式區塊，請使用流程控制關鍵字 BEGIN 和 END。  
  
 BREAK  
 結束最內層的 WHILE 迴圈。 將執行出現在 END 關鍵字 (表示迴圈結束) 之後的任何陳述式。  
  
 CONTINUE  
 重新啟動 WHILE 迴圈，忽略 CONTINUE 關鍵字之後的任何陳述式。  
  
## <a name="remarks"></a>備註  
 如果一個或多個 WHILE 迴圈具有巢狀結構，內層的 BREAK 會跳到下一個最外層的迴圈。 內層迴圈尾端之後的所有陳述式會先執行，然後重新啟動下一個最外層迴圈。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-break-and-continue-with-nested-ifelse-and-while"></a>A. 使用 BREAK 和 CONTINUE 搭配巢狀 IF...ELSE 和 WHILE  
 在下列範例中，如果產品的平均標價小於 `$300`，`WHILE` 迴圈會將標價加倍，再選取最大價格。 如果最大價格小於或等於 `$500`，`WHILE` 迴圈會重新啟動，價格會再加倍。 這個迴圈會繼續使價格加倍，直到最大價格大於 `$500`，再結束 `WHILE` 迴圈及列印訊息。  
  
```sql  
USE AdventureWorks2012;  
GO  
WHILE (SELECT AVG(ListPrice) FROM Production.Product) < $300  
BEGIN  
   UPDATE Production.Product  
      SET ListPrice = ListPrice * 2  
   SELECT MAX(ListPrice) FROM Production.Product  
   IF (SELECT MAX(ListPrice) FROM Production.Product) > $500  
      BREAK  
   ELSE  
      CONTINUE  
END  
PRINT 'Too much for the market to bear';  
```  
  
### <a name="b-using-while-in-a-cursor"></a>B. 在資料指標中使用 WHILE  
 下列範例使用 `@@FETCH_STATUS` 控制 `WHILE` 迴圈中的資料指標活動。  
  
```sql  
DECLARE @EmployeeID as NVARCHAR(256)
DECLARE @Title as NVARCHAR(50)

DECLARE Employee_Cursor CURSOR FOR  
SELECT LoginID, JobTitle   
FROM AdventureWorks2012.HumanResources.Employee  
WHERE JobTitle = 'Marketing Specialist';  
OPEN Employee_Cursor;  
FETCH NEXT FROM Employee_Cursor INTO @EmployeeID, @Title;  
WHILE @@FETCH_STATUS = 0  
   BEGIN  
      Print '   ' + @EmployeeID + '      '+  @Title 
      FETCH NEXT FROM Employee_Cursor INTO @EmployeeID, @Title;  
   END;  
CLOSE Employee_Cursor;  
DEALLOCATE Employee_Cursor;  
GO 
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="c-simple-while-loop"></a>C：簡單的 While 迴圈  
 在下列範例中，如果產品的平均標價小於 `$300`，`WHILE` 迴圈會將標價加倍，再選取最大價格。 如果最大價格小於或等於 `$500`，`WHILE` 迴圈會重新啟動，價格會再加倍。 這個迴圈會繼續使價格加倍，直到最高價格大於 `$500` 為止，然後結束 `WHILE` 迴圈。  
  
```sql  
-- Uses AdventureWorks  
  
WHILE ( SELECT AVG(ListPrice) FROM dbo.DimProduct) < $300  
BEGIN  
    UPDATE dbo.DimProduct  
        SET ListPrice = ListPrice * 2;  
    SELECT MAX ( ListPrice) FROM dbo.DimProduct  
    IF ( SELECT MAX (ListPrice) FROM dbo.DimProduct) > $500  
        BREAK;  
END  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/alter-trigger-transact-sql.md)   
 [流程控制語言 &#40;Transact-SQL&#41;](~/t-sql/language-elements/control-of-flow.md)   
 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)   
 [資料指標 &#40;Transact-SQL&#41;](../../t-sql/language-elements/cursors-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)  
  
  


