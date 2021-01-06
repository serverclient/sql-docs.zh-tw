---
title: 可用性群組精靈：新增 IP 位址
description: '描述 SQL Server Management Studio [可用性群組精靈] 中 [指定複本] 頁面上找到的 [新增 IP 位址] 對話方塊選項。 '
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: end-user-help
f1_keywords:
- sql13.swb.availabilitygrouplistener.addipaddress.f1
ms.assetid: 98c9ad3b-ff3c-4c1d-b344-59a72fca137c
author: cawrites
ms.author: chadam
ms.openlocfilehash: 6b6f405786daf40e1241af48ee3e442cea998a6f
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643776"
---
# <a name="add-ip-address-dialog-box-sql-server-management-studio"></a>[加入 IP 位址] 對話方塊 (SQL Server Management Studio)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此 F1 說明主題描述 **[加入 IP 位址]** 對話方塊的選項。 可從 **[新增可用性群組接聽程式]** 對話方塊以及  或 **的** [指定複本] [!INCLUDE[ssAoNewAgWiz](../../../includes/ssaonewagwiz-md.md)] 頁面的 [!INCLUDE[ssAoAddRepWiz](../../../includes/ssaoaddrepwiz-md.md)] [接聽程式] [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]索引標籤存取此對話方塊。  
  
## <a name="prerequisites"></a>Prerequisites  
 開始將子網路加入至可用性群組接聽程式之前，請務必知道每個子網路的 IP 位址，以及 IPv4 位址的子網路遮罩。  
  
##  <a name="add-ip-address-options"></a><a name="PageOptions"></a> 加入 IP 位址選項  
 **子網路**  
 使用此下拉式清單，為您要加入至可用性群組接聽程式的子網路選取位址。 根據預設，子網路擁有 IPv4 位址和 IPv6 位址。 第一次使用 **[加入 IP 位址]** 對話方塊時， **[子網路]** 下拉式清單會為裝載可用性群組之複本的每個子網路顯示這兩種子網路位址。 若要將給定的子網路加入至接聽程式，請選取其中一個子網路位址。  
  
 在完成 **[加入 IP 位址]** 對話方塊，並按一下 **[確定]** 將選定的子網路位址加入至接聽程式之後， **[子網路]** 下拉式清單會篩選出該子網路位址。 所有未選定的子網路位址會保留在下拉式清單中。 針對每個子網路，務必只將一個子網路位址加入至接聽程式，否則接聽程式建立會失敗。  
  
 **位址**  
 使用此欄位，為選定的子網路位址輸入靜態 IP 位址。 請連絡您的網路系統管理員以取得此靜態 IP 位址。 請務必為選定的子網路位址輸入有效位址，否則接聽程式建立會失敗。  
  
 **IPv4 位址**  
 如果您選取了子網路的 IPv4 子網路位址，請在這裡輸入有效的 IPv4 靜態位址。  
  
 **子網路遮罩**  
 對於 IPv4 位址，此唯讀欄位顯示選定之子網路的子網路遮罩。  
  
 **IPv6 位址**  
 如果您選取了子網路的 IPv6 子網路位址，請在這裡輸入有效的 IPv6 靜態位址。  
  
 **確定**  
 按一下以加入所選位址的子網路，以及指定的靜態 IP 位址。 包含這些值的資料列就會加入至 **[新增可用性群組接聽程式]** 或 **[指定複本]** 對話方塊的子網路方格中。  
  
> [!IMPORTANT]  
>  **[加入 IP 位址]** 對話方塊不會驗證 IP 位址。 此對話方塊也不會阻止您為已加入至可用性群組接聽程式的子網路加入第二個子網路位址。  
  
 **取消**  
 按一下以取消選取，並返回 [新增可用性群組接聽程式]  對話方塊或 [接聽程式]  索引標籤，而不加入任何子網路的靜態 IP 位址。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [建立或設定可用性群組接聽程式 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)  
  
-   [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)  
  
-   [使用 [將複本加入可用性群組中精靈] &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [可用性群組接聽程式、用戶端連接性及應用程式容錯移轉 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)   
 [AlwaysOn 用戶端連接性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-client-connectivity-sql-server.md)  
  
  
