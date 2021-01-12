---
description: sys.dm_io_cluster_shared_drives (Transact-SQL)
title: sys.dm_io_cluster_shared_drives (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_io_cluster_shared_drives_TSQL
- sys.dm_io_cluster_shared_drives
- dm_io_cluster_shared_drives_TSQL
- dm_io_cluster_shared_drives
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_io_cluster_shared_drives dynamic management view
ms.assetid: c8fcced8-c780-49dc-99bd-6beb3ca532c4
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 42c6b5bc337ae80099037181dc0d5fb0681f36bf
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096524"
---
# <a name="sysdm_io_cluster_shared_drives-transact-sql"></a>sys.dm_io_cluster_shared_drives (Transact-SQL)
[!INCLUDE [sql-asdbmi-pdw](../../includes/applies-to-version/sql-asdbmi-pdw.md)]

  如果目前的伺服器執行個體是一個叢集伺服器，這個檢視會傳回每個共用磁碟機的磁碟機名稱。 如果目前的伺服器執行個體不是叢集執行個體，它會傳回空的資料列集。  
  
> [!NOTE]  
>  若要從呼叫這個 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用名稱 **sys.dm_pdw_nodes_io_cluster_shared_drives**。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**DriveName**|**nchar(2)**|代表參與叢集共用磁碟陣列之個別磁碟的磁碟機名稱 (磁碟機代號)。 資料行不可為 Null。|  
|**pdw_node_id**|**int**|**適用** 于： ssPDW<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="remarks"></a>備註  
 當啟用叢集時，容錯移轉叢集執行個體要求資料和記錄檔必須放在共用磁碟上，以便在執行個體容錯移轉至其他節點時，仍可存取這些檔案。 此檢視表中的每個資料列，都代表這個叢集 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體所用的單一共用磁碟。 只有此檢視表所列出的磁碟可以用來儲存這個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的資料或記錄檔。 這份檢視中所列出的磁碟，即為與該執行個體相關聯之叢集資源群組中的磁碟。  
  
> [!NOTE]  
>  這個檢視表在未來的版本將會被取代。 我們建議您改為使用 [sys.dm_io_cluster_valid_path_names &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-io-cluster-valid-path-names-transact-sql.md) 。  
  
## <a name="permissions"></a>權限  
 使用者必須具有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的 VIEW SERVER STATE 權限。  
  
## <a name="examples"></a>範例  
 下列範例會使用 sys.dm_io_cluster_shared_drives 來判斷叢集伺服器執行個體上的共用磁碟機：  
  
```  
SELECT * FROM sys.dm_io_cluster_shared_drives;  
```  
  
 結果集如下：  
  
 DriveName  
  
 --------\-  
  
 m  
  
 n  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_io_cluster_valid_path_names &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-io-cluster-valid-path-names-transact-sql.md)   
 [sys.dm_os_cluster_nodes &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-cluster-nodes-transact-sql.md)   
 [sys.fn_servershareddrives &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-servershareddrives-transact-sql.md)   
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)  
  
  
