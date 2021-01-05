---
title: 在次要資料庫上啟動資料移動
description: 了解如何針對參與 SQL Server 上 Always On 可用性群組的次要資料庫啟動資料移動。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- Availability Groups [SQL Server], databases
ms.assetid: 498eb3fb-6a43-434d-ad95-68a754232c45
author: cawrites
ms.author: chadam
ms.openlocfilehash: 262e797573690f8a648b552e782b0161f41ca7cc
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642985"
---
# <a name="start-data-movement-on-an-always-on-secondary-database-sql-server"></a>於 AlwaysOn 次要資料庫啟動資料移動 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  本主題描述在將資料庫加入 AlwaysOn 可用性群組之後如何啟動資料同步處理。 對於每個新的主要複本，必須在裝載次要複本的伺服器執行個體上準備次要資料庫。 然後每個次要資料庫必須手動聯結至可用性群組。  
  
> [!NOTE]  
>  如果在裝載可用性群組之可用性複本的每個伺服器執行個體上檔案路徑都相同， [[新增可用性群組精靈]](../../../database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio.md)、 [[將複本加入至可用性群組精靈]](../../../database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio.md)或 [[將資料庫加入至可用性群組精靈]](../../../database-engine/availability-groups/windows/availability-group-add-database-to-group-wizard.md) 可能會自動為您啟動資料同步處理。  
  
 若要手動啟動資料同步處理，您需要依次連接到裝載此可用性群組之次要複本的每個伺服器執行個體，並完成下列步驟：  
  
1.  還原每個主要資料庫及其交易記錄的目前備份 (使用 RESTORE WITH NORECOVERY)。 您可以使用下列其中一種替代方法：  
  
    -   使用 RESTORE WITH NORECOVERY，手動還原主要資料庫的最近資料庫備份，然後使用 RESTORE WITH NORECOVERY，還原每個後續的記錄備份。 在裝載可用性群組之次要複本的每個伺服器執行個體上，執行此還原順序。  
  
         **如需詳細資訊：＜＞**  
  
         [針對可用性群組手動準備次要資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)  
  
    -   如果您要將一或多個記錄傳送主要資料庫加入可用性群組，您可能可以從記錄傳送將一或多個對應的次要資料庫移轉至 AlwaysOn 可用性群組。 移轉記錄傳送次要資料庫會要求該資料庫使用與主要資料庫相同的資料庫名稱，以及該資料庫位於主控可用性群組之次要複本的伺服器執行個體上。 此外，可用性群組必須設定，才能以主要複本做為備份的優先考量，並且適合用於執行備份 (也就是說，它的備份優先權為 >0)。 備份作業在主要資料庫上執行之後，您將需要停用備份作業；還原作業在指定的次要資料庫上執行之後，您將需要停用還原作業。  
  
        > [!NOTE]  
        >  為可用性群組建立所有次要資料庫之後，如果您想要在次要複本上執行備份，則需要重新設定可用性群組的自動備份喜好設定。  
  
         **如需詳細資訊：＜＞**  
  
         [從記錄傳送移轉至 AlwaysOn 可用性群組的必要條件 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-migrating-log-shipping-to-always-on-availability-groups.md)  
  
         [設定可用性複本的備份 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server.md)  
  
2.  盡快將每個新備妥的次要資料庫聯結至可用性群組。  
  
     **如需詳細資訊：＜＞**  
  
     [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)  
  
##  <a name="related-tasks"></a><a name="LaunchWiz"></a> 相關工作  
  
-   [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)  
  
-   [使用 [將複本加入可用性群組中精靈] &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio.md)  
  
-   [使用將資料庫加入至可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/availability-group-add-database-to-group-wizard.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)  
  
  
