---
title: 容錯移轉叢集執行個體的容錯移轉原則
description: 描述 SQL Server 容錯移轉叢集執行個體可用的不同容錯移轉原則。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: failover-cluster-instance
ms.topic: conceptual
helpviewer_keywords:
- flexible failover policy
ms.assetid: 39ceaac5-42fa-4b5d-bfb6-54403d7f0dc9
author: cawrites
ms.author: chadam
ms.openlocfilehash: fa75cc2e8a120510edc711aafc37d268fec4dba7
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642717"
---
# <a name="failover-policy-for-failover-cluster-instances"></a>Failover Policy for Failover Cluster Instances
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體 (FCI) 中，一個給定時間只能有一個節點可以擁有 Windows Server 容錯移轉叢集 (WSFC) 叢集資源群組。 用戶端要求都是透過 FCI 中的此節點提供服務。 萬一發生失敗而且重新啟動不成功，群組擁有權會移至 FCI 中的另一個 WSFC 節點。 這項程序就稱為容錯移轉。 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 提高失敗偵測的可靠性，並提供靈活的容錯移轉原則。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 仰賴基礎 WSFC 服務進行容錯移轉偵測。 因此，兩個機制決定 FCI 的容錯移轉行為：一是原生 WSFC 功能，二是 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式所加入的功能。  
  
-   WSFC 叢集維護仲裁設定，以確保自動容錯移轉中的唯一容錯移轉目標。 WSFC 服務決定叢集是否一直保持最佳的仲裁健全狀況，並且據以使資源群組上線和離線。  
  
-   使用中的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體會透過專用連接，定期向 WSFC 資源群組回報一組元件診斷。 WSFC 資源群組維護容錯移轉原則，此原則會定義觸發重新啟動和容錯移轉的失敗狀況。  
  
 此主題討論上面的第二個機制。 如需 WSFC 仲裁組態和健全狀況偵測行為的詳細資訊，請參閱 [WSFC 仲裁模式和投票組態 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/wsfc-quorum-modes-and-voting-configuration-sql-server.md)。  
  
> [!IMPORTANT]  
>  AlwaysOn 可用性群組不允許 FCI 來回自動容錯移轉。 不過，AlwaysOn 可用性群組允許 FCI 來回手動容錯移轉。  
  
##  <a name="failover-policy-overview"></a><a name="Concepts"></a> 容錯移轉原則概觀  
 容錯移轉程序可以分為下列步驟：  
  
1.  [監視健全狀態](../../../sql-server/failover-clusters/windows/failover-policy-for-failover-cluster-instances.md#monitor)  
  
2.  [判斷失敗](../../../sql-server/failover-clusters/windows/failover-policy-for-failover-cluster-instances.md#determine)  
  
3.  [回應失敗](../../../sql-server/failover-clusters/windows/failover-policy-for-failover-cluster-instances.md#respond)  
  
###  <a name="monitor-the-health-status"></a><a name="monitor"></a> 監視健全狀態  
 對 FCI 三種類型的健全狀態進行監視：  
  
-   [SQL Server 服務的狀態](../../../sql-server/failover-clusters/windows/failover-policy-for-failover-cluster-instances.md#service)  
  
-   [SQL Server 執行個體的回應性](../../../sql-server/failover-clusters/windows/failover-policy-for-failover-cluster-instances.md#instance)  
  
-   [SQL Server 元件診斷](../../../sql-server/failover-clusters/windows/failover-policy-for-failover-cluster-instances.md#component)  
  
####  <a name="state-of-the-sql-server-service"></a><a name="service"></a> SQL Server 服務的狀態  
 WSFC 服務會監視使用中 FCI 節點上 SQL Server 服務的啟動狀態，以偵測 SQL Server 服務停止的情況。  
  
####  <a name="responsiveness-of-the-sql-server-instance"></a><a name="instance"></a> SQL Server 執行個體的回應性  
 在 SQL Server 啟動期間，WSFC 服務會使用 SQL Server Database Engine 資源 DLL，以獨佔模式用於監視健全狀態的不同執行緒上建立新的連接。 這可以確保 SQL 執行個體在負載下有必要資源可報告其健全狀態。 SQL Server 會使用這個專用連線，以重複模式執行 [sp_server_diagnostics &#40;Transact-SQL&#41;](../../../relational-databases/system-stored-procedures/sp-server-diagnostics-transact-sql.md) 系統預存程序，定期向資源 DLL 回報 SQL Server 元件的健全狀態。  
  
 資源 DLL 使用健全狀況檢查逾時來判斷 SQL 執行個體的回應性。 HealthCheckTimeout 屬性會定義資源 DLL 應該等候 sp_server_diagnostics 預存程序的時間，以便在超過該時間後，向 WSFC 服務回報 SQL 執行個體沒有回應。 此屬性可透過使用 T-SQL 和容錯移轉叢集管理員嵌入式管理單元進行設定。 如需詳細資訊，請參閱＜ [Configure HealthCheckTimeout Property Settings](../../../sql-server/failover-clusters/windows/configure-healthchecktimeout-property-settings.md)＞。 下列項目會描述此屬性會如何影響逾時和重複間隔設定：  
  
-   資源 DLL 會呼叫 sp_server_diagnostics 預存程序，並將重複間隔設定為 HealthCheckTimeout 設定的三分之一。  
  
-   如果 sp_server_diagnostics 預存程序變慢，或未傳回資訊，資源 DLL 會等待 HealthCheckTimeout 所指定的間隔，才向 WSFC 服務回報 SQL 執行個體沒有回應。  
  
-   如果專用連接中斷，資源 DLL 會在 HealthCheckTimeout 所指定的間隔內重試連接到 SQL 執行個體，才向 WSFC 服務回報 SQL 執行個體沒有回應。  
  
####  <a name="sql-server-component-diagnostics"></a><a name="component"></a> SQL Server 元件診斷  
 系統預存程序 sp_server_diagnostics 會定期收集 SQL 執行個體上的元件診斷。 收集到的診斷資訊會在下列每一個元件中顯示為一個資料列，並傳遞給呼叫執行緒。  
  
1.  系統  
  
2.  resource  
  
3.  查詢處理序  
  
4.  io_subsystem  
  
5.  活動  
  
 系統、資源和查詢處理序元件會用於失敗偵測。 io_subsytem 和事件元件只會用於診斷用途。  
  
 每個資訊的資料列集也會寫入至 SQL Server 叢集診斷記錄檔。 如需詳細資訊，請參閱 [檢視及閱讀容錯移轉叢集執行個體診斷記錄檔](../../../sql-server/failover-clusters/windows/view-and-read-failover-cluster-instance-diagnostics-log.md)。  
  
> [!TIP]  
>  sp_server_diagnostic 預存程序可供 SQL Server AlwaysOn 技術使用，它也可供任何 SQL Server 執行個體用於偵測問題及疑難排解。  
  
####  <a name="determining-failures"></a><a name="determine"></a> 判斷失敗  
 SQL Server Database Engine 資源 DLL 會使用 FailureConditionLevel 屬性，判斷偵測到的健全狀態是否為失敗狀況。 FailureConditionLevel 屬性定義了哪些偵測到的健全狀態導致重新啟動或容錯移轉。 多個選項等級可供使用，包括沒有自動重新啟動或容錯移轉，以及導致自動重新啟動或容錯移轉的所有可能失敗狀況。 如需有關如何設定這個屬性的詳細資訊，請參閱＜ [Configure FailureConditionLevel Property Settings](../../../sql-server/failover-clusters/windows/configure-failureconditionlevel-property-settings.md)＞。  
  
 失敗狀況會設定為遞增等級。 對於等級 1-5，每個等級都包含來自上一個等級的所有狀況，並加上自身的狀況。 這表示，每個等級都具有更高的容錯移轉或重新啟動可能性。 下表描述的是失敗狀況等級。  
  
 請檢閱 [sp_server_diagnostics &#40;Transact-SQL&#41;](../../../relational-databases/system-stored-procedures/sp-server-diagnostics-transact-sql.md)，此系統預存程序在失敗狀況層級扮演重要角色。  
  
|層級|條件|描述|  
|-----------|---------------|-----------------|  
|0|沒有自動的容錯移轉或重新啟動|指出任何失敗狀況都不會自動觸發容錯移轉或重新啟動。 這個等級只會用於系統維護的用途。|  
|1|伺服器關閉的容錯移轉或重新啟動|表示伺服器重新啟動或容錯移轉會在引發下列狀況時觸發：<br /><br /> SQL Server 服務已關閉。|  
|2|伺服器無回應的容錯移轉或重新啟動|表示伺服器重新啟動或容錯移轉會在引發下列任何一個狀況時觸發：<br /><br /> SQL Server 服務已關閉。<br /><br /> SQL Server 執行個體沒有回應 (資源 DLL 無法在 HealthCheckTimeout 設定內接收來自 sp_server_diagnostics 的資料)。|  
|3*|發生重大伺服器錯誤的容錯移轉或重新啟動|表示伺服器重新啟動或容錯移轉會在引發下列任何一個狀況時觸發：<br /><br /> SQL Server 服務已關閉。<br /><br /> SQL Server 執行個體沒有回應 (資源 DLL 無法在 HealthCheckTimeout 設定內接收來自 sp_server_diagnostics 的資料)。<br /><br /> 系統預存程序 sp_server_diagnostics 傳回「系統錯誤」。|  
|4|發生一般伺服器錯誤的容錯移轉或重新啟動|表示伺服器重新啟動或容錯移轉會在引發下列任何一個狀況時觸發：<br /><br /> SQL Server 服務已關閉。<br /><br /> SQL Server 執行個體沒有回應 (資源 DLL 無法在 HealthCheckTimeout 設定內接收來自 sp_server_diagnostics 的資料)。<br /><br /> 系統預存程序 sp_server_diagnostics 傳回「系統錯誤」。<br /><br /> 系統預存程序 sp_server_diagnostics 傳回「資源錯誤」。|  
|5|任何合格失敗狀況的容錯移轉或重新啟動|表示伺服器重新啟動或容錯移轉會在引發下列任何一個狀況時觸發：<br /><br /> SQL Server 服務已關閉。<br /><br /> SQL Server 執行個體沒有回應 (資源 DLL 無法在 HealthCheckTimeout 設定內接收來自 sp_server_diagnostics 的資料)。<br /><br /> 系統預存程序 sp_server_diagnostics 傳回「系統錯誤」。<br /><br /> 系統預存程序 sp_server_diagnostics 傳回「資源錯誤」。<br /><br /> 系統預存程序 sp_server_diagnostics 傳回「query_processing 錯誤」。|  
  
 *預設值  
  
####  <a name="responding-to-failures"></a><a name="respond"></a> 回應失敗  
 在偵測到一個或多個失敗狀況後，WSFC 服務回應失敗的方式取決於 WSFC 仲裁狀態以及 FCI 資源群組的重新啟動和容錯移轉設定。 如果 FCI 已經失去其 WSFC 仲裁，整個 FCI 就會離線，而且 FCI 已經失去其高可用性。 如果 FCI 仍然保留其 WSFC 仲裁，WSFC 服務的回應方式可能會先嘗試重新啟動失敗的節點，如果重新啟動嘗試不成功則容錯移轉。 重新啟動和容錯移轉設定是在容錯移轉叢集管理員嵌入式管理單元中進行設定。 如需這些設定的詳細資訊，請參閱 [\<Resource> 屬性：原則索引標籤](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725685(v=ws.11))。  
  
 如需維護仲裁健全狀況的詳細資訊，請參閱 [WSFC 仲裁模式和投票組態 &#40;SQL Server&#41;](../../../sql-server/failover-clusters/windows/wsfc-quorum-modes-and-voting-configuration-sql-server.md)。  
  
## <a name="see-also"></a>另請參閱  
 [ALTER SERVER CONFIGURATION &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-server-configuration-transact-sql.md)  
  
