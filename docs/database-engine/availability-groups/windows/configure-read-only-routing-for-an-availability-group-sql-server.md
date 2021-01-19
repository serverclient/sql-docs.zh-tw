---
title: 設定可用性群組的唯讀路由
description: 使用 Always On 可用性群組的唯讀路由 (透過 Transact-SQL (T-SQL) 或 PowerShell)，自動將所有唯讀流量路由至次要複本。
ms.custom: seodec18
ms.date: 02/25/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- read-only routing
- Availability Groups [SQL Server], readable secondary replicas
- Availability Groups [SQL Server], read-only routing
- readable secondary replicas
- Availability Groups [SQL Server], client connectivity
- Availability Groups [SQL Server], active secondary replicas
ms.assetid: 7bd89ddd-0403-4930-a5eb-3c78718533d4
author: cawrites
ms.author: chadam
ms.openlocfilehash: f7e96df4eba36bbcb3da18a1423b5162aef557a7
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170800"
---
# <a name="configure-read-only-routing-for-an-always-on-availability-group"></a>設定 Always On 可用性群組的唯讀路由
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  若要在 [!INCLUDE[ssnoversion](../../../includes/ssnoversion-md.md)] 中將 AlwaysOn 可用性群組設定為支援唯讀路由，可以使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 或 PowerShell。 「唯讀路由」  是指 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 將合格的唯讀連接要求路由至可用之 AlwaysOn [可讀取的次要複本](../../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md) (亦即在以次要角色執行時，設定為允許唯讀工作負載的複本) 的功能。 若要支援唯讀路由，可用性群組必須具有 [可用性群組接聽程式](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)。 唯讀用戶端必須將其連接要求導向至此接聽程式，且用戶端的連接字串必須將應用程式的意圖指定為「唯讀」。 換句話說必須是 *「讀取意圖的連接要求」* (Read-Intent Connection Request)。  

唯讀路由可在 [!INCLUDE[sssql15](../../../includes/sssql16-md.md)] 及更新版本中使用。

> [!NOTE]  
>  如需如何設定可讀取之次要複本的相關資訊，請參閱 [設定可用性複本上的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)。  

  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
  
-   可用性群組必須擁有可用性群組接聽程式。 如需詳細資訊，請參閱 [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)。  
  
-   一或多個可用性複本必須設定為接受次要角色的唯讀狀態 (亦即，成為 [可讀取的次要複本](../../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md))。 如需詳細資訊，請參閱 [設定可用性複本上的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)。  
  
-   您必須連接到裝載目前主要複本的伺服器執行個體。  

-   如果使用 SQL 登入，請確定已正確地設定帳戶。 如需詳細資訊，請參閱[管理可用性群組之資料庫的登入及工作 (SQL Server)](logins-and-jobs-for-availability-group-databases.md)。
  
##  <a name="what-replica-properties-do-you-need-to-configure-to-support-read-only-routing"></a><a name="RORReplicaProperties"></a> 您必須設定哪些複本屬性，才能支援唯讀路由？  
  
-   每一個要支援唯讀路由之可讀取的次要複本，皆必須指定 *「唯讀路由 URL」* (Read-Only Routing URL)。 只有在本機複本以次要角色執行時，此 URL 才會生效。 如有必要，您必須各自指定每個複本的唯讀路由 URL。 每個唯讀路由 URL 可用於將讀取意圖的連接要求路由至特定可讀取的次要複本。 一般而言，每個可讀取的次要複本都有一個指派的唯讀路由 URL。  
  
     如需計算可用性複本之唯讀路由 URL 的相關資訊，請參閱 [Calculating read_only_routing_url for Always On](https://web.archive.org/web/20170512023255/https://blogs.msdn.microsoft.com/mattn/2012/04/25/calculating-read_only_routing_url-for-alwayson/)
  
-   如果要支援唯讀路由的可用性複本為主要複本時，則必須指定 *「唯讀路由清單」* (Read-Only Routing List)。 只有本機複本以主要角色執行時，給定的唯讀路由清單才會生效。 如有必要，您必須各自指定每個複本的這份清單。 一般而言，每份唯讀路由清單皆會包含每一個唯讀路由 URL，並在清單結尾提供本機複本的 URL。  
  
    > [!NOTE]  
    >  讀取意圖的連接要求會路由至目前主要複本的唯讀路由清單上的第一個可用項目。 不過，唯讀複本之間會支援負載平衡。 如需詳細資訊，請參閱[設定唯讀複本之間的負載平衡](../../../database-engine/availability-groups/windows/configure-read-only-routing-for-an-availability-group-sql-server.md#loadbalancing)。  
  
> [!NOTE]  
>  如需可用性群組接聽程式的相關資訊，以及唯讀路由的詳細資訊，請參閱 [可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)。  
  
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
  
|Task|權限|  
|----------|-----------------|  
|若要在建立可用性群組時設定複本|需要 **系統管理員 (sysadmin)** 固定伺服器角色的成員資格，以及 CREATE AVAILABILITY GROUP 伺服器權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。|  
|若要修改可用性複本|需要可用性群組的 ALTER AVAILABILITY GROUP 權限、CONTROL AVAILABILITY GROUP 權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。|  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
  
### <a name="configure-a-read-only-routing-list"></a>設定唯讀路由清單  
 您可以遵循下列步驟使用 TRANSACT-SQL 來設定唯讀路由。 如需程式碼範例，請參閱本節稍後的 [範例 (Transact-SQL)](#TsqlExample)。  
  
1.  連接到裝載主要複本的伺服器執行個體。  
  
2.  如果您要針對新的可用性群組指定複本，請使用 [CREATE AVAILABILITY GROUP](../../../t-sql/statements/create-availability-group-transact-sql.md)[!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式。 如果您要加入或修改現有可用性群組的複本，請使用 [ALTER AVAILABILITY GROUP](../../../t-sql/statements/alter-availability-group-transact-sql.md)[!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式。  
  
    -   若要設定次要角色的唯讀路由，請在 ADD REPLICA 或 MODIFY REPLICA WITH 子句中指定 SECONDARY_ROLE 選項，如下所示：  
  
         SECONDARY_ROLE **(** READ_ONLY_ROUTING_URL **='** TCP **://** _system-address_ **:** _port_ **')**  
  
         唯讀路由 URL 的參數如下：  
  
         *system-address*  
         這是明確識別目的地電腦系統的字串，例如系統名稱、完整網域名稱或 IP 位址。  
  
         *port*  
         這是 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體之 Database Engine 所使用的通訊埠編號。  
  
         例如：   `SECONDARY_ROLE (READ_ONLY_ROUTING_URL = N'TCP://COMPUTER01.contoso.com:1433')`  
  
         在 MODIFY REPLICA 子句中，ALLOW_CONNECTIONS 是選擇性的 (如果複本已經設定為允許唯讀連接的話)。  
  
         如需詳細資訊，請參閱 [Calculating read_only_routing_url for Always On](/archive/blogs/mattn/calculating-read_only_routing_url-for-alwayson)(計算 AlwaysOn 的 read_only_routing_url)。  
  
    -   若要設定主要角色的唯讀路由，請在 ADD REPLICA 或 MODIFY REPLICA WITH 子句中指定 PRIMARY_ROLE 選項，如下所示：  
  
         PRIMARY_ROLE **(** READ_ONLY_ROUTING_LIST **=('** _server_ **'** [ **,** ...*n* ] **))**  
  
         其中， *server* 會識別裝載可用性群組中唯讀次要複本的伺服器執行個體。  
  
         例如：   `PRIMARY_ROLE (READ_ONLY_ROUTING_LIST=('Server1','Server2'))`  
  
        > [!NOTE]  
        >  您必須先設定唯讀路由 URL，然後再設定唯讀路由清單。  
  
###  <a name="configure-load-balancing-across-read-only-replicas"></a><a name="loadbalancing"></a> 設定唯讀複本之間的負載平衡  
 從 [!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)]開始，您可以在一組唯讀複本中設定負載平衡。 以往，唯讀路由一律會將流量導向路由清單中第一個可用的複本。 若要利用這項功能，使用單層巢狀括弧括住 **CREATE AVAILABILITY GROUP** 或 **ALTER AVAILABILITY GROUP** 命令中的 **READ_ONLY_ROUTING_LIST** 伺服器執行個體。  
  
 例如，下列路由清單會跨 `Server1` 和 `Server2`這兩個唯讀複本，進行讀取意圖連接要求的負載平衡。 括住這些伺服器的巢狀括弧可識別負載平衡集。 如果該負載平衡集中都沒有可用的複本，它將繼續嘗試循序連接到唯讀路由清單中的其他複本 `Server3` 和 `Server4`。  
  
```sql  
READ_ONLY_ROUTING_LIST = (('Server1','Server2'), 'Server3', 'Server4')  
```  
  
 請注意，路由清單中的每個項目本身也可以是一組負載平衡的唯讀複本。 下列範例示範此作業。  
  
```sql  
READ_ONLY_ROUTING_LIST = (('Server1','Server2'), ('Server3', 'Server4', 'Server5'), 'Server6')  
```  
  
 僅支援單層巢狀括弧。  
  
###  <a name="example-transact-sql"></a><a name="TsqlExample"></a> 範例 &#40;Transact-SQL&#41;  
 下列範例會將現有可用性群組 `AG1` 的兩個可用性複本修改成支援唯讀路由 (如果其中一個複本目前擁有主要角色的話)。 為了識別裝載可用性複本的伺服器執行個體，此範例會指定執行個體名稱：`COMPUTER01` 和 `COMPUTER02`。  
  
```  
ALTER AVAILABILITY GROUP [AG1]  
 MODIFY REPLICA ON  
N'COMPUTER01' WITH   
(SECONDARY_ROLE (ALLOW_CONNECTIONS = READ_ONLY));  
ALTER AVAILABILITY GROUP [AG1]  
 MODIFY REPLICA ON  
N'COMPUTER01' WITH   
(SECONDARY_ROLE (READ_ONLY_ROUTING_URL = N'TCP://COMPUTER01.contoso.com:1433'));  
  
ALTER AVAILABILITY GROUP [AG1]  
 MODIFY REPLICA ON  
N'COMPUTER02' WITH   
(SECONDARY_ROLE (ALLOW_CONNECTIONS = READ_ONLY));  
ALTER AVAILABILITY GROUP [AG1]  
 MODIFY REPLICA ON  
N'COMPUTER02' WITH   
(SECONDARY_ROLE (READ_ONLY_ROUTING_URL = N'TCP://COMPUTER02.contoso.com:1433'));  
  
ALTER AVAILABILITY GROUP [AG1]   
MODIFY REPLICA ON  
N'COMPUTER01' WITH   
(PRIMARY_ROLE (READ_ONLY_ROUTING_LIST=('COMPUTER02','COMPUTER01')));  
  
ALTER AVAILABILITY GROUP [AG1]   
MODIFY REPLICA ON  
N'COMPUTER02' WITH   
(PRIMARY_ROLE (READ_ONLY_ROUTING_LIST=('COMPUTER01','COMPUTER02')));  
GO  
  
```  
  
##  <a name="using-powershell"></a><a name="PowerShellProcedure"></a> 使用 PowerShell  
  
### <a name="configure-a-read-only-routing-list"></a>設定唯讀路由清單  
 您可以依照下列步驟使用 PowerShell 來設定唯讀路由。 如需程式碼範例，請參閱本節稍後的 [範例 (PowerShell)](#PSExample)。  
  
1.  將預設值 (**cd**) 設定為裝載主要複本的伺服器執行個體。  
  
2.  將可用性複本加入可用性群組時，請使用 **New-SqlAvailabilityReplica** Cmdlet。 修改現有的可用性複本時，請使用 **Set-SqlAvailabilityReplica** Cmdlet。 相關參數如下所示：  
  
    -   若要設定次要角色的唯讀路由，請指定 **ReadonlyRoutingConnectionUrl"** _url_ **"** 參數。  
  
         其中， *url* 是路由至複本進行唯讀連接時要使用的連接性完整網域名稱 (FQDN) 和通訊埠。 例如：`-ReadonlyRoutingConnectionUrl "TCP://DBSERVER8.manufacturing.Adventure-Works.com:7024"`  
  
         如需詳細資訊，請參閱 [Calculating read_only_routing_url for Always On](/archive/blogs/mattn/calculating-read_only_routing_url-for-alwayson)(計算 AlwaysOn 的 read_only_routing_url)。  
  
    -   若要設定主要角色的連接存取，請指定 **ReadonlyRoutingList"** _server_ **"** [ **,** ...*n* ]，其中 *server* 會識別裝載可用性群組中唯讀次要複本的伺服器執行個體。 例如：`-ReadOnlyRoutingList "SecondaryServer","PrimaryServer"`  
  
        > [!NOTE]  
        >  您必須先設定複本的唯讀路由 URL，然後再設定其唯讀路由清單。  
  
    > [!NOTE]  
    >  若要檢視 Cmdlet 的語法，請在 **PowerShell 環境中使用** Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Cmdlet。 如需詳細資訊，請參閱 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)。  
  
### <a name="set-up-and-use-the-sql-server-powershell-provider"></a>設定和使用 SQL Server PowerShell 提供者  
  
-   [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md)  
  
-   [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)  
  
###  <a name="example-powershell"></a><a name="PSExample"></a> 範例 (PowerShell)  
 下列範例會將可用性群組中的主要複本和一個次要複本設定為唯讀路由。 首先，此範例會將唯讀路由 URL 指派給每個複本。 然後，它會設定主要複本的唯讀路由清單。 在連接字串中設定 "ReadOnly" 屬性的連接將會重新導向至次要複本。 如果這個次要複本無法讀取 (由 **ConnectionModeInSecondaryRole** 設定決定)，連接將會導向回到主要複本。  
  
```  
Set-Location SQLSERVER:\SQL\PrimaryServer\default\AvailabilityGroups\MyAg  
$primaryReplica = Get-Item "AvailabilityReplicas\PrimaryServer"  
$secondaryReplica = Get-Item "AvailabilityReplicas\SecondaryServer"  
  
Set-SqlAvailabilityReplica -ReadOnlyRoutingConnectionUrl "TCP://PrimaryServer.domain.com:1433" -InputObject $primaryReplica  
Set-SqlAvailabilityReplica -ReadOnlyRoutingConnectionUrl "TCP://SecondaryServer.domain.com:1433" -InputObject $secondaryReplica  
Set-SqlAvailabilityReplica -ReadOnlyRoutingList "SecondaryServer","PrimaryServer" -InputObject $primaryReplica  
```  
  
##  <a name="follow-up-after-configuring-read-only-routing"></a><a name="FollowUp"></a> 後續操作：設定唯讀路由之後  
 一旦目前的主要複本和可讀取的次要複本都設定為支援兩種角色的唯讀路由之後，可讀取的次要複本就可以從經由可用性群組接聽程式連接的用戶端接收讀取意圖連接要求。  
  
> [!TIP]  
>  當您使用 [bcp 公用程式](../../../tools/bcp-utility.md) 或 [sqlcmd 公用程式](../../../tools/sqlcmd-utility.md)時，您可以藉由指定 **-K ReadOnly** 參數，為啟用唯讀存取的任何次要複本指定唯讀存取。  
  
###  <a name="requirements-and-recommendations-for-client-connection-strings"></a><a name="ConnStringReqsRecs"></a> 用戶端連接字串的需求和建議  
 若要讓用戶端應用程式使用唯讀路由，其連接字串必須滿足下列需求：  
  
-   使用 TCP 通訊協定。  
  
-   將應用程式意圖屬性 (Attribute)/屬性 (Property) 設定成唯讀。  
  
-   參考設定為支援唯讀路由之可用性群組的接聽程式。  
  
-   參考該可用性群組中的資料庫。  
  
 此外，我們建議連接字串要啟用多重子網路容錯移轉，以便針對每個子網路上的每個複本支援平行用戶端執行緒。 這樣做可在容錯移轉之後盡量縮短用戶端重新連接時間。  
  
 連接字串的語法主要取決於應用程式所使用的 SQL Server 提供者。 下列 .NET Framework Data Provider 4.0.2 for SQL Server 範例連接字串說明了針對唯讀路由運作時必要且建議的連接字串部分。  
  
```  
Server=tcp:MyAgListener,1433;Database=Db1;IntegratedSecurity=SSPI;ApplicationIntent=ReadOnly;MultiSubnetFailover=True  
```  
  
 如需唯讀應用程式意圖和唯讀路由的詳細資訊，請參閱 [可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)。  
  
### <a name="if-read-only-routing-is-not-working-correctly"></a>如果唯讀路由未正確運作  
 如需針對唯讀路由組態進行疑難排解的相關資訊，請參閱 [唯讀路由未正確運作](../../../database-engine/availability-groups/windows/troubleshoot-always-on-availability-groups-configuration-sql-server.md#ROR)。  
  
##  <a name="next-steps"></a><a name="RelatedTasks"></a> 後續步驟 
**若要檢視唯讀路由組態**  
  
-   [sys.availability_read_only_routing_lists &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-read-only-routing-lists-transact-sql.md)  
  
-   [sys.availability_replicas &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-availability-replicas-transact-sql.md) (**read_only_routing_url** 資料行)  
  
**若要設定用戶端連接存取**  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
-   [設定可用性複本的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)  
  
**若要在應用程式中使用連接字串**  
  
-   [高可用性/災害復原的 SQL Server Native Client 支援](../../../relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery.md)  
  
-   [搭配 SQL Server Native Client 使用連接字串關鍵字](../../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md)  
  
**部落格：**  
  
-    [Calculating read_only_routing_url for Always On](/archive/blogs/mattn/calculating-read_only_routing_url-for-alwayson)  
  
-    [SQL Server Always On 小組部落格：官方 SQL Server Always On 小組部落格](/archive/blogs/sqlalwayson/)  
  
-    [CSS SQL Server 工程師部落格](/archive/blogs/psssql/)  
  
**白皮書：**  
  
-    [Microsoft 的 SQL Server 2012 白皮書](https://social.technet.microsoft.com/wiki/contents/articles/13146.white-paper-gallery-for-sql-server.aspx#[Category]SQLServer2012)  
  
-    [SQL Server 客戶諮詢團隊白皮書](https://techcommunity.microsoft.com/t5/DataCAT/bg-p/DataCAT/)  

**其他內容**

- [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   

- [使用中次要：可讀取的次要複本 &#40;Always On 可用性群組&#41;](../../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)   

- [關於可用性複本的用戶端連接存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/about-client-connection-access-to-availability-replicas-sql-server.md)   
 
- [可用性群組接聽程式、用戶端連線及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)