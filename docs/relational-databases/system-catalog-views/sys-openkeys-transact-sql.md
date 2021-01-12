---
description: sys.openkeys (Transact-SQL)
title: sys. sys.openkeys (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- openkeys_TSQL
- sys.openkeys_TSQL
- openkeys
- sys.openkeys
dev_langs:
- TSQL
helpviewer_keywords:
- sys.openkeys catalog view
ms.assetid: 719a1259-2398-4fcb-ba05-aeabba7aec21
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 9a9770b2982bde2237a0a5d5c4d44e154f8ce609
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096807"
---
# <a name="sysopenkeys-transact-sql"></a>sys.openkeys (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  此目錄檢視會傳回目前工作階段所開啟的加密金鑰資訊。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**database_id**|**int**|包含金鑰的資料庫識別碼。|  
|**database_name**|**sysname**|包含金鑰的資料庫名稱。|  
|**key_id**|**int**|金鑰的識別碼。 識別碼在資料庫中是唯一的。|  
|**key_name**|**sysname**|金鑰的名稱。 在資料庫中，這是唯一的。|  
|**key_guid**|**varbinary**|金鑰的 GUID。 在資料庫中，這是唯一的。|  
|**opened_date**|**datetime**|開啟金鑰的日期和時間。|  
|**status**|**int**|如果金鑰在中繼資料中為有效則為 1。 如果在中繼資料中找不到金鑰則為 0。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [加密階層](../../relational-databases/security/encryption/encryption-hierarchy.md)   
 [OPEN SYMMETRIC KEY &#40;Transact-SQL&#41;](../../t-sql/statements/open-symmetric-key-transact-sql.md)  
  
  
