---
description: sys.conversation_priorities (Transact-SQL)
title: sys.conversation_priorities (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- conversation_priorities_TSQL
- conversation_priorities
- sys.conversation_priorities_TSQL
- sys.conversation_priorities
dev_langs:
- TSQL
helpviewer_keywords:
- conversations [Service Broker], priorities
- Service Broker, conversations
- sys.conversation_priorities catalog view
ms.assetid: 7cbb9171-3310-4aae-8458-755c882d6462
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 76972ee7aac973ef6d7ce4276f95a99454c872c1
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102838"
---
# <a name="sysconversation_priorities-transact-sql"></a>sys.conversation_priorities (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含目前資料庫中所建立之每一個交談優先權的資料列，如下表所示： 
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|Priority_id|**int**|用來唯一識別交談優先權的號碼。 不是 NULLABLE。|  
|NAME|**sysname**|交談優先權的名稱。 不是 NULLABLE。|  
|service_contract_id|**int**|為此交談優先權指定之合約的識別碼。 這可以在 sys.service_contracts 中的 service_contract_id 資料行上繫結。 NULLABLE。|  
|local_service_id|**int**|針對此交談優先權指定為本機服務之服務的識別碼。 此資料行可以在 sys.services 中的 service_contract_id 資料行上繫結。 NULLABLE。|  
|remote_service_name|**nvarchar(256)**|針對此交談優先權指定為遠端服務之服務的名稱。 NULLABLE。|  
|priority|**tinyint**|此交談優先權中所指定的優先權等級。 不是 NULLABLE。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="examples"></a>範例  
 下列範例列出交談優先權，其方式是使用繫結來顯示合約和本機服務名稱。  
  
```  
SELECT scp.name AS priority_name,  
       ssc.name AS contract_name,  
       ssvc.name AS local_service_name,  
       scp.remote_service_name,  
       scp.priority AS priority_level  
FROM sys.conversation_priorities AS scp  
    INNER JOIN sys.service_contracts AS ssc  
       ON scp.service_contract_id = ssc.service_contract_id  
    INNER JOIN sys.services AS ssvc  
       ON scp.local_service_id = ssvc.service_id  
ORDER BY priority_name, contract_name,  
         local_service_name, remote_service_name;  
  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER BROKER PRIORITY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-broker-priority-transact-sql.md)   
 [CREATE BROKER PRIORITY &#40;Transact-SQL&#41;](../../t-sql/statements/create-broker-priority-transact-sql.md)   
 [DROP BROKER PRIORITY &#40;Transact-SQL&#41;](../../t-sql/statements/drop-broker-priority-transact-sql.md)   
 [sys. services &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-services-transact-sql.md)   
 [sys.service_contracts &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-service-contracts-transact-sql.md)  
  
  
