---
description: DROP EXTERNAL FILE FORMAT (Transact-SQL)
title: DROP EXTERNAL FILE FORMAT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/07/2017
ms.prod: sql
ms.prod_service: sql-data-warehouse, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: 8cf9009b-59f9-4aac-bef1-dcf2cf0708b2
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b3bb2aa5bff79c4e3f256ab999511249e3daca76
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095690"
---
# <a name="drop-external-file-format-transact-sql"></a>DROP EXTERNAL FILE FORMAT (Transact-SQL)
[!INCLUDE [sqlserver2016-asdbmi-asa-pdw](../../includes/applies-to-version/sqlserver2016-asdbmi-asa-pdw.md)]

  移除 PolyBase 外部檔案格式。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Drop an external file format  
DROP EXTERNAL FILE FORMAT external_file_format_name  
[;]  
```  
  
## <a name="arguments"></a>引數  
 *external_file_format_name*  
 要卸除的外部檔案格式名稱。  
  
## <a name="metadata"></a>中繼資料  
 若要檢視外部檔案格式清單，請使用 [sys.external_file_formats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-external-file-formats-transact-sql.md) 系統檢視。  
  
```sql  
SELECT * FROM sys.external_file_formats;  
```  
  
## <a name="permissions"></a>權限  
 需要 ALTER ANY EXTERNAL FILE FORMAT。  
  
## <a name="general-remarks"></a>一般備註  
 卸除外部檔案格式並不會移除外部資料。  
  
## <a name="locking"></a>鎖定  
 在外部檔案格式物件上採取共用鎖定。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-basic-syntax"></a>A. 使用基本語法  
  
```sql  
DROP EXTERNAL FILE FORMAT myfileformat;  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE EXTERNAL FILE FORMAT &#40;TRANSACT-SQL&#41;](../../t-sql/statements/create-external-file-format-transact-sql.md)  
  
  

