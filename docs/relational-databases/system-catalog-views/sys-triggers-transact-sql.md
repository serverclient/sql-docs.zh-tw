---
description: sys.triggers (Transact-SQL)
title: sys. 觸發程式 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- triggers
- triggers_TSQL
- sys.triggers
- sys.triggers_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.triggers catalog view
ms.assetid: cefa4fc4-b8b9-4cd7-b124-eed5283acbfc
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: b9fbf3f59de6e7508fd8ee1fac13a98bb36170b1
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094312"
---
# <a name="systriggers-transact-sql"></a>sys.triggers (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  針對每個 TR 或 TA 類型的觸發程序物件，各包含一個資料列。 DML 觸發程式名稱是以架構為範圍，因此在 **sys. 物件** 中是可見的。 而 DDL 觸發程序名稱是由父實體來限定範圍，因此只會顯示在這份檢視中。  
  
 **Parent_class** 和 **名稱** 資料行可唯一識別資料庫中的觸發程式。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|觸發程序名稱。 DML 觸發程序名稱是由結構描述限定範圍。 DDL 觸發程序名稱是以父實體來限定範圍。|  
|object_id|**int**|物件識別碼。 在資料庫中，這是唯一的。|  
|**parent_class**|**tinyint**|觸發程序父系的類別。<br /><br /> 0 = DDL 觸發程序的資料庫。<br /><br /> 1 = DML 觸發程序的物件或資料行。|  
|**parent_class_desc**|**nvarchar(60)**|觸發程序父類別的描述。<br /><br /> DATABASE<br /><br /> OBJECT_OR_COLUMN|  
|**parent_id**|**int**|觸發程序父系的識別碼，如下所示：<br /><br /> 0 = 以資料庫為父系的觸發程序。<br /><br /> 針對 DML 觸發程式，這是定義 DML 觸發程式之資料表或視圖的 **object_id** 。|  
|**type**|**char(2)**|物件類型：<br /><br /> TA = 組件 (CLR) 觸發程序<br /><br /> TR = SQL 觸發程序|  
|**type_desc**|**nvarchar(60)**|物件類型的描述。<br /><br /> CLR_TRIGGER<br /><br /> SQL_TRIGGER|  
|**create_date**|**datetime**|建立觸發程序的日期。|  
|**modify_date**|**datetime**|上次利用 ALTER 陳述式來修改物件的日期。|  
|**is_ms_shipped**|**bit**|利用內部 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件來代表使用者所建立的觸發程序。|  
|**is_disabled**|**bit**|觸發程序已停用。|  
|**is_not_for_replication**|**bit**|觸發程序是建立為 NOT FOR REPLICATION。|  
|**is_instead_of_trigger**|**bit**|1 = INSTEAD OF 觸發程序<br /><br /> 0 = AFTER 觸發程序。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [安全性目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
