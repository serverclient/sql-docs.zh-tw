---
description: sys.server_assembly_modules (Transact-SQL)
title: sys.server_assembly_modules (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- server_assembly_modules_TSQL
- sys.server_assembly_modules
- server_assembly_modules
- sys.server_assembly_modules_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.server_assembly_modules catalog view
ms.assetid: af799e38-2d16-49b2-bcf5-6f9199af899e
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 9449fe5719934222532a251426436415a26cd233
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100158"
---
# <a name="sysserver_assembly_modules-transact-sql"></a>sys.server_assembly_modules (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對 TA 類型伺服器層級觸發程序的每個組件模組，各包含一個資料列。 這份檢視會將組件觸發程序對應至基礎 CLR 實作。 您可以將此關聯加入至 **sys.server_triggers**。 元件必須載入到 **master** 資料庫中。 Tuple (object_id) 是關聯性的索引鍵。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|這是對定義這個組件模組之物件的 FOREIGN KEY 參考。|  
|**assembly_id**|**int**|建立這個模組所用之組件的識別碼。 組件必須載入到 master 資料庫中。|  
|**assembly_class**|**sysname**|定義這個模組之組件內的類別名稱。|  
|**assembly_method**|**sysname**|定義這個模組之類別內的方法名稱。 如果是彙總函式 (AF)，則為 NULL。|  
|**execute_as_principal_id**|**int**|EXECUTE AS 伺服器主體的識別碼。<br /><br /> 在預設或 EXECUTE AS CALLER 的情況下為 NULL。<br /><br /> 如果 EXECUTE AS SELF EXECUTE AS，則為指定之主體的識別碼 \<principal> 。<br /><br /> -2 = EXECUTE AS OWNER。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)  
  
  
