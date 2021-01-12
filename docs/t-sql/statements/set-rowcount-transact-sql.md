---
description: SET ROWCOUNT (Transact-SQL)
title: SET ROWCOUNT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SET_ROWCOUNT_TSQL
- ROWCOUNT_TSQL
- SET ROWCOUNT
- ROWCOUNT
dev_langs:
- TSQL
helpviewer_keywords:
- row return limitations [SQL Server]
- SET ROWCOUNT statement
- number of rows affected by statement
- ROWCOUNT option
- counting rows
- stopping queries
- limiting rows returned
- queries [SQL Server], stopping
ms.assetid: c6966fb7-6421-47ef-98f3-82351f2f6bdc
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: fca568e68a289943cc49324b45991120f0578eb2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091912"
---
# <a name="set-rowcount-transact-sql"></a>SET ROWCOUNT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  使 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在傳回指定的資料列數之後，停止處理查詢。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
SET ROWCOUNT { number | @number_var }   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *number* | @*number_var*  
 這是停止特定查詢之前所要處理的資料列數，這是一個整數。  
  
## <a name="remarks"></a>備註  
  
> [!IMPORTANT]  
>  使用 SET ROWCOUNT 不會影響將來 SQL Server 版本中的 DELETE、INSERT 和 UPDATE 陳述式。 請避免在新的開發工作中使用 SET ROWCOUNT 搭配 DELETE、INSERT 和 UPDATE 陳述式，並計畫修改目前正在使用它的應用程式。 如需類似的行為，請使用 TOP 語法。 如需詳細資訊，請參閱 [TOP &#40;Transact-SQL&#41;](../../t-sql/queries/top-transact-sql.md)。  
  
 若要將這個選項設為關閉，以便傳回所有資料列，請指定 SET ROWCOUNT 0。  
  
 設定 SET ROWCOUNT 選項會使大部分 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式在受到指定資料列數影響之後停止處理。 其中包括觸發程序。 ROWCOUNT 選項不會影響動態資料指標，但它會限制索引鍵集和非感應式資料指標之資料列集。 在使用這個選項時應該要特別小心。  
  
 如果資料列計數值比較小，SET ROWCOUNT 會覆寫 SELECT 陳述式 TOP 關鍵字。  
  
 SET ROWCOUNT 的設定是在執行階段進行設定，而不是在剖析階段進行設定。  
  
## <a name="permissions"></a>權限  
 需要 public 角色中的成員資格。  
  
## <a name="examples"></a>範例  
 SET ROWCOUNT 會在指定的資料列數之後停止處理。 在下列範例中，請注意超過 500 個資料列符合 `Quantity` 小於 `300` 的準則。 不過在套用 SET ROWCOUNT 之後，您會看出並未傳回所有資料列。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT count(*) AS Count  
FROM Production.ProductInventory  
WHERE Quantity < 300;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Count 
 ----------- 
 537 
 
 (1 row(s) affected)
 ```  
  
 現在將 `ROWCOUNT` 設為 `4`，而傳回所有資料列，則示範只傳回 4 個資料列。  
  
```sql
SET ROWCOUNT 4;  
SELECT *  
FROM Production.ProductInventory  
WHERE Quantity < 300;  
GO  
  
-- (4 row(s) affected)
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 SET ROWCOUNT 會在指定的資料列數之後停止處理。 在下列範例中，請注意，有超過 20 個資料列符合 `AccountType = 'Assets'` 的準則。 不過在套用 SET ROWCOUNT 之後，您會看出並未傳回所有資料列。  
  
```sql
-- Uses AdventureWorks  
  
SET ROWCOUNT 5;  
SELECT * FROM [dbo].[DimAccount]  
WHERE AccountType = 'Assets';  
```  
  
 若要傳回所有資料列，請將 ROWCOUNT 設為 0。  
  
```sql
-- Uses AdventureWorks  
  
SET ROWCOUNT 0;  
SELECT * FROM [dbo].[DimAccount]  
WHERE AccountType = 'Assets';  
```  
  
## <a name="see-also"></a>另請參閱  
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)  
  
  

