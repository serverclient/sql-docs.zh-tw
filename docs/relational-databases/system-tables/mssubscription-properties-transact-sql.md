---
description: MSsubscription_properties (Transact-SQL)
title: MSsubscription_properties (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSsubscription_properties
- MSsubscription_properties_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSsubscription_properties system table
ms.assetid: f96fc1ae-b798-4b05-82a7-564ae6ef23b8
author: cawrites
ms.author: chadam
ms.openlocfilehash: 9927dd1e15f04a073d0fb3cf7f95e13b1f425e0c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100488"
---
# <a name="mssubscription_properties-transact-sql"></a>MSsubscription_properties (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSsubscription_properties** 資料表包含在訂閱者端執行複寫代理程式所需之參數資訊的資料列。 這個資料表是儲存在「訂閱者」的訂閱資料庫中以供提取訂閱之用，或是儲存在「散發者」的散發資料庫中供發送訂閱之用。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**publisher**|**sysname**|發行者的名稱。|  
|**publisher_db**|**sysname**|發行者資料庫的名稱。|  
|**出版**|**sysname**|發行集的名稱。|  
|**publication_type**|**int**|發行集類型：<br /><br /> **0** = 交易式。<br /><br /> **2** = 合併。|  
|**publisher_login**|**sysname**|用於發行者端之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證的登入識別碼。|  
|**publisher_password**|**Nvarchar (524)**|用於發行者端之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證的密碼 (已加密)。|  
|**publisher_security_mode**|**int**|在發行者端實作的安全性模式：<br /><br /> **0**  =  [!INCLUDE[msCoName](../../includes/msconame-md.md)] SQL Server 驗證。<br /><br /> **1** 個  =  [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 驗證。<br /><br /> **2** = 同步處理觸發程式會使用靜態 **sysservers** 專案來執行遠端程序呼叫 (RPC) ，而 *發行者* 必須定義在 **sysservers** 資料表中，做為遠端伺服器或連結的伺服器。|  
|**轉銷商**|**sysname**|散發者的名稱。|  
|**distributor_login**|**sysname**|在散發者端用於執行 SQL Server 驗證的登入識別碼。|  
|**distributor_password**|**Nvarchar (524)**|用於散發者端之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證的密碼 (已加密)。|  
|**distributor_security_mode**|**int**|在散發者端實作的安全性模式：<br /><br /> **0**  =  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證。<br /><br /> **1** = Windows 驗證。|  
|**ftp_address**|**sysname**|散發者的檔案傳輸通訊協定 (FTP) 服務的網路位址。|  
|**ftp_port**|**int**|散發者的 FTP 服務通訊埠編號。|  
|**ftp_login**|**sysname**|用於連接到 FTP 服務的使用者名稱。|  
|**ftp_password**|**Nvarchar (524)**|用於連接到 FTP 服務的使用者密碼。|  
|**alt_snapshot_folder**|**nvarchar(255)**|指定快照集替代資料夾的位置。|  
|**working_directory**|**nvarchar(255)**|用於儲存資料和結構描述檔案的工作目錄名稱。|  
|**use_ftp**|**bit**|指定利用 FTP 而不是一般通訊協定來擷取快照集。 如果是 **1**，則會使用 FTP。|  
|**dts_package_name**|**sysname**|指定 Data Transformation Services (DTS) 封裝的名稱。|  
|**dts_package_password**|**Nvarchar (524)**|指定封裝的密碼。|  
|**dts_package_location**|**int**|DTS 封裝的儲存位置。|  
|**enabled_for_syncmgr**|**bit**|指定是否能夠利用 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Synchronization Manager 來同步處理訂閱。<br /><br /> **0** = 訂用帳戶未向同步處理管理員註冊。<br /><br /> **1** = 訂用帳戶已向同步處理管理員註冊，而且可以在不啟動的情況下同步處理 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 。|  
|**offload_agent**|**bit**|指定是否能從遠端啟動代理程式。 如果是 **0**，則無法從遠端啟動代理程式。|  
|**offload_server**|**sysname**|指定遠端啟用所用之伺服器的網路名稱。|  
|**dynamic_snapshot_location**|**nvarchar(255)**|指定儲存快照集檔案的資料夾路徑。|  
|**use_web_sync**|**bit**|指定是否能夠利用 HTTP 來同步處理訂閱。 值為 **1** 表示已啟用這項功能。|  
|**internet_url**|**nvarchar(260)**|代表複寫接聽程式之 Web 同步處理位置的 URL。|  
|**internet_login**|**sysname**|當使用驗證來連接到主控 Web 同步處理的 Web 服務器時，合併代理程式所使用的登入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|**internet_password**|**Nvarchar (524)**|當使用驗證來連接到主控 Web 同步處理的 Web 服務器時，合併代理程式所使用之登入的密碼 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|**internet_security_mode**|**int**|連接到主控 Web 同步處理的 Web 服務器時所使用的驗證模式， **1** 值表示 Windows 驗證， **0** 值表示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證。|  
|**internet_timeout**|**int**|Web 同步處理要求到期之前的時間長度 (以秒為單位)。|  
|**hostname**|**sysname**|指定在聯結篩選的 **WHERE** 子句或邏輯記錄關聯性中使用這個函數時 **HOST_NAME** 的值。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [&#40;Transact-sql&#41;的複寫視圖 ](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [sp_helppullsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-helppullsubscription-transact-sql.md)   
 [sp_helpsubscription &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-helpsubscription-transact-sql.md)   
 [sp_helpsubscription_properties &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helpsubscription-properties-transact-sql.md)  
  
  
