---
description: FILEGROUP_ID (Transact-SQL)
title: FILEGROUP_ID (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FILEGROUP_ID_TSQL
- FILEGROUP_ID
dev_langs:
- TSQL
helpviewer_keywords:
- FILEGROUP_ID function
- identification numbers [SQL Server], filegroups
- filegroups [SQL Server], IDs
- IDs [SQL Server], filegroups
- names [SQL Server], filegroups
ms.assetid: 852a76d8-9e61-4a31-84ee-c7edb84a061c
author: cawrites
ms.author: chadam
ms.openlocfilehash: ac5ca26044fb7b6e0af151443cf7eed482ebb851
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092225"
---
# <a name="filegroup_id-transact-sql"></a>FILEGROUP_ID (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

此函數會傳回指定檔案群組名稱的檔案群組識別碼。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql 
FILEGROUP_ID ( 'filegroup_name' )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*filegroup_name* 這是 **sysname** 類型的運算式，代表會傳回其檔案群組識別碼 `FILEGROUP_ID` 的檔案群組名稱。  
  
## <a name="return-types"></a>傳回型別  
**int**  
  
## <a name="remarks"></a>備註  
*filegroup_name* 對應到 **sys.filegroups** 目錄檢視中的 **name** 資料行。  
  
## <a name="examples"></a>範例  
此範例會傳回 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `PRIMARY` 檔案群組的檔案群組識別碼。  
  
```sql  
SELECT FILEGROUP_ID('PRIMARY') AS [Filegroup ID];  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```
Filegroup ID  
------------  
1  

(1 row(s) affected)
 ```  
  
## <a name="see-also"></a>另請參閱  
 [FILEGROUP_NAME &#40;Transact-SQL&#41;](../../t-sql/functions/filegroup-name-transact-sql.md)   
 [中繼資料函數 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)   
 [sys.filegroups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-filegroups-transact-sql.md)  
  
  
