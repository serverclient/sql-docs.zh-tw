---
description: sys.database_event_session_actions (Azure SQL Database)
title: sys.database_event_session_actions (Azure SQL Database) Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
ms.assetid: 32494df1-7ab7-4b88-a858-6b1021d67433
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: d6b142c7a0a6d63fb6e0c2dcc59a5f2f701c2cfd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102802"
---
# <a name="sysdatabase_event_session_actions-azure-sql-database"></a>sys.database_event_session_actions (Azure SQL Database)
[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

  針對事件工作階段之每個事件的每個動作傳回資料列。  
  
||  
|-|  
|**適用于**： [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] V12 和任何較新版本。|  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|event_session_id|**int**|事件工作階段的識別碼。 不可為 Null。|  
|event_id|**int**|事件的識別碼。 這個識別碼在事件工作階段物件中是唯一的。 不可為 Null。|  
|NAME|**sysname**|動作的名稱。 可為 Null。|  
|套件|**sysname**|包含此事件之事件封裝的名稱。 可為 Null。|  
|name|**sysname**|包含此事件之模組的名稱。 可為 Null。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW DATABASE STATE 權限。  
  
## <a name="remarks"></a>備註  
 此檢視有下列關聯性基數。  
  
| 寄件者 | 收件者 | 關聯性 |
| ---- | -- | ------------ |
|sys.database_event_session_actions sys.database_event_session_actions.event_session_id|sys.sys. database_event_sessions. event_session_id|多對一|  
|sys.database_event_session_actions sys.database_event_session_actions.event_id<br /><br /> sys.database_event_session_actions sys.database_event_session_actions.event_session_id|sys.database_event_session_events sys.database_event_session_events.event_session_id<br /><br /> sys.database_event_session_events sys.database_event_session_events.event_id|多對一|  
  
  
