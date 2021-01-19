---
title: 選擇資料庫引擎升級方法 | Microsoft Docs
description: 此文章描述 SQL Server 中資料庫引擎的升級路徑，包括就地升級、移轉至新的安裝，以及輪流升級。
ms.custom: ''
ms.date: 07/19/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
ms.assetid: 5e57a427-2e88-4ef6-b142-4ccad97bcecc
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 4acdd7f1d8b6a39af4054f30381665d1504bcb71
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171830"
---
# <a name="choose-a-database-engine-upgrade-method"></a>選擇資料庫引擎升級方法
[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

當您為了讓停機時間及風險減到最少，而打算從舊版 SQL Server 升級 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 時，有多種方法可以考慮。 您可以就地執行升級、遷移到新的安裝或執行輪流升級。 下圖將協助您在這些方法中做出選擇。 下方也將討論圖表中的各個方法。 為協助您了解圖表中的決策點，另請檢閱 [計劃和測試資料庫引擎升級計畫](../../database-engine/install-windows/plan-and-test-the-database-engine-upgrade-plan.md)。  
  
 ![資料庫引擎升級方法決策樹](../../database-engine/install-windows/media/database-engine-upgrade-method-decision-tree.png "資料庫引擎升級方法決策樹")  
  
 **下載**  
  
-   若要下載 [!INCLUDE[SSnoversion](../../includes/ssnoversion-md.md)]，請前往  **[Evaluation Center](https://www.microsoft.com/evalcenter/evaluate-sql-server)** 。  
  
-   有 Azure 帳戶嗎？  接著前往 **[這裡](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2017-ws2019?tab=overview)** 加速已安裝 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] Developer Edition 的虛擬機器。  
  
> [!NOTE]  
>  您也可以考慮在升級計畫中升級 Azure SQL Database，或將您的 SQL Server 環境虛擬化。 本文未涵蓋這些文章，如有需要請參閱以下連結：
>   - [Azure 虛擬機器上的 SQL Server 概觀](https://azure.microsoft.com/services/virtual-machines/sql-server/#overview)
>   - [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 
>   - [在 Azure 中選取 SQL Server 選項](/azure/azure-sql/azure-sql-iaas-vs-paas-what-is-overview)。  
  
## <a name="upgrade-in-place"></a>就地升級  
 透過此方法，SQL Server 安裝程式會以新的 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] 位元取代現有的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 位元，然後升級每個系統及使用者資料庫，藉此升級現有的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝。  就地升級方法最簡易，其需要停機一段時間，需要後援時也會花費較長時間，而且未受所有案例支援。 如需支援與未支援的就地升級案例詳細資訊，請參閱 [支援的版本與版本升級](../../database-engine/install-windows/supported-version-and-edition-upgrades-version-15.md)。  
  
 此方法通常用在下列案例中：  
  
-   沒有高可用性 (HA) 組態的開發環境。  
  
-   可容許停機時間，並執行於最新硬體及軟體的非任務關鍵性生產環境。 停機時間取決於您的資料庫大小，以及 I/O 子系統的速度。 在記憶體最佳化資料表使用中時升級 SQL Server 2014 將會花費額外時間。 如需詳細資訊，請參閱 [計劃和測試資料庫引擎升級計劃](../../database-engine/install-windows/plan-and-test-the-database-engine-upgrade-plan.md)。  

> [!NOTE]
> 如需 Microsoft 如何使用此策略的範例，請參閱下列部落格文章：[升級或不升級](https://azure.microsoft.com/blog/azure-sql-server-to-upgrade-or-not-to-upgrade-that-is-the-question/)。 


> [!WARNING]  
>  執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式時， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體會因為執行升級前檢查而停止並重新啟動。  
  
> [!CAUTION]  
>  當您升級 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]時，會覆寫先前的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，所以它不再存在於電腦上。 升級之前，請備份 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫以及與先前的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體相關聯的其他物件。  
  
 下圖提供 [!INCLUDE[ssDE](../../includes/ssde-md.md)]就地升級必要步驟的整體概觀。  
  
 ![資料庫引擎升級非 HA 就地升級](../../database-engine/install-windows/media/database-engine-upgrade-non-ha-in-place-upgrade.png "資料庫引擎升級非 HA 就地升級")  
  
 如需詳細步驟，請參閱[使用安裝精靈升級 SQL Server &#40;安裝程式&#41;](../../database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup.md)。  
  
## <a name="migrate-to-a-new-installation"></a>遷移到新的安裝  
 透過此方法，您可以在建置新 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 環境的同時保有目前環境，前者通常在新的硬體上，並使用新版作業系統。 在新環境中安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 後，您可以執行幾個步驟讓新環境就緒，以便將現有使用者資料庫從現有環境遷移到新環境，並盡可能縮短停機時間。 這些步驟包括遷移下列項目：  
  
-   **系統物件：** 某些應用程式會相依於超出單一使用者資料庫範圍之外的資訊、實體和/或物件。 一般而言，應用程式相依於 master 和 msdb 資料庫以及使用者資料庫。 如果有資料庫正確運作所需的任何項目儲存在使用者資料庫外部，則必須設法讓目的地伺服器執行個體也能提供。 例如，應用程式的登入在 master 資料庫中儲存為中繼資料，就必須在目的地伺服器上加以重新建立。 若應用程式或資料庫維護計畫相依於 SQL Server Agent 作業，而其中繼資料儲存於 msdb 資料庫，則必須在目的地伺服器執行個體上重新建立那些作業。 伺服器層級觸發程序的中繼資料也同樣儲存在 master 中。  
 
   當將應用程式的資料庫移至其他伺服器執行個體時，您必須在目的地伺服器執行個體上重新建立 master 和 msdb 中相依實體及物件的所有中繼資料。 例如，如果資料庫應用程式使用伺服器層級觸發程序，僅在新系統上附加或還原資料庫是不夠的。 除非您以手動方式為 master 資料庫中的那些觸發程序重新建立中繼資料，否則資料庫無法如預期一般運作。 如需詳細資訊，請參閱[在另一個伺服器執行個體上提供可用的資料庫時，管理中繼資料 &#40;SQL Server&#41;](../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)。  
  
-   **儲存在 MSDB 中的 Integration Services 套件：** 如果您將套件儲存在 MSDB 中，就必須使用 [dtutil 公用程式](../../integration-services/dtutil-utility.md)來編寫那些套件的指令碼，或是將它們重新佈署到新的伺服器。 您必須先將封裝升級成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，然後才能在新的伺服器上使用封裝。 如需詳細資訊，請參閱 [Upgrade Integration Services Packages](../../integration-services/install-windows/upgrade-integration-services-packages.md)。  
  
-   **Reporting Services 加密金鑰：** 在報表伺服器組態中，建立用於加密機密資訊的對稱金鑰備份副本是很重要的一部分。 許多例行作業都需要金鑰的備份副本，這備份副本可以讓您在新安裝中重複使用現有的報表伺服器資料庫。 如需詳細資訊，請參閱 [備份與還原 Reporting Services 加密金鑰](../../reporting-services/install-windows/ssrs-encryption-keys-back-up-and-restore-encryption-keys.md) 以及 [Upgrade 以及 Migrate Reporting Services](../../reporting-services/install-windows/upgrade-and-migrate-reporting-services.md)  
  
 一旦新的   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 環境擁有與現有環境相同的系統物件，您就可以將使用者資料庫從現有系統遷移到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，在某種程度上將現有系統的停機時間減到最少。 您可以使用備份與還原完成資料庫遷移，若您處於 SAN 環境中，也可以藉由重新指出 LUN 來完成。 下圖描述這兩種方法的步驟。  
  
> [!CAUTION]  
>  停機時間取決於您的資料庫大小，以及 I/O 子系統的速度。 在記憶體最佳化資料表使用中時升級 SQL Server 2014 將會花費額外時間。 如需詳細資訊，請參閱 [計劃和測試資料庫引擎升級計劃](../../database-engine/install-windows/plan-and-test-the-database-engine-upgrade-plan.md)。  
  
 在遷移使用者資料庫後，您可以使用多種方法其中之一 (例如將伺服器重新命名、使用 DNS 項目、修改連接字串) 向新使用者指出新的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。  與就地升級相較之下，新的安裝方法可減少風險及停機時間，並有助於硬體及作業系統升級與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的更新同時進行。  
  
> [!NOTE]  
>  若您已經就地擁有高可用性 (HA) 解決方案或其他多個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體環境，請前往 [輪流升級](#rolling-upgrade)。 若您未就地擁有高可用性解決方案，則可以考慮暫時設定 [資料庫鏡像](../database-mirroring/setting-up-database-mirroring-sql-server.md) 進一步縮短停機時間以加速升級，或利用此機會設定 [AlwaysOn 可用性群組](../availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server.md) 當作永久 HA 解決方案。  
  
 例如，您可以使用此方法來升級：  
  
-   不支援之作業系統上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝。    
-   [!INCLUDE[ss2016](../../includes/sssql16-md.md)] 和更新版本的 SQL Server x86 安裝不支援 x86 安裝。   
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 新硬體和/或新版作業程式。    
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。   
-   [!INCLUDE[ss2016](../../includes/sssql16-md.md)] 和更新版本的 SQL Server 2005 不支援 SQL Server 2005 的就地升級。 如需詳細資訊，請參閱[是否從 SQL Server 2005 升級？](../../sql-server/end-of-support/sql-server-end-of-life-overview.md)

  
新安裝升級的必要步驟會依據您使用連接儲存體還是 SAN 儲存體而有些微不同。  
  
-   **連結的存放裝置環境：** 如果您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 環境使用連結的存放裝置，下圖及圖內的連結會引導您完成 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 之新安裝升級所需的步驟。  
  
     ![使用連結的儲存裝置備份及還原的新安裝升級方法](../../database-engine/install-windows/media/new-installation-upgrade-method-using-backup-and-restore-for-attached-storage.png "使用連接儲存裝置備份及還原的新安裝升級方法")  
  
-   **SAN 存放裝置環境：** 如果您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 環境使用 SAN 存放裝置，下圖及圖內的連結會引導您完成 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 之新安裝升級所需的步驟。  
  
     ![使用 SAN 存放裝置中斷連結和連結的新安裝升級方法](../../database-engine/install-windows/media/new-installation-upgrade-method-using-detach-and-attach-for-san-storage.png "使用 SAN 存放裝置中斷連結和連結的新安裝升級方法")  
  
## <a name="rolling-upgrade"></a>輪流升級  
 當 SQL Server 解決方案環境涉及必須以特定順序升級的多個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，便需要輪流升級，盡可能增加執行時間、減少風險並保留功能。 輪流升級基本上是有特定順序的多個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體升級，會在現有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體執行就地升級，或執行新安裝升級來加速升級專案中硬體和/或作業系統的升級。 在許多案例中，您都需要使用輪流升級方法。 下列文章記載這些案例：  
  
-   Always On 可用性群組：如需了解在此環境中執行輪流升級的詳細步驟，請參閱[升級 Always On 可用性群組複本執行個體](../../database-engine/availability-groups/windows/upgrading-always-on-availability-group-replica-instances.md)。    
-   容錯移轉叢集執行個體：如需了解在此環境中執行輪流升級的詳細步驟，請參閱[升級 SQL Server 容錯移轉叢集執行個體](../../sql-server/failover-clusters/windows/upgrade-a-sql-server-failover-cluster-instance.md)    
-   鏡像執行個體：如需了解在此環境中執行輪流升級的詳細步驟，請參閱[升級鏡像執行個體](../../database-engine/database-mirroring/upgrading-mirrored-instances.md)。    
-   記錄傳送執行個體：如需了解在此環境中執行輪流升級的詳細步驟，請參閱[升級 SQL Server 的記錄傳送 &#40;Transact-SQL&#41;](../../database-engine/log-shipping/upgrading-log-shipping-to-sql-server-2016-transact-sql.md)。    
-   複寫環境：如需了解在此環境中執行輪流升級的詳細步驟，請參閱[升級複寫的資料庫](../../database-engine/install-windows/upgrade-replicated-databases.md)。  
-   SQL Server Reporting Services 向外延展環境：如需了解在此環境中執行輪流升級的詳細步驟，請參閱[升級和移轉 Reporting Services](../../reporting-services/install-windows/upgrade-and-migrate-reporting-services.md)。  
  
## <a name="next-steps"></a>後續步驟
 [計劃和測試資料庫引擎升級計劃](../../database-engine/install-windows/plan-and-test-the-database-engine-upgrade-plan.md)   
 [完成資料庫引擎升級](../../database-engine/install-windows/complete-the-database-engine-upgrade.md)