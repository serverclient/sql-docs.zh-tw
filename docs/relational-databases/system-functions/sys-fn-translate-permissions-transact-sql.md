---
description: sys.fn_translate_permissions (Transact-SQL)
title: sys.fn_translate_permissions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.fn_translate_permissions
- sys.fn_translate_permissions_TSQL
- fn_translate_permissions
- fn_translate_permissions_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- permissions bitmask [SQL Server]
- sys.fn_translate_permissions function
- fn_translate_permissions function
ms.assetid: ac97121f-2bd0-4f71-8e45-42c8584edbc5
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: e4d00e0b945012cb8a1b583921328009d12a34ad
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094931"
---
# <a name="sysfn_translate_permissions-transact-sql"></a>sys.fn_translate_permissions (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  將 SQL 追蹤所傳回的權限位元遮罩解譯為權限名稱表。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.fn_translate_permissions ( level , perms )  
```  
  
## <a name="arguments"></a>引數  
 *level*  
 這是套用權限的安全性實體種類。 *層級* 是 **Nvarchar (60)**。  
  
 *perms*  
 這是權限資料行中傳回的位元遮罩。 *perms* 是 **Varbinary (16)**。  
  
## <a name="returns"></a>傳回  
 **table**  
  
## <a name="remarks"></a>備註  
 在 SQL 追蹤的 [ **許可權** ] 資料行中所傳回的值，是用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 來計算有效許可權之位元遮罩的整數表示。 25 種安全性實體的每一種都有它自己的權限集合，而且這些權限集合有對應的數值。 **sys.fn_translate_permissions** 將此位元遮罩轉譯為許可權名稱的資料表。  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。  
  
## <a name="example"></a>範例  
 下列查詢會使用 `sys.fn_builtin_permissions` 來顯示適用于憑證的許可權，然後使用傳回 `sys.fn_translate_permissions` 許可權位元遮罩的結果。  
  
```  
SELECT * FROM sys.fn_builtin_permissions('CERTIFICATE');  
SELECT '0001' AS Input, * FROM sys.fn_translate_permissions('CERTIFICATE', 0001);  
SELECT '0010' AS Input, * FROM sys.fn_translate_permissions('CERTIFICATE', 0010);  
SELECT '0011' AS Input, * FROM sys.fn_translate_permissions('CERTIFICATE', 0011);  
```  
  
## <a name="see-also"></a>另請參閱  
 [權限 &#40;資料庫引擎&#41;](../../relational-databases/security/permissions-database-engine.md)   
 [sys.server_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-server-permissions-transact-sql.md)   
 [sys.database_permissions &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-permissions-transact-sql.md)  
  
  
