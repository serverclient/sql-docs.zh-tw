---
title: 新增和卸除發行項 (現有發行集)
description: 了解如何在 SQL Server 的現有發行集中新增和卸除發行項。
ms.custom: seo-lt-2019
ms.date: 03/07/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: conceptual
helpviewer_keywords:
- articles [SQL Server replication], dropping
- deleting articles
- removing articles
- dropping articles
- adding articles
- administering replication, articles
- publications [SQL Server replication], adding and dropping articles
- articles [SQL Server replication], adding
ms.assetid: b148e907-e1f2-483b-bdb2-59ea596efceb
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-mi-current||>=sql-server-2016
ms.openlocfilehash: 183619e1802b435fbc21c2b44e32848bf7e578de
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171900"
---
# <a name="add-articles-to-and-drop-articles-from-existing-publications"></a>在現有發行集中加入和卸除發行項
[!INCLUDE[sql-asdbmi](../../../includes/applies-to-version/sql-asdbmi.md)]
  建立發行集後，可以新增或卸除發行項。 發行項可以隨時新增，但卸除發行項所需的動作取決於複寫的類型以及卸除發行項的時機。  
  
## <a name="adding-articles"></a>加入發行項  
 新增發行項涉及的動作包括：在發行集中新增發行項、建立發行集的新快照集、同步處理訂閱以套用新發行項的結構描述和資料。  
  
> [!NOTE]
>  如果您將發行項新增至合併式發行集且現有某發行項相依於新的發行項，則您必須使用 [sp_addmergearticle](../../../relational-databases/system-stored-procedures/sp-addmergearticle-transact-sql.md) 和 [sp_changemergearticle](../../../relational-databases/system-stored-procedures/sp-changemergearticle-transact-sql.md) 的 **\@processing_order** 參數來指定兩個發行項的處理順序。 請考慮下列狀況：您發行資料表但未發行該資料表所參考的函數。 如果您未發行該函數，該資料表就無法在「訂閱者」端建立。 當您將函式新增至發行集時：請將 **sp_addmergearticle** 的 **\@processing_order** 參數值指定為 **1**；並將 **sp_changemergearticle** 的 **\@processing_order** 參數值指定為 **2**，指定 **\@article** 參數的資料表名稱。 此處理順序可確保您先在「訂閱者」端建立函數之後才建立相依於此函數的資料表。 您可對每個發行項使用不同的編號，只要函數的編號低於資料表的編號即可。  
  
1.  透過下列方法之一新增一或多個發行項：  
  
    -   [在發行集中新增和卸除發行項 &#40;SQL Server Management Studio&#41;](../../../relational-databases/replication/publish/add-articles-to-and-drop-articles-from-a-publication.md)  
  
    -   [定義發行項](../../../relational-databases/replication/publish/define-an-article.md)  
  
2.  新增發行項到發行集後，您必須為發行集 (和所有資料分割，如果該發行集為含參數化篩選的合併式發行集) 建立一個新快照集。 隨後，「散發代理程式」或「合併代理程式」會將新發行項的結構描述和資料複製到「訂閱者」 (不會重新初始化整個發行集)。  
  
    -   若要建立新的快照集，請參閱＜ [Create and Apply the Initial Snapshot](../../../relational-databases/replication/create-and-apply-the-initial-snapshot.md)＞。  
  
    -   若要使用參數化篩選建立合併式發行集的新快照集，請參閱[使用參數化篩選建立合併式發行集的快照集](../../../relational-databases/replication/create-a-snapshot-for-a-merge-publication-with-parameterized-filters.md)。  
  
3.  建立快照集後，同步處理訂閱，以複製新發行項的結構描述和資料。  

    -   若要同步處理發送訂閱，請參閱＜ [Synchronize a Push Subscription](../../../relational-databases/replication/synchronize-a-push-subscription.md)＞。  
  
    -   若要同步處理提取訂閱，請參閱＜ [Synchronize a Pull Subscription](../../../relational-databases/replication/synchronize-a-pull-subscription.md)＞。  
  
## <a name="dropping-articles"></a>卸除發行項  
 發行項可以隨時從發行集中卸除，但必須考慮下列行為：  
  
-   從發行集中卸除發行項不會從發行集資料庫中移除物件，或從訂閱資料庫中移除對應物件。 必要時請使用 DROP \<Object> 來移除這些物件。 透過外部索引鍵條件約束卸除與其他已發佈發行項相關的發行項時，建議手動卸除訂閱者端的資料表，或執行視需要的指令碼：指定包含適當 DROP \<Object> 陳述式的指令碼。 如需詳細資訊，請參閱[在同步處理期間執行指令碼 &#40;複寫 Transact-SQL 程式設計&#41;](../../../relational-databases/replication/execute-scripts-during-synchronization-replication-transact-sql-programming.md)。  
  
-   如果合併式發行集的相容性層級為 90RTM 或更高，可以隨時卸除發行項，但需要一個新快照集。 此外：  
  
    -   如果發行項是聯結篩選或邏輯記錄關聯性中的父發行項，則必須先卸除此關聯性 (需要重新初始化)。  
  
    -   如果發行項含發行集中的最終參數化篩選，則訂閱必須重新初始化。  
  
-   對於相容性層級低於 90RTM 的合併式發行集，可以在訂閱的初始同步處理之前卸除發行項，無需特殊考量。 如果在同步處理一個或多個訂閱後卸除發行項，則必須卸除、重新建立並同步處理訂閱。  
  
-   對於快照式或交易式發行集，可以在建立訂閱之前卸除發行項，無需特殊考量。 如果在建立一或多個訂閱後卸除發行項，則必須卸除、重新建立並同步處理訂閱。 如需卸除訂閱的詳細資訊，請參閱[訂閱發行集](../../../relational-databases/replication/subscribe-to-publications.md)和 [sp_dropsubscription &#40;Transact-SQL&#41;](../../../relational-databases/system-stored-procedures/sp-dropsubscription-transact-sql.md)。 **sp_dropsubscription** 允許您從訂閱中卸除單一發行項，而非整個訂閱。  
  
1.  從發行集中卸除發行項涉及卸除發行項並建立該發行集的新快照集。 卸除發行項會使目前快照集無效，因此必須建立新快照集。  
  
    -   若要從發行集卸除發行項，請參閱[在發行集中新增和卸除發行項 (&#40;SQL Server Management Studio&#41;](../../../relational-databases/replication/publish/add-articles-to-and-drop-articles-from-a-publication.md) 或[刪除發行項](../../../relational-databases/replication/publish/delete-an-article.md)。  
  
2.  從發行集中卸除發行項後，您必須為發行集 (和所有資料分割，如果該發行集為含有參數化篩選的合併式發行集) 建立一個新快照集。  
  
    -   若要建立新的快照集，請參閱＜ [Create and Apply the Initial Snapshot](../../../relational-databases/replication/create-and-apply-the-initial-snapshot.md)＞。  
  
    -   若要使用參數化篩選建立合併式發行集的新快照集，請參閱[使用參數化篩選建立合併式發行集的快照集](../../../relational-databases/replication/create-a-snapshot-for-a-merge-publication-with-parameterized-filters.md)。  
  
 如上所提到的，在某些情況下，卸除發行項需要卸除、重新建立並同步處理訂閱。 如需詳細資訊，請參閱[訂閱發行集](../../../relational-databases/replication/subscribe-to-publications.md)和[同步處理資料](../../../relational-databases/replication/synchronize-data.md)。  
 
 > [!NOTE]
 > **[!INCLUDE[ssSQL15](../../../includes/sssql14-md.md)] Service Pack 2** 或更新版本和 **[!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)] Service Pack 1** 或更新版本，支援使用 **DROP TABLE** DDL 命令為參與異動複寫的發行項卸除資料表。 如果發行集支援 DROP TABLE DDL，則 DROP TABLE 作業會從發行集和資料庫卸除資料表。 記錄讀取器代理程式會為卸除資料表的散發資料庫張貼清理命令，並執行發行者中繼資料的清理。 如果記錄讀取器尚未處理參考卸除資料表的所有記錄檔記錄，就會忽略與卸除資料表建立關聯的新命令。 已處理的記錄會傳遞至散發資料庫中。 如果散發代理程式在記錄讀取器清理過時 (已卸除) 的發行項之前先處理它們，它們可能會套用在訂閱者資料庫上。 所有異動複寫發行集的 **預設** 設定為不支援 DROP TABLE DDL。 [KB 3170123](https://support.microsoft.com/help/3170123/supports-drop-table-ddl-for-articles-that-are-included-in-transactional-replication-in-sql-server-2014-or-in-sql-server-2016-sp1) 具有這項改良功能的更多詳細資料。

  
## <a name="see-also"></a>另請參閱  
 [發行資料和資料庫物件](../../../relational-databases/replication/publish/publish-data-and-database-objects.md)   
 [重新初始化訂閱](../../../relational-databases/replication/reinitialize-subscriptions.md)   
 [對發行集資料庫進行結構描述變更](../../../relational-databases/replication/publish/make-schema-changes-on-publication-databases.md)  
  
  
