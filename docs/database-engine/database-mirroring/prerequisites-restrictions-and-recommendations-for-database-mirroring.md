---
title: 資料庫鏡像：必要條件、限制與建議
description: 了解使用 SQL Server 設定資料庫鏡像的必要條件、限制和建議事項。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], deployment
- partners [SQL Server]
- database mirroring [SQL Server], prerequisites
- database mirroring [SQL Server], recommendations
- database mirroring [SQL Server], restrictions
- database mirroring [SQL Server], planning
- database mirroring [SQL Server], about database mirroring
ms.assetid: fdcf2251-9895-44c6-b81e-768fef32e732
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 0aca4b4b5250232f065b02ca21be7187ab42c119
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641471"
---
# <a name="prerequisites-restrictions-and-recommendations-for-database-mirroring"></a>資料庫鏡像的必要條件、限制和建議事項
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
    
> [!NOTE]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)] 請改用 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]。  
  
 本主題描述設定資料庫鏡像的必要條件和建議事項。 如需資料庫鏡像的簡介，請參閱 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)。  
  
  
##  <a name="support-for-database-mirroring"></a><a name="DbmSupport"></a> 支援資料庫鏡像  
 如需 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中資料庫鏡像支援的相關資訊，請參閱 [SQL Server 2016 的版本和支援的功能](../../sql-server/editions-and-components-of-sql-server-2016.md)。
  
 請注意，資料庫鏡像適用於任何支援的資料庫相容性層級。 如需支援的相容性層級相關資訊，請參閱 [ALTER DATABASE 相容性層級 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。  
  
  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
  
-   如果要建立鏡像工作階段，則夥伴伺服器和見證伺服器 (如果有的話) 必須在相同的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本上執行。  
  
-   兩個夥伴 (亦即，主體伺服器和鏡像伺服器) 必須都在執行相同的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本。 見證 (如果有的話) 可在任一版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上執行，只要該版本支援資料庫鏡像。  
  
    > [!NOTE]  
    >  您可以將做為鏡像工作階段夥伴的伺服器執行個體升級為最新版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 如需詳細資訊，請參閱 [升級鏡像執行個體](../../database-engine/database-mirroring/upgrading-mirrored-instances.md)。  
  
-   資料庫必須使用完整復原模式。 簡單與大量記錄復原模式不支援資料庫鏡像。 因此，鏡像資料庫的大量作業永遠都是完整記錄作業。 如需復原模式的相關資訊，請參閱[復原模式 &#40;SQL Server&#41;](../../relational-databases/backup-restore/recovery-models-sql-server.md)。  
  
-   確認鏡像伺服器有足夠的磁碟空間可用於鏡像資料庫。  
  
    > [!NOTE]  
    >  如需如何在複寫資料庫上使用資料庫鏡像的相關資訊，請參閱[資料庫鏡像和複寫 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-and-replication-sql-server.md)。  
  
-   在鏡像伺服器上建立鏡像資料庫時，請確定您使用 WITH NORECOVERY 指定了相同的資料庫名稱來還原主體資料庫的備份。 另外，您還必須再一次使用 WITH NORECOVERY 來套用該備份完成之後建立的所有記錄備份。  
  
    > [!IMPORTANT]  
    >  如果資料庫鏡像已停止，您必須先將主體資料庫上建立的所有後續記錄備份套用到鏡像資料庫，然後才能重新啟動鏡像。  
  
  
##  <a name="restrictions"></a><a name="Restrictions"></a> 限制  
  
-   只有使用者資料庫可進行鏡像。 您無法鏡像處理 **master**、 **msdb**、 **tempdb** 或 **model** 資料庫。  
  
-   鏡像資料庫不能在資料庫鏡像工作階段期間重新命名。  
  
-   資料庫鏡像不支援 FILESTREAM。 不能在主體伺服器上建立 FILESTREAM 檔案群組。 不能針對包含 FILESTREAM 檔案群組的資料庫設定資料庫鏡像。  
  
-   跨資料庫交易或分散式交易不支援資料庫鏡像。 如需詳細資訊，請參閱[資料庫鏡像或 AlwaysOn 可用性群組不支援跨資料庫交易 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/transactions-always-on-availability-and-database-mirroring.md)。  
  
  
##  <a name="recommendations-for-configuring-partner-servers"></a><a name="RecommendationsForPartners"></a> 設定夥伴伺服器的建議  
  
-   夥伴伺服器應該在可相比而且可以處理相同工作負載的系統上執行。  
  
    > [!NOTE]  
    >  如果您打算使用具有自動容錯移轉的高安全性模式，則每個容錯移轉夥伴的正常負載應該少於 50% 以下的 CPU 使用量。 如果您的工作負載使 CPU 超載，容錯移轉夥伴可能無法在鏡像工作階段中對其他伺服器執行個體發出 ping 命令， 進而導致不必要的容錯移轉。 如果您無法將 CPU 使用量保持在 50% 以下，我們建議您使用不含自動容錯移轉的高安全性模式或高效能模式。  
  
-   如果可行的話，鏡像資料庫的路徑 (包括磁碟機代號) 應該要和主體資料庫的路徑完全相同。 如果檔案配置必須不同，您必須在 RESTORE 陳述式中包含 MOVE 選項。 例如，如果主體資料庫位於磁碟機 'F:'，但是鏡像系統缺少 F: 磁碟機。  
  
    > [!IMPORTANT]  
    >  在建立鏡像資料庫時，如果您移動資料庫檔案，則稍後必須暫停鏡像，否則可能無法將檔案加入資料庫。  
  
-   鏡像工作階段中的所有伺服器執行個體都應該使用相同的主要字碼頁和定序。 如果有差異，可能會在鏡像設定期間發生問題。  
  
-   另外，也可以估計容錯移轉資料庫的時間，確定系統組態將會提供所需的效能。 如需詳細資訊，請參閱 [預估角色切換期間的服務中斷時間 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/estimate-the-interruption-of-service-during-role-switching-database-mirroring.md)的程序交換。  
  
-   為達最佳效能，請為鏡像使用專用的網路介面卡 (NIC)。  
  
-   關於廣域網路 (WAN) 對於高安全性模式的資料庫鏡像而言是否可靠，我們不提供任何建議。 如果您決定在 WAN 上使用高安全性模式，則將見證伺服器加入工作階段時要小心謹慎，因為可能會發生不必要的自動容錯移轉。 如需詳細資訊，請參閱本主題稍後的 [部署資料庫鏡像的建議](#RecommendationsForDeploying)。  
  
  
##  <a name="recommendations-for-deploying-database-mirroring"></a><a name="RecommendationsForDeploying"></a> 部署資料庫鏡像的建議  
 最佳資料庫鏡像效能是使用非同步作業所取得。 當使用同步作業之鏡像工作階段的工作負載產生大量交易記錄資料時，效能可能會變慢。  
  
 在測試環境中，適合瀏覽所有作業模式，以評估資料庫鏡像的效能。 然而，在實際環境中部署鏡像之前，必須先了解網路如何在實際環境中運作。  
  
 具有自動容錯移轉的高安全性模式是針對具有專用連接或相當簡單之網路組態的高服務網路所設計，可讓可能發生的網路失敗來源減至最少。 具有自動容錯移轉的高安全性模式一定需要這類高品質網路環境，而且建議所有資料庫鏡像工作階段都使用這類環境。 不過，高效能模式和不含自動容錯移轉的高安全性模式則較不受網路可靠性的影響。  
  
 因此，若為實際執行環境，建議您遵守下列部署指導方針：  
  
1.  以非同步、高效能模式開始執行。 這個模式對網路環境最不敏感，而且會提供瀏覽鏡像運作方式的最佳組態。 我們建議您以非同步的方式執行系統，除非您非常確信您的頻寬可支援鏡像，而且已具備了環境中鏡像設定和非同步模式效能的完整知識。 如需詳細資訊，請參閱 [Database Mirroring Operating Modes](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)。  
  
    > [!IMPORTANT]  
    >  在整個測試中，建議您監視工作階段，以找出讓資料庫鏡像失敗的網路錯誤。 如需有關可能之失敗來源的詳細資訊，請參閱＜ [Possible Failures During Database Mirroring](../../database-engine/database-mirroring/possible-failures-during-database-mirroring.md)＞。 如需如何監視資料庫鏡像的相關資訊，請參閱[監視資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/monitoring-database-mirroring-sql-server.md)。  
  
2.  當您確信非同步作業符合您的商務需求時，可能會想要嘗試同步作業來提升資料保護。 當您測試同步鏡像如何在環境中運作時，建議您先測試不含自動容錯移轉的高安全性模式。 這項測試的主要目的是要了解同步作業如何影響資料庫效能。 如需詳細資訊，請參閱 [Database Mirroring Operating Modes](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)。  
  
3.  等候啟用自動容錯移轉，直到您確信不含自動容錯移轉的高安全性模式已符合商務需求，而且網路錯誤不會導致失敗為止。 如需詳細資訊，請參閱 [資料庫鏡像工作階段期間的角色切換 &#40;SQL Server&#41;](../../database-engine/database-mirroring/role-switching-during-a-database-mirroring-session-sql-server.md)版本都可使用見證伺服器執行個體。  
  
  
## <a name="see-also"></a>另請參閱  
 [設定資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)   
 [資料庫鏡像和 Always On 可用性群組的傳輸安全性 &#40;SQL Server&#41;](../../database-engine/database-mirroring/transport-security-database-mirroring-always-on-availability.md)   
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)   
 [疑難排解資料庫鏡像組態 &#40;SQL Server&#41;](../../database-engine/database-mirroring/troubleshoot-database-mirroring-configuration-sql-server.md)  
  
