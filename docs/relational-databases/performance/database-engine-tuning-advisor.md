---
title: Database Engine Tuning Advisor | Microsoft 文件
description: 了解如何使用 Database Engine Tuning Advisor 進行疑難排解、微調一組大型查詢、分析設計變更，以及管理 SQL Server 中的儲存空間。
ms.custom: ''
ms.date: 01/09/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
f1_keywords:
- sql13.dta.general.f1
ms.assetid: 50dd0a0b-a407-4aeb-bc8b-b02a793aa30a
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f3b5176bb0b9ac123090f032e0908d57155ff592
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172180"
---
# <a name="database-engine-tuning-advisor"></a>Database Engine Tuning Advisor
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  [!INCLUDE[msCoName](../../includes/msconame-md.md)] Database Engine Tuning Advisor (DTA) 會分析資料庫，並提出可用來最佳化查詢效能的建議。 Database Engine Tuning Advisor 在您尚未深入了解資料庫結構或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]本質內容之前，就能夠選取及建立一組最佳的索引、索引檢視或資料表資料分割。 您可以使用 DTA 來執行下列工作。  
  
-   疑難排解特定問題查詢的效能  
  
-   微調跨一個或多個資料庫的一組大型查詢  
  
-   執行潛在實體設計變更的探勘假設分析  
  
-   管理儲存空間  
  
## <a name="database-engine-tuning-advisor-benefits"></a>Database Engine Tuning Advisor 優點  
 如果未完全了解資料庫結構以及針對資料庫執行的查詢，則很難最佳化查詢效能。 **Database Engine Tuning Advisor (DTA)** 可簡化這項工作，方法是分析目前的查詢計劃快取，或是分析所建立 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢的工作負載，以及建議適當的實體設計。 若為更高階的資料庫管理員，DTA 可提供強而有力的機制以執行不同實體設計替代方案的探勘假設分析。 DTA 可以提供下列資訊。  
  
-   利用查詢最佳化工具來分析工作負載中的查詢，以針對資料庫建議資料列存放區和[資料行存取區](../../relational-databases/performance/columnstore-index-recommendations-in-database-engine-tuning-advisor-dta.md)索引的最佳混合情況。  
  
-   針對工作負載所參考的資料庫來建議對齊或非對齊的資料分割。  
  
-   建議工作負載所參考之資料庫的索引檢視。  
  
-   分析所提出之變更的效果，其中包括索引用法、資料表之間的查詢分佈，以及工作負載中的查詢效能。  
  
-   建議針對一小組問題查詢來微調資料庫的方式。  
  
-   可讓您指定磁碟空間條件約束之類的進階選項來自訂建議。  
  
-   提供報表來總結針對給定工作負載來實作建議的效果。  

-   設想替代方案，讓您以假設性組態的形式來提供可能的設計選項，供 Database Engine Tuning Advisor 進行評估。

-   調整來自各種來源的工作負載，包括 SQL Server 查詢存放區、計畫快取、SQL Server Profiler 追蹤檔案或資料表，或是 SQL 檔案。

  
Database Engine Tuning Advisor 的設計目的為處理下列查詢工作負載類型：  
  
-   僅線上交易處理 (OLTP) 查詢  
  
-   僅線上分析處理 (OLAP) 查詢  
  
-   混合 OLTP 與 OLAP 查詢  
  
-   以查詢為主的工作負載 (查詢多於資料修改)  
  
-   以更新為主的工作負載 (資料修改多於查詢)  
  
## <a name="dta-components-and-concepts"></a>DTA 元件及概念  
 **Database Engine Tuning Advisor 圖形化使用者介面**  
 一種易用的介面，可用以指定工作負載，以及選取各種微調選項。  
  
 **dta** 公用程式  
 Database Engine Tuning Advisor 的命令提示字元版本。 **dta** 公用程式的設計，是為了讓您在應用程式和指令碼中使用 Database Engine Tuning Advisor 功能。  
  
 **工作負載**  
 Transact-SQL 指令碼檔案、追蹤檔案或追蹤資料表，內含代表您要微調之資料庫的工作負載。 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]開始，您可以將計畫快取指定為工作負載。  從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，您可以[將查詢存放區指定為工作負載](../../relational-databases/performance/tuning-database-using-workload-from-query-store.md)。 
  
 **XML 輸入檔**  
 Database Engine Tuning Advisor 可用來調整工作負載的 XML 格式檔案。 XML 輸入檔支援 GUI 或 **dta** 公用程式未提供的進階微調選項。  
  
## <a name="limitations-and-restrictions"></a>限制事項  
 Database Engine Tuning Advisor 還具有下列限制事項。  
  
-   無法新增或卸除唯一索引，或用於強制執行 `PRIMARY KEY` 或 `UNIQUE` 條件約束的索引。  
  
-   無法分析設定為單一使用者模式的資料庫。  
  
-   若您為微調建議所指定的最大磁碟空間超過實際的可用空間，則 Database Engine Tuning Advisor 會使用您所指定的值。 但是，當您執行建議指令碼來實作此項目時，若未先新增更多磁碟空間，指令碼就會失敗。 最大磁碟空間可透過 **dta** 公用程式的 **-B** 選項來指定，或在 [進階微調選項] 對話方塊中輸入值而加以指定。  
  
-   基於安全性考量，Database Engine Tuning Advisor 無法對位於遠端伺服器中的追蹤資料表，微調其中的工作負載。 若要解決此限制，您可以使用追蹤檔案，而非追蹤資料表，或是將追蹤資料表複製至遠端伺服器。  
  
-   當您使用條件約束 (例如您在使用 **-B** 選項或 [進階微調選項] 對話方塊指定微調建議的最大磁碟空間時，所加諸的條件約束) 時，可能會強制 Database Engine Tuning Advisor 卸除某些現有的索引。 在這種情況下，所產生的 Database Engine Tuning Advisor 建議，可能會導致負面的預期改善。  
  
-   如果指定條件約束以限制微調時間 (使用 **dta** 公用程式的 **-A** 選項，或勾選 [微調選項] 索引標籤上的 [限制微調時間])，Database Engine Tuning Advisor 可能會超過時間限制，才能產生精確的預期改善，以及針對目前為止所耗用的工作負載部分產生分析報告。  
  
-   Database Engine Tuning Advisor 在下列情況下可能不會進行建議：  
  
    1.  要微調的資料表，所含資料頁數小於 10 頁。  
  
    2.  建議的索引在目前的實體資料庫設計下，無法對查詢效能提供足夠的改善。  
  
    3.  執行 Database Engine Tuning Advisor 的使用者，不是 **db_owner** 資料庫角色或 **系統管理員 (sysadmin)** 固定伺服器角色的成員。 工作負載中的查詢，會在執行 Database Engine Tuning Advisor 之使用者的安全性內容中進行分析， 因此，使用者必須是 **db_owner** 資料庫角色的成員。  
  
-   Database Engine Tuning Advisor 會在 **msdb** 資料庫中儲存微調工作階段資料與其他資訊。 修改 **msdb** 資料庫有遺失微調工作階段資料的風險。 為降低此風險，請先備份 **msdb** 資料庫。  
  
## <a name="performance-considerations"></a>效能考量  
 Database Engine Tuning Advisor 在進行分析時，將會耗用大量的處理器與記憶體資源。 若要避免生產伺服器的速度減緩，請執行下列其中一個策略：  
  
-   趁伺服器空閒時再微調資料庫， 因為 Database Engine Tuning Advisor 會影響維護工作的效能。  
  
-   使用測試伺服器/生產伺服器功能。 如需詳細資訊，請參閱  [降低生產伺服器的微調負載](../../relational-databases/performance/reduce-the-production-server-tuning-load.md)。  
  
-   只指定要讓 Database Engine Tuning Advisor 分析的實體資料庫設計結構。 Database Engine Tuning Advisor 有許多選項，但只要指定必要的選項即可。  
  
## <a name="dependency-on-xp_msver-extended-stored-procedure"></a>xp_msver 擴充預存程序的相依性  
 Database Engine Tuning Advisor 依賴 **xp_msver** 擴充預存程序來提供完整的功能。 預設會開啟擴充預存程序。 Database Engine Tuning Advisor 會使用這個擴充預存程序，來提取您要微調的資料庫所在電腦上的處理器數目和可用的記憶體。 如果無法使用 **xp_msver** ，Database Engine Tuning Advisor 會假設執行 Database Engine Tuning Advisor 的電腦之硬體特性。 如果無法取得執行 Database Engine Tuning Advisor 之電腦的硬體特性，將會假設 1 個處理器和 1024 MB 的記憶體。  
  
 此相依性會影響分割的建議，因為所建議的分割數目是根據這兩個值 (處理器的數目和可用的記憶體) 而定。 此外，當您使用測試伺服器來微調實際伺服器時，此相依性也會影響微調結果。 在此案例中，Database Engine Tuning Advisor 會使用 **xp_msver** ，從實際伺服器提取硬體屬性。 在測試伺服器上微調工作負載之後，Database Engine Tuning Advisor 會使用這些硬體屬性來產生建議。 如需詳細資訊，請參閱 [xp_msver &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/xp-msver-transact-sql.md)。  
  
## <a name="database-engine-tuning-advisor-tasks"></a>Database Engine Tuning Advisor 工作  
 下表列出 Database Engine Tuning Advisor 的常見工作，以及描述如何執行這些工作的主題。  
  
|Database Engine Tuning Advisor 工作|主題|  
|-----------------------------------------|-----------|  
|初始化及啟動 Database Engine Tuning Advisor。<br /><br /> 透過指定計畫快取、建立指令碼或是產生追蹤檔案或追蹤資料表來建立工作負載。<br /><br /> 使用 Database Engine Tuning Advisor 圖形化使用者介面工具來微調資料庫。<br /><br /> 建立 XML 輸入檔來微調工作負載。<br /><br /> 檢視 Database Engine Tuning Advisor 使用者介面選項的描述。|[啟動及使用 Database Engine Tuning Advisor](../../relational-databases/performance/start-and-use-the-database-engine-tuning-advisor.md)|  
|檢視資料庫微調作業的結果。<br /><br /> 選取及實作微調建議。<br /><br /> 執行工作負載的假設探勘分析。<br /><br /> 檢閱現有的微調工作階段、根據現有微調工作階段來複製工作階段 <br />或是編輯現有微調建議，以進一步評估或實作。<br /><br /> 檢視 Database Engine Tuning Advisor 使用者介面選項的描述。|[檢視及處理 Database Engine Tuning Advisor 的輸出](../../relational-databases/performance/view-and-work-with-the-output-from-the-database-engine-tuning-advisor.md)|  
  
  
