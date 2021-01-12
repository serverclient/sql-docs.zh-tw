---
description: 資料層應用程式資料表 - sysdac_history_internal
title: sysdac_history_internal (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysdac_history_internal
- sysdac_history_internal_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysdac_history_internal
ms.assetid: 774a1678-0b27-42be-8adc-a6d7a4a56510
author: cawrites
ms.author: chadam
ms.openlocfilehash: eaea0060136a928e6fab1184c9fd0c08d88df09f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094889"
---
# <a name="data-tier-application-tables---sysdac_history_internal"></a>資料層應用程式資料表 - sysdac_history_internal
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含有關管理資料層應用程式 (DAC) 採取之動作的相關資訊。 此資料表儲存在 **msdb** 資料庫的 **dbo** 架構中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**action_id**|**int**|動作的識別碼|  
|**sequence_id**|**int**|識別動作中的步驟。|  
|**instance_id**|**uniqueidentifier**|DAC 執行個體的識別碼。 此資料行可以聯結在 [dbo.sysdac_instances &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/data-tier-application-views-dbo-sysdac-instances.md)的 **instance_id** 資料行上。|  
|**action_type**|**tinyint**|動作類型的識別碼：<br /><br /> **0** = 部署<br /><br /> **1** = 建立<br /><br /> **2** = 重新命名<br /><br /> **3** = 卸離<br /><br /> **4** = 刪除|  
|**action_type_name**|**Varchar (19)**|動作類型的名稱：<br /><br /> **deploy**<br /><br /> **create**<br /><br /> **rename**<br /><br /> **分離**<br /><br /> **delete**|  
|**dac_object_type**|**tinyint**|受到動作影響之物件類型的識別碼：<br /><br /> **0** = dacpac<br /><br /> **1** = 登入<br /><br /> **2** = 資料庫|  
|**dac_object_type_name**|**Varchar (8)**|受到動作影響之物件類型的名稱：<br /><br /> **dacpac** = DAC 實例<br /><br /> **登錄**<br /><br /> **database**|  
|**action_status**|**tinyint**|識別動作目前狀態的代碼：<br /><br /> **0** = 暫止<br /><br /> **1** = 成功<br /><br /> **2** = 失敗|  
|**action_status_name**|**Varchar (11)**|動作的目前狀態：<br /><br /> **等待**<br /><br /> **成功**<br /><br /> **失敗**|  
|**必要**|**bit**|在回復 DAC 作業時，由 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 使用。|  
|**dac_object_name_pretran**|**sysname**|認可包含動作之交易前的物件名稱。 僅用於資料庫與登入。|  
|**dac_object_name_posttran**|**sysname**|認可包含動作之交易後的物件名稱。 僅用於資料庫與登入。|  
|**sqlscript**|**nvarchar(max)**|在資料庫或登入上實作動作的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼。|  
|**負載**|**varbinary(max)**|儲存在二進位編碼字串中的 DAC 封裝定義。|  
|**註解**|**varchar(max)**|記錄 DAC 升級中接受潛在資料流失之使用者的登入。|  
|**error_string**|**nvarchar(max)**|動作發生錯誤時所產生的錯誤訊息。|  
|**created_by**|**sysname**|啟動建立此項目之動作的登入。|  
|**date_created**|**datetime**|建立此項目的日期和時間。|  
|**date_modified**|**datetime**|上次修改此項目的日期和時間。|  
  
## <a name="remarks"></a>備註  
 DAC 管理動作 (例如，部署或刪除 DAC) 會產生多個步驟。 針對每個動作都會指派一個動作識別碼。 每個步驟都會在 **sysdac_history_internal** 中指派序號和資料列，其中會記錄步驟的狀態。 當動作步驟啟動時，會建立每個資料列，並在需要時進行更新，以反映作業的狀態。 例如，您可以將部署 DAC 動作指派 **action_id** 12，並在 **sysdac_history_internal** 取得四個數據列：  
  
| action_id | sequence_id | action_type_name | dac_object_type_name |
| --------- | ----------- | ---------------- | -------------------- |
|12|0|建立|dacpac|  
|12|1|建立|login|  
|12|2|建立|[資料庫]|  
|12|3|重新命名|[資料庫]|  
  
 DAC 作業（例如 delete）不會從 **sysdac_history_internal** 中移除資料列。 您可以使用下列查詢手動刪除 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體上不再部署的之 DAC 的資料列：  
  
```sql  
DELETE FROM msdb.dbo.sysdac_history_internal  
WHERE instance_id NOT IN  
   (SELECT instance_id  
    FROM msdb.dbo.sysdac_instances_internal);  
```  
  
 刪除使用中 DAC 的資料列不會影響 DAC 作業；唯一的影響是，您將無法報告 DAC 的完整記錄。  
  
> [!NOTE]  
>  目前，沒有任何機制可刪除上的 **sysdac_history_internal** 資料列 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 。  
  
## <a name="permissions"></a>權限  
 需要系統管理員 (sysadmin) 固定伺服器角色中的成員資格。 此視圖的唯讀存取權可供具有連接 master 資料庫之許可權的所有使用者使用。  
  
## <a name="see-also"></a>另請參閱  
 [資料層應用程式](../../relational-databases/data-tier-applications/data-tier-applications.md)   
 [dbo.sysdac_instances &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/data-tier-application-views-dbo-sysdac-instances.md)   
 [sysdac_instances_internal &#40;Transact-sql&#41;](../../relational-databases/system-tables/data-tier-application-tables-sysdac-instances-internal.md)  
  
  
