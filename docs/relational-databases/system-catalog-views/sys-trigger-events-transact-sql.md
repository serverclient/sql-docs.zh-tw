---
description: sys.trigger_events (Transact-SQL)
title: sys.trigger_events (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- trigger_events_TSQL
- trigger_events
- sys.trigger_events
- sys.trigger_events_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.trigger_events catalog view
ms.assetid: 92540447-131c-491c-b033-c064c7d950e1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 2caa1b4e7c140228dd870250ad1cd55f83fc3483
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094337"
---
# <a name="systrigger_events-transact-sql"></a>sys.trigger_events (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  針對引發觸發程序的每個事件，各包含一個資料列。  
  
> [!NOTE]  
>  **sys.trigger_events** 不適用於事件通知。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**\<Columns inherited from sys.events>**|不適用|從 [sys. 事件](../../relational-databases/system-catalog-views/sys-events-transact-sql.md)繼承 **object_id**、**類型** **type_desc** 資料行。|  
|**is_first**|**bit**|觸發程序被標示為這個事件要引發的第一個觸發程序。|  
|**is_last**|**bit**|觸發程序被標示為這個事件要引發的最後一個觸發程序。|  
|**event_group_type**|**int**|觸發程序建立所在的事件群組，如果未在事件群組上建立則為 null。|  
|**event_group_type_desc**|**nvarchar(60)**|觸發程序建立所在之事件群組的描述，如果未在事件群組上建立則為 null。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)  
  
  
