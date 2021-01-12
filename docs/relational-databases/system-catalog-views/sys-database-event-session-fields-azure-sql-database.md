---
description: sys.database_event_session_fields (Azure SQL Database)
title: sys.database_event_session_fields (Azure SQL Database) Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
ms.assetid: 9b5c94d6-612c-4e0f-976d-ac6ba55da3ac
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 864bccc837046ebeed303b66d9f3719b8ff67a9d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102760"
---
# <a name="sysdatabase_event_session_fields-azure-sql-database"></a>sys.database_event_session_fields (Azure SQL Database)
[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

  針對事件和目標上明確設定的每一個可自訂資料行傳回資料列。  
  
||  
|-|  
|**適用于**： [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] V12 和任何較新版本。|  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|event_session_id|**int**|事件工作階段的識別碼。 不可為 Null。|  
|object_id|**int**|這個欄位相關聯之物件的識別碼。 不可為 Null。|  
|NAME|**sysname**|欄位的名稱。 不可為 Null。|  
|value|**sql_variant**|此欄位的值。 不可為 Null。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW DATABASE STATE 權限。  
  
## <a name="remarks"></a>備註  
 此檢視有下列關聯性基數。  
  
| 寄件者 | 收件者 | 關聯性 |
| ---- | -- | ------------ |
|sys.database_event_session_actions sys.database_event_session_actions.event_session_id|sys.database_event_sessions sys.database_event_sessions.event_session_id|多對一|  
|sys.database_event_session_actions sys.database_event_session_actions.event_id<br /><br /> sys.database_event_session_actions sys.database_event_session_actions.object_id<br /><br /> sys.database_event_session_actions sys.database_event_session_actions.event_session_id|sys.database_event_session_events sys.database_event_session_events.event_session_id<br /><br /> sys.database_event_session_events sys.database_event_session_events.event_id|多對一|  
|sys.database_event_session_actions sys.database_event_session_actions.event_session_id<br /><br /> sys.database_event_session_actions sys.database_event_session_actions.object_id|sys.database_event_session_targets sys.database_event_session_targets.event_session_id<br /><br /> sys.database_event_session_targets sys.database_event_session_targets.target_id|多對一|  
  
## <a name="see-also"></a>另請參閱  
 [擴充事件](../../relational-databases/extended-events/extended-events.md)  
  
  
