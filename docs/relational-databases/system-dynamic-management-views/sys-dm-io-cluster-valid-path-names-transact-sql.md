---
description: sys.dm_io_cluster_valid_path_names (Transact-SQL)
title: sys.dm_io_cluster_valid_path_names (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_io_cluster_valid_path_names
- dm_io_cluster_valid_path_names_TSQL
- sys.dm_io_cluster_valid_path_names_TSQL
- dm_io_cluster_valid_path_names
dev_langs:
- TSQL
helpviewer_keywords:
- dm_io_cluster_valid_path_names
- sys.dm_io_cluster_valid_path_names
- cluster valid path names
- csv name
- cluster shared volume names
ms.assetid: 5bc8a0e5-6c72-425b-8c58-f276eb9add2c
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 24bddc071b9ad5b64ef796f16718d1465dec7eaa
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097574"
---
# <a name="sysdm_io_cluster_valid_path_names-transact-sql"></a>sys.dm_io_cluster_valid_path_names (Transact-SQL)
[!INCLUDE[sqlserver](../../includes/applies-to-version/sqlserver.md)]

  傳回 SQL Server 容錯移轉叢集執行個體之所有有效共用磁碟的相關資訊，包括叢集共用磁碟區。 如果執行個體尚未叢集化，則會傳回空的資料列集。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**path_name**|**NVarchar (512)**|可當做資料庫和記錄檔之根目錄使用的磁碟區掛接點或磁碟機路徑。 不可為 Null。|  
|**cluster_owner_node**|**NVarchar (64)**|目前的磁碟機擁有者。 如果是叢集共用磁碟區 (CSV)，擁有者為主控 MetaData Server 的節點。 不可為 Null。|  
|**is_cluster_shared_volume**|**bit**|如果此路徑所在的磁碟機為叢集共用磁碟區，則傳回 1，否則傳回 0。|  
  
## <a name="remarks"></a>備註  
 SQL Server 容錯移轉叢集執行個體 (FCI) 必須在 FCI 的所有節點之間使用共用儲存體，以便儲存資料和記錄檔案。 這份檢視表所列出的磁碟是與執行個體相關聯之叢集資源群組中的磁碟，也是可用於資料或記錄檔案儲存的僅有磁碟。  
  
> [!NOTE]  
>  此視圖將會取代未來版本中 [sys.dm_io_cluster_shared_drives &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-io-cluster-shared-drives-transact-sql.md) 。  
  
## <a name="permissions"></a>權限  
 使用者必須具有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的 VIEW SERVER STATE 權限。  
  
## <a name="examples"></a>範例  
 下列範例會使用 sys.dm_io_cluster_valid_path_names 來判斷叢集伺服器執行個體上的共用磁碟機：  
  
```  
SELECT * FROM sys.dm_io_cluster_valid_path_names;  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_os_cluster_nodes &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-cluster-nodes-transact-sql.md)   
 [sys.dm_io_cluster_shared_drives &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-io-cluster-shared-drives-transact-sql.md)   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)  
  
  

