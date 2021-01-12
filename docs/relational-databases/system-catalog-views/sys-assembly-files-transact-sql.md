---
description: sys.assembly_files (Transact-SQL)
title: sys.assembly_files (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.assembly_files
- assembly_files_TSQL
- assembly_files
- sys.assembly_files_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.assembly_files catalog view
ms.assetid: 1a384a2c-5556-4d12-a2ba-4da781363143
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: b8613a3f35a7cb7f4d0ca49167583abcf837c852
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102854"
---
# <a name="sysassembly_files-transact-sql"></a>sys.assembly_files (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對組成組件的每個檔案，各包含一個資料列。  
    
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**assembly_id**|**int**|這個檔案所屬的組件識別碼。|  
|**name**|**nvarchar(260)**|組件檔的名稱。|  
|**file_id**|**int**|檔案識別碼。 在組件中，這是唯一的。 編號 1 的檔案識別碼代表組件 DLL。|  
|**content**|**varbinary(max)**|檔案內容。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;的 CLR 元件類別目錄檢視 ](../../relational-databases/system-catalog-views/clr-assembly-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [ASSEMBLYPROPERTY &#40;Transact-sql&#41;](../../t-sql/functions/assemblyproperty-transact-sql.md)  
  
  
