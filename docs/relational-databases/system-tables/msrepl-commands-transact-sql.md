---
description: MSrepl_commands (Transact-SQL)
title: MSrepl_commands (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSrepl_commands
- MSrepl_commands_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSrepl_commands system table
ms.assetid: 53b9f9cd-9429-47a0-aba2-908fc60e7036
author: cawrites
ms.author: chadam
ms.openlocfilehash: c939e1c5112aee07738ad5d368cb0ef05d804618
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98090971"
---
# <a name="msrepl_commands-transact-sql"></a>MSrepl_commands (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSrepl_commands** 資料表包含已複寫命令的資料列。 這份資料表儲存在散發資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**publisher_database_id**|**int**|發行者資料庫的識別碼。|  
|**xact_seqno**|**varbinary(16)**|交易序號。|  
|**type**|**int**|命令類型。|  
|**article_id**|**int**|發行項的識別碼。|  
|**originator_id**|**int**|發起者的識別碼。|  
|**command_id**|**int**|命令的識別碼。|  
|**partial_command**|**bit**|指出這是否為部分命令。|  
|**command**|**Varbinary (1024)**|命令值。|  
|**hashkey**|**int**|僅供內部使用。|  
|**originator_lsn**|**varbinary(16)**|識別原始發行集中，該命令的 LSN。 用於點對點異動複寫中。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [&#40;Transact-sql&#41;的複寫視圖 ](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [sp_replcmds &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-replcmds-transact-sql.md)  
  
  
