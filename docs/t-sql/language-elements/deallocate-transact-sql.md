---
description: DEALLOCATE (Transact-SQL)
title: DEALLOCATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DEALLOCATE
- DEALLOCATE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- locking [SQL Server], cursors
- DEALLOCATE statement
- deallocations [SQL Server]
- deleting cursor references
- removing cursor references
ms.assetid: c75cf73d-0268-4c57-973d-b8a84ff801fa
author: cawrites
ms.author: chadam
ms.openlocfilehash: af7031fbcfaf4efacbe4f3692547635030049c64
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093514"
---
# <a name="deallocate-transact-sql"></a>DEALLOCATE (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  移除資料指標參考。 取消配置最後一個資料指標參考時，[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會釋出組成資料指標的資料結構。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DEALLOCATE { { [ GLOBAL ] cursor_name } | @cursor_variable_name }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *cursor_name*  
 這是已宣告的資料指標名稱。 如果全域和本機資料指標同時存在且名稱是 *cursor_name*，若指定了 `GLOBAL`，*cursor_name* 就是全域資料指標，如果未指定 `GLOBAL`，則為區域資料指標。  
  
 @*cursor_variable_name*  
 這是 **資料指標** 變數的名稱。 @*cursor_variable_name* 的類型必須是 **cursor**。  
  
## <a name="remarks"></a>備註  
處理資料指標的陳述式會利用資料指標名稱或資料指標變數來參考資料指標。 `DEALLOCATE` 會移除資料指標和資料指標名稱或資料指標變數之間的關聯。 如果名稱或變數是最後一個參考資料指標的項目，就會取消配置資料指標，釋出資料指標所用的任何資源。 在 `DEALLOCATE` 時，會釋出用來保護擷取隔離的捲動鎖定。 用來保護更新 (其中包括透過資料指標所進行的定位更新) 的交易鎖定，會保留到交易結束時。  
  
`DECLARE CURSOR` 陳述式會配置資料指標並將它和資料指標產生關聯。  
  
```sql  
DECLARE abc SCROLL CURSOR FOR  
SELECT * FROM Person.Person;  
```  
  
在資料指標名稱與資料指標產生關聯之後，直到將這個資料指標取消配置之前，相同範圍 (全域或區域) 的其他資料指標不能使用這個名稱。  
  
 資料指標變數會利用兩種方法之一來關聯於資料指標：  
  
-   依名稱，利用 `SET` 陳述式將資料指標設為資料指標變數。  
  
    ```sql  
    DECLARE @MyCrsrRef CURSOR;  
    SET @MyCrsrRef = abc;  
    ```  
  
-   不需要定義資料指標名稱，也可以建立資料指標以及將資料指標與變數產生關聯。  
  
    ```sql  
    DECLARE @MyCursor CURSOR;  
    SET @MyCursor = CURSOR LOCAL SCROLL FOR  
    SELECT * FROM Person.Person;  
    ```  
  
 `DEALLOCATE <@cursor_variable_name>` 陳述式只會移除具名變數對資料指標的參考。 要等到批次、預存程序或觸發程序結束而離開範圍之後，此變數才會取消配置。 在 `DEALLOCATE <@cursor_variable_name>` 陳述式之後，您可以利用 SET 陳述式，將此變數與另一個資料指標產生關聯。  
  
```sql  
USE AdventureWorks2012;  
GO  
  
DECLARE @MyCursor CURSOR;  
SET @MyCursor = CURSOR LOCAL SCROLL FOR  
    SELECT * FROM Sales.SalesPerson;  
  
DEALLOCATE @MyCursor;  
  
SET @MyCursor = CURSOR LOCAL SCROLL FOR  
    SELECT * FROM Sales.SalesTerritory;  
GO  
```  
  
您不需要明確取消配置資料指標變數。 當變數離開範圍時，會隱含地取消配置。  
  
## <a name="permissions"></a>權限  
 `DEALLOCATE` 的權限預設會授與任何有效使用者。  
  
## <a name="examples"></a>範例  
 下列指令碼會顯示資料指標如何保存，直到最後一個名稱或參考這些資料指標的變數取消配置為止。  
  
```sql  
USE AdventureWorks2012;  
GO  
-- Create and open a global named cursor that  
-- is visible outside the batch.  
DECLARE abc CURSOR GLOBAL SCROLL FOR  
    SELECT * FROM Sales.SalesPerson;  
OPEN abc;  
GO  
-- Reference the named cursor with a cursor variable.  
DECLARE @MyCrsrRef1 CURSOR;  
SET @MyCrsrRef1 = abc;  
-- Now deallocate the cursor reference.  
DEALLOCATE @MyCrsrRef1;  
-- Cursor abc still exists.  
FETCH NEXT FROM abc;  
GO  
-- Reference the named cursor again.  
DECLARE @MyCrsrRef2 CURSOR;  
SET @MyCrsrRef2 = abc;  
-- Now deallocate cursor name abc.  
DEALLOCATE abc;  
-- Cursor still exists, referenced by @MyCrsrRef2.  
FETCH NEXT FROM @MyCrsrRef2;  
-- Cursor finally is deallocated when last referencing  
-- variable goes out of scope at the end of the batch.  
GO  
-- Create an unnamed cursor.  
DECLARE @MyCursor CURSOR;  
SET @MyCursor = CURSOR LOCAL SCROLL FOR  
SELECT * FROM Sales.SalesTerritory;  
-- The following statement deallocates the cursor  
-- because no other variables reference it.  
DEALLOCATE @MyCursor;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [CLOSE &#40;Transact-SQL&#41;](../../t-sql/language-elements/close-transact-sql.md)   
 [資料指標](../../relational-databases/cursors.md)   
 [DECLARE @local_variable &#40;Transact-SQL&#41;](../../t-sql/language-elements/declare-local-variable-transact-sql.md)   
 [FETCH &#40;Transact-SQL&#41;](../../t-sql/language-elements/fetch-transact-sql.md)   
 [OPEN &#40;Transact-SQL&#41;](../../t-sql/language-elements/open-transact-sql.md)  
  
  
