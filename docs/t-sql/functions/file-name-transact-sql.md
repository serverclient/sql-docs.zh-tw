---
description: FILE_NAME (Transact-SQL)
title: FILE_NAME (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FILE_NAME_TSQL
- FILE_NAME
dev_langs:
- TSQL
helpviewer_keywords:
- viewing file names
- file names [SQL Server], FILE_NAME
- IDs [SQL Server], files
- file IDs [SQL Server]
- names [SQL Server], files
- displaying file names
- identification numbers [SQL Server], files
- FILE_NAME function
- logical file names [SQL Server]
ms.assetid: 68b298aa-ce47-4af5-b59f-9a1b46d48326
author: cawrites
ms.author: chadam
ms.openlocfilehash: 6f5588c385e9118469db95ee0de202711f5a8e3e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092258"
---
# <a name="file_name-transact-sql"></a>FILE_NAME (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

此函式會傳回給定之檔案識別碼 (ID) 的邏輯檔案名稱。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
FILE_NAME ( file_id )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*file_id*  
會傳回其檔案名稱 `FILE_NAME` 的檔案識別碼。 *file_id* 具有 **int** 資料類型。  
  
## <a name="return-types"></a>傳回型別  
**nvarchar(128)**  
  
## <a name="remarks"></a>備註  
*file_ID* 對應於 sys.master_files 目錄檢視或 sys.database_files 目錄檢視中的 file_id 資料行。  
  
## <a name="examples"></a>範例  
此範例會傳回 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `file_ID 1` 和 `file_ID` 的檔案名稱。  
  
```sql  
SELECT FILE_NAME(1) AS 'File Name 1', FILE_NAME(2) AS 'File Name 2';  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
File Name 1                File Name 2  
-------------------------  ------------------------  
AdventureWorks2012_Data    AdventureWorks2012_Log  

(1 row(s) affected)
``` 
  
## <a name="see-also"></a>另請參閱  
 [FILE_IDEX &#40;Transact-SQL&#41;](../../t-sql/functions/file-idex-transact-sql.md)   
 [中繼資料函數 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)   
 [sys.database_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)   
 [sys.master_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)  
  
