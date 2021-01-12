---
description: 變更追蹤目錄檢視-sys.change_tracking_databases
title: sys.change_tracking_databases (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/08/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- change_tracking_databases
- sys.change_tracking_databases_TSQL
- sys.change_tracking_databases
- change_tracking_databases_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.change_tracking_databases
- change tracking [SQL Server], sys.change_tracking_databases
ms.assetid: bb233baa-2991-4904-a0eb-3772b81121a4
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 5a4122bb16ea7fced5acb078f28f8b11c37c54c5
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093250"
---
# <a name="change-tracking-catalog-views---syschange_tracking_databases"></a>變更追蹤目錄檢視-sys.change_tracking_databases
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  針對已啟用變更追蹤的每一個資料庫，各傳回一個資料列。  

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|database_id|**int**|資料庫的識別碼。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體內，這是唯一的。|  
|is_auto_cleanup_on|**bit**|指出在經過指定的保留週期後，是否要自動清除變更追蹤資料：<br /><br /> 0 = Off<br /><br /> 1 = On|  
|retention_period|**int**|如果正在使用自動清除，保留週期會指定變更追蹤資料保留在資料庫中的時間。|  
|retention_period_units_desc|**nvarchar(60)**|指定保留週期的描述：<br /><br /> 分鐘<br /><br /> 小時<br /><br /> 天|  
|retention_period_units|**tinyint**|保留週期之時間的單位：<br /><br /> 1 = 分鐘<br /><br /> 2 = 小時<br /><br /> 3 = 日|  
  
## <a name="permissions"></a>權限  
 系統會針對 sys.change_tracking_databases 和 sys.databases 進行相同的權限檢查。 如果 sys.change_tracking_databases 的呼叫端不是資料庫的擁有者，查看對應之資料列所需的最低權限為 ALTER ANY DATABASE 或 VIEW ANY DATABASE 伺服器層級權限，或是 master 資料庫或目前資料庫中的 CREATE DATABASE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [變更追蹤 &#40;Transact-sql&#41;的目錄檢視 ](./catalog-views-transact-sql.md)   
 [追蹤資料變更 &#40;SQL Server&#41;](../../relational-databases/track-changes/track-data-changes-sql-server.md)  
  
