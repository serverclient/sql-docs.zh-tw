---
description: '&#x40;&#x40;FETCH_STATUS (Transact-SQL)'
title: FETCH_STATUS (Transact-SQL)
ms.custom: ''
ms.date: 09/18/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- '@@FETCH_STATUS'
- '@@FETCH_STATUS_TSQL'
dev_langs:
- TSQL
helpviewer_keywords:
- FETCH statement
- status information [SQL Server], FETCH
- '@@FETCH_STATUS function'
ms.assetid: 93659193-e4ff-4dfb-9043-0c4114921b91
author: cawrites
ms.author: chadam
ms.openlocfilehash: ef3c22679bed84906f325095f28155dd34f16a09
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092315"
---
# <a name="x40x40fetch_status-transact-sql"></a>&#x40;&#x40;FETCH_STATUS (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

此函數會傳回針對連接目前開啟的任何資料指標所發出的最後一個資料指標 FETCH 陳述式的狀態。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
@@FETCH_STATUS  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-type"></a>傳回類型  
 **integer**  
  
## <a name="return-value"></a>傳回值  
  
|傳回值|描述|  
|------------------|-----------------|  
|&nbsp;0|FETCH 陳述式成功。|  
|-1|FETCH 陳述式失敗，或資料列已超出結果集。|  
|-2|遺漏提取的資料列。|
|-9|資料指標並未執行擷取作業。|  
  
## <a name="remarks"></a>備註  
由於 `@@FETCH_STATUS` 在連線的所有資料指標的全域範圍內有效，因此，請小心使用。 在執行 FETCH 陳述式之後，`@@FETCH_STATUS` 的測試必須在針對另一個資料指標執行任何其他 FETCH 陳述式之前進行。 在連線進行任何擷取之前，並未定義 `@@FETCH_STATUS`。  
  
例如，使用者從一個資料指標執行 FETCH 陳述式，然後再呼叫預存程序來開啟和處理來自另一個資料指標的結果。 當控制權從所呼叫的預存程序傳回時，`@@FETCH_STATUS` 會反映預存程序中所執行的最後一個 FETCH，而不是在呼叫預存程序之前執行的 FETCH 陳述式。  
  
若要擷取特定資料指標的最後一個擷取狀態，請查詢 **sys.dm_exec_cursors** 動態管理函式的 **fetch_status** 資料行。  
  
## <a name="examples"></a>範例  
此範例使用 `@@FETCH_STATUS` 控制 `WHILE` 迴圈中的資料指標活動。  
  
```sql  
DECLARE Employee_Cursor CURSOR FOR  
SELECT BusinessEntityID, JobTitle  
FROM AdventureWorks2012.HumanResources.Employee;  
OPEN Employee_Cursor;  
FETCH NEXT FROM Employee_Cursor;  
WHILE @@FETCH_STATUS = 0  
   BEGIN  
      FETCH NEXT FROM Employee_Cursor;  
   END;  
CLOSE Employee_Cursor;  
DEALLOCATE Employee_Cursor;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [資料指標函數 &#40;Transact-SQL&#41;](../../t-sql/functions/cursor-functions-transact-sql.md)   
 [FETCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/fetch-transact-sql.md)  
  
  
