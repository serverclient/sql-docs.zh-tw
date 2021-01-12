---
description: FILE_IDEX (Transact-SQL)
title: FILE_IDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FILE_IDEX
- FILE_IDEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- FILE_IDEX function
- IDs [SQL Server], files
- file IDs [SQL Server]
- names [SQL Server], files
- identification numbers [SQL Server], files
- file names [SQL Server], FILE_IDEX
ms.assetid: 7532fea5-ee5e-4edd-b98b-111a7ba56c8e
author: cawrites
ms.author: chadam
ms.openlocfilehash: 0f7739a422e82e496c772454bb596980497adef3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092298"
---
# <a name="file_idex-transact-sql"></a>FILE_IDEX (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

此函數會傳回目前資料庫中資料、記錄或全文檢索檔案之指定邏輯資料名稱的檔案識別碼 (ID)。 
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
FILE_IDEX ( file_name )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *file_name*  
類型是 **sysname** 的運算式，此運算式會傳回檔案名稱的檔案識別碼值 'FILE_IDEX'。 
  
## <a name="return-types"></a>傳回型別  
**int**  
  
發生錯誤時傳回 **NULL**  
  
## <a name="remarks"></a>備註  
*file_name* 對應至 [sys.master_files](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md) 或 [sys.database_files](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)目錄檢視 **name** 資料行中所顯示的邏輯檔案名稱。  
  
您可以在 SELECT 清單、WHERE 子句或支援使用運算式的任何位置使用 `FILE_IDEX`。 如需詳細資訊，請參閱[運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
## <a name="examples"></a>範例  
  
### <a name="a-retrieving-the-file-id-of-a-specified-file"></a>A. 擷取指定檔案的檔案識別碼  
此範例會傳回 `AdventureWorks_Data` 檔案的檔案識別碼。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT FILE_IDEX('AdventureWorks2012_Data') AS 'File ID';  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
File ID   
-------   
1  
(1 row(s) affected)  
```  
  
### <a name="b-retrieving-the-file-id-when-the-file-name-is-not-known"></a>B. 當檔案名稱未知時，擷取檔案識別碼  
此範例會傳回 `AdventureWorks` 記錄檔的檔案識別碼。 Transact-SQL (T-SQL) 程式碼片段會從 `sys.database_files` 目錄檢視選取邏輯檔案名稱，其中檔案類型等於 `1` (記錄檔)。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT FILE_IDEX((SELECT TOP (1) name FROM sys.database_files WHERE type = 1)) AS 'File ID';  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
File ID   
-------   
2  
```  
  
### <a name="c-retrieving-the-file-id-of-a-full-text-catalog-file"></a>C. 擷取全文檢索目錄檔的檔案識別碼  
此範例會傳回全文檢索檔案的檔案識別碼。 T-SQL 程式碼片段會從 `sys.database_files` 目錄檢視選取邏輯檔案名稱，其中檔案類型等於 `4` (全文檢索)。 如果全文檢索目錄不存在，此程式碼將會傳回 'NULL'。
  
```sql  
SELECT FILE_IDEX((SELECT name FROM sys.master_files WHERE type = 4))  
AS 'File_ID';  
```  
  
## <a name="see-also"></a>另請參閱  
 [中繼資料函數 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)   
 [sys.database_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)   
 [sys.master_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)  
  
  
