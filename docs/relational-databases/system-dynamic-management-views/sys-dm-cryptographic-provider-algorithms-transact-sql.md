---
description: sys.dm_cryptographic_provider_algorithms (Transact-SQL)
title: sys.dm_cryptographic_provider_algorithms (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_cryptographic_provider_algorithms_TSQL
- sys.dm_cryptographic_provider_algorithms
- sys.dm_cryptographic_provider_algorithms_TSQL
- dm_cryptographic_provider_algorithms
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_cryptographic_provider_algorithms dynamic management function
ms.assetid: 8bcccb37-5cfb-4e1e-a0bb-7ff4c279fe8e
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 452e348cdea08ffffce43ea96fecb3673efd50ef
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095224"
---
# <a name="sysdm_cryptographic_provider_algorithms-transact-sql"></a>sys.dm_cryptographic_provider_algorithms (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回可延伸金鑰管理 (EKM) 提供者所支援的演算法。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.dm_cryptographic_provider_algorithms ( provider_id )  
```  
  
## <a name="arguments"></a>引數  
 *provider_id*  
 EKM 提供者的識別碼，沒有預設值。  
  
## <a name="tables-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|algorithm_id|**int**|演算法的識別碼。|  
|algorithm_tag|**nvarchar(60)**|演算法的識別標記。|  
|key_type|**nvarchar(128)**|顯示金鑰類型。 傳回 ASYMMETRIC KEY 或 SYMMETRIC KEY。|  
|key_length|**int**|指出金鑰長度 (以位元為單位)。|  
  
## <a name="permissions"></a>權限  
 使用者必須是 Public 資料庫角色的成員。  
  
## <a name="examples"></a>範例  
 下列範例顯示識別碼為 `1234567` 之提供者的提供者選項。  
  
```  
SELECT * FROM sys.dm_cryptographic_provider_algorithms(1234567);  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [可延伸金鑰管理 &#40;EKM&#41;](../../relational-databases/security/encryption/extensible-key-management-ekm.md)   
 [安全性相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql.md)  
  
  
