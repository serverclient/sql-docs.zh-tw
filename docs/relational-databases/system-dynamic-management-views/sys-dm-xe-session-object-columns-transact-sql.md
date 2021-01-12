---
description: sys.dm_xe_session_object_columns (Transact-SQL)
title: sys.dm_xe_session_object_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_xe_session_object_columns_TSQL
- sys.dm_xe_session_object_columns_TSQL
- dm_xe_session_object_columns
- sys.dm_xe_session_object_columns
dev_langs:
- TSQL
helpviewer_keywords:
- xe
- sys.dm_xe_session_object_columns dynamic management view
ms.assetid: e97f3307-2da6-4c54-b818-a474faec752e
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 14213f30fed5d57a794c147486aa8f9bcc077575
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096418"
---
# <a name="sysdm_xe_session_object_columns-transact-sql"></a>sys.dm_xe_session_object_columns (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  顯示繫結至工作階段之物件的組態值。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|event_session_address|**varbinary(8)**|事件工作階段的記憶體位址。 與 sys.dm_xe_sessions.address 之間是多對一的關聯性。 不可為 Null。|  
|column_name|**nvarchar(256)**|組態值的名稱。 不可為 Null。|  
|column_id|**int**|資料行的識別碼。 在物件中，這是唯一的。 不可為 Null。|  
|column_value|**Nvarchar (3072)**|資料行的設定值。 可為 Null。|  
|object_type|**nvarchar(60)**|物件的類型。 不可為 Null。 object_type 是下列其中一項：<br /><br /> event<br /><br /> 目標|  
|object_name|**nvarchar(256)**|這個資料行所屬之物件的名稱。 不可為 Null。|  
|object_package_guid|**uniqueidentifier**|包含物件之封裝的 GUID。 不可為 Null。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
### <a name="relationship-cardinalities"></a>關聯性基數  
  
|寄件者|收件者|關聯性|  
|----------|--------|------------------|  
|dm_xe_session_object_columns dm_xe_session_object_columns.object_name、<br /><br /> dm_xe_session_object_columns.object_package_guid|sys.dm_xe_objects sys.dm_xe_objects.package_guid、<br /><br /> sys.dm_xe_objects.name|多對一|  
|dm_xe_session_object_columns dm_xe_session_object_columns.column_name、<br /><br /> dm_xe_session_object_columns.column_id|sys.dm_xe_object_columns。 name，<br /><br /> sys.dm_xe_object_columns.column_id|多對一|  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)  
  
  

