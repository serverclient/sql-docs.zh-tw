---
title: 設定可用性群組的複寫
description: 了解搭配您的 Always On 可用性群組設定 SQL Server 複寫所需的詳細程序。
ms.custom: seodec18
ms.date: 01/25/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], interoperability
- replication [SQL Server], AlwaysOn Availability Groups
ms.assetid: 4e001426-5ae0-4876-85ef-088d6e3fb61c
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 7a8fc6787491c50b490ca0b9c25ab0d45acd52f2
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97639996"
---
# <a name="configure-replication-with-always-on-availability-groups"></a>設定 Always On 可用性群組的複寫

[!INCLUDE[sql windows only](../../../includes/applies-to-version/sql-windows-only.md)]

  設定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 複寫和 AlwaysOn 可用性群組包含七個步驟。 下列各節將詳細說明每個步驟。  
  
##  <a name="1-configure-the-database-publications-and-subscriptions"></a><a name="step1"></a> 1.設定資料庫發行集和訂閱  
 **設定散發者**  
  
 散發資料庫不能搭配 SQL Server 2012 和 SQL Server 2014 置於可用性群組中。 SQL 2016 和更新版本支援將散發資料庫放置到可用性群組內，但用於合併、雙向或點對點複寫拓撲的分散式資料庫除外。 如需詳細資訊，請參閱[在可用性群組中設定散發資料庫](../../../relational-databases/replication/configure-distribution-availability-group.md)。
  
1.  在散發者端設定散發。 如果預存程序正用於組態，請執行 **sp_adddistributor**。 您可以使用 *\@password* 參數來識別遠端發行者連接到散發者時使用的密碼。 設定遠端散發者時，每個遠端發行者也需要此密碼。  
  
    ```  
    USE master;  
    GO  
    EXEC sys.sp_adddistributor  
        @distributor = 'MyDistributor',  
        @password = '**Strong password for distributor**';  
    ```  
  
2.  在散發者端建立散發資料庫。 如果預存程序正用於組態，請執行 **sp_adddistributiondb**。  
  
    ```  
    USE master;  
    GO  
    EXEC sys.sp_adddistributiondb  
        @database = 'distribution',  
        @security_mode = 1;  
    ```  
  
3.  設定遠端發行者。 如果預存程序正用於設定散發者，請執行 **sp_adddistpublisher**。 *\@security_mode* 參數是用來決定從複寫代理程式執行的發行者驗證預存程序如何連線到目前主要複本。 如果設定為 1，就會使用 Windows 驗證來連接到目前主要複本。 如果設定為 0，就會使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 驗證搭配指定的 *\@login* 和 *\@password* 值。 在每個次要複本上指定的登入和密碼必須有效，才能讓驗證預存程序成功連接到該複本。  
  
    > [!NOTE]  
    >  如果任何修改的複寫代理程式在散發者以外的電腦上執行，則使用 Windows 驗證來連接到主要複本時，就必須針對複本主機電腦之間的通訊設定 Kerberos 驗證。 使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 登入來連接到目前主要複本時，不需要 Kerberos 驗證。  
  
    ```  
    USE master;  
    GO  
    EXEC sys.sp_adddistpublisher  
        @publisher = 'AGPrimaryReplicaHost',  
        @distribution_db = 'distribution',  
        @working_directory = '\\MyReplShare\WorkingDir',  
        @login = 'MyPubLogin',  
        @password = '**Strong password for publisher**';  
    ```  
  
 如需詳細資訊，請參閱 [sp_adddistpublisher &#40;Transact-SQL&#41;](../../../relational-databases/system-stored-procedures/sp-adddistpublisher-transact-sql.md)。  
  
 **在原始發行者端設定發行者**  
  
1.  設定遠端散發。 如果預存程序正用於設定發行者，請執行 **sp_adddistributor**。 請以在散發者端執行 **sp_adddistrbutor** 時用來設定散發的相同值，來指定 *\@password* 的值。  
  
    ```  
    exec sys.sp_adddistributor  
        @distributor = 'MyDistributor',  
        @password = 'MyDistPass'  
    ```  
  
2.  啟用資料庫進行複寫。 如果預存程序正用於設定發行者，請執行 **sp_replicationdboption**。 如果要針對資料庫設定異動和合併式複寫，就必須啟用每個複寫。  
  
    ```  
    USE master;  
    GO  
    EXEC sys.sp_replicationdboption  
        @dbname = 'MyDBName',  
        @optname = 'publish',  
        @value = 'true';  
  
    EXEC sys.sp_replicationdboption  
        @dbname = 'MyDBName',  
        @optname = 'merge publish',  
        @value = 'true';  
    ```  
  
3.  建立複寫發行集、發行項和訂閱。 如需有關如何設定複寫的詳細資訊，請參閱＜發行資料和資料庫物件＞。  
  
##  <a name="2-configure-the-always-on-availability-group"></a><a name="step2"></a> 2.設定 AlwaysOn 可用性群組  
 在預期的主要複本上，建立具有已發行 (或即將發行) 資料庫做為成員資料庫的可用性群組。 如果使用可用性群組精靈，您就可以允許精靈一開始同步處理次要複本資料庫，也可以使用備份和還原來手動執行初始化。  
  
 針對可用性群組建立複寫代理程式將用來連接到目前主要複本的 DNS 接聽程式。 指定的接聽程式名稱將當做原始發行者/已發行資料庫配對的重新導向目標使用。 例如，如果您要使用 DDL 來設定可用性群組，可以使用下列程式碼範例，針對名為 `MyAG` 的現有可用性群組指定可用性群組接聽程式：  
  
```  
ALTER AVAILABILITY GROUP 'MyAG'   
    ADD LISTENER 'MyAGListenerName' (WITH IP (('10.120.19.155', '255.255.254.0')));  
```  
  
 如需詳細資訊，請參閱[建立及設定可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/creation-and-configuration-of-availability-groups-sql-server.md)。  

  
##  <a name="3-ensure-that-all-of-the-secondary-replica-hosts-are-configured-for-replication"></a><a name="step3"></a> 3.確定所有次要複本主機都設定為複寫  
 在每個次要複本主機上，確認 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 已經設定為支援複寫。 您可以在每個次要複本主機上執行下列查詢，以便判斷是否已安裝複寫：  
  
```  
USE master;  
GO  
DECLARE @installed int;  
EXEC @installed = sys.sp_MS_replication_installed;  
SELECT @installed;  
```  
  
 如果 *\@installed* 為 0，您就必須將複寫新增到 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝。  
  
##  <a name="4-configure-the-secondary-replica-hosts-as-replication-publishers"></a><a name="step4"></a> 4.將次要複本主機設定為複寫發行者  
 次要複本無法做為複寫發行者或重新發行者，但是您必須設定複寫，才能讓次要複本在容錯移轉之後接管。 在散發者端，設定每個次要複本主機的散發。 請指定當原始發行者加入至散發者時指定的相同散發資料庫和工作目錄。 如果您要使用預存程序來設定散發，請使用 **sp_adddistpublisher** ，讓遠端發行者與散發者產生關聯。 如果 *\@login* 和 *\@password* 已用於原始發行者，請在您新增次要複本主機作為發行者時，針對每個項目指定相同的值。  
  
```  
EXEC sys.sp_adddistpublisher  
    @publisher = 'AGSecondaryReplicaHost',  
    @distribution_db = 'distribution',  
    @working_directory = '\\MyReplShare\WorkingDir',  
    @login = 'MyPubLogin',  
    @password = '**Strong password for publisher**';  
```  
  
 在每個次要複本主機上，設定散發。 您可以將原始發行者的散發者識別為遠端散發者。 請使用原本在散發者端執行 **sp_adddistributor** 時所使用的相同密碼。 如果預存程序正在用於設定散發，則會使用 **sp_adddistributor** 的 *\@password* 參數來指定密碼。  
  
```  
EXEC sp_adddistributor   
    @distributor = 'MyDistributor',  
    @password = '**Strong password for distributor**';  
```  
  
 在每個次要複本主機上，確定資料庫發行集的發送訂閱者顯示成連結的伺服器。 如果預存程序正用於設定遠端發行者，請使用 **sp_addlinkedserver** ，將訂閱者 (如果原本不存在的話) 當作連結的伺服器加入至發行者。  
  
```  
EXEC sys.sp_addlinkedserver   
    @server = 'MySubscriber';  
```  
  
##  <a name="5-redirect-the-original-publisher-to-the-ag-listener-name"></a><a name="step5"></a> 5.將原始發行者重新導向至 AG 接聽程式名稱  
 在散發者端的散發資料庫中，執行 **sp_redirect_publisher** 預存程序，以便讓原始發行者和已發行資料庫與可用性群組的可用性群組接聽程式名稱產生關聯。  
  
```  
USE distribution;  
GO  
EXEC sys.sp_redirect_publisher   
@original_publisher = 'MyPublisher',  
    @publisher_db = 'MyPublishedDB',  
    @redirected_publisher = 'MyAGListenerName';  
```  
  
##  <a name="6-run-the-replication-validation-stored-procedure-to-verify-the-configuration"></a><a name="step6"></a> 6.執行複寫驗證預存程序以確認組態  
 在散發者端的散發資料庫中，執行 **sp_validate_replica_hosts_as_publishers** 預存程序，以便確認所有複本主機現在都設定為當作已發行資料庫的發行者。  
  
```  
USE distribution;  
GO  
DECLARE @redirected_publisher sysname;  
EXEC sys.sp_validate_replica_hosts_as_publishers  
    @original_publisher = 'MyPublisher',  
    @publisher_db = 'MyPublishedDB',  
    @redirected_publisher = @redirected_publisher output;  
```  
  
 在每個可用性群組複本主機上， **sp_validate_replica_hosts_as_publishers** 預存程序應該從具有足夠授權的登入執行，以便查詢可用性群組的相關資訊。 與 **sp_validate_redirected_publisher** 不同之處在於，它會使用呼叫端的認證，而不會使用保留在 msdb.dbo.MSdistpublishers 中的登入來連接到可用性群組複本。  
  
> [!NOTE]  
>  在驗證不允許讀取存取或需要指定讀取意圖的次要複本主機時，**sp_validate_replica_hosts_as_publishers** 會失敗並發生下列錯誤。  
>   
>  訊息 21899、層級 11、狀態 1、程序 **sp_hadr_verify_subscribers_at_publisher**、行 109  
>   
>  在重新導向的發行者 'MyReplicaHostName' 上用以判斷原始發行者 'MyOriginalPublisher' 的訂閱者是否有 sysserver 項目的查詢失敗，發生錯誤 '976'，錯誤訊息為「錯誤 976，層級 14，狀態 1，訊息：目標資料庫 'MyPublishedDB' 正參與可用性群組，目前無法供查詢存取。 資料移動已暫停，或者可用性複本無法進行讀取存取。 若要允許唯讀存取可用性群組中的這個資料庫和其他資料庫，請啟用群組中一個或多個次要可用性複本的讀取存取。  如需詳細資訊，請參閱《 **線上叢書》的＜** ALTER AVAILABILITY GROUP [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 陳述式＞。  
>   
>  複本主機 'MyReplicaHostName' 發生了一個或多個發行者驗證錯誤。  
  
 這是預期行為。 您必須直接在主機上查詢 sysserver 項目，藉以確認訂閱者伺服器項目是否存在這些次要複本主機上。  
  
##  <a name="7-add-the-original-publisher-to-replication-monitor"></a><a name="step7"></a> 7.將原始發行者加入至複寫監視器  
 在每個可用性群組複本上，將原始發行者加入至複寫監視器。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **複寫**  
  
-   [維護 AlwaysOn 發行集資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/maintaining-an-always-on-publication-database-sql-server.md)  
  
-   [複寫、變更追蹤、變更資料擷取和 AlwaysOn 可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/replicate-track-change-data-capture-always-on-availability.md)  
  
-   [複寫管理常見問題集](../../../relational-databases/replication/administration/frequently-asked-questions-for-replication-administrators.md)  
  
 **若要建立並設定可用性群組**  
  
-   [使用可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)  
  
-   [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)  
  
-   [建立可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/create-an-availability-group-transact-sql.md)  
  
-   [建立可用性群組 &#40;SQL Server PowerShell&#41;](../../../database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell.md)  
  
-   [在新增或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)  
  
-   [針對 AlwaysOn 可用性群組建立資料庫鏡像端點 &#40;SQL Server PowerShell&#41;](../../../database-engine/availability-groups/windows/database-mirroring-always-on-availability-groups-powershell.md)  
  
-   [將次要複本聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)  
  
-   [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
- [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)   
- [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md) 
- [Always On 可用性群組：互通性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-availability-groups-interoperability-sql-server.md)   
- [SQL Server 複寫](../../../relational-databases/replication/sql-server-replication.md)  
- 如果使用非預設的連接埠，請參閱 [Walkthrough Publisher, Distributor, Subscriber in AlwaysOn Availability Groups](https://repltalk.com/2019/03/09/walkthrough-publisher-distributor-subscriber-in-alwayson-availability-groups) (AlwaysOn 可用性群組中的逐步解說發行者、散發者、訂閱者)。
