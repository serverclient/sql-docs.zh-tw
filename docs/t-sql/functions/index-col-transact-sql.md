---
description: INDEX_COL (Transact-SQL)
title: INDEX_COL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- INDEX_COL_TSQL
- INDEX_COL
dev_langs:
- TSQL
helpviewer_keywords:
- displaying column names
- INDEX_COL function
- viewing column names
- column names [SQL Server]
- names [SQL Server], columns
ms.assetid: 4db1fb3b-e46f-43fb-b269-a5b6e8b39ed0
author: cawrites
ms.author: chadam
ms.openlocfilehash: 4e1b37288462aea0eaeaab0a8299c5cfe6886723
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093555"
---
# <a name="index_col-transact-sql"></a>INDEX_COL (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回索引資料行名稱。 如果是 XML 索引，則傳回 NULL。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
INDEX_COL ( '[ database_name . [ schema_name ] .| schema_name ]  
    table_or_view_name', index_id , key_id )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *database_name*  
 這是資料庫的名稱。  
  
 *schema_name*  
 這是索引所屬的結構描述名稱。  
  
 *table_or_view_name*  
 這是資料表或索引檢視的名稱。 *table_or_view_name* 必須以單引號分隔，而且可以用資料庫名稱和結構描述名稱完整限定。  
  
 *index_id*  
 這是索引的識別碼。 *index_ID* 是 **int**。  
  
 *key_id*  
 這是索引鍵資料行位置。 *key_ID* 是 **int**。  
  
## <a name="return-types"></a>傳回型別  
 **nvarchar (128** **)**  
  
## <a name="exceptions"></a>例外狀況  
 當發生錯誤，或呼叫端沒有檢視物件的權限時，便會傳回 NULL。  
  
 使用者只能檢視使用者擁有或被授與某些權限之安全性實體的中繼資料。 這表示發出中繼資料的內建函數 (例如，INDEX_COL) 會在使用者不具有該物件任何權限時傳回 NULL。 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-index_col-to-return-an-index-column-name"></a>A. 利用 INDEX_COL 傳回索引資料行名稱  
 下列範例會傳回 `PK_SalesOrderDetail_SalesOrderID_LineNumber` 索引中兩個索引鍵資料行的資料行名稱。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT   
    INDEX_COL (N'AdventureWorks2012.Sales.SalesOrderDetail', 1,1) AS  
        [Index Column 1],   
    INDEX_COL (N'AdventureWorks2012.Sales.SalesOrderDetail', 1,2) AS  
        [Index Column 2]  
;  
GO  
```  
  
 以下為結果集：  
  
```  
Index Column 1      Index Column 2  
-----------------------------------------------  
SalesOrderID        SalesOrderDetailID  
```  
  
## <a name="see-also"></a>另請參閱  
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [中繼資料函數 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
 [sys.index_columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-index-columns-transact-sql.md)  
  
  
