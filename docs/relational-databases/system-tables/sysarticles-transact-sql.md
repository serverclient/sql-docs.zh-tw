---
description: sysarticles (Transact-SQL)
title: sysarticles (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sysarticles
- sysarticles_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysarticles system table
ms.assetid: 9d9d5d51-6d8f-4e42-84a9-82e58eb0301e
author: cawrites
ms.author: chadam
ms.openlocfilehash: e27f397908ffdc5eae3945cd5b704bbf4a825c2d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096104"
---
# <a name="sysarticles-transact-sql"></a>sysarticles (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對本機資料庫中定義的每個發行項，各包含一個資料列。 這份資料表儲存在發行的資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**>artid**|**int**|提供發行項唯一識別碼的識別欄位。|  
|**creation_script**|**nvarchar(255)**|發行項的結構描述指令碼。|  
|**del_cmd**|**nvarchar(255)**|當隨著資料表發行項而複寫刪除時，所用的複寫命令類型。 如需詳細資訊，請參閱[指定交易式發行項變更的傳播方式](../../relational-databases/replication/transactional/transactional-articles-specify-how-changes-are-propagated.md)。|  
|**description**|**nvarchar(255)**|發行項的描述性項目。|  
|**dest_table**|**sysname**|目的地資料表的名稱。|  
|**filter**|**int**|用來進行水平資料分割的預存處理序識別碼。|  
|**filter_clause**|**ntext**|用來進行水平篩選的發行項 WHERE 子句。|  
|**ins_cmd**|**nvarchar(255)**|當隨著資料表發行項而複寫插入時，所用的複寫命令類型。 如需詳細資訊，請參閱[指定交易式發行項變更的傳播方式](../../relational-databases/replication/transactional/transactional-articles-specify-how-changes-are-propagated.md)。|  
|**name**|**sysname**|發行項的相關聯名稱，在發行集內是唯一的。|  
|**objid**|**int**|已發行的資料表物件識別碼。|  
|**pubid**|**int**|發行項所屬發行集的識別碼。|  
|**pre_creation_cmd**|**tinyint**|DROP TABLE、DELETE TABLE 或 TRUNCATE 的預先建立命令：<br /><br /> **0** = 無。<br /><br /> **1** = 卸載。<br /><br /> **2** = 刪除。<br /><br /> **3** = 截斷。|  
|**status**|**tinyint**|發行項選項和狀態的位元遮罩，它可能是一或多個這些值的位元邏輯 OR 結果：<br /><br /> **1** = 發行項為使用中。<br /><br /> **8** = 在 INSERT 語句中包含資料行名稱。<br /><br /> **16** = 使用參數化語句。<br /><br /> **24** = 兩者都會在 INSERT 語句中包含資料行名稱，並使用參數化語句。<br /><br /> **64** = [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]<br /><br /> 例如，使用參數化語句的使用中發行項在此資料行中的值為 **17** 。 **0** 值表示發行項為非使用中，且未定義任何其他屬性。|  
|**sync_objid**|**int**|代表發行項定義之資料表或檢視的識別碼。|  
|**type**|**tinyint**|發行項的類型：<br /><br /> **1** = 記錄式發行項。<br /><br /> **3** = 具有手動篩選的記錄式發行項。<br /><br /> **5** = 具有手動 view 的記錄式文章。<br /><br /> **7** = 具有手動篩選和手動視圖的記錄式發行項。<br /><br /> **8** = 預存程式執行。<br /><br /> **24** = 可序列化預存程式執行。<br /><br /> **32** = 預存程式 (只) 的架構。<br /><br /> **64** = 僅) View (架構。<br /><br /> **128** = 函式 (僅) 的架構。|  
|**upd_cmd**|**nvarchar(255)**|當隨著資料表發行項而複寫更新時，所用的複寫命令類型。 如需詳細資訊，請參閱[指定交易式發行項變更的傳播方式](../../relational-databases/replication/transactional/transactional-articles-specify-how-changes-are-propagated.md)。|  
|**schema_option**|**二元 (8)**|發行項的結構描述產生選項位元遮罩，這會控制要針對發行項結構描述的哪些部分編寫指令碼，才能傳遞到訂閱者。 如需結構描述選項的詳細資訊，請參閱 [sp_addarticle &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addarticle-transact-sql.md)。|  
|**dest_owner**|**sysname**|目的地資料庫的資料表擁有者。|  
|**ins_scripting_proc**|**int**|當複寫 INSERT 陳述式時，所執行的已登錄之自訂預存程序或指令碼。|  
|**del_scripting_proc**|**int**|當複寫 DELETE 陳述式時，所執行的已登錄之自訂預存程序或指令碼。|  
|**upd_scripting_proc**|**int**|當複寫 UPDATE 陳述式時，所執行的已登錄之自訂預存程序或指令碼。|  
|**custom_script**|**nvarchar(2048)**|在 DDL 觸發程序結束時，所執行的已登錄之自訂預存程序或指令碼。|  
|**fire_triggers_on_snapshot**|**bit**|指出當套用快照集時，是否執行複寫的觸發程序，它可以是下列值之一：<br /><br /> **0** = 不執行觸發程式。<br /><br /> **1** = 觸發程式執行。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [&#40;Transact-sql&#41;的複寫視圖 ](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [sp_addarticle &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addarticle-transact-sql.md)   
 [sp_changearticle &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-changearticle-transact-sql.md)   
 [sp_helparticle &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helparticle-transact-sql.md)  
  
  
