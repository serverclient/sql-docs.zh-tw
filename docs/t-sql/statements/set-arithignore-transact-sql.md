---
description: SET ARITHIGNORE (Transact-SQL)
title: SET ARITHIGNORE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/04/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SET ARITHIGNORE
- SET_ARITHIGNORE_TSQL
- ARITHIGNORE
- ARITHIGNORE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- SET ARITHIGNORE statement
- overflow errors [SQL Server]
- ARITHIGNORE option
- divide-by-zero errors
ms.assetid: 71b2c2a5-c83a-4dfe-8469-237987a6e503
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: edb7e4a13ad5339cedf3ca465453fe158c42b668
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091892"
---
# <a name="set-arithignore-transact-sql"></a>SET ARITHIGNORE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  控制在查詢期間，是從溢位或除以零的錯誤傳回錯誤訊息。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database

SET ARITHIGNORE { ON | OFF }
```

```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  

SET ARITHIGNORE OFF
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="remarks"></a>備註
 SET ARITHIGNORE 設定只會控制是否傳回錯誤訊息。 在包含溢位或除以零的錯誤之計算中，不論這項設定為何，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 都會傳回 NULL。 您可以利用 SET ARITHABORT 設定來判斷查詢是否終止。 這項設定不會影響 INSERT、UPDATE 和 DELETE 陳述式期間所發生的錯誤。  
  
 如果 SET ARITHABORT 或 SET ARITHIGNORE 是 OFF，而 SET ANSI_WARNINGS 是 ON，當發現除以零或溢位的錯誤時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回錯誤訊息。  
  
 SET ARITHIGNORE 的設定是在執行階段進行設定，而不是在剖析階段進行設定。  
  
 若要檢視此設定的目前設定，請執行下列查詢。  
  
```sql  
DECLARE @ARITHIGNORE VARCHAR(3) = 'OFF';  
IF ( (128 & @@OPTIONS) = 128 ) SET @ARITHIGNORE = 'ON';  
SELECT @ARITHIGNORE AS ARITHIGNORE;  
```  
  
## <a name="permissions"></a>權限  
 需要 public 角色中的成員資格。  
  
## <a name="examples"></a>範例  
 下列範例會示範如何搭配這兩類查詢錯誤來使用這兩個 `SET ARITHIGNORE` 設定。  
  
```sql  
SET ARITHABORT OFF;  
SET ANSI_WARNINGS OFF  
GO  
  
PRINT 'Setting ARITHIGNORE ON';  
GO  
-- SET ARITHIGNORE ON and testing.  
SET ARITHIGNORE ON;  
GO  
SELECT 1 / 0 AS DivideByZero;  
GO  
SELECT CAST(256 AS TINYINT) AS Overflow;  
GO  
  
PRINT 'Setting ARITHIGNORE OFF';  
GO  
-- SET ARITHIGNORE OFF and testing.  
SET ARITHIGNORE OFF;  
GO  
SELECT 1 / 0 AS DivideByZero;  
GO  
SELECT CAST(256 AS TINYINT) AS Overflow;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
 下列範例會示範除以零和溢位錯誤。 這個範例不會傳回這些錯誤的錯誤訊息，因為 ARITHIGNORE 是 OFF。  
  
```sql  
-- SET ARITHIGNORE OFF and testing.  
SET ARITHIGNORE OFF;  
SELECT 1 / 0 AS DivideByZero;  
SELECT CAST(256 AS TINYINT) AS Overflow;  
```  
  
## <a name="see-also"></a>另請參閱  
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET ARITHABORT &#40;Transact-SQL&#41;](../../t-sql/statements/set-arithabort-transact-sql.md)  
  
  

