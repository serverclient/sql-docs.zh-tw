---
title: 管理可用性群組中資料庫的作業登入
description: 描述如何為使用 Always On 可用性群組中參與資料庫的作業管理登入。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], deploying
- Availability Groups [SQL Server], failover
- failover [SQL Server], AlwaysOn Availability Groups
ms.assetid: d7da14d3-848c-44d4-8e49-d536a1158a61
author: cawrites
ms.author: chadam
ms.openlocfilehash: 80f55802e887e5f5064a454d3f59532e86ac144b
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644204"
---
# <a name="manage-logins-for-jobs-using-databases-in-an-always-on-availability-group"></a>為使用 Always On 可用性群組中資料庫的作業管理登入
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  您應該在每個 AlwaysOn 可用性群組的主要資料庫及其對應的次要資料庫上，固定維護一組相同的使用者登入和 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 代理程式作業。 登入和作業必須在裝載可用性群組之可用性複本的每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體上重新產生。  
  
-   **[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 代理程式作業**  
  
     您必須從裝載原始主要複本的伺服器執行個體，將相關作業手動複製到裝載原始次要複本的伺服器執行個體。 對於所有資料庫，您需要在每個相關作業的開頭加入邏輯，讓工作只在主要資料庫上執行，也就是說，只有在本機複本是資料庫的主要複本時才執行。  
  
     裝載可用性群組之可用性複本的伺服器執行個體可能會以不同的磁帶機代號或之類的方式予以個別設定。 每個可用性複本的作業都必須允許這類差異。  
  
     請注意，備份作業可以使用 [sys.fn_hadr_is_preferred_backup_replica](../../../relational-databases/system-functions/sys-fn-hadr-backup-is-preferred-replica-transact-sql.md) 函數根據可用性群組備份喜好設定，識別本機複本是否為備份慣用的複本。 使用 [維護計畫精靈](../../../relational-databases/maintenance-plans/use-the-maintenance-plan-wizard.md) 建立的備份作業原本使用此函數。 至於其他備份作業，我們建議您使用此函數做為備份作業中的條件，如此備份作業就只會在慣用複本上執行。 如需詳細資訊，請參閱 [使用中次要：在次要複本上備份 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/active-secondaries-backup-on-secondary-replicas-always-on-availability-groups.md)。  
  
-   **登入**  
  
     如果您要使用自主資料庫，可以在資料庫中設定包含的使用者，而且不需要針對這些使用者，在裝載次要複本的伺服器執行個體上建立登入。 若為非自主可用性資料庫，您就必須在裝載可用性複本的伺服器上執行個體上建立登入的使用者。 如需詳細資訊，請參閱 [CREATE USER &#40;Transact-SQL&#41;](../../../t-sql/statements/create-user-transact-sql.md)。  
  
     如果有任何應用程式使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 驗證或本機 Windows 登入，請參閱本主題稍後的 [Logins Of Applications That Use SQL Server Authentication or a Local Windows Login](../../../database-engine/availability-groups/windows/logins-and-jobs-for-availability-group-databases.md#SSauthentication) (使用 SQL Server 驗證或本機 Windows 登入之應用程式的登入)。  
  
    > [!NOTE]  
    >  在伺服器執行個體上未定義或定義不正確之 SQL Server 登入的資料庫使用者無法登入此執行個體。 這類使用者就是伺服器執行個體上的資料庫 *「被遺棄使用者」* (Orphaned User)。 如果某位使用者在給定的伺服器執行個體上被遺棄，您可以隨時設定使用者登入。 如需詳細資訊，請參閱[孤立的使用者疑難排解 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server.md)。  
  
-   **其他中繼資料**  
  
     登入和作業不是唯一需要在裝載給定可用性群組之次要複本的每一個伺服器執行個體上重新建立的資訊。 例如，您可能需要重新建立伺服器組態設定、認證、加密資料、權限、複寫設定、Service Broker 應用程式、觸發程序 (伺服器層級) 等等。 如需詳細資訊，請參閱 [在另一個伺服器執行個體上提供可用的資料庫時，管理中繼資料 &#40;SQL Server&#41;](../../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)。  
  
##  <a name="logins-of-applications-that-use-sql-server-authentication-or-a-local-windows-login"></a><a name="SSauthentication"></a> Logins Of Applications That Use SQL Server Authentication or a Local Windows Login  
 如果應用程式使用 SQL Server 驗證或本機 Windows 登入，可能會由於 SID 不相符造成遠端 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體無法解析該應用程式的登入。 SID 不相符將導致此登入成為遠端伺服器執行個體上的被遺棄使用者。 若應用程式是在容錯移轉後連接到鏡像資料庫或記錄傳送資料庫，或者連接到從備份初始化的複寫訂閱者資料庫，可能就會發生這個問題。  
  
 建議您在設定這類應用程式而要使用由遠端 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體所裝載的資料庫時，應採取預防措施以避免此問題。 預防的方法包括從本機 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體傳送登入和密碼到遠端 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體。 如需如何預防此問題的詳細資訊，請參閱知識庫文件 918992：[如何在 SQL Server 的執行個體之間傳送登入和密碼](https://support.microsoft.com/kb/918992/)。  
  
> [!NOTE]  
>  此問題會影響位於不同電腦上的 Windows 本機帳戶。 但是，網域帳戶不至於發生此問題，因為每一部電腦上的 SID 都相同。  
  
 如需詳細資訊，請參閱 Database Engine 部落格文章： [Orphaned Users with Database Mirroring and Log Shipping](/archive/blogs/sqlserverfaq/orphaned-users-with-database-mirroring-and-log-shipping) (孤立的使用者與資料庫鏡像及記錄傳送)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [建立登入](../../../relational-databases/security/authentication-access/create-a-login.md)  
  
-   [建立資料庫使用者](../../../relational-databases/security/authentication-access/create-a-database-user.md)。  
  
-   [建立作業](../../../ssms/agent/create-a-job.md)  
  
-   [在另一個伺服器執行個體上提供可用的資料庫時，管理中繼資料 &#40;SQL Server&#41;](../../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [自主資料庫](../../../relational-databases/databases/contained-databases.md)   
 [建立作業](../../../ssms/agent/create-jobs.md)  
  
