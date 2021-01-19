---
title: 相容性憑證 | Microsoft Docs
description: 相容性憑證會消除應用程式相容性的風險，讓您得以升級在內部部署和雲端中的 SQL Server 資料庫。
ms.custom: ''
ms.date: 08/26/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
helpviewer_keywords:
- compatibility [SQL Server], databases
- compatibility levels [SQL Server], after upgrade
- Database Engine [SQL Server], upgrading
- Databases [SQL Server], upgrading
- Database Engine [SQL Server], upgrading
- compatibility [SQL Server], certification
- compatibility level [SQL Server], upgrades
ms.assetid: 3c036813-36cf-4415-a0c9-248d0a433856
author: pmasl
ms.author: pelopes
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 14b78d968fcb3350c488c9353df6017c4616f632
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170900"
---
# <a name="compatibility-certification"></a>相容性認證

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

相容性憑證可讓企業將內部、雲端上和邊緣上的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫升級並現代化，以排除應用程式相容性的風險。 

相同的 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 可提供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] (包括受控執行個體)。 此共用的 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 表示使用者資料庫可以在內部部署 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 與 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 之間順暢移動，而在資料庫中以 [!INCLUDE[tsql](../../includes/tsql-md.md)] 執行的應用程式程式碼將繼續在來源系統中運作。

針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的每個新版本，預設相容性層級設為 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的版本。 但是會保留舊版的相容性層級，以保持現有應用程式的相容性。 您可以在[這裡](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#supported-dbcompats)查看此相容性矩陣。
因此，經認證使用指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本的應用程式，**實際上已經認證使用該版本的預設相容性層級**。

例如，資料庫相容性層級 130 是 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 中的預設值。 由於相容性層級會強制執行特定的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 功能性和查詢最佳化行為，因此 **經認證在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 上運作的資料庫已在資料庫相容性層級 130 上以隱含方式認證**。 只要資料庫相容性層級保留為 130，則此資料庫可以在較新版本 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (例如 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]) 和 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 中依再現況工作。 

這是 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 持續整合作業模型的基本原則。 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 會持續在 Azure 中改善並升級，但因為現有資料庫會保留其目前的相容性層級，因此即使在升級至基礎 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 之後，仍會繼續以原先設計的方式運作。 

這也是 SharePoint Server 2016 和 SharePoint Server 2019 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 與 [!INCLUDE[ssSDSMIfull](../../includes/sssdsmifull-md.md)]上的認證方式，其可供針對這些 SharePoint 伺服器版本使用支援的資料庫相容性層級來部署任何 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]。 如需詳細資訊，請參閱 [SharePoint Server 2016 的硬體和軟體需求](/sharepoint/install/hardware-and-software-requirements#minimum-requirements-for-a-database-server-in-a-farm)以及 [SharePoint Server 2019 的硬體和軟體需求](/sharepoint/install/hardware-and-software-requirements-2019#minimum-requirements-for-a-database-server-in-a-farm)。

## <a name="managing-upgrade-risk-with-compatibility-certification"></a>使用相容性憑證管理升級風險
使用相容性憑證是將資料庫現代化的重要方法。 藉由以相容性層級為基礎來進行認證，開發人員可以設定在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 上支援應用程式的技術需求，但會將應用程式生命週期從資料庫平台生命週期分離。 這可讓公司根據生命週期原則來保留升級 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 的需求、利用不相依於程式碼的新延展性和效能增強功能，並透過升級來連線應用程式以 **維護其功能狀態**。

任何升級的主要風險因素為可能會對功能和效能造成負面影響。 相容性憑證可讓您安心管理這些升級風險：

-  在與 [!INCLUDE[tsql](../../includes/tsql-md.md)] 行為相關的情況下，任何變更都表示需要重新認證應用程式以確保正確。 不過，[資料庫相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)設定提供與舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的回溯相容性，其僅適用於指定的資料庫而非整部伺服器。 保持資料庫相容性層級的一致性，可確保現有應用程式查詢會在 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 升級前後繼續顯示相同的行為。 如需 [!INCLUDE[tsql](../../includes/tsql-md.md)] 行為和相容性層級的詳細資訊，請參閱[使用相容性層級以提供回溯相容性](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#backwardCompat)。

-  在與效能相關的情況下，由於查詢最佳化工具的改善會隨著每個版本引進，所以可能會在不同 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 版本之間遇到查詢計畫的差異。 當有某些變更可能對指定的查詢或工作負載造成不利的可能性時，升級範圍中的查詢計劃差異通常會成為風險。 反過來說，這項風險也是重新進行憑證的動機，但可能會延遲升級，並造成生命週期和支援方面的挑戰。 
   之所以將「查詢最佳化工具」改善限制在新版本預設相容性層級 (換句話說，即可供任何新版本使用的最高相容性層級)，是為了降低升級風險。 相容性憑證包含 **查詢計劃圖形保護**：在升級 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 後立即將資料庫相容性層級維持原狀的概念，可解釋為在新版本中使用與升級前相同的查詢最佳化模型，且查詢計劃圖形不應變更。 
   如需詳細資訊，請參閱本文的[為何使用查詢計劃圖形？](#queryplan_shape)一節。
   
如需有關相容性層級的詳細資訊，請參閱[使用相容性層級以提供回溯相容性](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#backwardCompat)。
   
> [!IMPORTANT]
> 針對已通過指定相容性層級認證的現有應用程式，請升級 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]，並 **維持** 先前的資料庫相容性層級。 在此案例中，不需要重新認證應用程式。 如需詳細資訊，請參閱本文稍後的[相容性層級和資料庫引擎升級](#compatibility-levels-and-database-engine-upgrades)。
>
> 若要進行新的開發工作，或現有的應用程式需要使用新功能 (例如[智慧型查詢處理](../../relational-databases/performance/intelligent-query-processing.md)以及某些新的 [!INCLUDE[tsql](../../includes/tsql-md.md)])，請規劃將資料庫相容性層級升級至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中可用的最新層級，並重新認證應用程式以便在該相容性層級下運作。 如需有關升級資料庫相容性層級的詳細資訊，請參閱[升級資料庫相容性層級的最佳做法](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#best-practices-for-upgrading-database-compatibility-level)。
   
### <a name="why-query-plan-shape"></a><a name="queryplan_shape"></a> 為何使用查詢計劃圖形？      
查詢計劃圖形係指組成查詢計劃之各種運算子的視覺表示方式。 這包括搜尋、掃描、聯結和排序等運算子，以及它們之間表示資料流程與必須執行以產生所需結果集之作業順序的連接。 查詢計畫圖形由查詢最佳化工具所決定。

若要在升級期間維持穩定的查詢效能，其中一個基本目標就是確保使用相同的查詢計劃圖形。 達成此目標的做法是，不要在升級後立即變更資料庫相容性層級，即使基礎 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 具有不同的版本也一樣。 如果查詢執行生態系統中沒有任何其他變更 (例如可用資源方面或基礎資料中之資料散發方面的重大變更)，則查詢效能應該維持不變。 

不過，保留查詢計劃圖形並非唯一在升級後可能影響效能的因素。 如果您將資料庫移至較新的 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 並也進行環境變更，則即使查詢計劃在不同版本間保留相同圖形，也可能引進將對查詢效能有立即影響的因素。 這些環境變更可能包括新 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 具有較多或較少可用的記憶體和 CPU 資源、伺服器或資料庫設定選項變更，或是影響查詢計劃建立方式的資料散發變更。 這就是為什麼必須瞭解維持資料庫相容性層級可防止查詢計劃 **圖形** 變更，但無法防護影響查詢效能的其他環境層面，其中有些是使用者起始的變更。

如需詳細資訊，請參閱[查詢處理架構指南](../../relational-databases/query-processing-architecture-guide.md#optimizing-select-statements)。
   
## <a name="compatibility-certification-benefits"></a>相容性憑證的優點
使用相容性方法 (而不是命名版本方法) 的資料庫憑證，有幾個立即優點：

-  **將應用程式憑證從平台分離**。 由於其共用 [!INCLUDE[ssde_md](../../includes/ssde_md.md)]，因此對於只需要執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢的應用程式，不需要維護 Azure 和內部部署的個別憑證流程。
-  由於可在資料庫平台現代化的期間將應用程式從資料庫平台層升級週期分開，以減少中斷並改善變更管理，因此可以 **降低升級風險**。
-  **在不變更程式碼的情況下升級**。 藉由保留與來源系統相同的相容性層級，在不需變更程式碼的情況下即可升級至新版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)]，且不需立即重新認證，直到應用程式需要利用僅供較高資料庫相容性層級使用的增強功能時，才需重新認證。
- 使用不受資料庫相容性層級所管制的增強功能，不需要變更應用程式即可 **改善管理能力和擴充性**。 例如，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中這些包含： 
  - 豐富的監視和疑難排解改善，以及新的[系統動態管理檢視](../../relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)、[擴充事件](../../relational-databases/extended-events/extended-events.md)與[自動調整](../../relational-databases/automatic-tuning/automatic-tuning.md)。 
  - 改善的可擴縮性，例如[自動軟體 NUMA](../../database-engine/configure-windows/soft-numa-sql-server.md#automatic-soft-numa)、[加速資料庫復原](../../relational-databases/accelerated-database-recovery-concepts.md)，或[記憶體最佳化的 tempdb 中繼資料](../../relational-databases/in-memory-database.md#memory-optimized-tempdb-metadata)。

新資料庫仍會設定為 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 版本的預設相容性層級。 但是，當資料庫從任何舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 移至新版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 時，資料庫會保留其現有的相容性層級。 

> [!IMPORTANT]
> 將資料庫移至新版本 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 之前，請先確認資料庫相容性層級是否仍受支援。 您可以在[這裡](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#arguments)看到資料庫相容性層級支援矩陣。 
>
> 升級相容性層級低於所允許層級的資料庫 (例如 90，這是 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 中的預設值) 時，會將資料庫設定為允許的最低相容性層級 (100)。
>
> 若要判斷目前的相容性層級，請查詢 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 的 **compatibility_level** 資料行。

## <a name="compatibility-levels-and-database-engine-upgrades"></a>相容性層級和資料庫引擎升級
若要將 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 升級至最新版本，同時維護升級前已存在的資料庫相容性層級及其可支援性狀態，則建議執行 **資料庫** (可程式性物件，例如預存程序、函數、觸發程序等) 及 **應用程式** (使用擷取應用程式所傳送動態程式碼的工作負載追蹤) 中應用程式程式碼的 **靜態功能介面區驗證**。

您可使用 [Microsoft Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 工具 (DMA) 輕鬆完成這項作業。 在 DMA 工具輸出中，由於沒有關於遺失或不相容功能的錯誤，因此可防止應用程式在新的目標版本上出現任何功能迴歸的情況。 如果需要變更以確保資料庫可在新版本中運作，則 DMA 將可供找出需要變更的位置，以及可用的因應措施。 如需詳細資訊，請參閱 [Data Migration Assistant 總覽](../../dma/dma-overview.md) (機器翻譯)。   

> [!TIP]
> 將資料庫從舊版本 (例如 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 或 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]) 移至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssazure_md](../../includes/ssazure_md.md)] 的新版本時，這項功能驗證特別重要，因為應用程式程式碼可能使用了已中止的 [!INCLUDE[tsql](../../includes/tsql-md.md)]，而不受資料庫相容性層級的保護。 但是，從較新版本 (例如 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]) 移至 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 或 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 時，則無需擔心 [!INCLUDE[tsql](../../includes/tsql-md.md)] 已中止的情況發生。 如需 [!INCLUDE[tsql](../../includes/tsql-md.md)] 已中止的詳細資訊，請參閱[使用相容性層級以提供回溯相容性](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#backwardCompat)。

> [!NOTE]
> DMA 支援資料庫相容性層級 100 (含) 以上。 已排除 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 作為來源版本。   

> [!IMPORTANT]
> [!INCLUDE[msCoName](../../includes/msconame-md.md)] 建議執行一些基本測試，以驗證升級是否成功，同時維護先前的資料庫相容性層級。 您應該決定基本測試對您自己的應用程式和情節所代表的意義。   

> [!IMPORTANT]
> [!INCLUDE[msCoName](../../includes/msconame-md.md)] 會在下列情況下提供查詢計畫圖形保護：
>
> - 新版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (目標) 執行所在的硬體，相當於舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (來源) 執行所在的硬體。
> - 目標 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和來源 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上使用相同的[受支援資料庫相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#supported-dbcompats)。
> - 在目標 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 與來源 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上都會使用 **相同** 的資料庫與工作負載。 
>
> 上述情況中所發生的任何查詢計劃圖形迴歸 (相較於來源 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]) 都會予以解決。 如果發生這種情況，請連絡 Microsoft 客戶支援。
  
## <a name="see-also"></a>另請參閱 
[更改資料庫相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)       
[檢視或變更資料庫的相容性層級](../../relational-databases/databases/view-or-change-the-compatibility-level-of-a-database.md)       
[升級資料庫相容性層級的最佳做法](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#best-practices-for-upgrading-database-compatibility-level)