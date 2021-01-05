---
title: 變更中繼資料：跨叢集可用性群組移轉
description: 進行跨叢集移轉時，藉由變更 SQL Server 執行個體的 HADR 叢集內容，即可變更由哪個叢集來管理 Always On 可用性群組內可用性複本的中繼資料。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], WSFC clusters
- Availability replicas [SQL Server], change WSFC cluster context
ms.assetid: ecd99f91-b9a2-4737-994e-507065a12f80
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: bce85840c2a0b2aa1cabbbbeb6d518b9c9c4acb4
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643098"
---
# <a name="change-which-cluster-manages-the-metadata-for-replicas-in-an-always-on-availability-group"></a>變更哪個叢集管理 Always On 可用性群組複本的中繼資料

[!INCLUDE[sql windows only](../../../includes/applies-to-version/sql-windows-only.md)]

  本主題描述如何使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 和更新版本中的 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 切換 [!INCLUDE[ssSQL11SP1](../../../includes/sssql11sp1-md.md)] 執行個體的 HADR 叢集內容。 「HADR 叢集內容」  會決定哪個 Windows Server 容錯移轉叢集 (WSFC) 叢集管理伺服器執行個體所裝載可用性複本的中繼資料。  
  
 僅在跨叢集移轉 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 至新 WSFC 叢集上的 [!INCLUDE[ssSQL11SP1](../../../includes/sssql11sp1-md.md)] 執行個體時，才切換 HADR 叢集內容。 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 的跨叢集移轉支援以最短的可用性群組停機時間升級為 [!INCLUDE[win8](../../../includes/win8-md.md)] 或 [!INCLUDE[win8srv](../../../includes/win8srv-md.md)] 。 如需詳細資訊，請參閱 [針對作業系統升級進行 AlwaysOn 可用性群組的跨叢集移轉](/previous-versions/sql/sql-server-2012/jj873730(v=msdn.10))。  
  
> [!CAUTION]  
>  僅在跨叢集移轉 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 部署時才切換 HADR 叢集內容。  
  
##  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項  
  
-   您只能將 HADR 叢集內容從本機 WSFC 叢集切換至遠端叢集，然後從遠端叢集切換回本機叢集。 您無法將 HADR 叢集內容從某一個遠端叢集切換至另一個遠端叢集。  
  
-   HADR 叢集內容只有在 SQL Server 執行個體未裝載任何可用性複本時，才能切換至遠端叢集。  
  
-   遠端 HADR 叢集內容隨時可以切換回本機叢集。 不過，只要伺服器執行個體裝載任何可用性複本，內容就不能再次切換。  
  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
  
-   您變更 HADR 叢集內容所在的伺服器執行個體必須執行 [!INCLUDE[ssSQL11SP1](../../../includes/sssql11sp1-md.md)] 或更新版本 (Enterprise Edition 或更新版本)。  
  
-   伺服器執行個體必須針對 AlwaysOn 啟用。 如需詳細資訊，請參閱[啟用和停用 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server.md)。  
  
-   若要符合從本機叢集內容切換至遠端叢集的資格，伺服器執行個體不可裝載任何可用性複本。 [sys.availability_replicas](../../../relational-databases/system-catalog-views/sys-availability-replicas-transact-sql.md) 目錄檢視不應傳回任何資料列。  
  
     如果伺服器執行個體上有任何可用性複本存在，您必須先執行下列其中一項操作，才能變更 HADR 叢集內容：  
  
    |複本角色|動作|連結|  
    |------------------|------------|----------|  
    |Primary|讓可用性群組離線。|[讓可用性群組離線 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/take-an-availability-group-offline-sql-server.md)|  
    |次要|從本身的可用性群組移除複本|[將次要複本從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-replica-from-an-availability-group-sql-server.md)|  
  
-   所有同步認可複本都必須是 SYNCHRONIZED，您才能從遠端叢集切換至本機叢集。  
  
##  <a name="recommendations"></a><a name="Recommendations"></a> 建議  
  
-   我們建議您指定完整網域名稱。 這是因為，為了尋找簡短名稱的目標 IP 位址，ALTER SERVER CONFIGURATION 會使用 DNS 解析。 在某些情況下，根據 DNS 搜尋順序，使用簡短名稱可能會產生混淆。 例如，請考慮下列命令，該命令是在 `abc` 網域 (`node1.abc.com`) 中的節點上執行。 預期的目的地叢集是 `CLUS01` 網域 ( `xyz` ) 中的`clus01.xyz.com`叢集。 不過，本機網域主機也會裝載名為 `CLUS01` (`clus01.abc.com`) 的叢集。  
  
     如果已指定目標叢集的簡短名稱 `CLUS01`，則 DNS 名稱解析可能會傳回錯誤叢集 `clus01.abc.com`的 IP 位址。 為避免發生這類混淆情況，請指定目標叢集的完整名稱，如下列範例所示：  
  
    ```  
    ALTER SERVER CONFIGURATION SET HADR CLUSTER CONTEXT = 'clus01.xyz.com'  
    ```  
  
  
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
  
-   **SQL Server 登入**  
  
     需要 CONTROL SERVER 權限。  
  
-   **SQL Server 服務帳戶**  
  
     伺服器執行個體的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶必須具備：  
  
    -   開啟目的地 WSFC 叢集的權限。  
  
    -   遠端 WSFC 讀寫存取。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
 **變更可用性複本的 WSFC 叢集內容**  
  
1.  連接到裝載可用性群組之主要複本或次要複本的伺服器執行個體。  
  
2.  使用 [ALTER SERVER CONFIGURATION](../../../t-sql/statements/alter-server-configuration-transact-sql.md) 陳述式的 SET HADR CLUSTER CONTEXT 子句，如下所示：  
  
     ALTER SERVER CONFIGURATION SET HADR CLUSTER CONTEXT **=** { **'** _windows\_cluster_ **'** | LOCAL }  
  
     其中  
  
     *windows_cluster*  
     WSFC 叢集的叢集物件名稱 (CON)。 您可以指定簡短名稱或完整網域名稱。 我們建議您指定完整網域名稱。 如需詳細資訊，請參閱本主題稍後的 [建議](#Recommendations)。  
  
     LOCAL  
     本機 WSFC 叢集。  
  
### <a name="examples"></a>範例  
 下列範例會將 HADR 叢集內容變更為不同叢集。 為了識別目的地 WSFC 叢集 `clus01`，此範例會指定完整叢集物件名稱 `clus01.xyz.com`。  
  
```  
ALTER SERVER CONFIGURATION SET HADR CLUSTER CONTEXT = 'clus01.xyz.com';  
```  
  
 下列範例會將 HADR 叢集內容變更為本機 WSFC 叢集。  
  
```  
ALTER SERVER CONFIGURATION SET HADR CLUSTER CONTEXT = LOCAL;  
```  
  
##  <a name="follow-up-after-switching-the-cluster-context-of-an-availability-replica"></a><a name="FollowUp"></a> 後續操作：切換可用性複本的叢集內容之後  
 新的 HADR 叢集內容會立即生效，不需要重新啟動伺服器執行個體。 HADR 叢集內容設定是持續性的執行個體層級設定，即使伺服器執行個體重新啟動也會保持不變。  
  
 藉由查詢 [sys.dm_hadr_cluster](../../../relational-databases/system-dynamic-management-views/sys-dm-hadr-cluster-transact-sql.md) 動態管理檢視確認新的 HADR 叢集內容，如下所示：  
  
```  
SELECT cluster_name FROM sys.dm_hadr_cluster  
```  
  
 此查詢應該會傳回您設定其 HADR 叢集內容之叢集的名稱。  
  
 當 HADR 叢集內容切換至新的叢集時：  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體目前裝載的任何可用性複本的中繼資料都會清除。  
  
-   之前屬於某個可用性複本的所有資料庫現在都會是 RESTORING 狀態。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [移除可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-an-availability-group-listener-sql-server.md)  
  
-   [讓可用性群組離線 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/take-an-availability-group-offline-sql-server.md)  
  
-   [將次要複本加入至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/add-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [將次要複本從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-replica-from-an-availability-group-sql-server.md)  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
-   [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
  
-   [SQL Server 2012 技術文件](https://msdn.microsoft.com/library/bb418445\(SQL.10\).aspx)  
  
-   [SQL Server Always On 小組部落格：官方 SQL Server Always On 小組部落格](/archive/blogs/sqlalwayson/)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-availability-groups-sql-server.md)   
 [SQL Server 的 Windows Server 容錯移轉叢集 &#40;WSFC&#41;](../../../sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server.md)   
 [ALTER SERVER CONFIGURATION &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-server-configuration-transact-sql.md)  
  
