---
description: '&#x40;&#x40;CURSOR_ROWS (Transact-SQL)'
title: '@@CURSOR_ROWS (Transact-SQL) | Microsoft Docs'
ms.custom: ''
ms.date: 08/18/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- '@@CURSOR_ROWS'
- '@@CURSOR_ROWS_TSQL'
dev_langs:
- TSQL
helpviewer_keywords:
- '@@CURSOR_ROWS function'
- cursors [SQL Server], last-opened
- last-opened cursor
- asynchronous cursors [SQL Server]
ms.assetid: 31bd7a97-7f28-42a8-ba24-24d16d22973d
author: cawrites
ms.author: chadam
ms.openlocfilehash: 7df1f0df488313e5d13afe829ec25710477ffbcf
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097198"
---
# <a name="x40x40cursor_rows-transact-sql"></a>&#x40;&#x40;CURSOR_ROWS (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

這會傳回在連線所開啟的最後一個資料指標中，目前符合的資料列數。 若要提升效能，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可以非同步地擴展大型索引鍵集和靜態資料指標。 您可以呼叫 `@@CURSOR_ROWS` 來決定在呼叫 @@CURSOR_ROWS 時擷取資料指標適用的資料列數目。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
@@CURSOR_ROWS  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>傳回類型
**integer**
  
## <a name="return-value"></a>傳回值  
  
|傳回值|描述|  
|---|---|
|-*m*|資料指標非同步地擴展。 傳回的值 (-*m*) 是目前在索引鍵集中的資料列數。|  
|-1|資料指標是動態的。 由於動態資料指標會反映所有變更；因此，資料指標適用的資料列數會不斷改變。 資料指標不一定會擷取所有合格的資料列。|  
|0|未開啟任何資料列、最後開啟的資料指標沒有適合的資料列，或最後開啟的資料指標已關閉或取消配置。|  
|*n*|已充分擴展資料指標。 傳回的值 (*n*) 是資料指標中的總資料列數。|  
  
## <a name="remarks"></a>備註  
如果非同步地開啟最後一個資料指標，`@@CURSOR_ROWS` 會傳回負數。 如果 sp_configure 資料指標臨界值的值超過 0，且資料指標結果集中的資料列數超過資料指標臨界值，便會非同步地開啟索引鍵集驅動程式或靜態資料指標。
  
## <a name="examples"></a>範例  
此範例會先宣告一個資料指標，然後使用 `SELECT` 來顯示 `@@CURSOR_ROWS` 的值。 在資料指標開啟之前，這個設定值是 `0`，值 `-1` 表示資料指標索引鍵集非同步地擴展。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT @@CURSOR_ROWS;  
DECLARE Name_Cursor CURSOR FOR  
SELECT LastName ,@@CURSOR_ROWS FROM Person.Person;  
OPEN Name_Cursor;  
FETCH NEXT FROM Name_Cursor;  
SELECT @@CURSOR_ROWS;  
CLOSE Name_Cursor;  
DEALLOCATE Name_Cursor;  
GO             
```  
  
結果集如下。
  
```
-----------
0  
```

```
LastName
---------------
Sanchez
```

```
-----------
-1
```  
  
## <a name="see-also"></a>另請參閱
[資料指標函數 &#40;Transact-SQL&#41;](../../t-sql/functions/cursor-functions-transact-sql.md)  
[OPEN &#40;Transact-SQL&#41;](../../t-sql/language-elements/open-transact-sql.md)
  
  
