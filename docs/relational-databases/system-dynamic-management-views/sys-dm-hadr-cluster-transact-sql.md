---
description: sys.dm_hadr_cluster (Transact-SQL)
title: sys.dm_hadr_cluster (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 01/31/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_hadr_cluster
- dm_hadr_cluster_HADR
- sys.dm_hadr_cluster_TSQL
- dm_hadr_cluster
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- sys.dm_hadr_cluster catalog view
- Availability Groups [SQL Server], WSFC clusters
ms.assetid: 13ce70e4-9d43-4a80-a826-099e6213bf85
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: aa289a58047e1108e94a6b70f86eb366807f5af1
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097641"
---
# <a name="sysdm_hadr_cluster-transact-sql"></a>sys.dm_hadr_cluster (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  如果裝載已啟用之實例的 Windows Server 容錯移轉叢集 (WSFC) 節點 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 具有 WSFC 仲裁， **sys.dm_hadr_cluster** 會傳回一個資料列，該資料列會公開叢集名稱以及有關仲裁的資訊。 如果 WSFC 節點沒有仲裁，則不傳回任何資料列。  
 > [!TIP]
 > 從開始 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] ，這個動態管理檢視除了 Always On 可用性群組之外，還支援 Always On 容錯移轉叢集實例。

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**cluster_name**|**nvarchar(128)**|WSFC 叢集的名稱，該叢集會裝載啟用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 執行個體。|  
|**quorum_type**|**tinyint**|這個 WSFC 叢集所使用的仲裁類型，可為下列其中一個值：<br /><br /> 0 = 節點多數。 此仲裁設定可維持一半節點的失敗數 (四捨五入) 減一。 例如，在七個節點的叢集上，這個仲裁設定可以維持三個節點失敗數。<br /><br /> 1 = 節點與磁碟多數。 如果磁碟見證依然在線上，此仲裁設定可維持一半節點的失敗數 (四捨五入)。 例如，如果在六個節點的叢集中，有磁碟見證處於線上，則可維持三個節點失敗數。 如果磁碟見證離線或失敗，此仲裁設定可維持一半節點的失敗數 (四捨五入) 減一。 例如，如果在六個節點的叢集中，有失敗的磁碟見證，則可維持兩個節點 (3-1=2) 失敗數。<br /><br /> 2 = 節點與檔案共用多數。 此仲裁設定的運作方式類似於「節點與磁碟多數」，但是會使用檔案共用見證，而非磁碟見證。<br /><br /> 3 = 無多數，僅限磁碟。 如果仲裁磁碟在線上，此仲裁設定可維持所有節點的失敗數，除了一以外。<br /><br /> 4 = 未知的仲裁。 叢集的未知仲裁。<br /><br /> 5 = 雲端見證。 叢集會利用仲裁仲裁的 Microsoft Azure。 如果有可用的雲端見證，叢集可以承受一半節點的失敗 (進位) 。|  
|**quorum_type_desc**|**varchar(50)**|**Quorum_type** 的描述，下列其中一個：<br /><br /> NODE_MAJORITY<br /><br /> NODE_AND_DISK_MAJORITY<br /><br /> NODE_AND_FILE_SHARE_MAJORITY<br /><br /> NO_MAJORITY:_DISK_ONLY <br /><br /> UNKNOWN_QUORUM <br /><br /> CLOUD_WITNESS|  
|**quorum_state**|**tinyint**|WSFC 仲裁的狀態，下列其中一個值：<br /><br /> 0 = 未知仲裁狀態<br /><br /> 1 = 正常仲裁<br /><br /> 2 = 強制仲裁|  
|**quorum_state_desc**|**varchar(50)**|**Quorum_state** 的描述，下列其中一個：<br /><br /> UNKNOWN_QUORUM_STATE<br /><br /> NORMAL_QUORUM<br /><br /> FORCED_QUORUM|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [Always On 可用性群組動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/always-on-availability-groups-dynamic-management-views-functions.md)   
 [AlwaysOn 可用性群組目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/always-on-availability-groups-catalog-views-transact-sql.md)   
 [監視可用性群組 &#40;Transact-sql&#41;](../../database-engine/availability-groups/windows/monitor-availability-groups-transact-sql.md)   
 [sys.dm_hadr_cluster_members &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-members-transact-sql.md)  
  
  
