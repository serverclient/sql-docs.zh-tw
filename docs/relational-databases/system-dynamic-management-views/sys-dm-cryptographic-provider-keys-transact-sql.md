---
description: sys.dm_cryptographic_provider_keys (Transact-SQL)
title: sys.dm_cryptographic_provider_keys (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_cryptographic_provider_keys_TSQL
- dm_cryptographic_provider_keys_TSQL
- dm_cryptographic_provider_keys
- sys.dm_cryptographic_provider_keys
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_cryptographic_provider_keys dynamic management function
ms.assetid: 5a8c1421-c56b-44b5-96e5-4f01782a0c7c
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 668f98d9b15fd38792f7a6a48a92c955d17ca94d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099926"
---
# <a name="sysdm_cryptographic_provider_keys-transact-sql"></a>sys.dm_cryptographic_provider_keys (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回可延伸金鑰管理 (EKM) 提供者所提供之金鑰的相關資訊。  

 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
dm_cryptographic_provider_keys ( provider_id )  
```  
  
## <a name="arguments"></a>引數  
 *provider_id*  
 EKM 提供者的識別碼，沒有預設值。  
  
## <a name="tables-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**key_id**|**int**|提供者之金鑰的識別碼。|  
|**key_name**|**nvarchar(512)**|提供者之金鑰的名稱。|  
|**key_thumbprint**|**varbinary(32)**|金鑰之提供者的指模。|  
|**algorithm_id**|**int**|提供者之演算法的識別碼。|  
|**algorithm_tag**|**int**|提供者之演算法的標記。|  
|**key_type**|**nchar(256)**|提供者之金鑰的類型。|  
|**key_length**|**int**|提供者之金鑰的長度。|  
  
## <a name="permissions"></a>權限  
 查詢此檢視時，它會利用提供者驗證使用者內容，並列舉使用者可以看到的所有金鑰。  
  
 如果使用者無法利用 EKM 提供者驗證，將不會傳回任何金鑰資訊。  
  
## <a name="examples"></a>範例  
 下列範例顯示識別碼為 `1234567` 之提供者的金鑰屬性。  
  
```  
SELECT * FROM sys.dm_cryptographic_provider_keys(1234567);  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [可延伸金鑰管理 &#40;EKM&#41;](../../relational-databases/security/encryption/extensible-key-management-ekm.md)  
  
  
