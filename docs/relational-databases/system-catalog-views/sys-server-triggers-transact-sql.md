---
description: sys.server_triggers (Transact-SQL)
title: sys.server_triggers (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- server_triggers
- sys.server_triggers_TSQL
- server_triggers_TSQL
- sys.server_triggers
dev_langs:
- TSQL
helpviewer_keywords:
- sys.server_triggers catalog view
ms.assetid: 25926ff4-9271-45bf-bc32-d5d3344bd47a
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 2c7d1187c7182b185b2780d237ea52bd725e2601
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099208"
---
# <a name="sysserver_triggers-transact-sql"></a>sys.server_triggers (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含所有 object_type 為 TR 或 TA 的伺服器層級 DDL 觸發程序組。 在 CLR 觸發程式的情況下，元件必須載入到 **master** 資料庫中。 所有的伺服器層級 DDL 觸發程序名稱，都在一個全域範圍內。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|觸發程序的名稱。|  
|object_id|**int**|物件的識別碼。|  
|**parent_class**|**tinyint**|父系的類別。 它一律是：<br /><br /> 100 = 伺服器|  
|**parent_class_desc**|**nvarchar(60)**|父類別的描述。 它一律是：<br /><br /> SERVER。|  
|**parent_id**|**int**|SERVER 上的觸發程序，一律為 0。|  
|**type**|**char(2)**|物件類型：<br /><br /> TA = 組件 (CLR) 觸發程序<br /><br /> TR = SQL 觸發程序|  
|**type_desc**|**nvarchar(60)**|物件類型類別的描述。<br /><br /> CLR_TRIGGER<br /><br /> SQL_TRIGGER|  
|**create_date**|**datetime**|建立觸發程序的日期。|  
|**modify_date**|**datetime**|上次利用 ALTER 陳述式來修改觸發程序的日期。|  
|**is_ms_shipped**|**bit**|利用內部 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件來代表使用者所建立的觸發程序。|  
|**is_disabled**|**bit**|1 = 觸發程序已停用。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
