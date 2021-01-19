---
title: 資源管理員資源集區
description: SQL Server Resource Governor 會針對內送應用程式要求可在資源集區中使用的 CPU、實體 IO 和記憶體數量指定限制。
ms.custom: ''
ms.date: 10/20/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- Resource Governor, resource pool
- resource pool [SQL Server], overview
- resource pool [SQL Server]
ms.assetid: 306b6278-e54f-42e6-b746-95a9315e0cbe
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 4028f19dc9d7ac319b083b5e6077021da59e7a9b
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172310"
---
# <a name="resource-governor-resource-pool"></a>資源管理員資源集區
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資源管理員中，資源集區代表 [!INCLUDE[ssDE](../../includes/ssde-md.md)]執行個體的實體資源子集。 資源管理員可讓您針對內送應用程式要求可在資源集區使用的 CPU、實體 IO 和記憶體數量指定限制。 每個資源集區都會包含一個或多個工作負載群組。 在工作階段啟動時，資源管理員分類會將此工作階段指派給特定的工作負載群組，並且此工作階段必須使用指派給該工作負載群組的資源來執行。  
  
## <a name="resource-pool-concepts"></a>資源集區概念  
 資源集區 (或集區) 代表伺服器的實體資源。 您可以將集區視為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體內部的虛擬 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 集區具有兩個部分。 其中一個部分不會與其他集區重疊，以便達到最小資源保留的效果。 另一個部分則與其他集區共用，以便支援最大可能的資源耗用量。 集區資源的定義方式為針對各資源 (CPU、記憶體和實體 IO) 指定下列一個或多個設定：  
  
-   **MIN_CPU_PERCENT 和 MAX_CPU_PERCENT**  
  
     這兩個設定是在發生 CPU 競爭時，資源集區中所有要求的保證平均 CPU 頻寬下限和上限。 您可以使用這些設定，根據每個工作負載的需求，為多個工作負載建立可預測的 CPU 資源使用量。 例如，假設公司的銷售和行銷部門共用相同的資料庫。 銷售部門有高查詢優先權的 CPU 運算密集工作負載。 行銷部門也有 CPU 運算密集的工作負載，但是查詢優先權較低。 透過為各部門建立個別的資源集區，您可以指派銷售資源集區的 CPU 百分比「下限」為 70，而行銷資源集區的 CPU 百分比「上限」為 30。 這樣可以確保銷售工作負載接收到其所需的 CPU 資源，而行銷工作負載會與銷售工作負載的 CPU 需求隔離。 請注意，CPU 百分比上限是機率的最大值。 如果有可用的 CPU 容量，工作負載最多可以使用到 100%。 此上限值只適用於發生 CPU 資源競爭時。 在此範例中，如果銷售工作負載關閉，行銷工作負載可在需要時使用 100% 的 CPU。  
  
-   **CAP_CPU_PERCENT**  
  
     CAP_CPU_PERCENT 設定是 CPU 頻寬硬體上限，適用於資源集區中的所有要求。 與集區相關聯的工作負載可以使用高於 MAX_CPU_PERCENT 值 (如果有的話) 但不高於 CAP_CPU_PERCENT 值的 CPU 容量。 在上述範例中，假設已針對行銷部門的資源使用量收費。 他們想要可預測的帳單，不想支付超過 30% 的 CPU。 只要將行銷資源集區的 CAP_CPU_PERCENT 設為 30 就可以做到這一點。  
  
-   **MIN_MEMORY_PERCENT 和 MAX_MEMORY_PERCENT**  
  
     這兩個設定是保留記憶體數量的下限及上限，適用於不能與其他資源集區共享的資源集區。 對於不包含記憶體最佳化資料表的資料庫，這裡所指的記憶體是查詢執行授與記憶體，而非緩衝集區記憶體 (例如，資料和索引頁面)。 設定集區的記憶體最小值，表示您可以確保可能在此資源集區中執行的任何要求，都可使用所指定的記憶體百分比。 這是與 MIN_CPU_PERCENT 相較下的重要差異指標，因為在此情況下，即使集區對於區內的工作負載群組沒有任何要求，記憶體仍可保留在指定的資源集區中。 因此，在使用此設定時請務必小心，因為即使沒有任何作用中的要求，也無法將此記憶體提供給任何其他集區使用。 設定集區的記憶體最大值，表示在此集區中執行要求時，要求所取得的記憶體永遠不會高於整體記憶體的百分比。

     若要使用 Resource Governor 來管理記憶體最佳化資料表的記憶體，您應該將資料庫繫結至不同的資源集區。 如需詳細資訊，請參閱[將包含記憶體最佳化資料表的資料庫繫結至資源集區](../in-memory-oltp/bind-a-database-with-memory-optimized-tables-to-a-resource-pool.md)。
  
-   **AFFINITY**  
  
     此設定可讓您將資源集區與一個或多個排程器或 NUMA 節點進行相似化，藉以進一步隔離 CPU 資源。 使用上述的銷售及行銷案例，我們假設銷售部門需要更加隔離的環境，並且隨時都要有 100% 的 CPU 核心。 藉由使用 AFFINITY 選項，銷售及行銷工作負載可以在不同的 CPU 上排程。 假設行銷集區的 CAP_CPU_PERCENT 仍在，行銷工作負載繼續使用一個核心的 30% 上限，而銷售工作負載使用另一個核心的 100%。 就銷售及行銷工作負載而言，它們在兩台隔離的電腦上執行。  
  
-   **MIN_IOPS_PER_VOLUME 和 MAX_IOPS_PER_VOLUME**  
  
     這兩個設定是資源集區每個磁碟區每秒的實體 IO 作業數 (IOPS) 下限及上限。 您可以使用這些設定，針對指定資源集區，控制為使用者執行緒發出的實體 IO 數。 例如，銷售部門以大型批次產生數份月底報表。 這些批次中的查詢會產生 IO，可能使磁碟區飽和，並影響資料庫中其他較高優先權工作負載的效能。 為了將此工作負載隔離，將銷售部門資源集區的 MIN_IOPS_PER_VOLUME 設為 20，並將 MAX_IOPS_PER_VOLUME 設為 100，以控制可為工作負載發出的 IO 層級。  
  
所有集區之 MIN 值的總和不得超過伺服器資源的 100%。 此外，MAX 和 CAP 值則可設定為 MIN 與 100% (含) 之間範圍中的任何值。 此外，在設定 CPU 或記憶體時，MAX 和 CAP 值則可設定為 MIN 與 100% (含) 之間範圍中的任何值。  
  
如果某個集區已經定義了非零的 MIN，其他集區的有效 MAX 值就會重新調整。 因為集區的最小已設定 MAX 值和其他集區 MIN 值的總和都是從 100% 中扣除。  
  
下表將說明部分的上述概念。 此表顯示了內部集區、預設集區和兩個使用者定義集區的設定。 
  
|集區名稱|MIN % 設定|MAX % 設定|計算出有效的 MAX %|計算出共用的 %|註解|  
|---------------|-------------------|-------------------|--------------------------------|-------------------------|-------------|  
|內部|0|100|100|0|有效的 MAX% 和共用的 % 不適用於內部集區。|  
|預設|0|100|30|30|有效的 MAX 值計算為：min(100,100-(20+50)) = 30。 計算出共用的 % 為有效的 MAX - MIN = 30。|  
|集區 1|20|100|50|30|有效的 MAX 值計算為：min(100,100-50) = 50。 計算出共用的 % 為有效的 MAX - MIN = 30。|  
|集區 2|50|70|70|20|有效的 MAX 值計算為：min(70,100-20) = 70。 計算出共用的 % 為有效的 MAX - MIN = 20。|  

下列公式用於計算上表中的有效 MAX% 和共用的 %：  
  
-   Min(X,Y) 代表較小的 X 和 Y 值。  
  
-   Sum(X) 代表所有集區之 X 值的總和。  
  
-   全部共用的 % = 100 - sum(MIN %)。  
  
-   有效的 MAX % = min(X,Y)。  
  
-   共用的 % = 有效的 MAX % - MIN %。  

以上表為例，我們可以進一步說明建立另一個集區時會進行的調整。 這個集區是「集區 3」，而且 MIN % 設定為 5。  
  
|集區名稱|MIN % 設定|MAX % 設定|計算出有效的 MAX %|計算出共用的 %|註解|  
|---------------|-------------------|-------------------|--------------------------------|-------------------------|-------------|  
|內部|0|100|100|0|有效的 MAX% 和共用的 % 不適用於內部集區。|  
|預設|0|100|25|25|有效的 MAX 值計算為：min(100,100-(20+50+5)) = 25。 計算出共用的 % 為有效的 MAX - MIN = 25。|  
|集區 1|20|100|45|25|有效的 MAX 值計算為：min(100,100-55) = 45。 計算出共用的 % 為有效的 MAX - MIN = 25。|  
|集區 2|50|70|70|20|有效的 MAX 值計算為：min(70,100-25) = 70。 計算出共用的 % 為有效的 MAX - MIN = 20。|  
|集區 3|5|100|30|25|有效的 MAX 值計算為：min(100,100-70) = 30。 計算出共用的 % 為有效的 MAX - MIN = 25。|  
  
 集區的共用部分是用來指出可用資源的範圍 (如果資源可用的話)。 不過，當資源被耗用時，它們就會移至指定的集區而且無法共用。 當指定之集區中沒有任何要求時，這樣做可改善資源使用率，而且設定給集區的資源可釋放給其他集區使用。  
  
 某些極端的集區組態情況如下：  
  
-   所有集區都定義了最小值，而這些值總計代表伺服器資源的 100%。 在此情況下，有效的最大值就等於最小值。 這就相當於將伺服器資源分成許多非重疊的部分，不論資源是否在任何指定的集區內部耗用都一樣。  
  
-   所有集區的最小值都是零。 所有集區會競爭可用資源，而且其最終大小是以每個集區的資源耗用量為基礎。 原則等其他因素會在發展最終集區大小中扮演某個角色。  
  
資源管理員預先定義了兩個資源集區：內部集區和預設集區。 您還可以加入其他集區。  
  
**內部集區**  
  
內部集區代表 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 本身所耗用的資源。 這個集區永遠僅包含內部群組，而且您無法用任何方式來變更此集區。 內部集區的資源耗用量並沒有限制。 此集區中的任何工作負載都會被視為對於伺服器運作很重要，而且資源管理員允許內部集區對其他集區施壓，即使這樣做違反針對其他集區所設定的限制也一樣。  
  
> [!NOTE]  
>  內部集區和內部群組資源使用量不會從整體資源使用量中扣除。 百分比是從可用的整體資源中計算所得。  
  
**預設集區**  
  
預設集區是第一個預先定義的使用者集區。 進行任何組態設定之前，預設集區僅包含預設群組。 您無法建立或卸除預設集區，但是可以變更此集區。 除了預設群組以外，預設集區可以包含使用者定義的群組。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，例行的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 作業會有預設資源集區，而外部處理序 (例如執行 R 指令碼) 會有預設外部資源集區。  
  
> [!NOTE]  
>  雖然預設群組可變更，但是您無法將它移出預設集區之外。  
  
**外部集區**  
  
使用者可以定義外部集區，以定義外部處理序的資源。 針對 R 服務，此會特別用來管理 `rterm.exe`、`BxlServer.exe` 及其所衍生的其他處理序。  
  
**使用者定義的資源集區**  
  
使用者定義的資源集區是您為環境中特定的工作負載所建立的資源集區。 資源管理員會針對建立、變更和卸除資源集區提供 DDL 陳述式。  
  
## <a name="resource-pool-tasks"></a>資源集區工作  
  
|工作描述|發行項|  
|----------------------|-----------|  
|描述如何建立資源集區。|[建立資源集區](../../relational-databases/resource-governor/create-a-resource-pool.md)|  
|描述如何變更資源集區設定。|[變更資源集區設定](../../relational-databases/resource-governor/change-resource-pool-settings.md)|  
|描述如何刪除資源集區。|[刪除資源集區](../../relational-databases/resource-governor/delete-a-resource-pool.md)|  
  
## <a name="see-also"></a>另請參閱  
 [資源管理員](../../relational-databases/resource-governor/resource-governor.md)   
 [資源管理員工作負載群組](../../relational-databases/resource-governor/resource-governor-workload-group.md)   
 [資源管理員分類函數](../../relational-databases/resource-governor/resource-governor-classifier-function.md)   
 [使用範本來設定資源管理員](../../relational-databases/resource-governor/configure-resource-governor-using-a-template.md)   
 [檢視資源管理員屬性](../../relational-databases/resource-governor/view-resource-governor-properties.md)  
  
  
