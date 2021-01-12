---
description: sys.availability_databases_cluster (Transact-SQL)
title: sys.availability_databases_cluster (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.availability_databases_cluster_TSQL
- sys.availability_databases_cluster
- availability_databases_cluster_TSQL
- availability_databases_cluster
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- Availability Groups [SQL Server], WSFC clusters
- sys.availability_databases_cluster catalog view
- Availability Groups [SQL Server], databases
ms.assetid: 8d9c57e5-7f39-4315-b466-92748231140a
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 1316bf5c7a619eb5f9c204ec32e1ac6e63e5d4bb
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099220"
---
# <a name="sysavailability_databases_cluster-transact-sql"></a>sys.availability_databases_cluster (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對實例上的每個可用性資料庫，各包含一個資料列， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 該實例會針對 Windows Server 容錯移轉叢集 (WSFC) 叢集中的任何 Always On 可用性群組來裝載可用性複本，不論本機複製資料庫是否已聯結至可用性群組。  
  
> [!NOTE]  
>  當資料庫加入至可用性群組時，主要資料庫會自動聯結至此群組。 次要資料庫必須先在每個次要複本上備妥，然後才能聯結至可用性群組。   
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**group_id**|**uniqueidentifier**|可用性群組的唯一識別碼，資料庫會參與此可用性群組 (如果有的話)。<br /><br /> NULL = 資料庫不是可用性群組中可用性複本的一部分。|  
|**group_database_id**|**uniqueidentifier**|資料庫參與之可用性群組 (如果有的話) 內資料庫的唯一識別碼。 在主要複本上和資料庫已加入可用性群組的每個次要複本上，此資料庫的 **group_database_id** 都相同。<br /><br /> NULL = 資料庫不是任何可用性群組中可用性複本的一部分。|  
|**database_name**|**sysname**|已經加入至可用性群組的資料庫名稱。|  
  
## <a name="permissions"></a>權限  
 如果 **sys.availability_databases_cluster** 的呼叫端不是資料庫的擁有者，則查看對應資料列所需的最小許可權是 ALTER any DATABASE 或 VIEW any database 伺服器層級許可權，或 **master** 資料庫中的 CREATE database 許可權。  
  
## <a name="see-also"></a>另請參閱  
 [sys.availability_groups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md)   
 [sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)   
 [sys.dm_hadr_database_replica_states &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)   
 [sys.dm_hadr_database_replica_cluster_states &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-cluster-states-transact-sql.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)  
  
  
