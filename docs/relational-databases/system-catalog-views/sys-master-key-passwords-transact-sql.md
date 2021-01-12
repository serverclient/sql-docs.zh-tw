---
description: sys.master_key_passwords (Transact-SQL)
title: sys.master_key_passwords (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 04/06/2020
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.master_key_passwords_TSQL
- master_key_passwords_TSQL
- sys.master_key_passwords
- master_key_passwords
dev_langs:
- TSQL
helpviewer_keywords:
- sys.master_key_passwords catalog view
ms.assetid: b8e18cff-a9e6-4386-98ce-1cd855506e03
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 1552e621ba2b0d16e8a9e505a460b682bf33accd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095556"
---
# <a name="sysmaster_key_passwords-transact-sql"></a>sys.master_key_passwords (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  針對使用 **sp_control_dbmasterkey_password** 預存程式加入的每個資料庫主要金鑰密碼，各傳回一個資料列。 保護主要金鑰所用的密碼，是儲存在認證存放區中。 認證名稱的格式如下： # #DBMKEY_<database_family_guid>_<random_password_guid # #。 密碼會儲存為認證秘密。 針對使用 **sp_control_dbmasterkey_password** 新增的每個密碼， **sys. 認證** 中會有一個資料列。  
  
 此視圖中的每個資料列都會顯示 **credential_id** 和資料庫的 **family_guid** ，其主要金鑰是由與該認證相關聯的密碼所保護。 在 **credential_id** 上使用 **sys. 認證** 的聯結將會傳回有用的欄位，例如 **create_date** 和認證名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**credential_id**|**int**|密碼所屬的認證識別碼。 這個識別碼在伺服器執行個體中是唯一的。|  
|**family_guid**|**uniqueidentifier**|建立時原始資料庫的唯一識別碼。 當資料庫還原或附加之後，這個 GUID 仍然不變，即使資料庫名稱改變了也是如此。<br /><br /> 如果服務主要金鑰的自動解密失敗， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會使用 **family_guid** 來識別可能包含用來保護資料庫主要金鑰之密碼的認證。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [sp_control_dbmasterkey_password &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-control-dbmasterkey-password-transact-sql.md)   
 [安全性目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md)   
 [CREATE SYMMETRIC KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-symmetric-key-transact-sql.md)   
 [加密階層](../../relational-databases/security/encryption/encryption-hierarchy.md)  
  
  
