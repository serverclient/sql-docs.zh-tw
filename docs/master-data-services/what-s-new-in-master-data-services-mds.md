---
title: 新功能
description: 瞭解 Master Data Services SQL Server 2019 版中的變更、增強功能和更新。
ms.custom: ''
ms.date: 07/08/2016
ms.prod: sql
ms.prod_service: mds
ms.reviewer: ''
ms.technology: master-data-services
ms.topic: conceptual
ms.assetid: ad530f60-d480-4457-ba7a-93a10c8a1695
author: lrtoyou1223
ms.author: lle
ms.openlocfilehash: 7e731d14967d808aa57f3fa0e001e5e593238ffd
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170430"
---
# <a name="what39s-new-in-master-data-services-mds"></a>Master Data Services (MDS) 的新功能

[!INCLUDE [SQL Server - Windows only ASDBMI  ](../includes/applies-to-version/sql-windows-only-asdbmi.md)]

  本主題摘要 [!INCLUDE[ssCurrent](../includes/sscurrent-md.md)] 的 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]版本中變更及更新。 
  
 如需如何在 [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]中整理資料的概觀，請參閱 [Master Data Services 概觀](../master-data-services/master-data-services-overview-mds.md)。 
  
 若要安裝 Master Data Services、設定資料庫和網站，以及部署範例模型，請參閱 [Master Data Services 概觀 (MDS)](../master-data-services/master-data-services-overview-mds.md)。  
  
 **下載**  
  
-   若要下載 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)]，請前往  **[Evaluation Center](https://www.microsoft.com/evalcenter/evaluate-sql-server-2016)** 。  
  
-   有 Azure 帳戶嗎？  接著前往 **[這裡](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)** ，來加速已安裝 [!INCLUDE[ssCurrent](../includes/sscurrent-md.md)] 的虛擬機器。  
  
##  <a name="improved-performance"></a>改良的效能  
  
 效能增強功能可讓您建立更大的模型、更有效率地載入資料，以及獲得更佳的整體效能。 這包括改進了 Microsoft Excel 增益集的效能，可降低資料載入時間，並可讓增益集能處理較大型的實體。  
  
 如需 Microsoft Excel 增益集的詳細資訊，請參閱 [適用於 Microsoft Excel 的 Master Data Services 增益集](../master-data-services/microsoft-excel-add-in/master-data-services-add-in-for-microsoft-excel.md)。  
  
 下列改良功能包括在內。  
  
-   實體層級具備資料壓縮，預設會啟用。 啟用資料壓縮時，所有與實體相關的資料表和索引，都會以 SQL 資料列層級壓縮來進行壓縮。 如此大幅降低了在讀取或更新主要資料時的磁碟 I/O，尤其是當主要資料有數百萬個資料列及 (或) 有許多 NULL 值資料行時。  
  
     因為在 SQL Server 引擎端會稍微增加 CPU 的使用量，所以如果在伺服器上有繫結 CPU，即可以藉由編輯實體來關閉資料壓縮。  
  
     如需詳細資訊，請參閱[建立實體 &#40;Master Data Services&#41;](../master-data-services/create-an-entity-master-data-services.md)和[資料壓縮](../relational-databases/data-compression/data-compression.md)。  
  
-   預設會啟用動態內容壓縮 IIS 功能。 如此可大幅降低 xml 回應的大小並省掉了網路 I/O，但 CPU 使用量會增加。 如果在伺服器上有繫結 CPU，您可以對 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] Web.config 檔案新增下列設定，關閉資料壓縮。  
  
    ```  
    <configuration>  
       \<system.webServer>  
          <urlCompression doStaticCompression="true" doDynamicCompression="false " />  
       \</system.webServer>  
    </configuration>  
  
    ```  
  
     如需詳細資訊，請參閱 [URL 壓縮](https://www.iis.net/configreference/system.webserver/urlcompression)  
  
-   下列新的 SQL Server Agent 工作會進行索引與記錄的維護。  
  
    -   MDS_MDM_Sample_Index_Maintenace  
  
    -   MDS_MDM_Sample_Log_Maintenace  
  
 MDS_MDM_Sample_Index_Maintenance 工作預設會每週執行。 您可以修改排程。 您也可以使用 udpDefragmentation 預存程序，在任何時間手動執行工作。 建議您在每次插入或更新大量的主要資料時，執行預存程序，或是在從現有版本建立新版本之後執行預存程序。  
  
 分散程度超過 30% 的索引會於線上重建。 重建期間，相同資料表上的 CRUD 作業效能會受到影響。 如果在意效能降低的問題，建議您於營業時間執行預存程序。 如需索引片段的詳細資訊，請參閱＜ [Reorganize and Rebuild Indexes](../relational-databases/indexes/reorganize-and-rebuild-indexes.md)＞。  
  
 如需詳細資訊，請參閱 Master Data Services 部落格上的 [SQL Server 2016 效能與調整改善](https://techcommunity.microsoft.com/t5/sql-server-integration-services/performance-and-scale-improvement-in-sql-server-2016/ba-p/388225)一文。  
  
##  <a name="improved-security"></a>已改善安全性  
  
 新的進階使用者函數權限，可讓使用者或群組與 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]先前版本的伺服器管理員擁有相同的權限。 進階使用者權限可以指派給多個使用者與群組。 在先前版本中，原先安裝了 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] 的使用者，即為伺服器管理員，而且很難將此權限轉移給另一個使用者或群組。 如需詳細資訊，請參閱[功能區域權限 &#40;Master Data Services&#41;](../master-data-services/functional-area-permissions-master-data-services.md)。  
  
 使用者現已可在模型層級明確地獲派系統管理員權限。 這表示如果使用者稍後才獲派模型樹狀子目錄 (例如實體層級) 的權限，他並不會失去此系統管理員權限。  
  
 在此版本的 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] 中，我們藉由引進下列新的權限，來提供更多權限層級：讀取、建立、更新與刪除。 例如，具有更新權限的使用者現已可更新主要資料，而無須建立或刪除資料。 當您授與使用者建立、更新或刪除的權限時，使用者會自動獲派讀取權限。 您也可以合併讀取、建立、更新與刪除權限。  
  
 當您升級至 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)][!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]時，舊有權限會轉換成新的權限，如下表所示。  
  
|先前版本中的權限|新的權限|  
|------------------------------------|--------------------|  
|原先安裝了 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] 的使用者具有伺服器系統管理員權限。|具有進階使用者函數權限的使用者|  
|使用者具有模型層級的更新權限，但沒有任何模型子樹狀目錄的權限，因此暗示為模型管理員。|使用者擁有模型層級的明確系統管理員權限。|  
|使用者擁有唯讀權限。|使用者擁有讀取存取權限。|  
|使用者擁有更新權限。|使用者擁有所有四種存取權限︰建立、更新、刪除與讀取。|  
|使用者擁有拒絕權限|使用者擁有拒絕權限|  
  
 如需有關權限的詳細資訊，請參閱[安全性 &#40;Master Data Services&#41;](../master-data-services/security-master-data-services.md)。  
  
##  <a name="improved-transaction-log-maintenance"></a>改進的交易記錄維護  
  
 您現已可用預先決定的間隔時間或根據排程，使用系統設定同時在模型層級清理交易記錄。 對於具備大量資料變更與 ETL 處理序的 MDS 系統而言，這些資料表可以指數成長，並會導致效能下降以及儲存體空間的問題。  
  
 下列類型的資料可以從記錄檔中移除。  
  
-   超過指定天數的交易歷程記錄。  
  
-   超過指定天數的驗證歷程記錄。  
  
-   在指定天數前執行的暫存批次。  
  
 您可以設定在模型層級使用系統設定從交易記錄檔移除資料的頻率。 如需詳細資訊，請參閱[系統設定 &#40;Master Data Services&#41;](../master-data-services/system-settings-master-data-services.md) 和[建立模型 &#40;Master Data Services&#41;](../master-data-services/create-a-model-master-data-services.md)。 如需交易的詳細資訊，請參閱[交易 &#40;Master Data Services&#41;](../master-data-services/transactions-master-data-services.md)。  
  
 SQL Server Agent 工作 MDS_MDM_Sample_Log_Maintenace，會觸發清除交易記錄的程序，並於每晚執行。 您可以使用 SQL Server Agent 來修改此工作的排程。  
  
 也可以呼叫預存程序來清除交易記錄檔。 如需詳細資訊，請參閱[交易 &#40;Master Data Services&#41;](../master-data-services/transactions-master-data-services.md)。  
  
## <a name="improved-troubleshooting"></a>改良版疑難排解  
  
 在 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)][!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]中已加入能改善偵錯並更容易針對問題進行疑難排解的功能。 如需詳細資訊，請參閱[追蹤 &#40;Master Data Services&#41;](../master-data-services/tracing-master-data-services.md)。  
  
## <a name="improved-manageability"></a>改良版管理性  
  
 管理性經過改良之後，有助於降低維護成本，進而對投資報酬率 (ROI) 產生正面的影響。 這些改良版的功能包括交易記錄維護與改善安全性，同時還提供下列新的功能。  
  
-   可使用長度超過 50 個字元的屬性名稱。  
  
-   可重新命名及隱藏 Name 與 Code 屬性。  
  
 如需詳細資訊，請參閱下列主題。  
  
-   [模型 &#40;Master Data Services&#41;](../master-data-services/models-master-data-services.md)  
  
-   [實體 &#40;Master Data Services&#41;](../master-data-services/entities-master-data-services.md)  
  
-   [交易 &#40;Master Data Services&#41;](../master-data-services/transactions-master-data-services.md)  
  
-   [安全性 &#40;Master Data Services&#41;](../master-data-services/security-master-data-services.md)  

## <a name="business-rule-improvements"></a>商務規則的改進
 **管理商務規則 (適用於 Excel 的 MDS 增益集)**  
  
 您可以在適用於 Excel 的 Master Data Services 增益集中，管理商務規則 (像是建立與編輯商務規則)。 商務規則可用於驗證資料。  
 
 **Business Rules Extension**  
  
 您可以套用使用者定義的 SQL 指令碼，來擴充商務規則條件與動作。 SQL 函數可用作為條件。 SQL 預存程序可用作為動作。 如需詳細資訊，請參閱[商務規則延伸模組 &#40;Master Data Services&#41;](../master-data-services/business-rules-extension-master-data-services.md)。 
 
 **重新設計商務規則管理經驗**  
  
 MDS 中的商務規則管理體驗，已全盤重新設計以改善使用體驗。 如需有關此功能的詳細資訊，請參閱[商務規則 &#40;Master Data Services&#41;](../master-data-services/business-rules-master-data-services.md)。  
  
 **已從適用於 Excel 的 MDS 增益集移除商務規則管理功能**  
  
 因為我們重新設計了有關商務規則管理功能的體驗，所以已從適用於 Excel 的 MDS 增益集中將其移除。    

 **新的商務規則條件**  
  
 已加入七個新的商務規則條件，提供一組完整的條件。 如需詳細資訊，請參閱[商務規則條件 &#40;Master Data Services&#41;](../master-data-services/business-rule-conditions-master-data-services.md)。  

## <a name="derived-hierarchy-improvements"></a>衍生階層的改進

 **衍生階層中的多對多關聯性**  
  
 您現已可建立會顯示多對多關聯性的衍生階層。 兩個實體之間的多對多關聯性，可能會透過使用第三個實體 (提供兩者間的對應)，進行模型化。 對應實體是具有兩個或多個參考其他實體的網域屬性實體。  
  
 例如，實體 M 有一個會參考 A 的網域屬性以及一個參考 B 的網域屬性。您就可以使用對應實體建立從 A 到 B 的階層。  
  
 如需詳細資訊，請參閱[在衍生階層中顯示多對多關聯性 &#40;Master Data Services&#41;](../master-data-services/show-many-to-many-relationships-in-derived-hierarchies-master-data-services.md)  
 
 **編輯衍生階層中的多對多關聯性**  
  
 透過修改對應的實體成員的方式，可編輯多對多關聯性。 如需詳細資訊，請參閱 [在衍生階層中顯示多對多關聯性 &#40;Master Data Services&#41;](../master-data-services/show-many-to-many-relationships-in-derived-hierarchies-master-data-services.md)。  
 
 **已改進衍生階層管理體驗**  
  
 MDS 中的衍生階層管理體驗已經過改良。 如需有關此功能的詳細資訊，請參閱[建立衍生階層 &#40;Master Data Services&#41;](../master-data-services/create-a-derived-hierarchy-master-data-services.md)。  
  
 因為我們重新設計了有關商務規則管理功能的體驗，所以已從適用於 Excel 的 MDS 增益集中將其移除。  
 
## <a name="attribute-improvements"></a>屬性的改進   
    
 **自訂索引**  
  
 您可以在一個屬性上建立非叢集索引 (單一索引) 或在實體中 (複合索引) 的屬性清單上建立非叢集索引，以協助改善查詢效能。 如需詳細資訊，請參閱[自訂索引 &#40;Master Data Services&#41;](../master-data-services/custom-index-master-data-services.md)。  
 
  **屬性篩選**  
  
 若是分葉成員的網域屬性，可以使用篩選父屬性來限制允許的網域屬性值。 如需詳細資訊，請參閱[建立網域屬性 &#40;Master Data Services&#41;](../master-data-services/create-a-domain-based-attribute-master-data-services.md)。  
 
## <a name="entity-and-member-improvements"></a>實體和成員的改進 
  
 **實體同步關聯性**  
  
 您可以藉由建立實體同步關聯性，在不同的模型之間分享實體資料。 如需詳細資訊，請參閱[實體同步關係 &#40;Master Data Services&#41;](../master-data-services/entity-sync-relationship-master-data-services.md)。  
  
 **清除虛刪除的成員**  
  
 您現已可清除 (永久刪除) 模型版本中所有已虛刪除的成員。 刪除成員只會停用或虛刪除該名成員。 如需詳細資訊，請參閱[清除版本成員 &#40;Master Data Services&#41;](../master-data-services/purge-version-members-master-data-services.md)。  
 
## <a name="improvements-for-managing-changes"></a>管理變更的改進 
  
 **成員修訂歷程記錄**  
  
 變更成員時會記錄成員修訂歷程記錄。 您可以復原修訂歷程記錄，以及對其進行檢視和加上註解修訂。 您可以使用 **Log Retention Days** 屬性來指定保留歷程記錄資料的時間長度。 如需詳細資訊，請參閱[成員修訂歷程記錄 &#40;Master Data Services&#41;](../master-data-services/member-revision-history-master-data-services.md)。  
  
 **合併衝突**  
  
 如果您嘗試發佈已由另一位使用者變更的資料，則該發佈將會失敗並會出現衝突錯誤。 若要解決此錯誤，可以執行合併衝突，然後重新發佈所做的變更。 如需詳細資訊，請參閱 [合併衝突 (Master Data Services)](../master-data-services/merge-conflicts-master-data-services.md) 和 [合併衝突 (適用於 Excel 的 MDS 增益集)](../master-data-services/microsoft-excel-add-in/merge-conflicts-mds-add-in-for-excel.md)。  
  
 **變更集**  
  
 您可以使用變更集將暫止變更儲存至實體，而且可以檢視並修改暫止變更。 如果實體需要核准變更，則必須將暫止變更儲存至變更集，並提交由系統管理員核准。 如需詳細資訊，請參閱 [&#40;Master Data Services&#41;的變更集 ](../master-data-services/changesets-master-data-services.md)。  
  
 **變更集電子郵件與管理**  
  
 在此版本中，您現已可檢視及管理模型和版本的所有變更。 也可以在每次需要核准的實體發生變更集狀態變更時，收到電子郵件通知。 如需詳細資訊，請參閱[管理變更集 &#40;Master Data Services&#41;](../master-data-services/manage-changesets-master-data-services.md) 和[通知 &#40;Master Data Services&#41;](../master-data-services/notifications-master-data-services.md)。  
  
 **檢視及管理修訂歷程記錄**  
  
 您可依實體和依成員來檢視及管理修訂記錄。 如果您有更新的權限，可以將成員復原回先前的版本。 如需詳細資訊，請參閱[成員修訂歷程記錄 &#40;Master Data Services&#41;](../master-data-services/member-revision-history-master-data-services.md)。  
 
## <a name="tool-and-sample-improvements"></a>工具和範例的改進 
  
 **在適用於 Excel 的 MDS 增益集中儲存或開啟查詢檔案**  
  
 您可以從實體總管頁面上按一下 [Excel]  ，儲存查詢檔案的捷徑。 或是可以在適用於 Excel 的 MDS 增益集中，開啟儲存在電腦上的查詢檔案。 使用 QueryOpener 應用程式可開啟已儲存的檔案。 如需詳細資訊，請參閱[捷徑查詢檔案 &#40;適用於 Excel 的 MDS 增益集&#41;](../master-data-services/microsoft-excel-add-in/shortcut-query-files-mds-add-in-for-excel.md)。  
  
 查詢檔案內含來自檔案總管頁面的篩選與階層資訊。  
   
 **已更新模型部署封裝範例**  
  
 為支援新的案例，已更新了封裝範例。 如需詳細資訊，請參閱 [SQL Server 範例：模型部署封裝 (MDS)](../master-data-services/sql-server-samples-model-deployment-packages-mds.md)。  

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]

[!INCLUDE[contribute-to-content](../includes/paragraph-content/contribute-to-content.md)]
  
## <a name="see-also"></a>另請參閱  
 [SQL Server 2016 版本支援的 Master Data Services 和 Data Quality Services 功能](../master-data-services/master-data-services-and-data-quality-services-features-support.md)  
 [取代的 Master Data Services 功能](../master-data-services/deprecated-master-data-services-features.md)  
 [已停止的 Master Data Services 功能](../master-data-services/discontinued-master-data-services-features.md)
