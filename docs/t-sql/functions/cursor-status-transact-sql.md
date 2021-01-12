---
description: CURSOR_STATUS (Transact-SQL)
title: CURSOR_STATUS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CURSOR_STATUS
- CURSOR_STATUS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- status information [SQL Server], cursors
- CURSOR_STATUS function
- cursors [SQL Server], status information
ms.assetid: 3a4a840e-04f8-43bd-aada-35d78c3cb6b0
author: cawrites
ms.author: chadam
ms.openlocfilehash: 853cae9252c43a176d208b7208f2ca595366a151
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093686"
---
# <a name="cursor_status-transact-sql"></a>CURSOR_STATUS (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

針對指定的參數，`CURSOR_STATUS` 會顯示資料指標宣告是否已傳回資料指標和結果集。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CURSOR_STATUS   
     (  
          { 'local' , 'cursor_name' }   
          | { 'global' , 'cursor_name' }   
          | { 'variable' , 'cursor_variable' }   
     )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
'local'  
指定一個常數來指示資料指標來源是本機資料指標名稱。
  
'*cursor_name*'  
資料指標的名稱。 資料指標名稱必須符合[資料庫識別碼規則](../../relational-databases/databases/database-identifiers.md)。
  
'global'  
指定一個常數來指示資料指標的來源是全域資料指標名稱。
  
'variable'  
指定一個常數來指示資料指標的來源是區域變數。
  
'*cursor_variable*'  
資料指標變數的名稱。 您必須使用 **cursor** 資料類型來定義資料指標變數。
  
## <a name="return-types"></a>傳回類型
**smallint**
  
|傳回值|資料指標名稱|資料指標變數|  
|---|---|---|
|1|資料指標結果集至少有一個資料列。<br /><br /> 非機密資料指標和索引鍵集資料指標，其結果集至少有一個資料列。<br /><br /> 動態資料指標的結果集可以有零、一或多個資料列。|配置給這個變數的資料指標是開啟的。<br /><br /> 非機密資料指標和索引鍵集資料指標，其結果集至少有一個資料列。<br /><br /> 動態資料指標的結果集可以有零、一或多個資料列。|  
|0|資料指標結果集是空的。*|配置給這個變數的資料指標是開啟的，但結果集確定空白。*|  
|-1|資料指標已關閉。|配置給這個變數的資料指標已關閉。|  
|-2|不適用。|可能有下列其中一種情況：<br /><br /> 先前呼叫的程序未指派任何資料指標給這個 OUTPUT 變數。<br /><br /> 先前指派的程序指派一個資料指標給這個 OUTPUT 變數，但程序完成時，這個資料指標在關閉狀態中。 因此，這個資料指標已取消配置，並未傳回給發出呼叫的程序。<br /><br /> 未指派任何資料指標給已宣告的資料指標變數。|  
|-3|含指定名稱的資料指標不存在。|含指定名稱的資料指標變數不存在，或這個變數存在，但還沒有配置資料指標。|  
  
*動態資料指標永遠不會傳回這個結果。
  
## <a name="examples"></a>範例  
此範例會使用 `CURSOR_STATUS` 函式來顯示資料指標在宣告後、開啟後及關閉後的狀態。
  
```sql
CREATE TABLE #TMP  
(  
   ii INT  
)  
GO  
  
INSERT INTO #TMP(ii) VALUES(1)  
INSERT INTO #TMP(ii) VALUES(2)  
INSERT INTO #TMP(ii) VALUES(3)  
  
GO  
  
--Create a cursor.  
DECLARE cur CURSOR  
FOR SELECT * FROM #TMP  
  
--Display the status of the cursor before and after opening  
--closing the cursor.  
  
SELECT CURSOR_STATUS('global','cur') AS 'After declare'  
OPEN cur  
SELECT CURSOR_STATUS('global','cur') AS 'After Open'  
CLOSE cur  
SELECT CURSOR_STATUS('global','cur') AS 'After Close'  
  
--Remove the cursor.  
DEALLOCATE cur  
  
--Drop the table.  
DROP TABLE #TMP  
  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
After declare
---------------
-1  
  
After Open
----------
1  
  
After Close
-----------
-1
```  
  
## <a name="see-also"></a>另請參閱
[資料指標函數 &#40;Transact-SQL&#41;](../../t-sql/functions/cursor-functions-transact-sql.md)  
[資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)
  
  
