---
title: 使用物件總管詳細資料監視可用性群組 | Microsoft Docs
description: 了解如何使用 SQL Server Management Studio 來監視及管理現有的 AlwaysOn 可用性群組、可用性複本與可用性資料庫。
ms.custom: ''
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.availabilitygroup.OEdetails.f1
helpviewer_keywords:
- Availability Groups [SQL Server], monitoring
- Availability Groups [SQL Server], databases
- Availability Groups [SQL Server]
ms.assetid: 84affc47-40e0-43d9-855e-468967068c35
author: cawrites
ms.author: chadam
ms.openlocfilehash: e6b0c8547718c1a7580108427314a4a58f4ba0b5
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641819"
---
# <a name="use-object-explorer-details-to-monitor-availability-groups"></a>使用物件總管詳細資料監視可用性群組
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 的 [物件總管詳細資料] 窗格來監視及管理現有的 AlwaysOn 可用性群組、可用性複本和可用性資料庫。  
  
> [!NOTE]  
>  如需使用 [物件總管詳細資料] 窗格的詳細資訊，請參閱 [物件總管詳細資料窗格](../../../ssms/object/object-explorer-details-pane.md)。  
  
  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
 您必須連接到裝載主要複本或次要複本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體 (伺服器執行個體)。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 **監視可用性群組、可用性複本和可用性資料庫**  
  
1.  在 [檢視] 功能表上，按一下 **[物件總管詳細資料]** 或按 **F7** 鍵。  
  
2.  在 [物件總管] 中，連接到您要監視可用性群組的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體，然後按一下伺服器名稱以展開伺服器樹狀目錄。  
  
3.  依序展開 [Always On 高可用性] 節點和 [可用性群組] 節點。  
  
4.  **[物件總管詳細資料]** 窗格會顯示連接之伺服器執行個體裝載複本的每個可用性群組。 對於每個可用性群組，[伺服器執行個體 (主要)] 資料行會顯示目前裝載主要複本的伺服器執行個體名稱。  若要顯示有關給定之可用性群組的詳細資訊，請在 [物件總管] 中選取它。  
  
5.  接著 **[物件總管詳細資料]** 窗格會顯示此可用性群組的 **[可用性複本]** 和 **[可用性資料庫]** 節點：  
  
    -   當您在 [物件總管] 中展開 **[可用性群組]** 節點並選取 **[可用性複本]** 節點時， **[物件總管詳細資料]** 窗格會顯示有關群組中每個可用性複本的資訊。 如需詳細資訊，請參閱本主題稍後的＜ [可用性複本詳細資料](#AvReplicaDetails)＞。  
  
         若要對多個可用性複本執行作業，請選取它們，並以滑鼠右鍵按一下它們，開啟列出可用命令的內容功能表。  
  
    -   當您在 [物件總管] 中展開 **[可用性群組]** 節點並選取 **[可用性資料庫]** 節點時， **[物件總管詳細資料]** 窗格會顯示有關群組中每個可用性資料庫的資訊。 如需詳細資訊，請參閱本主題稍後的＜ [可用性資料庫詳細資料](#AvDbDetails)＞。  
  
         若要對多個可用性資料庫執行作業，請選取它們，並以滑鼠右鍵按一下它們，開啟列出可用命令的內容功能表。  
  
##  <a name="availability-groups-details"></a><a name="AvGroupsDetails"></a> 可用性群組詳細資料  
 **[可用性群組]** 詳細資料畫面顯示下列資料行：  
  
 **名稱**  
 列出所選可用性群組的 [可用性複本]、[可用性資料庫] 和 [可用性群組接聽程式] 資料夾。  
  
##  <a name="availability-replica-details"></a><a name="AvReplicaDetails"></a> 可用性複本詳細資料  
 **[可用性複本]** 詳細資料畫面顯示下列資料行：  
  
 **伺服器執行個體**  
 顯示裝載可用性複本的伺服器執行個體名稱，以及表示此伺服器執行個體與本機伺服器執行個體之間目前連接狀態的圖示。  
  
 **角色**  
 指出可用性複本的目前角色： **主要** 或 **次要**。 如需 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 角色的詳細資訊，請參閱 [Always On 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)。  
  
 **次要角色的連接模式**  
 指出執行次要角色之給定可用性複本 (也就是做為次要複本) 的資料庫是否可接受來自用戶端的連接。  
  
 可能的值如下：  
  
|值|描述|  
|-----------|-----------------|  
|**不允許連接**|當此可用性複本做為次要複本時，不允許直接連接到可用性資料庫。 次要資料庫不適用於讀取。|  
|**只允許讀取意圖的連接**|當此複本做為次要複本時，只允許進行直接唯讀連接。 可讀取複本中的所有資料庫。|  
|**允許所有連接**|當此複本做為次要複本時，允許與這些資料庫之間的所有連接。|  
  
 **連接狀態**  
 指出次要複本目前是否已連接到主要複本。 可能的值如下：  
  
|值|描述|  
|-----------|-----------------|  
|**已中斷連線**|若為遠端可用性複本，表示它與本機可用性複本已中斷連接。 本機複本對 [已中斷連接] 狀態的回應取決於其角色，如下所示：<br /><br /> 在主要複本上，如果次要複本已中斷連接，主要複本上的次要資料庫就會標示為 **[未同步處理]** ，而且主要複本會等候次要複本重新連接。<br /><br /> 在次要複本上，一旦偵測到它已中斷連接之後，次要複本就會嘗試重新連接到主要複本。|  
|**已連接**|目前連接到本機複本的遠端可用性複本。|  
|**NULL**|如果本機複本是次要複本，其他次要複本的此值就是 NULL。|  
  
 **同步處理狀態**  
 指出次要複本目前是否與主要複本進行同步處理。 可能的值如下：  
  
|值|描述|  
|-----------|-----------------|  
|**[未同步處理]**|資料庫未同步處理或者尚未聯結至可用性群組。|  
|**已同步處理**|資料庫會與目前主要複本 (如果有的話) 或最後一個主要複本的主要資料庫進行同步處理。<br /><br /> 注意:在效能模式中，資料庫永遠不會處於同步處理狀態。|  
|**NULL**|未知的狀態。 當本機伺服器執行個體無法與 WSFC 容錯移轉叢集通訊 (亦即，本機節點不屬於 WSFC 仲裁的一部分) 時，就會出現這個值。|  
  
> [!NOTE]  
>  如需可用性複本效能計數器的相關資訊，請參閱 [SQLServer，可用性複本](../../../relational-databases/performance-monitor/sql-server-availability-replica.md)。  
  
##  <a name="availability-database-details"></a><a name="AvDbDetails"></a> 可用性資料庫詳細資料  
 **[可用性資料庫]** 詳細資料畫面顯示給定之可用性群組中可用性資料庫的下列屬性：  
  
 **名稱**  
 可用性資料庫的名稱。  
  
 **同步處理狀態**  
 指出可用性資料庫目前是否與主要複本進行同步處理。  
  
 可能的同步處理狀態如下所示：  
  
|值|描述|  
|-----------|-----------------|  
|正在同步處理|次要資料庫已經收到主要資料庫中尚未寫入磁碟 (強行寫入) 的交易記錄檔記錄。<br /><br /> 注意:在非同步認可模式中，同步處理模式一律為 [正在同步處理]。|  
|||  
  
 **已暫停**  
 指出可用性資料庫目前是否已上線。 可能的值如下：  
  
|值|描述|  
|-----------|-----------------|  
|**已暫停**|此狀態指出資料庫在本機已暫停，需要手動恢復。<br /><br /> 在主要複本上，此值對次要資料庫是不可靠的。 若要可靠地判斷次要資料庫是否已暫止，請在裝載此資料庫的次要複本上查詢它。|  
|**未聯結**|指出次要資料庫尚未聯結至可用性群組或已從群組中移除。|  
|**線上存取**|指出在本機可用性複本上的資料庫未暫止，並且已連接到資料庫。|  
|**未連接**|指出次要複本目前無法連接到主要複本。|  
  
> [!NOTE]  
>  如需可用性資料庫效能計數器的詳細資訊，請參閱 [SQL Server，資料庫複本](../../../relational-databases/performance-monitor/sql-server-database-replica.md)。  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_os_performance_counters &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md)   
 [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)   
 [檢視可用性群組屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-group-properties-sql-server.md)   
 [檢視可用性複本屬性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/view-availability-replica-properties-sql-server.md)  
  
  
