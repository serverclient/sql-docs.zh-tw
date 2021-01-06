---
title: 變更主要 & 次要記錄傳送角色
description: 了解如何設定您的次要資料庫以作為 SQL Server 記錄傳送解決方案的主要複本。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: log-shipping
ms.topic: conceptual
helpviewer_keywords:
- log shipping [SQL Server], role changes
- secondary data files [SQL Server], roles changed between
- primary databases [SQL Server]
- initial role changes [SQL Server]
- log shipping [SQL Server], failover
- failover [SQL Server], log shipping
ms.assetid: 2d7cc40a-47e8-4419-9b2b-7c69f700e806
author: cawrites
ms.author: chadam
ms.openlocfilehash: 927ea18c7644d18e4dcf2a094e9ba0f962dcbd54
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644444"
---
# <a name="change-roles-between-primary-and-secondary-log-shipping-servers-sql-server"></a>變更主要與次要記錄傳送伺服器間的角色 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 記錄傳送組態容錯移轉到次要伺服器之後，您可以設定次要資料庫做為主要資料庫。 接著，您就可以視需要交換主要與次要資料庫。  
  
## <a name="performing-the-initial-role-change"></a>執行初始角色變更  
 初次想要容錯移轉到次要資料庫，並將它作為主要資料庫時，您必須採取一連串的步驟。 遵循並完成這些初始步驟之後，您就可以輕輕鬆鬆交換主要資料庫與次要資料庫間的角色。  
  
1.  從主要資料庫手動容錯移轉到次要資料庫。 請確定要使用 NORECOVERY，在主要伺服器上備份使用中的交易記錄檔。 如需詳細資訊，請參閱 [容錯移轉至記錄傳送次要 &#40;SQL Server&#41;](../../database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server.md)。  
  
2.  停用原始主要伺服器上的記錄傳送備份作業，以及原始次要伺服器上的複製與還原作業。  
  
3.  在次要資料庫上 (要它作為新的主要資料庫)，使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 設定記錄傳送。 如需詳細資訊，請參閱[設定記錄傳送 &#40;SQL Server&#41;](../../database-engine/log-shipping/configure-log-shipping-sql-server.md)。 包括以下步驟：  
  
    1.  使用您已為原始主要伺服器建立的相同共用，來建立備份。  
  
    2.  新增次要資料庫時，請在 **[次要資料庫設定]** 對話方塊的 **[次要資料庫]** 方塊中，輸入原始的資料庫名稱。  
  
    3.  在 **[次要資料庫設定]** 對話方中，選取 **[否，次要資料庫已初始化]** 。  
  
4.  如果您先前的記錄傳送組態已啟用記錄傳送監視，請將記錄傳送監視重新設定為監視新的記錄傳送組態。  將 threshold_alert_enabled 設定為 1 會指定當超過 restore_threshold 時將會引發警示。 執行下列命令，以資料庫的名稱取代 *database_name* ：  
  
    1.  **在新的主要伺服器上**  
  
         執行下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式：  
  
        ```sql  
        -- Statement to execute on the new primary server  
        USE msdb  
        GO  
        EXEC master.dbo.sp_change_log_shipping_secondary_database @secondary_database = N'database_name', @threshold_alert_enabled = 1;  
        GO  
        ```  
  
    2.  **在新的次要伺服器上**  
  
         執行下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式：  
  
        ```sql  
        -- Statement to execute on the new secondary server  
        USE msdb  
        GO  
        EXEC master.dbo.sp_change_log_shipping_primary_database @database=N'database_name', @threshold_alert_enabled = 1;  
        GO  
        ```  
  
## <a name="swapping-roles"></a>交換角色  
 完成上述初始角色變更的步驟後，可遵循此節的步驟變更主要資料庫與次要資料庫間的角色。 若要執行角色變更，請遵循以下的一般步驟：  
  
1.  使次要資料庫連線工作，使用 NORECOVERY 備份主要伺服器上的交易記錄檔。  
  
2.  停用原始主要伺服器上的記錄傳送備份作業，以及原始次要伺服器上的複製與還原作業。  
  
3.  啟用次要伺服器 (新的主要伺服器) 上的記錄傳送備份作業，以及主要伺服器 (新的次要伺服器) 上的複製與還原工作。  
  
> [!IMPORTANT]  
>  當您將次要資料庫變更為主要資料庫時，為了提供一致的經驗給使用者和應用程式，可能需要在新主要伺服器執行個體上為資料庫重新建立部份或全部的中繼資料，例如登入和作業。 如需詳細資訊，請參閱 [在另一個伺服器執行個體上提供可用的資料庫時，管理中繼資料 &#40;SQL Server&#41;](../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [容錯移轉至記錄傳送次要 &#40;SQL Server&#41;](../../database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server.md)  
  
-   [角色切換後針對登入和作業進行管理 &#40;SQL Server&#41;](../../sql-server/failover-clusters/management-of-logins-and-jobs-after-role-switching-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [記錄傳送資料表與預存程序](../../database-engine/log-shipping/log-shipping-tables-and-stored-procedures.md)  
  
  
