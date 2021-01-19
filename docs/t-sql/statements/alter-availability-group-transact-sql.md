---
description: ALTER AVAILABILITY GROUP (Transact-SQL)
title: ALTER AVAILABILITY GROUP (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/02/2018
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_AVAILABILITY_GROUP_TSQL
- ALTER_AVAILABILITY_TSQL
- ALTER AVAILABILITY GROUP
- ALTER AVAILABILITY
dev_langs:
- TSQL
helpviewer_keywords:
- Availability Groups [SQL Server], availability replicas
- ALTER AVAILABILITY GROUP statement
- Availability Groups [SQL Server], configuring
- Availability Groups [SQL Server], Transact-SQL statements
ms.assetid: f039d0de-ade7-4aaf-8b7b-d207deb3371a
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 23cd700fd8b25778118b2141bf0205dcbd87c92e
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170460"
---
# <a name="alter-availability-group-transact-sql"></a>ALTER AVAILABILITY GROUP (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  改變 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的現有 Always On 可用性群組。 大部分 ALTER AVAILABILITY GROUP 引數僅在目前的主要複本上支援。 不過，JOIN、FAILOVER 和 FORCE_FAILOVER_ALLOW_DATA_LOSS 引數只在次要複本上支援。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
ALTER AVAILABILITY GROUP group_name   
  {  
     SET ( <set_option_spec> )   
   | ADD DATABASE database_name   
   | REMOVE DATABASE database_name  
   | ADD REPLICA ON <add_replica_spec>   
   | MODIFY REPLICA ON <modify_replica_spec>  
   | REMOVE REPLICA ON <server_instance>  
   | JOIN  
   | JOIN AVAILABILITY GROUP ON <add_availability_group_spec> [ ,...2 ]  
   | MODIFY AVAILABILITY GROUP ON <modify_availability_group_spec> [ ,...2 ]  
   | GRANT CREATE ANY DATABASE  
   | DENY CREATE ANY DATABASE  
   | FAILOVER  
   | FORCE_FAILOVER_ALLOW_DATA_LOSS   
   | ADD LISTENER 'dns_name' ( <add_listener_option> )  
   | MODIFY LISTENER 'dns_name' ( <modify_listener_option> )  
   | RESTART LISTENER 'dns_name'  
   | REMOVE LISTENER 'dns_name'  
   | OFFLINE  
  }  
[ ; ]  
  
<set_option_spec> ::=   
    AUTOMATED_BACKUP_PREFERENCE = { PRIMARY | SECONDARY_ONLY| SECONDARY | NONE }  
  | FAILURE_CONDITION_LEVEL  = { 1 | 2 | 3 | 4 | 5 }   
  | HEALTH_CHECK_TIMEOUT = milliseconds  
  | DB_FAILOVER  = { ON | OFF }   
  | DTC_SUPPORT  = { PER_DB | NONE }  
  | REQUIRED_SYNCHRONIZED_SECONDARIES_TO_COMMIT = { integer }
  
<server_instance> ::=   
 { 'system_name[\instance_name]' | 'FCI_network_name[\instance_name]' }  
  
<add_replica_spec>::=  
  <server_instance> WITH  
    (  
       ENDPOINT_URL = 'TCP://system-address:port',  
       AVAILABILITY_MODE = { SYNCHRONOUS_COMMIT | ASYNCHRONOUS_COMMIT | CONFIGURATION_ONLY },  
       FAILOVER_MODE = { AUTOMATIC | MANUAL }   
       [ , <add_replica_option> [ ,...n ] ]  
    )   
  
  <add_replica_option>::=  
       SEEDING_MODE = { AUTOMATIC | MANUAL }  
     | BACKUP_PRIORITY = n  
     | SECONDARY_ROLE ( {   
            [ ALLOW_CONNECTIONS = { NO | READ_ONLY | ALL } ]   
        [,] [ READ_ONLY_ROUTING_URL = 'TCP://system-address:port' ]  
     } )  
     | PRIMARY_ROLE ( {   
            [ ALLOW_CONNECTIONS = { READ_WRITE | ALL } ]   
        [,] [ READ_ONLY_ROUTING_LIST = { ( '<server_instance>' [ ,...n ] ) | NONE } ]  
        [,] [ READ_WRITE_ROUTING_URL = { ( '<server_instance>' ) ] 
     } )  
     | SESSION_TIMEOUT = integer
  
<modify_replica_spec>::=  
  <server_instance> WITH  
    (    
       ENDPOINT_URL = 'TCP://system-address:port'   
     | AVAILABILITY_MODE = { SYNCHRONOUS_COMMIT | ASYNCHRONOUS_COMMIT }   
     | FAILOVER_MODE = { AUTOMATIC | MANUAL }   
     | SEEDING_MODE = { AUTOMATIC | MANUAL }   
     | BACKUP_PRIORITY = n  
     | SECONDARY_ROLE ( {   
          ALLOW_CONNECTIONS = { NO | READ_ONLY | ALL }   
        | READ_ONLY_ROUTING_URL = 'TCP://system-address:port'   
          } )  
     | PRIMARY_ROLE ( {   
          ALLOW_CONNECTIONS = { READ_WRITE | ALL }   
        | READ_ONLY_ROUTING_LIST = { ( '<server_instance>' [ ,...n ] ) | NONE }   
          } )  
     | SESSION_TIMEOUT = seconds  
    )   
  
<add_availability_group_spec>::=  
 <ag_name> WITH  
    (  
       LISTENER_URL = 'TCP://system-address:port',  
       AVAILABILITY_MODE = { SYNCHRONOUS_COMMIT | ASYNCHRONOUS_COMMIT },  
       FAILOVER_MODE = MANUAL,  
       SEEDING_MODE = { AUTOMATIC | MANUAL }  
    )  
  
<modify_availability_group_spec>::=  
 <ag_name> WITH  
    (  
       LISTENER = 'TCP://system-address:port'  
       | AVAILABILITY_MODE = { SYNCHRONOUS_COMMIT | ASYNCHRONOUS_COMMIT }  
       | SEEDING_MODE = { AUTOMATIC | MANUAL }  
    )  
  
<add_listener_option> ::=  
   {  
      WITH DHCP [ ON ( <network_subnet_option> ) ]  
    | WITH IP ( { ( <ip_address_option> ) } [ , ...n ] ) [ , PORT = listener_port ]  
   }  
  
  <network_subnet_option> ::=  
     'ipv4_address', 'ipv4_mask'    
  
  <ip_address_option> ::=  
     {   
        'four_part_ipv4_address', 'four_part_ipv4_mask'  
      | 'ipv6_address'  
     }  
  
<modify_listener_option>::=  
    {  
       ADD IP ( <ip_address_option> )   
     | PORT = listener_port  
    }  
  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *group_name*  
 指定新的可用性群組名稱。 *group_name* 必須是有效的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼，而且在 WSFC 叢集的所有可用性群組中必須是唯一的。  
  
 AUTOMATED_BACKUP_PREFERENCE **=** { PRIMARY \| SECONDARY_ONLY \| SECONDARY \| NONE }  
 指定在選擇要在何處執行備份時，有關備份作業應該如何評估主要複本的喜好設定。 您可以編寫給定備份作業，將自動備份喜好設定納入考量。 請務必了解，喜好設定並不是由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 強制施行，所以它對於隨選備份沒有任何影響。  
  
 只有主要複本上才支援。  
  
 其值如下：  
  
 PRIMARY  
 指定備份一定要在主要複本上進行。 如果當您在次要複本上執行備份時，需要不支援的備份功能 (例如建立差異備份)，這個選項會很實用。  
  
> [!IMPORTANT]  
>  如果您計畫要使用記錄傳送來準備可用性群組的任何次要資料庫，請將自動備份喜好設定設為 [主要]，直到所有次要資料庫都已經準備完成並且聯結至可用性群組為止。  
  
 SECONDARY_ONLY  
 指定絕對不能在主要複本上執行備份。 如果主要複本是唯一的線上複本，不應該進行備份。  
  
 SECONDARY  
 指定應該在次要複本上進行備份，但是主要複本是唯一線上複本的情況例外。 在此情況下，應該在主要複本上進行備份。 此為預設行為。  
  
 無  
 指定當您選擇要執行備份的複本時，您希望備份作業忽略可用性複本的角色。 請注意，備份作業可能會評估其他因素，例如每個可用性複本的備份優先權，搭配其操作狀態和連接狀態。  
  
> [!IMPORTANT]  
>  不會強制執行 AUTOMATED_BACKUP_PREFERENCE 設定。 這個喜好設定的解譯取決於您在給定可用性群組之資料庫的備份作業中所編寫的邏輯 (如果有的話)。 自動備份喜好設定對於隨選備份沒有任何影響。 如需詳細資訊，請參閱[設定可用性複本的備份 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server.md)。  
  
> [!NOTE]  
>  若要檢視現有可用性群組的自動備份喜好設定，請選取 [sys.availability_groups](../../relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md) 目錄檢視的 **automated_backup_preference** 或 **automated_backup_preference_desc** 資料行。 此外，[sys.fn_hadr_backup_is_preferred_replica  &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-hadr-backup-is-preferred-replica-transact-sql.md) 可用來判斷慣用備份複本。  此函數永遠都會針對至少其中一個複本傳回 1，即使當 `AUTOMATED_BACKUP_PREFERENCE = NONE` 時亦然。  
  
 FAILURE_CONDITION_LEVEL **=** { 1 \| 2 \| **3** \| 4 \| 5 }  
 指定哪一個失敗狀況將會觸發這個可用性群組的自動容錯移轉。 FAILURE_CONDITION_LEVEL 是在群組層級上設定，但只有在為同步認可可用性模式 (AVAILABILITY_MODE **=** SYNCHRONOUS_COMMIT) 設定的可用性複本上才會顯出重要性。 此外，只有當主要和次要複本已設定自動容錯移轉模式 (FAILOVER_MODE **=** AUTOMATIC) 而且次要複本目前與主要複本同步時，失敗狀況才可以觸發自動容錯移轉。  
  
 只有主要複本上才支援。  
  
 失敗狀況層級 (1-5) 的範圍從最低限制 (層級 1) 到最高限制 (層級 5)。 給定的狀況層級包含所有較少限制的層級。 因此，最嚴格的狀況層級 5 包含四個較少限制的狀況層級 (1-4)，層級 4 則包含層級 1-3，依此類推。 下表描述與每個層級對應的失敗狀況。  
  
|層級|失敗狀況|  
|-----------|-----------------------|  
|1|指定在發生以下任何情況時應該起始自動容錯移轉：<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務關閉。<br /><br /> 由於未從伺服器執行個體收到 ACK，所以用於連接到 WSFC 叢集的可用性群組租用已到期。 如需詳細資訊，請參閱 [How It Works:SQL Server Always On Lease Timeout](https://techcommunity.microsoft.com/t5/sql-server-support/how-it-works-sql-server-alwayson-lease-timeout/ba-p/317268) (運作方式：SQL Server Always On 租用逾時)。|  
|2|指定在發生以下任何情況時應該起始自動容錯移轉：<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體未連接到叢集，而且已超出使用者指定之可用性群組的 HEALTH_CHECK_TIMEOUT 臨界值。<br /><br /> 可用性複本處於失敗狀態。|  
|3|指定應該在嚴重 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 內部錯誤發生時起始自動容錯移轉，例如執行緒同步鎖定遭到遺棄、嚴重的寫入存取違規或是傾印過多。<br /><br /> 此為預設行為。|  
|4|指定應該在發生中度 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 內部錯誤時起始自動容錯移轉，例如 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 內部資源集區中持續的記憶體不足狀況。|  
|5|指定應該在發生任何符合的失敗狀況時起始自動容錯移轉，這些狀況包括：<br /><br /> SQL 引擎工作者執行緒已耗盡。<br /><br /> 偵測到無法解決的死結。|  
  
> [!NOTE]  
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體對用戶端要求缺少回應與可用性群組無關。  
  
 FAILURE_CONDITION_LEVEL 和 HEALTH_CHECK_TIMEOUT 值會針對給定群組定義「彈性容錯移轉原則」。 這個具彈性的容錯移轉原則讓您能夠更精確控制哪些條件必須造成自動容錯移轉。 如需詳細資訊，請參閱[可用性群組自動容錯移轉的彈性容錯移轉原則 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/configure-flexible-automatic-failover-policy.md)。  
  
 HEALTH_CHECK_TIMEOUT **=** *milliseconds*  
 指定在 WSFC 叢集假設伺服器執行個體緩慢或沒有回應之前，[sp_server_diagnostics](../../relational-databases/system-stored-procedures/sp-server-diagnostics-transact-sql.md) 系統預存程序傳回伺服器健全狀況資訊的等候時間 (以毫秒為單位)。 HEALTH_CHECK_TIMEOUT 是在群組層級上設定，但是只有在為具有自動容錯移轉的同步認可可用性模式 (AVAILABILITY_MODE **=** SYNCHRONOUS_COMMIT) 設定的可用性複本上才會顯出重要性。  此外，只有當主要和次要複本已設定自動容錯移轉模式 (FAILOVER_MODE **=** AUTOMATIC) 而且次要複本目前與主要複本同步時，健康情況檢查逾時才可以觸發自動容錯移轉。  
  
 預設 HEALTH_CHECK_TIMEOUT 值為 30000 毫秒 (30 秒)。 最小值為 15000 毫秒 (15 秒)，最大值為 4294967295 毫秒。  
  
 只有主要複本上才支援。  
  
> [!IMPORTANT]  
>  **sp_server_diagnostics** 不會在資料庫層級執行健全狀況檢查。  
  
 DB_FAILOVER  **=** { ON | OFF }  
 指定當主要複本上的資料庫離線時要採取的回應。 設定為 ON 時，若在可用性群組中針對資料庫有 ONLINE 以外的任何狀態，就會觸發自動容錯移轉。 當此選項設定為 OFF 時，只有執行個體的健康情況會用來觸發自動容錯移轉。  
 
 如需此設定的詳細資訊，請參閱[資料庫層級健康情況偵測選項](../../database-engine/availability-groups/windows/sql-server-always-on-database-health-detection-failover-option.md) 

DTC_SUPPORT  **=** { PER_DB | NONE }  
指定是否為此可用性群組啟用分散式交易。 只有從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始的可用性群組資料庫才支援分散式交易，且從 [!INCLUDE[ssSQL16](../../includes/sssql16-md.md)] SP2 開始才支援跨資料庫交易。 `PER_DB` 會建立支援這些交易的可用性群組，並會將涉及可用性群組中資料庫的跨資料庫交易自動升級為分散式交易。 `NONE` 可防止將跨資料庫交易自動升級為分散式交易，且不會向 DTC 中穩定的 RMID 註冊資料庫。 使用 `NONE` 設定時不會防止分散式交易，但資料庫容錯移轉和自動復原在某些情況下可能無法成功。 如需詳細資訊，請參閱[資料庫鏡像或 AlwaysOn 可用性群組不支援跨資料庫交易 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/transactions-always-on-availability-and-database-mirroring.md)。 
 
> [!NOTE]
> 在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] Service Pack 2 中引入了變更可用性群組 DTC_SUPPORT 設定的支援。 此選項不能與舊版搭配使用。 若要變更舊版 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] 中的這項設定，您必須 DROP (卸除) 並再次 CREATE (建立) 可用性群組。
 
 REQUIRED_SYNCHRONIZED_SECONDARIES_TO_COMMIT   
 在 SQL Server 2017 中導入。 用來在主要認可交易之前，設定認可所需的同步次要複本最小數目。 保證 SQL Server 交易會等候，直到最小數目之次要複本上的交易記錄都已更新為止。 預設值為 0，所提供的行為和 SQL Server 2016 相同。 最小值為 0。 最大值是複本數目減 1。 此選項會與同步認可模式下的複本關聯。 當複本處於同步認可模式時，主要複本上的寫入會等候，直到次要同步複本上的寫入認可到複本資料庫交易記錄為止。 如果裝載次要同步複本的 SQL Server 停止回應，則裝載主要複本的 SQL Server 會將該次要複本標記為 NOT SYNCHRONIZED 並繼續進行。 當沒有回應的資料庫回到線上，會處於「未同步」狀態，而且該複本會被標記為狀況不良，直到主要複本再次使其變成同步為止。 此設定可確保在每個交易認可的複本達到最小數目之前，主要複本不會繼續進行。 如果無法使用複本的最小數目，則主要複本上的認可會失敗。 針對叢集類型 `EXTERNAL`，設定會在可用性群組被新增到叢集資源時變更。 請參閱[可用性群組設定的高可用性和資料保護](../../linux/sql-server-linux-availability-group-ha.md)。
  
 ADD DATABASE *database_name*  
 指定您想要加入至可用性群組之一個或多個使用者資料庫的清單。 這些資料庫必須位於裝載目前主要複本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體上。 您可以為可用性群組指定多個資料庫，但每個資料庫只能屬於一個可用性群組。 如需可用性群組可支援之資料庫類型的詳細資訊，請參閱 [Always On 可用性群組的先決條件、限制與建議 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。 若要了解哪些本機資料庫已屬於可用性群組，請參閱 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中的 **replica_id** 資料行。  
  
 只有主要複本上才支援。  
  
> [!NOTE]  
>  建立可用性群組之後，您將需要連接至裝載次要複本的每個伺服器執行個體，然後準備每個次要資料庫，並將其聯結至該可用性群組。 如需詳細資訊，請參閱 [於 AlwaysOn 次要資料庫啟動資料移動 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/start-data-movement-on-an-always-on-secondary-database-sql-server.md)。  
  
 REMOVE DATABASE *database_name*  
 從可用性群組中移除指定的主要資料庫和對應的次要資料庫。 只有主要複本上才支援。  
  
 如需從可用性群組中移除可用性資料庫之後所建議後續操作的詳細資訊，請參閱[將主要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/remove-a-primary-database-from-an-availability-group-sql-server.md)。  
  
 ADD REPLICA ON  
 指定要在可用性群組中裝載次要複本的 SQL 伺服器執行個體 (一個到八個)。  每個複本是由後面接著 WITH (...) 子句的伺服器執行個體位址所指定。  
  
 只有主要複本上才支援。  
  
 您必須將每個新的次要複本聯結至可用性群組。 如需詳細資訊，請參閱本節稍後的 JOIN 選項描述。  
  
 \<server_instance>  
 指定複本主機的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體位址。 此位址格式取決於執行個體為預設執行個體還是具名執行個體，以及它是獨立的執行個體還是容錯移轉叢集執行個體 (FCI)。 語法如下所示：  
  
 { '*system_name*[\\*instance_name*]' | '*FCI_network_name*[\\*instance_name*]' }  
  
 這個位址的元件如下所示：  
  
 *system_name*  
 這是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 目標執行個體所在之電腦系統的 NetBIOS 名稱。 這部電腦必須是 WSFC 節點。  
  
 *FCI_network_name*  
 這是用來存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集的網路名稱。 如果伺服器執行個體當做 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉夥伴來參與，則使用它。 在 FCI 伺服器執行個體上執行 SELECT [@@SERVERNAME](../../t-sql/functions/servername-transact-sql.md)，會傳回它的完整 '*FCI_network_name*[\\*instance_name*]' 字串 (這是完整複本名稱)。  
  
 *instance_name*  
 這是 *system_name* 或 *FCI_network_name* 所裝載且已啟用 Always On 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體名稱。 如果是預設伺服器執行個體， *instance_name* 為選擇性。 執行個體名稱不區分大小寫。 在獨立伺服器執行個體上，這個值名稱與執行 SELECT [@@SERVERNAME](../../t-sql/functions/servername-transact-sql.md) 所傳回的值相同。  
  
 \  
 這個分隔符號只在指定 *instance_name* 時使用，以便與 *system_name* 或 *FCI_network_name* 區隔。  
  
 如需 WSFC 節點與伺服器執行個體先決條件的詳細資訊，請參閱 [Always On 可用性群組的先決條件、限制與建議 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
 ENDPOINT_URL ='TCP://*system-address*:*port*'  
 在要裝載您正在加入或修改之可用性複本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體上，指定[資料庫鏡像端點](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)的 URL 路徑。  
  
 ENDPOINT_URL 在 ADD REPLICA ON 子句中是必要的，而在 MODIFY REPLICA ON 子句中則是選擇性。  如需詳細資訊，請參閱 [在加入或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)設定伺服器執行個體時常見的問題。  
  
 **'** TCP **://** _system-address_ **:** _port_ **'**  
 指定 URL 以指定端點 URL 或唯讀的路由 URL。 URL 參數如下所示：  
  
 *system-address*  
 這是明確識別目的地電腦系統的字串，例如系統名稱、完整網域名稱或 IP 位址。  
  
 *port*  
 這是與伺服器執行個體之鏡像端點相關聯的通訊埠編號 (針對 ENDPOINT_URL 選項)，或伺服器執行個體之 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 所使用的通訊埠編號 (針對 READ_ONLY_ROUTING_URL 選項)。  
  
 AVAILABILITY_MODE **=** { SYNCHRONOUS_COMMIT | ASYNCHRONOUS_COMMIT | CONFIGURATION_ONLY }  
 指定在主要複本可以認可給定主要資料庫上的交易之前，主要複本是否必須等候次要複本認可將記錄檔記錄強化 (寫入) 至磁碟。 相同主要複本上不同資料庫的交易可以獨立認可。  
  
 SYNCHRONOUS_COMMIT  
 指定在這個次要複本上強化交易之前，主要複本將會等候認可交易 (同步-認可模式)。 您最多可以為三個複本指定 SYNCHRONOUS_COMMIT，包括主要複本在內。  
  
 ASYNCHRONOUS_COMMIT  
 指定主要複本會認可交易，而不等候這個次要複本強化記錄 (同步-認可可用性模式)。 您最多可以為五個可用性複本指定 ASYNCHRONOUS_COMMIT，包括主要複本在內。  

 CONFIGURATION_ONLY 指定主要複本同步地將可用性群組設定資料認可到此複本上的 master 資料庫。 複本將不會包含使用者資料。 此選項：

- 可裝載於任何 SQL Server 版本，包括 Express Edition。
- 要求 CONFIGURATION_ONLY 複本的資料鏡像端點類型必須是 `WITNESS`。
- 這無法改變。
- 這在 `CLUSTER_TYPE = WSFC` 時無效。 

   如需詳細資訊，請參閱[僅限設定複本](../../linux/sql-server-linux-availability-group-ha.md)。
    
 AVAILABILITY_MODE 在 ADD REPLICA ON 子句中是必要的，而在 MODIFY REPLICA ON 子句中則是選擇性。 如需詳細資訊，請參閱 [可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)或 PowerShell，針對 AlwaysOn 可用性群組執行規劃的手動容錯移轉或強制手動容錯移轉 (強制容錯移轉)。  
  
 FAILOVER_MODE **=** { AUTOMATIC | MANUAL }  
 指定您所定義之可用性複本的容錯移轉模式。  
  
 AUTOMATIC  
 啟用自動容錯移轉。 只有當您同時指定 AVAILABILITY_MODE = SYNCHRONOUS_COMMIT 時，才支援 AUTOMATIC。 您可以為三個可用性複本指定 AUTOMATIC，包括主要複本在內。  
  
> [!NOTE]  
>  在 SQL Server 2016 之前，您只能使用兩個自動容錯移轉複本，包括主要複本在內
  
> [!NOTE]  
>  SQL Server 容錯移轉叢集執行個體 (FCI) 不支援依照可用性群組進行自動容錯移轉，因此任何由 FCI 裝載的可用性複本只能設定為手動容錯移轉。  
  
 MANUAL  
 允許資料庫管理員手動容錯移轉或強制手動容錯移轉 (「強制容錯移轉」)。  
  
 FAILOVER_MODE 在 ADD REPLICA ON 子句中是必要的，而在 MODIFY REPLICA ON 子句中則是選擇性。 有兩種手動容錯移轉類型存在，也就是不遺失資料的手動容錯移轉以及強制容錯移轉 (可能遺失資料)，不同情況下會支援不同類型。  如需詳細資訊，請參閱本主題稍後的 [容錯移轉及容錯移轉模式 &#40;AlwaysOn 可用性群組&#41;](../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md)。  
  
 SEEDING_MODE **=** { AUTOMATIC | MANUAL }  
 指定一開始如何植入次要複本。  
  
 AUTOMATIC  
 啟用直接植入。 此方法會透過網路植入次要複本。 此方法不要求您必須在複本上備份和還原主要資料庫的複本。  
  
> [!NOTE]  
>  針對直接植入，您必須呼叫 **ALTER AVAILABILITY GROUP** 搭配 **GRANT CREATE ANY DATABASE** 選項，以允許在每個次要複本上建立資料庫。  
  
 MANUAL  
 指定手動植入 (預設值)。 此方法要求您必須在主要複本上建立資料庫的備份，並在次要複本上手動還原該備份。  
  
 BACKUP_PRIORITY **=** _n_  
 指定在這個複本上執行備份的優先權 (相對於相同可用性群組中的其他複本)。 這個值是 0 到 100 範圍之間的整數。 這些值具有以下意義：  
  
-   1..100 表示可以選擇可用性複本來執行備份。 1 表示最低優先權，100 表示最高優先權。 如果 BACKUP_PRIORITY = 1，則只有當目前沒有更高優先權的可用性複本可用時，才會選擇此可用性複本來執行備份。  
  
-   0 表示絕對不會選擇這個可用性複本來執行備份。 例如，這對於您永遠不希望將備份容錯移轉到其中的遠端可用性複本十分有用。  
  
 如需詳細資訊，請參閱[使用中次要：在次要複本上備份 &#40;Always On 可用性群組&#41;](../../database-engine/availability-groups/windows/active-secondaries-backup-on-secondary-replicas-always-on-availability-groups.md)。  
  
 SECONDARY_ROLE **(** ... **)**  
 指定將會在此可用性複本目前擁有次要角色 (亦即，每當它是次要複本時) 時生效的角色專屬設定。 在括弧內指定任一個或兩個次要角色選項。 如果您同時指定兩個選項，則使用逗號分隔清單。  
  
 次要角色選項如下：  
  
 ALLOW_CONNECTIONS **=** { NO | READ_ONLY | ALL }  
 指定執行次要角色之給定可用性複本 (也就是做為次要複本) 的資料庫是否可接受來自用戶端的連接，下列其中一個值：  
  
 否  
 不允許使用者連接至這個複本的次要資料庫。 無法讀取這些資料庫。 此為預設行為。  
  
 READ_ONLY  
 只允許連線到 Application Intent 屬性設定為 **ReadOnly** 之次要複本中的資料庫。 如需有關這個屬性的詳細資訊，請參閱＜ [Using Connection String Keywords with SQL Server Native Client](../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md)＞。  
  
 ALL  
 次要複本的資料庫允許所有連接進行唯讀存取。  
  
 如需詳細資訊，請參閱[使用中次要：可讀取的次要複本 &#40;Always On 可用性群組&#41;](../../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)。  
  
 READ_ONLY_ROUTING_URL **='** TCP **://** _system-address_ **:** _port_ **'**  
 指定向此可用性複本路由傳送讀取意圖連接要求所使用的 URL。 這是 SQL Server Database Engine 接聽的 URL。 SQL Server Database Engine 的預設執行個體通常會接聽 TCP 通訊埠 1433。  
  
 針對具名執行個體，您可以查詢 [sys.dm_tcp_listener_states](../../relational-databases/system-dynamic-management-views/sys-dm-tcp-listener-states-transact-sql.md) 動態管理檢視的 **port** 和 **type_desc** 資料行來取得連接埠號碼。 伺服器執行個體會使用 Transact-SQL 接聽程式 (**type_desc='TSQL'** )。  
  
 如需計算可用性複本之唯讀路由 URL 的詳細資訊，請參閱[計算 Always On 的 read_only_routing_url](/archive/blogs/mattn/calculating-read_only_routing_url-for-alwayson)。  
  
> [!NOTE]  
>  若是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的具名執行個體，則應該將 Transact-SQL 接聽程式設定為使用特定通訊埠。 如需詳細資訊，請參閱[設定伺服器接聽特定 TCP 通訊埠 &#40;SQL Server 組態管理員&#41;](../../database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port.md)。  
  
 PRIMARY_ROLE **(** ... **)**  
 指定將會在此可用性複本目前擁有主要角色 (亦即，每當它是主要複本時) 時生效的角色專屬設定。 在括弧內指定任一個或兩個主要角色選項。 如果您同時指定兩個選項，則使用逗號分隔清單。  
  
 主要角色選項如下：  
  
 ALLOW_CONNECTIONS **=** { READ_WRITE | ALL }  
 指定執行主要角色之給定可用性複本 (也就是做為主要複本) 的資料庫可從用戶端接受的連接類型，下列其中一個值：  
  
 READ_WRITE  
 不允許 Application Intent 連接屬性設為 **ReadOnly** 的連接。  當 Application Intent 屬性設為 **ReadWrite** 或是未設定 Application Intent 連接屬性時，便會允許連接。 如需有關 Application Intent 連接屬性的詳細資訊，請參閱＜ [Using Connection String Keywords with SQL Server Native Client](../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md)＞。  
  
 ALL  
 主要複本的資料庫允許所有連接。 此為預設行為。  
  
 READ_ONLY_ROUTING_LIST **=** { **('** \<server_instance> **'** [ **,** ...*n* ] **)** | NONE }  
 針對這個可用性群組，指定裝載可用性複本之伺服器執行個體的逗號分隔清單，以次要角色執行時，這些可用性複本會符合下列需求：  
  
-   設定為允許所有連接或唯讀連接 (請參閱 SECONDARY_ROLE 選項的 ALLOW_CONNECTIONS 引數，如上所示)。  
  
-   定義其唯讀的路由 URL (請參閱 SECONDARY_ROLE 選項的 READ_ONLY_ROUTING_URL 引數，如上所示)。  
  
 READ_ONLY_ROUTING_LIST 值如下：  
  
 \<server_instance>  
 針對以次要角色執行時，可讀取之次要複本的可用性複本，指定其主機之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的位址。  
  
 使用逗號分隔清單指定可能裝載可讀取之次要複本的所有伺服器執行個體。 唯讀的路由將遵循清單中指定伺服器執行個體的順序。 如果您將複本的主機伺服器執行個體包含在複本的唯讀路由清單中，將此伺服器執行個體放在清單結尾通常是很好的作法，讓讀取意圖的連接通往次要複本 (如果有一個可用的次要複本的話)。  
  
 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，您可以針對可讀取次要複本之間的讀取意圖要求進行負載平衡。 這可以透過將複本放在唯讀路由清單內的一組巢狀括號中來指定。 如需詳細資訊與範例，請參閱[設定唯讀複本之間的負載平衡](../../database-engine/availability-groups/windows/configure-read-only-routing-for-an-availability-group-sql-server.md#loadbalancing)。  
  
 無  
 指定當此可用性複本是主要複本時，將不支援唯讀路由。 此為預設行為。 搭配 MODIFY REPLICA ON 使用時，此值會停用現有的清單 (如果有的話)。  

 READ_WRITE_ROUTING_URL **=** { **('** \<server_instance> **')** }  
 適用於：SQL Server (從 SQL Server 2019 (15.x) 開始) 

 針對這個可用性群組，指定裝載可用性複本的伺服器執行個體，以主要角色執行時，這些可用性複本會符合下列需求：
-   複本規格 PRIMARY_ROLE 包括 READ_WRITE_ROUTING_URL。
-   連接字串是 ReadWrite，其方式是將 ApplicationIntent 定義為 ReadWrite，或不要設定 ApplicationIntent，讓預設值 (ReadWrite) 生效。

如需詳細資訊，請參閱[次要到主要複本讀取/寫入連線重新導向 (Always On 可用性群組)](../../database-engine/availability-groups/windows/secondary-replica-connection-redirection-always-on-availability-groups.md)。

 SESSION_TIMEOUT **=** _seconds_  
 指定工作階段逾時期限 (以秒為單位)。 如果您沒有指定這個選項，依預設，這個期間是 10 秒。 最小值是 5 秒。  
  
> [!IMPORTANT]  
>  我們建議您讓逾時期限保持在 10 秒或更久。  
  
 如需工作階段逾時期間的詳細資訊，請參閱 [Always On 可用性群組概觀 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)。  
  
 MODIFY REPLICA ON  
 修改可用性群組的任何複本。 要修改的複本清單針對每一個複本包含伺服器執行個體位址和 WITH (...) 子句。  
  
 只有主要複本上才支援。  
  
 REMOVE REPLICA ON  
 從可用性群組中移除指定的次要複本。 您不能從可用性群組中移除目前的主要複本。 當移除該複本時，它會停止接收資料。 其次要資料庫會從可用性群組中移除，並且進入 RESTORING 狀態。  
  
 只有主要複本上才支援。  
  
> [!NOTE]  
>  如果您在某個複本處於無法使用或失敗狀態時刪除該複本，則當它恢復連線狀態時，將會發現它不再屬於原來的可用性群組。  
  
 JOIN  
 使得本機伺服器執行個體裝載指定之可用性群組內的次要複本。  
  
 只有在尚未聯結至可用性群組的次要複本上支援。  
  
 如需詳細資訊，請參閱 [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)。  
  
 FAILOVER  
起始可用性群組的手動容錯移轉，而不會讓連接的次要複本遺失資料。 將裝載主要複本的複本是「容錯移轉目標」。  容錯移轉目標將會接管主要角色，並復原每個資料庫的副本，然後讓這兩個資料庫連線，做為新的主要資料庫。 之前的主要複本會同時轉換到次要角色，且其資料庫會變成次要資料庫，並立即遭到暫停。 您可能可以透過一連串的失敗，來回切換這些角色。  
  
 只有在目前與主要複本同步處理的同步認可次要複本上支援。 請注意，次要複本也必須與主要複本同步處理，才能在同步認可模式下執行。  
  
> [!NOTE]  
>  只要容錯移轉目標接受了命令，就會傳回容錯移轉命令。 不過，在可用性群組完成容錯移轉之後，會以非同步方式復原資料庫。  
  
 如需執行已規劃的手動容錯移轉之限制、先決條件與建議的詳細資訊，請參閱[執行可用性群組的已規劃手動容錯移轉 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server.md)。  
  
 FORCE_FAILOVER_ALLOW_DATA_LOSS  
 > [!CAUTION]  
>  強制容錯移轉 (可能包含某些資料遺失) 嚴格來說是一種災害復原方法。 因此，我們強烈建議您只有在主要複本不再執行、您願意承擔遺失資料的風險，而且您必須立即將服務還原到可用性群組時，才進行強制容錯移轉。  
  
 僅在角色處於 SECONDARY 或 RESOLVING 狀態的複本上支援。 --您輸入容錯移轉命令所在的複本，稱為 *容錯移轉目標*。  
  
 強制可用性群組容錯移轉至容錯移轉目標 (可能會遺失資料)。 容錯移轉目標將會接管主要角色，並復原每個資料庫的副本，然後讓這兩個資料庫連線，做為新的主要資料庫。 在任一個剩餘的次要複本上，會暫停每個次要資料庫，到手動繼續為止。 當之前的主要複本變成可用複本時，會切換到次要角色，且其資料庫會變成暫停的次要資料庫。  
  
> [!NOTE]  
>  只要容錯移轉目標接受了命令，就會傳回容錯移轉命令。 不過，在可用性群組完成容錯移轉之後，會以非同步方式復原資料庫。  
  
 如需強制容錯移轉的限制、先決條件與建議，以及強制容錯移轉對可用性群組中先前主要資料庫之影響的詳細資訊，請參閱[執行可用性群組的強制手動容錯移轉 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server.md)。  
  
 ADD LISTENER **'** _dns\_name_ **'(** \<add_listener_option> **)**  
 為這個可用性群組定義新的可用性群組接聽程式。 只有主要複本上才支援。  
  
> [!IMPORTANT]
>  在建立第一個接聽程式之前，強烈建議您閱讀[建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)。  
> 
>  針對給定的可用性群組建立接聽程式之後，我們強烈建議您執行以下操作：  
> 
>  -   要求網路管理員將接聽程式的 IP 位址保留為專用。  
> -   將接聽程式的 DNS 主機名稱提供給應用程式開發人員，以便在要求與這個可用性群組進行用戶端連接時，用於連接字串中。  
  
 *dns_name*  
 指定可用性群組接聽程式的 DNS 主機名稱。 接聽程式的 DNS 名稱在網域和 NetBIOS 中都必須是唯一的。  
  
 *dns_name* 是字串值。 此名稱只能包含英數字元、虛線 (-) 和連字號 (_) (順序不拘)。 DNS 主機名稱不區分大小寫。 最大長度是 63 個字元。  
  
 我們建議您指定一個有意義的字串。 例如，如果是名為 `AG1`的可用性群組，有意義的 DNS 主機名稱會是 `ag1-listener`。  
  
> [!IMPORTANT]  
>  NetBIOS 只會辨識 DNS 名稱中的前 15 個字元。 如果您有兩個由相同 Active Directory 所控制的 WSFC 叢集，而且嘗試使用超過 15 個字元的名稱以及完全相同的 15 個字元前置詞，在這兩個叢集中建立可用性群組接聽程式，就會收到一則錯誤，指出系統無法讓虛擬網路名稱資源上線。 如需有關 DNS 名稱之前置詞命名規則的詳細資訊，請參閱＜ [指派網域名稱](https://technet.microsoft.com/library/cc731265\(WS.10\).aspx)＞。  
  
 JOIN AVAILABILITY GROUP ON  
 加入 *分散式可用性群組*。 當您建立分散式可用性群組時，叢集上建立所在的可用性群組就是主要可用性群組。 加入該分散式可用性群組的可用性群組是次要可用性群組。  
  
 \<ag_name>  
 指定組成半組分散式可用性群組的可用性群組名稱。  
  
 LISTENER **='** TCP **://** _system-address_ **:** _port_ **'**  
 指定與可用性群組相關聯之接聽程式的 URL 路徑。  
  
 LISTENER 子句是必要的。  
  
 **'** TCP **://** _system-address_ **:** _port_ **'**  
 指定與可用性群組相關聯之接聽程式的 URL。 URL 參數如下所示：  
  
 *system-address*  
 這是能明確識別接聽程式的字串，例如系統名稱、完整網域名稱或 IP 位址。  
  
 *port*  
 這是與可用性群組之鏡像端點相關聯的連接埠號碼。 請注意，這不是接聽程式的連接埠。  
  
 AVAILABILITY_MODE **=** { SYNCHRONOUS_COMMIT | ASYNCHRONOUS_COMMIT }  
 指定在主要複本可以認可指定主要資料庫上的交易之前，主要複本是否必須等候次要可用性群組認可將記錄檔記錄強化 (寫入) 至磁碟。  
  
 SYNCHRONOUS_COMMIT  
 指定在這個次要可用性群組上強化交易之前，主要複本會等候認可交易。 您最多可以為兩個可用性群組指定 SYNCHRONOUS_COMMIT，包括主要可用性群組在內。  
  
 ASYNCHRONOUS_COMMIT  
 指定主要複本會認可交易，而不等候這個次要可用性群組強化記錄。 您最多可以為兩個可用性群組指定 ASYNCHRONOUS_COMMIT，包括主要可用性群組在內。  
  
 AVAILABILITY_MODE 子句是必要的。  
  
 FAILOVER_MODE **=** { MANUAL }  
 指定分散式可用性群組的容錯移轉模式。  
  
 MANUAL  
 可讓資料庫管理員啟用已規劃的手動容錯移轉或強制手動容錯移轉 (通常稱為「強制容錯移轉」)。  
  
 不支援自動容錯移轉至次要可用性群組。  
  
 SEEDING_MODE **=** { AUTOMATIC | MANUAL }  
 指定一開始如何植入次要可用性群組。  
  
 AUTOMATIC  
 啟用自動植入。 此方法會透過網路植入次要可用性群組。 此方法不會要求您在次要可用性群組複本上備份和還原主要資料庫複本。  
  
 MANUAL  
 指定手動植入。 此方法會要求您在主要複本上建立資料庫的備份，並在次要可用性群組複本上手動還原該備份。  
  
 MODIFY AVAILABILITY GROUP ON  
 修改分散式可用性群組的任何可用性群組設定。 要修改的可用性群組清單包含每個可用性群組的可用性群組名稱與 WITH (...) 子句。  
  
> [!IMPORTANT]  
>  此命令必須在主要可用性群組和次要可用性群組執行個體上重複。  
  
 GRANT CREATE ANY DATABASE  
 允許可用性群組代表支援直接植入的主要複本建立資料庫 (SEEDING_MODE = AUTOMATIC)。 在次要複本加入可用性群組之後，此參數應可在支援直接植入的每個次要複本上執行。  需要 CREATE ANY DATABASE 權限。  
  
 DENY CREATE ANY DATABASE  
 移除可用性群組能夠代表主要複本建立資料庫的能力。  
  
 \<add_listener_option>  
 ADD LISTENER 可接受下列其中一個選項：  
  
 WITH DHCP [ ON { **('** _four\_part\_ipv4\_address_ **','** _four\_part\_ipv4\_mask_ **')** } ]  
 指定可用性群組接聽程式將會使用動態主機設定通訊協定 (DHCP)。  或者，使用 ON 子句以識別將建立此接聽程式的網路。 DHCP 受限於單一子網路，這個子網路用於可用性群組中主控可用性複本的每個伺服器執行個體。  
  
> [!IMPORTANT]  
>  不建議在實際執行環境中使用 DHCP。 如果有停機時間且 DHCP IP 租用到期，則需要額外的時間來註冊與接聽程式 DNS 名稱相關聯的新 DHCP 網路 IP 位址，而影響用戶端連接。 但是，DHCP 適合用於設定開發和測試環境，以驗證可用性群組的基本功能，也適合與應用程式整合。  
  
 例如：  
  
 `WITH DHCP ON ('10.120.19.0','255.255.254.0')`  
  
 WITH IP **(** { **('** _four\_part\_ipv4\_address_ **','** _four\_part\_ipv4\_mask_ **')**  |  **('** _ipv6\_address_ **')** } [ **,** ..._n_ ] **)** [ **,** PORT **=** _listener\_port_ ]  
 指定可用性群組接聽程式將使用一個或多個靜態 IP 位址，而不使用 DHCP。 若要建立跨多個子網路的可用性群組，接聽程式組態中每個子網路都需要一個靜態 IP 位址。 對於給定的子網路，靜態 IP 位址可以是 IPv4 位址或 IPv6 位址。 請與網路系統管理員連絡以取得將主控新可用性群組的可用性複本之每個子網路的靜態 IP 位址。  
  
 例如：  
  
 `WITH IP ( ('10.120.19.155','255.255.254.0') )`  
  
 *ipv4_address*  
 指定可用性群組接聽程式的 IPv4 四部分位址。 例如： `10.120.19.155` 。  
  
 *ipv4_mask*  
 指定可用性群組接聽程式的 IPv4 四部分遮罩。 例如： `255.255.254.0` 。  
  
 *ipv6_address*  
 指定可用性群組接聽程式的 IPv6 位址。 例如： `2001::4898:23:1002:20f:1fff:feff:b3a3` 。  
  
 PORT **=** *listener_port*  
 指定將供 WITH IP 子句所指定之可用性群組接聽程式使用的連接埠號碼 (*listener_port*)。 PORT 為選擇性。  
  
 支援預設通訊埠編號 1433。 但是，如果您有安全考量，我們建議您使用不同的通訊埠編號。  
  
 例如： `WITH IP ( ('2001::4898:23:1002:20f:1fff:feff:b3a3') ) , PORT = 7777`  
  
 MODIFY LISTENER **'** _dns\_name_ **'(** \<modify\_listener\_option\> **)**  
 修改這個可用性群組的現有可用性群組接聽程式。 只有主要複本上才支援。  
  
 \<modify\_listener\_option\>  
 MODIFY LISTENER 可接受下列其中一個選項：  
  
 ADD IP { **('** _four\_part\_ipv4\_address_ **','** _four\_part\_ipv4_mask_ **')** \| <b>('</b>dns\_name *ipv6\_address* __')__ }  
 將所指定 IP 位址新增至由 *dns\_name* 所指定的可用性群組接聽程式。  
  
 PORT **=** *listener_port*  
 請參閱本節稍早有關這個引數的描述。  
  
 RESTART LISTENER **'** _dns\_name_ **'**  
 重新啟動與指定的 DNS 名稱關聯的接聽程式。 只有主要複本上才支援。  
  
 REMOVE LISTENER **'** _dns\_name_ **'**  
 移除與指定的 DNS 名稱關聯的接聽程式。 只有主要複本上才支援。  
  
 OFFLINE  
 讓線上的可用性群組離線。 同步認可資料庫不會有資料遺失。  
  
 在可用性群組離線之後，其資料庫就無法供用戶端使用，而且您無法讓可用性群組恢復上線。 因此，將可用性群組資源移轉至新的 WSFC 叢集時，僅在跨叢集移轉 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 期間使用 OFFLINE 選項。  
  
 如需詳細資訊，請參閱[讓可用性群組離線 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/take-an-availability-group-offline-sql-server.md)。  
  
## <a name="prerequisites-and-restrictions"></a>必要條件和限制  
 如需可用性複本與其主機伺服器執行個體和電腦上之先決條件與限制的詳細資訊，請參閱 [Always On 可用性群組的先決條件、限制與建議 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
 如需 AVAILABILITY GROUP Transact-SQL 陳述式相關限制的詳細資訊，請參閱 [AlwaysOn 可用性群組的 Transact-SQL 陳述式概觀 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups.md)。  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要可用性群組的 ALTER AVAILABILITY GROUP 權限、CONTROL AVAILABILITY GROUP 權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。  也需要 ALTER ANY DATABASE 權限。   
  
## <a name="examples"></a>範例  
  
###  <a name="a-joining-a-secondary-replica-to-an-availability-group"></a><a name="Join_Secondary_Replica"></a> A. 將次要複本加入至可用性群組  
 下列範例會將您所連接的次要複本加入至 `AccountsAG` 可用性群組。  
  
```SQL  
ALTER AVAILABILITY GROUP AccountsAG JOIN;  
GO  
```  
  
###  <a name="b-forcing-failover-of-an-availability-group"></a><a name="Force_Failover"></a> B. 強制容錯移轉可用性群組  
 下列範例會將 `AccountsAG` 可用性群組強制容錯移轉至您所連接的次要複本。  
  
```SQL
ALTER AVAILABILITY GROUP AccountsAG FORCE_FAILOVER_ALLOW_DATA_LOSS;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/create-availability-group-transact-sql.md)   
 [ALTER DATABASE SET HADR &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-hadr.md)   
 [DROP AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/drop-availability-group-transact-sql.md)   
 [sys.availability_replicas &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-availability-replicas-transact-sql.md)   
 [sys.availability_groups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-availability-groups-transact-sql.md)   
 [針對 AlwaysOn 可用性群組設定進行疑難排解 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/troubleshoot-always-on-availability-groups-configuration-sql-server.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [可用性群組接聽程式、用戶端連線及應用程式容錯移轉 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)  
  
