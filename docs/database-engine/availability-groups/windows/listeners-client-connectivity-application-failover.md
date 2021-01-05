---
title: 連線到可用性群組接聽程式
description: 包含連線到 Always On 可用性群組接聽程式的資訊；例如，如何連線到主要複本和唯讀次要複本，以及如何使用 TLS/SSL 和 Kerberos。
ms.custom: contperf-fy21q1
ms.date: 02/27/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], listeners
- read-only routing
- read-intent connections [AlwaysOn Availability Groups]
- Availability Groups [SQL Server], configuring
- Availability Groups [SQL Server], read-only routing
- Availability Groups [SQL Server], client connectivity
ms.assetid: 76fb3eca-6b08-4610-8d79-64019dd56c44
author: cawrites
ms.author: chadam
ms.openlocfilehash: c0578d5522ca998e7243fd20b77daf8d3705bd85
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878908"
---
# <a name="connect-to-an-always-on-availability-group-listener"></a>連線到 Always On 可用性群組接聽程式 
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  
在[設定可用性群組接聽程式](create-or-configure-an-availability-group-listener-sql-server.md)之後，您必須更新連接字串，以連線到 Always On 可用性群組接聽程式。 這會將來自應用程式的流量自動路由至預定複本，而不需要在每次容錯移轉之後手動更新連接字串。 
  

##  <a name="connect-to-the-primary-replica"></a><a name="ConnectToPrimary"></a> 連線到主要複本  

在連接字串中指定可用性群組接聽程式的 DNS 名稱，以連線到主要複本進行讀寫存取。 

例如，若要在 SQL Server Management Studio 中透過接聽程式連線到主要複本，請在 [伺服器名稱] 欄位中輸入接聽程式 DNS 名稱： 

![在 SSMS 中連線到接聽程式](media/listeners-client-connectivity-application-failover/connect-to-listener-in-ssms.png)


在容錯移轉期間，當主要複本變更時，會中斷與接聽程式的現有連線，並將新連線路由至新的主要複本。  

ADO.NET 提供者 (System.Data.SqlClient) 的基本連接字串範例如下： 
  
```  
Server=tcp: AGListener,1433;Database=MyDB;Integrated Security=SSPI  
```  

您可執行下列 Transact-SQL (T-SQL) 命令，以確認目前透過接聽程式連線到哪個複本：

```sql
SELECT @@SERVERNAME
```

例如，當 SQLVM1 是主要複本時： 

![檢查複本連線](media/listeners-client-connectivity-application-failover/replica-server-name.png)


您仍然可以使用主要或次要複本的執行個體名稱來直接連線到 SQL Server 的執行個體，而不是使用可用性群組接聽程式。 不過，您將因此失去新連線會自動路由至目前新主要複本的優點。 此外，您將失去唯讀路由的優點，其中以 `read-intent` 指定的連線會自動路由至可讀取次要複本。 

##  <a name="connect-to-a-read-only-replica"></a><a name="ConnectToSecondary"></a> 連線到唯讀複本 

「唯讀路由」  是指將連入接聽程式連線自動路由至設定為允許唯讀工作負載的可讀取次要複本。 

如果下列條件成立，則會將連線自動路由至唯讀複本： 
 
-   至少一個次要複本設定為唯讀存取，且每個唯讀次要複本和主要複本都[設定為支援唯讀路由](configure-read-only-routing-for-an-availability-group-sql-server.md)。 

-   連接字串會參考可用性群組中的相關資料庫。 您有一個替代方案，就是將資料庫設定為連線所使用之登入的預設資料庫。 如需詳細資訊，請參閱[演算法如何使用唯讀路由](/archive/blogs/mattn/calculating-read_only_routing_url-for-alwayson)一文。

-   連接字串參考可用性群組接聽程式，而且內送連接的應用程式意圖設定為唯讀，例如，透過在 ODBC 或 OLEDB 連接字串或連接屬性 (attribute) 或屬性 (property) 中使用 **Application Intent=ReadOnly** 關鍵字。 

應用程式意圖屬性在登入期間是儲存在用戶端的工作階段，然後 SQL Server 會處理此意圖，並依據可用性群組的設定和次要複本中目標資料庫的目前讀寫狀態，決定要執行的動作。  

例如，若要使用 SQL Server Management Studio 連線到唯讀複本，請選取 連線到伺服器  對話方塊上的 選項  ，選取 其他連線參數  索引標籤，然後在文字方塊中指定 `ApplicationIntent=ReadOnly`：

![SSMS 中的唯讀連線](media/listeners-client-connectivity-application-failover/read-only-intent-in-ssms.png)
  
指定唯讀應用程式意圖的 ADO.NET 提供者 (System.Data.SqlClient) 其連接字串範例如下： 

```  
Server=tcp:AGListener;Database=AdventureWorks;Integrated Security=SSPI;ApplicationIntent=ReadOnly  
```  
  
如需詳細資訊，請參閱[設定可用性複本上的唯讀存取 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server.md)  

## <a name="non-default-port"></a>無預設連接埠

建立接聽程式時，您可指定接聽程式使用的連接埠。 如果連接埠是預設連接埠 1433，則當連線到接聽程式時，就不需要指定連接埠號碼。 不過，如果連接埠不是 1433，則必須以 `listenername,port` 格式在連接字串中指定連接埠，例如：

![使用非預設連接埠進行連線](media/listeners-client-connectivity-application-failover/specify-port-in-ssms.png)

指定接聽程式使用非預設連接埠的 ADO.NET 提供者 (System.Data.SqlClient) 其連接字串範例如下： 

```  
Server=tcp:AGListener,1445;Database=AdventureWorks;Integrated Security=SSPI 
```  

##  <a name="bypass-listeners"></a><a name="BypassAGl"></a> 略過接聽程式  

雖然可用性群組接聽程式支援容錯移轉重新導向和唯讀路由，但用戶端連接不一定要使用它們。 用戶端連接也可以直接參考 SQL Server 執行個體，而不連接到可用性群組接聽程式。  
  
對 SQL Server 執行個體來說，連線使用可用性群組接聽程式或另一個執行個體端點來登入並沒有關係。  SQL Server 執行個體會確認目標資料庫的狀態，並依據可用性群組的組態和執行個體上資料庫的目前狀態，允許或不允許連接。  例如，如果用戶端應用程式直接連線到 SQL Server 執行個體的通訊埠，並連線到裝載於可用性群組的目標資料庫，且目標資料庫處於主要狀態並已上線，則連線就會成功。  如果目標資料庫處於離線狀態或處於轉換狀態，資料庫連接就會失敗。  
  
另外，從資料庫鏡像移轉至 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]時，只要僅一個次要複本存在且不允許使用者連接，應用程式就可以指定資料庫鏡像連接字串。 

##  <a name="database-mirroring-connection-strings"></a><a name="DbmConnectionString"></a> 資料庫鏡像連接字串 
 如果可用性群組只擁有一個次要複本，並且已針對次要複本設定 ALLOW_CONNECTIONS = READ_ONLY 或 ALLOW_CONNECTIONS = NONE，則用戶端可以使用資料庫鏡像連接字串連接至主要複本。 從資料庫鏡像將現有的應用程式移轉到可用性群組時，這種方法會很實用，前提是您要將可用性群組限制為只能有兩個可用性複本 (一個主要複本和一個次要複本)。 如果您加入其他次要複本，您需要為可用性群組建立可用性群組接聽程式，並更新您的應用程式使用可用性群組接聽程式 DNS 名稱。  
  
 當使用資料庫鏡像連接字串時，用戶端可以使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client 或 .NET Framework Data Provider for [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]。 用戶端提供的連接字串至少必須提供一個伺服器執行個體名稱，也就是 *「初始夥伴名稱」* ，以識別一開始裝載您打算連接之可用性複本的伺服器執行個體。 此連接字串也可以選擇性地提供另一個伺服器執行個體的名稱，也就是 *「容錯移轉夥伴名稱」* (Failover Partner Name)，以識別一開始將次要複本裝載為容錯移轉夥伴名稱的伺服器執行個體。  
  
 如需資料庫鏡像連接字串的詳細資訊，請參閱 [將用戶端連接至資料庫鏡像工作階段 &#40;SQL Server&#41;](../../../database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server.md)。  
  
  
##  <a name="multi-subnet-failovers"></a><a name="SupportAgMultiSubnetFailover"></a> 多重子網路容錯移轉  
 
 若所用的用戶端程式庫可在連接字串中使用 MultiSubnetFailover 連線選項，即可根據所正在使用提供者的語法，將 MultiSubnetFailover 設定為 "True" 或 "Yes"，以最佳化可用性群組容錯移轉至不同的子網路。  
  
> [!NOTE]  
>  對於可用性群組接聽程式以及 SQL Server 容錯移轉叢集執行名稱的單一和多重子網路連接，建議使用此設定。  啟用此選項會增加額外的最佳化，甚至在單一子網路案例也一樣。  
  
 **MultiSubnetFailover** 連接選項只適用於 TCP 網路通訊協定，而且只在連接到可用性群組接聽程式時以及任何虛擬網路名稱連接到 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]時才受支援。  
  
 可啟用多重子網路容錯移轉的 ADO.NET 提供者 (System.Data.SqlClient) 連接字串範例如下：  
  
```  
Server=tcp:AGListener,1433;Database=AdventureWorks;Integrated Security=SSPI; MultiSubnetFailover=True  
```  
  
 **MultiSubnetFailover** 連接選項應該設定為 **True** ，即使可用性群組只跨越單一子網路也一樣。  這可讓您將新用戶端預先設定為支援子網路的未來跨越，而不需要在未來變更用戶端連接字串，此外也會最佳化單一子網路容錯移轉的容錯移轉效能。  雖然 **MultiSubnetFailover** 連接選項不是必要，但是它提供更快子網路容錯移轉的好處。  這是因為用戶端驅動程式會嘗試對與可用性群組平行相關的每個 IP 位址開啟 TCP 通訊端。  用戶端驅動程式會等候第一個成功回應的 IP，一旦回應，就會將它用於連接。  
  
##  <a name="listeners--tlsssl-certificates"></a><a name="SSLcertificates"></a> 接聽程式與 TLS/SSL 憑證  

連線到可用性群組接聽程式時，如果參與的 SQL Server 執行個體同時使用 TLS/SSL 憑證和工作階段加密，連線的用戶端驅動程式需要支援 TLS/SSL 憑證中的主體替代名稱，才能強制加密。  我們已計劃針對 ADO.NET (SqlClient)、Microsoft JDBC 和 SQL Native Client (SNAC)，提供憑證主體替代名稱的 SQL Server 驅動程式支援。  
  
您必須針對容錯移轉叢集中的每個參與伺服器節點來設定 X.509 憑證，並在憑證的主體替代名稱中設定所有可用性群組接聽程式清單。 

憑證值的格式為： 

```  
CN = Server.FQDN  
SAN = Server.FQDN,Listener1.FQDN,Listener2.FQDN
```

例如，您有下列值： 

```
Servername: Win2019   
Instance: SQL2019   
AG: AG2019   
Listener: Listener2019   
Domain: contoso.com  (which is also the FQDN)
```

針對有單一可用性群組的 WSFC，憑證應有伺服器的完整網域名稱 (FQDN)，以及接聽程式的 FQDN： 

```
CN: Win2019.contoso.com
SAN: Win2019.contoso.com, Listener2019.contoso.com 
```

若使用此設定，連線到執行個體 (`WIN2019\SQL2019`) 或接聽程式 (`Listener2019`) 時，系統將會加密您的連線。 

取決於網路設定的方式而定，有一小部分的客戶可能也需要將 NetBIOS 新增至 SAN。 在此情況下，憑證值應該是： 

```
CN: Win2019.contoso.com
SAN: Win2019,Win2019.contoso.com,Listener2019,Listener2019.contoso.com
```

如果 WSFC 有三個可用性群組接聽程式，例如：Listener1、Listener2、Listener3

則憑證值應該為： 

```
CN: Win2019.contoso.com
SAN: Win2019.contoso.com,Listener1.contoso.com,Listener2.contoso.com,Listener3.contoso.com
```
  
  
##  <a name="listeners-and-kerberos-spns"></a><a name="SPNs"></a> 接聽程式與 Kerberos (SPN) 

網域系統管理員必須在 Active Directory 中設定每個可用性群組接聽程式的伺服器主體名稱 (SPN)，以便針對接聽程式的用戶端連線啟用 Kerberos。 註冊 SPN 時，您必須使用裝載可用性複本的伺服器執行個體服務帳戶。 若要讓 SPN 在所有複本上運作，相同服務帳戶必須用於裝載可用性群組之 WSFC 叢集的所有執行個體。  
  
 您可以使用 **setspn** Windows 命令列工具來設定 SPN。  例如，若要設定 SPN 用於 `AG1listener.Adventure-Works.com` 可用性群組，而此可用性群組裝載於一組都設為使用 `corp\svclogin2`網域帳戶的 SQL Server 執行個體：  
  
```  
setspn -A MSSQLSvc/AG1listener.Adventure-Works.com:1433 corp\svclogin2  
```  
  
 如需有關為 SQL Server 手動註冊 SPN 的詳細資訊，請參閱＜ [註冊 Kerberos 連接的服務主體名稱](../../../database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections.md)＞。  
  


## <a name="next-steps"></a>後續步驟

當成功連線到接聽程式之後，請考慮將[唯讀工作負載](overview-of-always-on-availability-groups-sql-server.md)和[備份](configure-backup-on-availability-replicas-sql-server.md)卸載至次要複本，以提升效能。 您也可以檢閱各種[可用性群組監視策略](monitoring-of-availability-groups-sql-server.md)，以確保可用性群組的健康狀態。 

如需可用性群組的詳細資訊，請參閱 [Always On 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)。