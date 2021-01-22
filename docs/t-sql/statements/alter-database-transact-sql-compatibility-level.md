---
description: ALTER DATABASE 相容性層級 (Transact-SQL)
title: ALTER DATABASE 相容性層級 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/22/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- COMPATIBILITY_LEVEL_TSQL
- COMPATIBILITY_LEVEL
dev_langs:
- TSQL
helpviewer_keywords:
- 80 compatibility level
- ALTER DATABASE statement, compatibility levels
- 90 compatibility level
- compatibility levels [SQL Server]
- 100 compatibility level
- db compatibility level
- db compat level
ms.assetid: ca5fd220-d5ea-4182-8950-55d4101a86f6
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: d16763f2bd009b411952c20cfb1115cf1c977d85
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689187"
---
# <a name="alter-database-transact-sql-compatibility-level"></a>ALTER DATABASE (Transact-SQL) 相容性層級

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

設定 [!INCLUDE[tsql](../../includes/tsql-md.md)] 及查詢處理行為，使其與指定版本的 SQL 引擎相容。 如需其他 ALTER DATABASE 選項，請參閱 [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md)。  

如需語法慣例的詳細資訊，請參閱 [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

## <a name="syntax"></a>語法

```syntaxsql
ALTER DATABASE database_name
SET COMPATIBILITY_LEVEL = { 150 | 140 | 130 | 120 | 110 | 100 | 90 }
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*database_name*：這是要修改的資料庫名稱。

COMPATIBILITY_LEVEL { 150 \| 140 \| 130 \| 120 \| 110 \| 100 \| 90 \| 80 } 是資料庫要設為相容的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本。 可以設定下列相容性層級值 (並非所有版本都支援上述所列的所有相容性層級)：

<a name="supported-dbcompats"></a>

|Products|資料庫引擎版本|預設相容性層級指定|支援的相容性層級值|
|-------------|-----------------------------|-------------------------------------|------------------------------------------|
|[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)]|15|150|150, 140, 130, 120, 110, 100|
|[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]|14|140|140、130、120、110、100|
|[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|12|150|150, 140, 130, 120, 110, 100|
|[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 受控執行個體|12|150|150, 140, 130, 120, 110, 100|
|[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]|13|130|130、120、110、100|
|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]|12|120|120、110、100|
|[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]|11|110|110、100、90|
|[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]|10.5|100|100、90、80|
|[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]|10|100|100、90、80|
|[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]|9|90|90、80|
|[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)]|8|80|80|

> [!IMPORTANT]
> SQL Server 和 Azure SQL Database 的資料庫引擎版本號碼無法彼此相互比較，且更像是這些個別產品的內部組建編號。 Azure SQL Database 資料庫引擎是以和 SQL Server 資料庫引擎相同的程式碼基底作為基礎。 最重要的是，Azure SQL Database 資料庫引擎一律具有最新的 SQL 資料庫引擎位元。 Azure SQL Database 版本 12 比 SQL Server 版本 15 更新。

## <a name="remarks"></a>備註
針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的所有安裝，預設相容性層級與 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的版本相關聯。 新資料庫會設定為這個層級，除非 **model** 資料庫具有更低的相容性層級。 針對從任何舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 連接或還原的資料庫，資料庫會保留其現有的相容性層級 (如果其至少為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體所允許最低層級)。 移動相容性層級低於 [!INCLUDE[ssde_md](../../includes/ssde_md.md)] 所允許層級的資料庫時，會自動將資料庫設定為允許的最低相容性層級。 這同樣適用於系統和使用者資料庫。

附加或還原資料庫以及就地升級之後，[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 預期會有下列行為：

- 如果使用者資料庫的相容性層級在升級前為 100 或更高層級，則在升級後仍會保持相同。
- 如果使用者資料庫在升級前的相容性層級為 90，則在升級後的資料庫中，相容性層級會設定為 100，這是 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 支援的最低相容性層級)。
- tempdb、模型、msdb 和資源資料庫的相容性層級，會針對指定的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 版本設定為預設相容性層級。 
- master 系統資料庫會繼續保有升級前的相容性層級。 這將不會影響使用者資料庫行為。 

針對預先存在且在較低相容性層級執行的資料庫，只要應用程式不需利用只在較高資料庫相容性層級才提供的增強功能，其就是維護先前資料庫相容性層級的有效方法。 若要進行新的開發工作，或是現有的應用程式需要使用新功能 (例如[智慧型查詢處理](../../relational-databases/performance/intelligent-query-processing.md)以及某些新的 [!INCLUDE[tsql](../../includes/tsql-md.md)])，請規劃將資料庫相容性層級升級至可用的最新層級。 如需詳細資訊，請參閱[相容性層級和資料庫引擎升級](../../database-engine/install-windows/compatibility-certification.md#compatibility-levels-and-database-engine-upgrades)。     

> [!NOTE]
> 如果沒有使用者物件與相依性，通常就能安全地升級至預設的相容性層級。 如需詳細資訊，請參閱[建議 - master 資料庫](../../relational-databases/databases/master-database.md#recommendations)。

使用 `ALTER DATABASE` 變更資料庫的相容性層級。 資料庫的新相容性層級設定會在兩個情況下生效：發出 `USE <database>` 命令時，或使用該資料庫作為預設資料庫內容來處理新登入時。
若要檢視資料庫目前的相容性層級，請查詢 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中的 `compatibility_level` 資料行。

> [!NOTE]
> 舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所建立並升級至 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] RTM 或 Service Pack 1 的[散發資料庫](../../relational-databases/replication/distribution-database.md)具有相容性層級 90，其他資料庫則不予支援。 這不會影響複寫功能。 升級至更新版本的 Service Pack 和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本會增加散發資料庫的相容性層級，以符合 **master** 資料庫的相容性層級。

> [!NOTE]
> 從 **2019 年 11 月** 開始，在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，新建資料庫的預設相容性層級是 150。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 不會更新現有資料庫的資料庫相容性層級。 這是由客戶自己決定。        
> [!INCLUDE[msCoName](../../includes/msconame-md.md)] 強烈建議客戶規劃升級至最新的相容性層級，以利用最新的查詢最佳化改善項目。        

若要針對整個資料庫利用相容性層級 120 或更高版本，但同時又要加入對應至資料庫相容性層級 110 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 的 [**基數估計**](../../relational-databases/performance/cardinality-estimation-sql-server.md)模型，請參閱 [ALTER DATABASE SCOPED CONFIGURATION](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)，特別是其關鍵字 `LEGACY_CARDINALITY_ESTIMATION = ON`。

如需如何在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 上兩個不同相容性層級之間評估您最重要查詢的效能差異詳細資料，請參閱 [Improved Query Performance with Compatibility Level 130 in Azure SQL Database](/archive/blogs/sqlserverstorageengine/improved-query-performance-with-compatibility-level-130-in-azure-sql-database) (在 Azure SQL Database 中使用相容性層級 130 改善的查詢效能)。 請注意，本文是指相容性層級 130 和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，但 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 可使用相同的方法升級至 140 或更高層級。

請執行以下查詢來判斷您所連線至的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 版本。

```sql
SELECT SERVERPROPERTY('ProductVersion');
```

> [!NOTE]
> [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)].上並未支援依相容性層級而改變的所有功能。

若要判斷目前的相容性層級，請查詢 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 的 **compatibility_level** 資料行。

```sql
SELECT name, compatibility_level FROM sys.databases;
```

## <a name="compatibility-levels-and-database-engine-upgrades"></a>相容性層級和資料庫引擎升級
資料庫相容性層級是協助資料庫現代化的重要工具，它允許升級 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]，同時透過維護升級前的相同資料庫相容性層級，來讓連線的應用程式保持在運作狀態。 這表示可以從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的舊版 (例如 [!INCLUDE[ssKatmai](../../includes/ssKatmai-md.md)]) 升級至 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 或 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] (包括受控執行個體)，而不需要變更任何應用程式 (資料庫連線除外)。 如需詳細資訊，請參閱[相容性憑證](../../database-engine/install-windows/compatibility-certification.md)。

只要應用程式不需要使用僅限較高資料庫相容性層級的增強功能，即為升級 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 並維護先前資料庫相容性層級的有效方法。 如需使用相容性層級來提供回溯相容性的詳細資訊，請參閱[相容性憑證](../../database-engine/install-windows/compatibility-certification.md)。

## <a name="best-practices-for-upgrading-database-compatibility-level"></a>升級資料庫相容性層級的最佳做法
如需升級相容性層級的建議工作流程，請參閱[變更資料庫相容性模式並使用查詢存放區](../../database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store.md)。 此外，如需升級資料庫相容性層級的協助體驗，請參閱[使用 Query Tuning Assistant 升級資料庫](../../relational-databases/performance/upgrade-dbcompat-using-qta.md)。

## <a name="compatibility-levels-and-stored-procedures"></a>相容性層級和預存程序
當執行預存程序時，它會使用定義所在之資料庫的目前相容性層級。 當資料庫的相容性設定改變時，也會同時自動重新編譯它的所有預存程序。

## <a name="using-compatibility-level-for-backward-compatibility"></a><a name="backwardCompat"></a> 使用相容性層級來提供回溯相容性
[資料庫相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)設定提供與 [!INCLUDE[tsql](../../includes/tsql-md.md)] 相關之舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的回溯相容性，以及僅針對指定資料庫的查詢最佳化行為，而不是針對整部伺服器。  

從相容性模式 130 開始，任何會影響修正和功能的新查詢計劃只會刻意新增至新的相容性層級。 這種作法是為了將升級期間因新的查詢最佳化行為而可能引發的查詢計劃變更，所導致效能降低而產生的風險降到最低。      

從應用程式的觀點來看，請使用較低相容性層級作為更安全的移轉路徑，協助您解決相關相容性層級設定所控制行為的版本差異。 目標仍然應該在某個時間點升級為最新的相容性層級，以透過受控方式繼承一些新功能，例如[智慧型查詢處理](../../relational-databases/performance/intelligent-query-processing.md)。 

如需詳細資料，包括升級資料庫相容性層級的建議工作流程，請參閱[升級資料庫相容性層級的最佳做法](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#best-practices-for-upgrading-database-compatibility-level)。

> [!IMPORTANT]
> 在指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中導入的 **已停用** 功能 **不會** 受到相容性層級保護。 這是指 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 中已移除的功能。
> 例如，`FASTFIRSTROW` 提示已在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 中停用，並以 `OPTION (FAST n )` 提示取代。 將資料庫相容性層級設定為 110 不會還原已停用的提示。  
>  
> 如需已中止功能的詳細資訊，請參閱 [SQL Server 中已中止的資料庫引擎功能](../../database-engine/discontinued-database-engine-functionality-in-sql-server.md)，以及 [SQL Server 2014 中已中止的資料庫引擎功能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016?preserve-view=true&view=sql-server-2014)。

> [!IMPORTANT]
> 指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中導入的 **重大變更** **可能不會** 受到相容性層級保護。 這是指 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 版本之間的行為變更。 [!INCLUDE[tsql](../../includes/tsql-md.md)] 行為通常會受到相容性層級保護。 但是，已變更或已移除的系統物件 **不會** 受到相容性層級保護。
>
> 一個受相容性層級 **保護** 的重大變更範例為從日期時間轉換成日期時間 2 資料類型的隱含轉換。 在資料庫相容性層級 130 之下，這些會顯示藉由考量小數部分的毫秒而改善的精確度，會導致不同的轉換值。 若要還原先前的轉換行為，請將資料庫相容性層級設定為 120 或更低。
>
> 相容性層級 **未保護** 的重大變更範例為：
>
> - 在系統物件中變更資料欄名稱。 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 中，sys.dm_os_sys_info 中的資料行 *single_pages_kb* 已重新命名為 *pages_kb*。 無論相容性層級為何，查詢 `SELECT single_pages_kb FROM sys.dm_os_sys_info` 都會產生錯誤 207 (無效的資料行名稱)。
> - 移除的系統物件。 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 中，已移除 `sp_dboption`。 無論相容性層級為何，陳述式 `EXEC sp_dboption 'AdventureWorks2016', 'autoshrink', 'FALSE';` 都會產生錯誤 2812 (找不到預存程序 'sp_dboption')。
>
> 如需中斷性變更的詳細資訊，請參閱 [SQL Server 2019 資料庫引擎功能的中斷性變更](../../database-engine/breaking-changes-to-database-engine-features-in-sql-server-version-15.md)、[SQL Server 2017 資料庫引擎功能的中斷性變更](../../database-engine/breaking-changes-to-database-engine-features-in-sql-server-2017.md)、[SQL Server 2016 資料庫引擎功能的中斷性變更](../../database-engine/breaking-changes-to-database-engine-features-in-sql-server-2016.md)，以及 [SQL Server 2014 資料庫引擎功能的中斷性變更](/previous-versions/sql/2014/database-engine/breaking-changes-to-database-engine-features-in-sql-server-2016?preserve-view=true&view=sql-server-2014)。

## <a name="differences-between-compatibility-levels"></a>相容性層級之間的差異
針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的所有安裝，預設相容性層級與 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的版本相關聯，如[此表](#supported-dbcompats)所示。 針對新的開發工作，請務必計畫在最新的資料庫相容性層級上認證應用程式。

除非新 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語法可以藉由製造與使用者 [!INCLUDE[tsql](../../includes/tsql-md.md)] 程式碼的衝突來中斷現有應用程式，否則不受資料庫相容性層級管制。 這些例外狀況會記載於本文的後續章節中，其概述特定相容性層級之間的差異。

資料庫相容性層級也會提供與舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的回溯相容性，因為從任何舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 附加或還原的資料庫會保留其現有相容性層級 (如果相同或高於所允許最低相容性層級)。 這會在本文的[使用相容性層級進行回溯相容性](#backwardCompat)一節中討論。

從資料庫相容性層級 130 開始，任何會影響查詢計劃的新修正和功能，只會新增至可用的最新相容性層級，也稱為預設相容性層級。 這種作法是為了將升級期間因新的查詢最佳化行為而可能引發的查詢計劃變更，所導致效能降低而產生的風險降到最低。 

僅新增至新版本 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的預設相容性層級上，影響計劃的基本變更是：

1.  **針對舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (追蹤旗標 4199) 所發行的查詢最佳化工具修正，會在較新版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的預設相容性層級中自動啟用**。 **適用範圍：** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。

    例如，當 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 發行時，針對舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (以及各自的相容性層級 100 到 120) 發行的所有查詢最佳化工具修正，都會變成針對使用 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 預設相容性層級 (130) 的資料庫自動啟用。 僅 RTM 後查詢最佳化工具修正程式需要明確啟用。
    
    > [!NOTE]
    > 若要啟用查詢最佳化工具修正程式，您可以使用下列方法：    
    >
    > - 在伺服器層級，使用[追蹤旗標 4199](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md#4199)。
    > - 在資料庫層級，使用 [ALTER 資料庫範圍設定 (Transact-SQL)](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md) 中的 [`QUERY_OPTIMIZER_HOTFIXES`] 選項。
    > - 在查詢層級，使用 `USE HINT 'ENABLE_QUERY_OPTIMIZER_HOTFIXES'`[查詢提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint)。
    
    之後，當 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 發行時，在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] RTM 之後所發行的所有查詢最佳化工具修正程式都會針對使用 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 預設相容性層級 (140) 的資料庫自動啟用。 這是包含所有舊版修正的累計行為。 同樣地，僅 RTM 後查詢最佳化工具修正程式需要明確啟用。  
    
    下表摘要說明這個行為：
    
    |資料庫引擎 (DE) 版本|資料庫相容性層級|TF 4199|來自所有先前資料庫相容性層級的的 QO 變更|RTM 後 DE 版本的 QO 變更|
    |----------|----------|---|------------|--------|
    |13 ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)])|100 至 120<br /><br /><br />130|關閉<br />另一<br /><br />關閉<br />另一|**Disabled**<br />啟用<br /><br />**已啟用**<br />啟用|已停用<br />啟用<br /><br />已停用<br />啟用|
    |14 ([!INCLUDE[ssSQL17](../../includes/sssql17-md.md)])|100 至 120<br /><br /><br />130<br /><br /><br />140|關閉<br />另一<br /><br />關閉<br />另一<br /><br />關閉<br />另一|**Disabled**<br />啟用<br /><br />**已啟用**<br />啟用<br /><br />**已啟用**<br />啟用|已停用<br />啟用<br /><br />已停用<br />啟用<br /><br />已停用<br />啟用|
    |15 ([!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)]) 和 12 ([!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)])|100 至 120<br /><br /><br />130 至 140<br /><br /><br />150|關閉<br />另一<br /><br />關閉<br />另一<br /><br />關閉<br />另一|**Disabled**<br />啟用<br /><br />**已啟用**<br />啟用<br /><br />**已啟用**<br />啟用|已停用<br />啟用<br /><br />已停用<br />啟用<br /><br />已停用<br />啟用|
    
    > [!IMPORTANT]
    > 追蹤旗標 4199 不會保護處理錯誤結果或存取違規錯誤的「查詢最佳化工具」修正。 那些修正不會被視為選擇性。
 
2.  **[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 上發行的 [基數估計工具](../../relational-databases/performance/cardinality-estimation-sql-server.md)的變更，僅在新版本[!INCLUDE[ssDE](../../includes/ssde-md.md)]的預設相容性層級中啟用**，但無法在先前的相容性層級上使用。 

    例如，當 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 發行時，基數估計工具的變更僅適用於使用 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 預設相容性層級 (130) 的資料庫。 先前的相容性層級會保留 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 之前可用的基數估計行為。 
    
    之後，當 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 發行時，基數估計程序的較新變更僅適用於使用 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 預設相容性層級 (140) 的資料庫。 資料庫相容性層級 130 保留 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 基數估計行為。
    
    下表摘要說明這個行為：
    
    |資料庫引擎版本|資料庫相容性層級|新版本 CE 變更|
    |----------|--------|-------------|
    |13 ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)])|< 130<br />130|已停用<br />啟用|
    |14 ([!INCLUDE[ssSQL17](../../includes/sssql17-md.md)])<sup>1</sup>|< 140<br />140|已停用<br />啟用|
    |15 ([!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)])<sup>1</sup>|< 150<br />150|已停用<br />啟用|
    
    <sup>1</sup> 也適用於 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。
    
> [!IMPORTANT]
> 特定相容性層級之間的其他差異，可在本文的後續章節中取得。

## <a name="differences-between-compatibility-level-140-and-level-150"></a>相容性層級 140 和 150 之間的差異
本節描述相容性層級 150 所導入的新行為。

|相容性層級設定為 140 或更低|相容性層級設定為 150|
|--------------------------------------------------|-----------------------------------------|
|關聯式資料倉儲和分析工作負載可能無法利用資料行存放區索引，因為 OLTP 額外負荷、缺少廠商支援或其他限制。  若沒有資料行存放區索引，這些工作負載就無法從批次執行模式中獲益。|分析工作負載現在可使用批次執行模式，而不需要資料行存放區索引。 如需詳細資訊，請參閱[資料列存放區上的批次模式](../../relational-databases/performance/intelligent-query-processing.md#batch-mode-on-rowstore)。|
|要求不足的記憶體授權大小導致溢出到磁碟的資料列模式查詢，可能會在連續執行時繼續發生問題。|要求不足的記憶體授權大小導致溢出到磁碟的資料列模式查詢，可能已改進在連續執行時的效能。 如需詳細資訊，請參閱[資料列模式記憶體授與回饋](../../relational-databases/performance/intelligent-query-processing.md#row-mode-memory-grant-feedback)。|
|要求過多的記憶體授權大小導致發生並行問題的資料列模式查詢，可能會在連續執行時繼續發生問題。|要求過多的記憶體授權大小導致發生並行問題的資料列模式查詢，可能已改進在連續執行時的並行。 如需詳細資訊，請參閱[資料列模式記憶體授與回饋](../../relational-databases/performance/intelligent-query-processing.md#row-mode-memory-grant-feedback)。|
|參考 T-SQL 純量 UDF 的查詢會使用反覆引動、缺少成本，以及強制序列執行。 |T-SQL 純量會轉換成「內嵌」在呼叫查詢中的對等關聯運算式，而這通常可讓效能大幅提升。 如需詳細資訊，請參閱 [T-SQL 純量內嵌](../../relational-databases/performance/intelligent-query-processing.md#scalar-udf-inlining)。|
|資料表變數針對基數估計值使用固定猜測。  如果實際的資料列數目遠高於猜測的值，下游作業的效能可能會受到負面影響。 |新方案會使用在第一次編譯時遇到的資料表值函式實際基數，而不是定點猜測。 如需詳細資訊，請參閱[資料表變數延遲編譯](../../relational-databases/performance/intelligent-query-processing.md#table-variable-deferred-compilation)。|

如需資料庫相容性層級 150 所提供的查詢處理功能詳細資訊，請參閱 [SQL Server 2019 中的新功能](../../sql-server/what-s-new-in-sql-server-ver15.md)及 [SQL 資料庫中的智慧查詢處理](../../relational-databases/performance/intelligent-query-processing.md)。

## <a name="differences-between-compatibility-level-130-and-level-140"></a>相容性層級 130 和 140 之間的差異

本節描述相容性層級 140 所導入的新行為。

|相容性層級設定為 130 或更低|相容性層級設定為 140|
|--------------------------------------------------|-----------------------------------------|
|參考多重陳述式資料表值函式之陳述式的基數估計會使用固定的資料列猜測。|參考多重陳述式資料表值函式之合格陳述式的基數估計會使用函式輸出的實際基數。 這是透過針對多重陳述式資料表值函式使用 **交錯執行** 來啟用。|
|要求不足的記憶體授權大小導致溢出到磁碟的批次模式查詢，可能會在連續執行時繼續發生問題。|要求不足的記憶體授權大小導致溢出到磁碟的批次模式查詢，可能已改進在連續執行時的效能。 這是透過 **批次模式記憶體授與意見反應** 所啟用，如果批次模式運算子已發生溢出，它將會更新已快取計畫的記憶體授權大小。 |
|要求過多的記憶體授權大小導致發生並行問題的批次模式查詢，可能會在連續執行時繼續發生問題。|要求過多的記憶體授權大小導致發生並行問題的批次模式查詢，可能已改進在連續執行時的並行。 這是透過 **批次模式記憶體授與意見反應** 所啟用，如果原本要求過量，它將會更新已快取計畫的記憶體授權大小。|
|包含聯結運算子的批次模式查詢適合用於三個實體聯結演算法，包括巢狀迴圈、雜湊聯結，以及合併聯結。 如果聯結輸入的基數估計不正確，可能會選取不適當的聯結演算法。 如果發生此問題，效能將會降低，且不適當的聯結演算法將會保持在使用中，直到快取的計畫重新編譯為止。|有一個額外的聯結運算子，稱為 **自適性聯結**。 如果外部組件聯結輸入的基數估計不正確，可能會選取不適當的聯結演算法。 如果發生此問題且陳述式符合自適性聯結的條件，將會動態為較小的聯結輸入使用巢狀迴圈，為較大的聯結輸入使用雜湊聯結，不需要重新編譯。 |
|參考資料行存放區索引的簡單式計畫不符合批次模式執行的條件。 |系統會捨棄參考資料行存放區索引的簡單式計畫，有利於符合批次模式執行條件的計畫。|
|`sp_execute_external_script` UDX 運算子只能在資料列模式中執行。|`sp_execute_external_script` UDX 運算子符合批次模式執行的條件。|
|多重陳述式資料表值函式 (TVF) 沒有交錯執行 |交錯執行多重陳述式 TVF 以提升計畫品質。|

SQL Server 2017 之前的 SQL Server 較早版本中，追蹤旗標 4199 之下的修正程式現在已經預設啟用。 具備相容性模式 140。 追蹤旗標 4199 將仍然適用於在 SQL Server 2017 之後發行的新查詢最佳化工具修正程式。 如需有關追蹤旗標 4199 的詳細資訊，請參閱[追蹤旗標 4199](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md#4199)。

## <a name="differences-between-compatibility-level-120-and-level-130"></a>相容性層級 120 和 130 之間的差異

本節描述相容性層級 130 所導入的新行為。

|相容性層級設定為 120 或更低|相容性層級設定為 130|
|--------------------------------------------------|-----------------------------------------|
|INSERT-SELECT 陳述式中的 INSERT 是單一執行緒。|INSERT-SELECT 陳述式中的 INSERT 是多執行緒，或可以有平行計畫。|
|針對經記憶體最佳化的資料表進行的查詢會執行單一執行緒。|針對經記憶體最佳化的資料表進行的查詢，現在可以有平行計畫。|
|已導入 SQL 2014 基數估計工具 **CardinalityEstimationModelVersion="120"**|搭配基數估計模型 130 取得進一步的基數估計 ( CE) 改進，這可從查詢計畫中看到。 **CardinalityEstimationModelVersion="130"**|
|批次模式與資料列模式會隨資料行存放區索引而改變：<br /><ul><li>在具有資料行存放區索引的資料表上執行的排序會以資料列模式執行 <li>視窗型函式彙總會以資料列模式 (例如 `LAG` 或 `LEAD`) 運作 <li>使用多個不同子句在資料行存放區資料表上進行的查詢會以資料列模式運作 <li>在 MAXDOP 1 之下執行，或以資料列模式執行的序列計畫</li></ul>| 批次模式與資料列模式會隨資料行存放區索引而改變：<br /><ul><li>在具有資料行存放區索引的表格上進行的排序現在會以批次模式運作 <li>視窗型彙總現在會以批次模式 (例如`LAG` 或 `LEAD`) 運作 <li>使用多個不同子句在資料行存放區資料表上進行的查詢會以批次模式運作 <li>在 MAXDOP 1 下執行的查詢，或以批次模式執行序列計畫</li></ul>|
|統計資料可以自動更新。 | 自動更新統計資料的邏輯在大型資料表上會更積極。 在實務上，這應該會減少客戶已經看到在查詢上發生效能問題的案例，其中的問題在於新插入的資料列會受到頻繁查詢，但統計資料卻尚未更新以包含那些值。 |
|追蹤 2371 在 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 中預設為「關閉」。 | [追蹤 2371](/archive/blogs/psssql/default-auto-statistics-update-threshold-change-for-sql-server-2016) 在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 中預設為「開啟」。 追蹤旗標 2371 會告知自動統計資料更新程式，在擁有很多資料列的資料表中，以較小但更聰明的資料列子集方式進行取樣。 <br/> <br/> 其中一項改進是在樣本中包含更多最近插入的資料列。 <br/> <br/> 另一項改進是讓查詢在更新統計資料程序執行時執行，而不是封鎖查詢。 |
|對於層級 120，統計資料會由單一執行緒程序進行取樣。|對於層級 130，統計資料則會由多執行緒程序 (平行處理序) 進行取樣。 |
|其限制為 253 個傳入外部索引鍵。| 指定資料表最多可由 10,000 個傳入外部索引鍵或類似參考進行參考。 相關限制，請參閱 [Create Foreign Key Relationships](../../relational-databases/tables/create-foreign-key-relationships.md)。 |
|允許使用已被取代的 MD2、MD4、MD5、SHA 和 SHA1 雜湊演算法。|只允許使用 SHA2_256 和 SHA2_512 雜湊演算法。|
||[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 包括在某些資料類型轉換和某些不常見作業中的改善。 如需詳細資料，請參閱[處理某些資料類型和不常見作業的 SQL Server 2016 改進 (機器翻譯)](https://support.microsoft.com/help/4010261/sql-server-2016-improvements-in-handling-some-data-types-and-uncommon)。|
|`STRING_SPLIT` 函式無法使用。|`STRING_SPLIT` 函式適用於相容性層級 130 或以上。 如果您的資料庫相容性層級低於 130，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將找不到且無法執行 `STRING_SPLIT` 函式。|

[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 之前的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 較早版本中，追蹤旗標 4199 之下的修正程式現在已經預設啟用。 具備相容性模式 130。 追蹤旗標 4199 將仍然適用於在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 之後發行的新查詢最佳化工具修正程式。 若要在 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 中使用較舊的查詢最佳化工具，您必須選取相容性層級 110。 如需有關追蹤旗標 4199 的詳細資訊，請參閱[追蹤旗標 4199](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md#4199)。

## <a name="differences-between-lower-compatibility-levels-and-level-120"></a>更低相容性層級和層級 120 之間的差異

本章節描述相容性層級 120 所導入的新行為。

|相容性層級設定為 110 或更低|相容性層級設定為 120|
|--------------------------------------------------|-----------------------------------------|
|使用舊版的查詢最佳化工具。|[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 包括可建立及最佳化查詢計劃之元件的大幅改善。 這個新的查詢最佳化工具功能取決於資料庫相容性層級 120 的使用。 新的資料庫應用程式應該使用資料庫相容性層級 120 加以開發，以便充分利用這些改良功能。 從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 移轉的應用程式應該謹慎測試，以確認良好的效能得以持續或改善。 如果效能降低，您可以將資料庫相容性層級設定為 110 或更低的數字，以便使用舊的查詢最佳化工具方法。<br /><br /> 資料庫相容性層級 120 會使用新的基數估計工具，其經過調整適合於新型資料倉儲和 OLTP 工作負載。 因效能問題而要將資料庫相容性層級設定為 110 之前，請先參閱[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)][資料庫引擎的新功能](../../sql-server/what-s-new-in-sql-server-2016.md)主題的＜查詢計劃＞一節提供的建議。|
|在低於 120 的相容性層級中，將 **日期** 值轉換成字串值時，會忽略語言設定。 請注意，此行為只針對 **日期** 類型。 請參閱下方＜範例＞一節中的範例 B。|將 **日期** 值轉換成字串值時，不會忽略語言設定。|
|`EXCEPT` 子句右邊的遞迴參考會建立無限迴圈。 下方＜範例＞一節中的範例 C 會示範此行為。|`EXCEPT` 子句中的遞迴參考會產生符合 ANSI SQL 標準的錯誤。|
|遞迴通用資料表運算式 (CTE) 允許複寫資料行名稱。|遞迴 CTE 不允許重複的資料行名稱。|
|如果觸發程序經過更改，則停用的觸發程序會再次啟用。|更改觸發程序不會變更觸發程序的狀態 (啟用或停用)。|
|OUTPUT INTO 資料表子句會忽略 `IDENTITY_INSERT SETTING = OFF` 並允許插入明確的值。|當 `IDENTITY_INSERT` 設為 OFF 時，您無法在資料表中插入識別資料行的明確的值。|
|當資料庫內含項目設定為部分時，驗證 `MERGE` 陳述式的 `OUTPUT` 子句中的 `$action` 欄位可能會傳回定序錯誤。|`MERGE` 陳述式的 `$action` 子句所傳回值的定序是資料庫定序，而不是伺服器定序，且不會傳回定序衝突錯誤。|
|`SELECT INTO` 陳述式永遠都會建立單一執行緒的插入作業。|`SELECT INTO` 陳述式可建立平行插入作業。 當插入大量資料列時，平行作業可以提升效能。|

## <a name="differences-between-lower-compatibility-levels-and-levels-100-and-110"></a>較低相容性層級與層級 100 和 110 之間的差異

本章節描述相容性層級 110 所導入的新行為。 本節也適用於 110 以上的相容性層級。

|相容性層級設定為 100 或更低|至少為 110 的相容性層級設定|
|--------------------------------------------------|--------------------------------------------------|
|Common Language Runtime (CLR) 資料庫物件是使用 CLR 4 版執行。 不過，CLR 4 版中導入的部分行為變更會加以忽略。 如需相關資訊，請參閱 [CLR 整合的新功能](../../relational-databases/clr-integration/clr-integration-what-s-new.md)。|CLR 資料庫物件是使用 CLR 4 版執行。|
|XQuery 函數 **string-length** 和 **substring** 會將每一個 Surrogate 計算為兩個字元。|XQuery 函數 **string-length** 和 **substring** 會將每一個 Surrogate 計算為一個字元。|
|可以在遞迴通用資料表運算式 (CTE) 查詢中使用 `PIVOT`。 但每個分組如有多個資料列，查詢會傳回的結果將會不正確。|不可在遞迴通用資料表運算式 (CTE) 查詢中使用 `PIVOT`。 傳回錯誤。|
|只有 RC4 演算法支援回溯相容性。 只有在資料庫相容性層級為 90 或 100 時，才能使用 RC4 或 RC4_128 加密新資料 (不建議使用)。在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 中使用 RC4 或 RC4_128 加密的資料，可以在任何相容性層級進行解密。|不可使用 RC4 或 RC4_128 加密新資料。 請改用較新的演算法，例如其中一個 AES 演算法。 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 中使用 RC4 或 RC4_128 加密的資料，可以在任何相容性層級進行解密。|
|除非用於計算資料行運算式，否則 **time** 和 **datetime2** 資料類型之 `CAST` 和 `CONVERT` 作業的預設樣式為 121。 若為計算資料行，預設樣式為 0。 當您建立計算資料行、將它們用於包含自動參數化的查詢或用於條件約束定義時，這種行為就會影響計算資料行。<br /><br /> 下方＜範例＞一節中的範例 D 會顯示樣式 0 與 121 之間的差異。 此範例不會示範上述的行為。 如需日期和時間樣式的詳細資訊，請參閱 [CAST 和 CONVERT](../../t-sql/functions/cast-and-convert-transact-sql.md)。|在相容性層級 110 底下，**time** 和 **datetime2** 資料類型之 `CAST` 和 `CONVERT` 作業的預設樣式一律為 121。 如果您的查詢仰賴舊的行為，請使用低於 110 的相容性層級，或在受影響的查詢中明確指定 0 樣式。<br /><br /> 將資料庫升級為相容性層級 110 不會變更已經儲存至磁碟的使用者資料。 您必須依適當情況手動更正這項資料。 例如，如果您使用了 `SELECT INTO`，根據包含上述計算資料行運算式的來源建立資料表，系統就會儲存資料 (使用樣式 0) 而非計算資料行定義本身。 您必須手動將這項資料更新為符合樣式 121。|
|分割區檢視所參考之 **smalldatetime** 類型的遠端資料表中的任何資料行都會對應為 **datetime**。 本機資料表中對應的資料行 (在選取清單的相同序數位置中) 必須為 **datetime** 類型。|分割區檢視所參考之 **smalldatetime** 類型的遠端資料表中的任何資料行都會對應為 **smalldatetime**。 本機資料表中對應的資料行 (在選取清單的相同序數位置中) 必須為 **smalldatetime** 類型。<br /><br /> 在升級到 110 後，分散式分割區檢視會因為資料類型不符合而失敗。 若要解決此問題，您可以將遠端資料表的資料類型變更為 **datetime** 或是將本機資料庫的相容性層級設定為 100 或更低層級。|
|`SOUNDEX` 函數會實作以下規則：<br /><br /> 1)如果大寫 H 或大寫 W 分隔擁有相同 `SOUNDEX` 代碼數字的兩個子音，則會忽略它們。<br /><br /> 2) 如果 *character_expression* 的前 2 個字元都有相同的 `SOUNDEX` 代碼數字，這兩個字元會包含在內。 否則，如果一組並存子音有相同的 `SOUNDEX` 代碼數字，除了第一個子音，所有子音都會被排除在外。|`SOUNDEX` 函數會實作以下規則：<br /><br /> 1) 如果大寫 H 或大寫 W 分隔擁有相同 `SOUNDEX` 代碼數字的兩個子音，則會忽略右邊的子音<br /><br /> 2) 如果一組並存子音有相同的 `SOUNDEX` 代碼數字，除了第一個子音，所有子音都會被排除在外。<br /><br /> <br /><br /> 其他規則可能會使 `SOUNDEX` 函數計算的值不同於在舊版相容性層級下計算的值。 升級到相容性層級 110 之後，您可能需要重建使用 `SOUNDEX` 函數的索引、堆積或 CHECK 條件約束。 如需詳細資訊，請參閱 [SOUNDEX](../../t-sql/functions/soundex-transact-sql.md)。|

## <a name="differences-between-compatibility-level-90-and-level-100"></a>相容性層級 90 和 100 之間的差異

本章節描述相容性層級 100 所導入的新行為。

|相容性層級設定為 90|相容性層級設定為 100|影響的可能性|
|----------------------------------------|-----------------------------------------|---------------------------|
|如果不論工作階段層級設定為何都會建立多重陳述式資料表值函式，則 QUOTED_IDENTIFER 設定一定會針對這種函數設定為 ON。|當建立多重陳述式資料表值函式時，可接受 QUOTED IDENTIFIER 工作階段設定。|中|
|當您建立或改變分割區函數時，評估此函數中的 **datetime** 和 **smalldatetime** 常值時會假設 US_English 為語言設定。|目前的語言設定可用來評估分割區函數中的 **datetime** 和 **smalldatetime** 常值。|中|
|`INSERT` 和 `SELECT INTO` 陳述式中允許 (但會忽略) `FOR BROWSE` 子句。|`INSERT` 和 `SELECT INTO` 陳述式中不允許 `FOR BROWSE` 子句。|中|
|`OUTPUT` 子句中允許全文檢索述詞。|`OUTPUT` 子句中不允許全文檢索述詞。|低|
|未支援 `CREATE FULLTEXT STOPLIST`、`ALTER FULLTEXT STOPLIST` 和 `DROP FULLTEXT STOPLIST`。 系統停用字詞表會自動與新的全文檢索索引產生關聯。|支援 `CREATE FULLTEXT STOPLIST`、`ALTER FULLTEXT STOPLIST`和 `DROP FULLTEXT STOPLIST`。|低|
|`MERGE` 不會強制為保留關鍵字。|MERGE 是完整的保留關鍵字。 100 和 90 相容性層級之下都支援 `MERGE` 陳述式。|低|
|使用 INSERT 陳述式的 \<dml_table_source> 引數會引發語法錯誤。|您可以在巢狀 INSERT、UPDATE、DELETE 或 MERGE 陳述式中擷取 OUTPUT 子句的結果，並將這些結果插入目標資料表或檢視表中。 這是利用 INSERT 陳述式的 \<dml_table_source> 引數所完成。|低|
|除非指定了 `NOINDEX`X，否則 `DBCC CHECKDB` 或 `DBCC CHECKTABLE` 會針對單一資料表或索引檢視表及它的所有非叢集索引和 XML 索引進行實體和邏輯一致性檢查。 不支援空間索引。|除非指定了 `NOINDEX`X，否則 `DBCC CHECKDB` 或 `DBCC CHECKTABLE` 會針對單一資料表及它的所有非叢集索引進行實體和邏輯一致性檢查。 但是根據預設，XML 索引、空間索引和索引檢視表只會進行實體一致性檢查。<br /><br /> 如果指定了 `WITH EXTENDED_LOGICAL_CHECKS`，將會針對索引檢視表、XML 索引和空間索引 (如果有的話) 執行邏輯檢查。 根據預設，實體一致性檢查會在邏輯一致性檢查之前執行。 如果也指定了 `NOINDEX`，則只會執行邏輯檢查。|低|
|搭配資料操作語言 (DML) 陳述式使用 OUTPUT 子句而且在陳述式執行期間發生執行階段錯誤時，就會終止和回復整個交易。|搭配資料操作語言 (DML) 陳述式使用 `OUTPUT` 子句，而且在陳述式執行期間發生執行階段錯誤時，其行為取決於 `SET XACT_ABORT` 設定。 如果 `SET XACT_ABORT` 為 OFF，使用 `OUTPUT` 子句之 DML 陳述式所產生的陳述式中止錯誤將會結束此陳述式，但是批次會繼續執行，而且不會回復交易。 如果 `SET XACT_ABORT` 為 ON，使用 OUTPUT 子句之 DML 陳述式所產生的所有執行階段錯誤將會結束批次，而且會回復交易。|低|
|不會強制 CUBE 和 ROLLUP 必須為保留關鍵字。|`CUBE` 和 `ROLLUP` 在 GROUP BY 子句中為保留關鍵字。|低|
|Strict 驗證會套用到 XML **anyType** 類型的元素。|Lax 驗證會套用到 **anyType** 類型的元素。 如需詳細資訊，請參閱[萬用字元元件和內容驗證](../../relational-databases/xml/wildcard-components-and-content-validation.md)。|低|
|資料操作語言陳述式無法查詢或修改特殊屬性 **xsi:nil** 和 **xsi:type**。<br /><br /> 這表示當 `/e/@*` 忽略 **xsi:nil** 和 **xsi:type** 屬性時，`/e/@xsi:nil` 會失敗。 但是，`/e` 會傳回 **xsi:nil** 和 **xsi:type** 屬性以便與 `SELECT xmlCol` 一致，即使 `xsi:nil = "false"` 也是如此。|特殊屬性 **xsi:nil** 和 **xsi:type** 會儲存為一般屬性，而且可進行查詢和修改。<br /><br /> 例如，執行 `SELECT x.query('a/b/@*')` 查詢會傳回所有屬性，包括 **xsi:nil** 和 **xsi:type**。 若要在查詢中排除這些類型，請將 `@*` 取代為 `@*[namespace-uri(.) != "`*insert xsi namespace uri*`"`，而非 `(local-name(.) = "type"` 或 `local-name(.) ="nil".`|低|
|將 XML 常數字串值轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 日期時間類型的使用者定義函數，會標示為決定性。|將 XML 常數字串值轉換成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 日期時間類型的使用者定義函數會標示為不具決定性。|低|
|XML 聯集和清單類型並未受到完整支援。|聯集和清單類型受到完整支援，包括以下功能：<br /><br /> 清單的聯集<br /><br /> 聯集的聯集<br /><br /> 不可部分完成之類型的清單<br /><br /> 聯集的清單|低|
|當此方法包含在檢視表或是內嵌資料表值函式內時，不會驗證 xQuery 方法所需的 SET 選項。|當此方法包含在檢視表或是內嵌資料表值函式內時，將會驗證 xQuery 方法所需的 SET 選項。 如果未能正確設定此方法的 SET 選項，將會引發錯誤。|低|
|包含行尾字元 (歸位字元和換行字元) 的 XML 屬性值不會根據 XML 標準來正規化。 也就是說，會傳回這兩個字元，而不是單一換行字元。|包含行尾字元 (歸位字元和換行字元) 的 XML 屬性值會根據 XML 標準來正規化。 也就是說，外部剖析之實體 (包括文件實體) 內的所有分行符號都會在輸入上正規化，其方式是將雙字元序列 #xD #xA 及任何緊接著 #xA 的 #xD 轉換成單一 #xA 字元。<br /><br /> 使用屬性來傳輸包含行尾字元之字串值的應用程式將不會在提交這些字元時收回這些字元。 為了避免正規化的程序，請使用 XML 數值字元實體來編碼所有行尾字元。|低|
|資料行屬性 `ROWGUIDCOL` 和 `IDENTITY` 可能會錯誤地命名為條件約束。 例如，陳述式 `CREATE TABLE T (C1 int CONSTRAINT MyConstraint IDENTITY)` 會執行，但是條件約束名稱不會保留，也無法供使用者存取。|資料行屬性 `ROWGUIDCOL` 和 `IDENTITY` 無法命名為條件約束。 傳回錯誤 156。|低|
|使用雙向指派來更新資料行 (例如 `UPDATE T1 SET @v = column_name = <expression>`) 會產生非預期的結果，因為陳述式執行期間可以在其他子句 (如 `WHERE` 和 `ON` 子句) 中使用變數的即時值，而不是陳述式起始值。 這會導致述詞的意義會根據每個資料列而以非預期的方式變更。<br /><br /> 只有當相容性層級設定為 90 時，才適用這個行為。|使用雙向指派來更新資料行會產生預期的結果，因為陳述式執行期間只會存取資料行的陳述式起始值。|低|
|請參閱下方＜範例＞一節中的範例 E。|請參閱下方＜範例＞一節中的範例 F。|低|
|ODBC 函數 {fn CONVERT()} 會使用語言的預設日期格式。 對於某些語言來說，預設格式為 YDM，這可能會在 CONVERT() 結合其他必須是 YMD 格式的函數 (例如 `{fn CURDATE()}`) 使用時產生轉換錯誤。|當 ODBC 函數 `{fn CONVERT()}` 轉換成 ODBC 資料類型 SQL_TIMESTAMP、SQL_DATE、SQL_TIME、SQLDATE、SQL_TYPE_TIME 和 SQL_TYPE_TIMESTAMP 時，會使用樣式 121 (與語言無關的 YMD 格式)。|低|
|日期時間內建 (如 DATEPART) 不會要求字串輸入值必須是有效的日期時間常值。 例如，`SELECT DATEPART (year, '2007/05-30')` 會編譯成功。|日期時間內建 (如 `DATEPART`) 會要求字串輸入值必須是有效的日期時間常值。 當使用無效的日期時間常值時會傳回錯誤 241。|低|
|當參數的類型為 Char 時，在 REPLACE 函式的第一個輸入參數內所指定尾端空格將會被修剪。 例如，在 SELECT '<' + REPLACE(CONVERT(char(6), 'ABC '), ' ', 'L') + '>' 陳述式中，值 'ABC ' 會被錯誤地判斷為 'ABC'。|一律保留尾端空格。 如果是依賴舊版函式行為的應用程式，則當您為此函式指定第一個輸入參數時，請使用 RTRIM 函式。 例如，下列語法會重現 SQL Server 2005 的行為：SELECT '<' + REPLACE(RTRIM(CONVERT(char(6), 'ABC ')), ' ', 'L') + '>'。|低|

## <a name="reserved-keywords"></a>保留關鍵字

相容性設定也決定了 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 所保留的關鍵字。 下表顯示每個相容性層級所使用的保留關鍵字。

|相容性層級設定|保留關鍵字|
|----------------------------------|-----------------------|
|130|有待決定。|
|120|無。|
|110|`WITHIN GROUP`, `TRY_CONVERT`, `SEMANTICKEYPHRASETABLE`, `SEMANTICSIMILARITYDETAILSTABLE`, `SEMANTICSIMILARITYTABLE`|
|100|`CUBE`, `MERGE`, `ROLLUP`|
|90|`EXTERNAL`, `PIVOT`, `UNPIVOT`, `REVERT`, `TABLESAMPLE`|

在給定的相容性層級中，保留關鍵字包含這個層級或這個層級以下所導入的所有關鍵字。 例如，對於層級 110 的應用程式而言，上表所列出的所有關鍵字都會保留下來。 在較低的相容性層級中，層級 100 的關鍵字仍是有效的物件名稱，但對應於這些關鍵字的層級 110 語言功能則無法使用。

導入之後，關鍵字會維持保留狀態。 例如，相容性層級 90 所導入的保留關鍵字 PIVOT，也會保留在層級 100 和 110 和 120 中。

如果應用程式使用的識別碼是其相容性層級的保留關鍵字，應用程式便會失敗。 若要解決這個問題，請用方括號 ( **[]** ) 或引號 ( **""** ) 來括住識別碼；例如，若要將使用識別碼 `EXTERNAL` 的應用程式升級到相容性層級 90，您可將識別碼改成 `[EXTERNAL]` 或 `"EXTERNAL"`。

如需詳細資訊，請參閱[保留關鍵字](../../t-sql/language-elements/reserved-keywords-transact-sql.md)。

## <a name="permissions"></a>權限

需要資料庫的 `ALTER` 權限。

## <a name="examples"></a>範例

### <a name="a-changing-the-compatibility-level"></a>A. 變更相容性層級

下列範例將 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的相容性層級變更為 110，這是 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 的預設層級。

```sql
ALTER DATABASE AdventureWorks2012
SET COMPATIBILITY_LEVEL = 110;
GO
```

下列範例會傳回目前資料庫的相容性層級。

```sql
SELECT name, compatibility_level
FROM sys.databases
WHERE name = db_name();
```

### <a name="b-ignoring-the-set-language-statement-except-under-compatibility-level-120"></a>B. 除非低於相容性層級 120，否則忽略 SET LANGUAGE 陳述式

下列查詢會忽略低於相容性層級 120 以外的 `SET LANGUAGE` 陳述式。

```sql
SET DATEFORMAT dmy;
DECLARE @t2 date = '12/5/2011' ;
SET LANGUAGE dutch;
SELECT CONVERT(varchar(11), @t2, 106);

-- Results when the compatibility level is less than 120.
12 May 2011

-- Results when the compatibility level is set to 120).
12 mei 2011
```

### <a name="c-for-compatibility-level-setting-of-110-or-lower-recursive-references-on-the-right-hand-side-of-an-except-clause-create-an-infinite-loop"></a>C. 相容性層級設定為 110 或更低時，EXCEPT 子句右邊的遞迴參考會建立無限迴圈

```sql
WITH
cte AS (SELECT * FROM (VALUES (1),(2),(3)) v (a)),
r
AS (SELECT a FROM Table1
UNION ALL
(SELECT a FROM Table1 EXCEPT SELECT a FROM r) )
SELECT a
FROM r;

```

### <a name="d-the-difference-between-styles-0-and-121"></a>D. 樣式 0 與 121 之間的差異

如需日期和時間樣式的詳細資訊，請參閱 [CAST 和 CONVERT](../../t-sql/functions/cast-and-convert-transact-sql.md)。

```sql
CREATE TABLE t1 (c1 time(7), c2 datetime2);

INSERT t1 (c1,c2) VALUES (GETDATE(), GETDATE());

SELECT CONVERT(nvarchar(16),c1,0) AS TimeStyle0
       ,CONVERT(nvarchar(16),c1,121)AS TimeStyle121
       ,CONVERT(nvarchar(32),c2,0) AS Datetime2Style0
       ,CONVERT(nvarchar(32),c2,121)AS Datetime2Style121
FROM t1;

-- Returns values such as the following.
TimeStyle0       TimeStyle121
Datetime2Style0      Datetime2Style121
---------------- ----------------
-------------------- --------------------------
3:15PM           15:15:35.8100000
Jun  7 2011  3:15PM  2011-06-07 15:15:35.8130000
```

### <a name="e-variable-assignment---top-level-union-operator"></a>E. 變數指派 - 最上層的 UNION 運算子
在包含最上層 UNION 運算子的陳述式中允許使用變數指派，但是會傳回非預期的結果。 例如在下列陳述式中，會將兩個資料表之聯集中的 `@v` 資料行值指派給 `BusinessEntityID` 區域變數。 就定義來說，如果 SELECT 陳述式傳回多個值，就會將最後傳回的值指派給變數。 在此情況下，便會將最後一個值正確地指派給變數，但是也會傳回 SELECT UNION 陳述式的結果集。

```sql
ALTER DATABASE AdventureWorks2012
SET compatibility_level = 110;
GO
USE AdventureWorks2012;
GO
DECLARE @v int;
SELECT @v = BusinessEntityID FROM HumanResources.Employee
UNION ALL
SELECT @v = BusinessEntityID FROM HumanResources.EmployeeAddress;
SELECT @v;
```

在包含最上層 UNION 運算子的陳述式中，不允許使用變數指派。 傳回錯誤 10734。 若要解決此錯誤，請重寫查詢，如下列範例所示。

```sql
DECLARE @v int;
SELECT @v = BusinessEntityID FROM
    (SELECT BusinessEntityID FROM HumanResources.Employee
     UNION ALL
     SELECT BusinessEntityID FROM HumanResources.EmployeeAddress) AS Test;
SELECT @v;
```

## <a name="see-also"></a>另請參閱 
[相容性憑證](../../database-engine/install-windows/compatibility-certification.md)       
[改變資料庫](../../t-sql/statements/alter-database-transact-sql.md)       
[保留關鍵字](../../t-sql/language-elements/reserved-keywords-transact-sql.md)       
[CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)       
[DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)       
[sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)       
[sys.database_files](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)       
[檢視或變更資料庫的相容性層級](../../relational-databases/databases/view-or-change-the-compatibility-level-of-a-database.md)       
[變更資料庫相容性模式並使用查詢存放區](../../database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store.md)       
[使用查詢調整小幫手來升級資料庫](../../relational-databases/performance/upgrade-dbcompat-using-qta.md)