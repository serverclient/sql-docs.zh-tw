---
description: OPEN (Transact-SQL)
title: OPEN (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- OPEN_TSQL
- OPEN
dev_langs:
- TSQL
helpviewer_keywords:
- opening cursors
- cursors [SQL Server], opening
- populating cursors [SQL Server]
- OPEN statement
- Transact-SQL cursors, opening
ms.assetid: fd1c5e3b-6045-4a42-b646-3fca76e874c1
author: cawrites
ms.author: chadam
ms.openlocfilehash: c0cab0ea4ed5eecc224c478018c16bf9d078d02f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102398"
---
# <a name="open-transact-sql"></a>OPEN (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  開啟 [!INCLUDE[tsql](../../includes/tsql-md.md)] 伺服器資料指標，並執行 DECLARE CURSOR 或 SET *cursor_variable* 陳述式上指定的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式來擴展資料指標。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
OPEN { { [ GLOBAL ] cursor_name } | cursor_variable_name }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 GLOBAL  
 指定 *cursor_name* 是全域資料指標。  
  
 *cursor_name*  
 這是已宣告的資料指標名稱。 如果全域和本機資料指標同時存在且名稱是 *cursor_name*，若指定了 GLOBAL，*cursor_name* 就是全域資料指標；否則，*cursor_name* 是本機資料指標。  
  
 *cursor_variable_name*  
 這是參考資料指標之資料指標變數的名稱。  
  
## <a name="remarks"></a>備註  
 如果是用 INSENSITIVE 或 STATIC 選項來宣告資料指標，OPEN 會建立一份暫存資料表來存放結果集。 當結果集中任何資料列的大小超出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料表的最大資料列大小時，OPEN 會失敗。 如果是用 KEYSET 選項來宣告資料指標，OPEN 會建立一份暫存資料表來存放索引鍵集。 暫存資料表儲存在 tempdb 中。  
  
 開啟資料指標之後，利用 @@CURSOR_ROWS 函數來擷取最後開啟之資料指標中符合的資料列數。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不支援非同步產生索引鍵集驅動或靜態 [!INCLUDE[tsql](../../includes/tsql-md.md)] 資料指標。 [!INCLUDE[tsql](../../includes/tsql-md.md)] 資料指標作業是以批次方式來處理，因此，不需要非同步產生 [!INCLUDE[tsql](../../includes/tsql-md.md)] 資料指標。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會繼續支援非同步索引鍵集驅動或靜態應用程式開發介面 (API) 伺服器資料指標，其中低度延遲 OPEN 會是一個顧慮，因為每一個資料指標作業都需要用戶端往返。  
  
## <a name="examples"></a>範例  
 下列範例會開啟一個資料指標，且會提取所有資料列。  
  
```sql  
DECLARE Employee_Cursor CURSOR FOR  
SELECT LastName, FirstName  
FROM AdventureWorks2012.HumanResources.vEmployee  
WHERE LastName like 'B%';  
  
OPEN Employee_Cursor;  
  
FETCH NEXT FROM Employee_Cursor;  
WHILE @@FETCH_STATUS = 0  
BEGIN  
    FETCH NEXT FROM Employee_Cursor  
END;  
  
CLOSE Employee_Cursor;  
DEALLOCATE Employee_Cursor;  
```  
  
## <a name="see-also"></a>另請參閱  
 [CLOSE &#40;Transact-SQL&#41;](../../t-sql/language-elements/close-transact-sql.md)   
 [@@CURSOR_ROWS &#40;Transact-SQL&#41;](../../t-sql/functions/cursor-rows-transact-sql.md)   
 [DEALLOCATE &#40;Transact-SQL&#41;](../../t-sql/language-elements/deallocate-transact-sql.md)   
 [DECLARE CURSOR &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-cursor-transact-sql.md)   
 [FETCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/fetch-transact-sql.md)  
  
  
