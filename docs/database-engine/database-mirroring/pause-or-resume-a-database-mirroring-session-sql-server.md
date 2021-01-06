---
title: 暫停與繼續資料庫鏡像工作階段
description: 了解如何使用 SQL Server Management Studio 或 Transact-SQL (T-SQL) 來暫停和繼續 SQL Server 資料庫鏡像工作階段。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- resuming database mirroring
- database mirroring [SQL Server], sessions
- database mirroring [SQL Server], pausing
- database mirroring [SQL Server], resuming
- pausing database mirroring
ms.assetid: 05ede3b4-6abe-4442-abb7-9f5aee1d6bc0
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: dad800c08e44975716c9bd4f32d0e716400a1ae7
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644287"
---
# <a name="pause-or-resume-a-database-mirroring-session-sql-server"></a>暫停或繼續資料庫鏡像工作階段 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 或 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] ，在 [!INCLUDE[tsql](../../includes/tsql-md.md)]中暫停或繼續資料庫鏡像。  
  
 **本主題內容**  
  
-   **開始之前：**  
  
     [安全性](#Security)  
  
-   **使用下列方法來執行 ReplaceThisText：**  
  
     [Transact-SQL](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
-   **後續操作：** [暫停或繼續資料庫鏡像之後](#FollowUp)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
 您可以在任何時候暫停資料庫鏡像工作階段，此工作階段可能會在發生瓶頸時提高效能，而且您可以隨時繼續暫停的工作階段。  
  
> [!CAUTION]  
>  在強制服務之後，當原始主體伺服器重新連接時，便會暫停鏡像。 在這種情況下繼續執行鏡像，很可能會造成原始主體伺服器上的資料遺失。 如需管理潛在資料遺失的資訊，請參閱[資料庫鏡像工作階段期間的角色切換 &#40;SQL Server&#41;](../../database-engine/database-mirroring/role-switching-during-a-database-mirroring-session-sql-server.md)。  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要資料庫的 ALTER 權限。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 若要暫停或繼續資料庫鏡像工作階段，請使用 **[資料庫屬性鏡像]** 頁面。  
  
#### <a name="to-pause-or-resume-database-mirroring"></a>若要暫停或繼續資料庫鏡像  
  
1.  在資料庫鏡像工作階段過程中，連接到主體伺服器執行個體，在 [物件總管] 中按一下伺服器名稱，以展開伺服器樹狀目錄。  
  
2.  展開 **[資料庫]** 並選取資料庫。  
  
3.  以滑鼠右鍵按一下資料庫，選取 [工作]  ，然後按一下 [鏡像]  。 這將會開啟在 **[資料庫屬性]** 對話方塊中的 **[鏡像]** 頁面。  
  
4.  若要暫停工作階段，請按一下 **[暫停]** 。  
  
     會出現提示字元要求確認；如果您按一下 **[是]** ，工作階段將暫停，然後按鈕將變更為 **[繼續]** 。  
  
     如需暫停工作階段之影響的詳細資訊，請參閱[暫停與繼續資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/pausing-and-resuming-database-mirroring-sql-server.md)。  
  
5.  若要繼續工作階段，請按一下 **[繼續]** 。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
  
#### <a name="to-pause-database-mirroring"></a>若要暫停資料庫鏡像  
  
1.  連接到任一個夥伴的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 。  
  
2.  在標準列中，按一下 **[新增查詢]** 。  
  
3.  發出下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式：  
  
     ALTER DATABASE *database_name* SET PARTNER SUSPEND  
  
     其中 *database_name* 是您要暫停其工作階段的鏡像資料庫。  
  
     下列範例會暫停 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 範例資料庫。  
  
    ```  
    ALTER DATABASE AdventureWorks2012 SET PARTNER SUSPEND;  
    ```  
  
##### <a name="to-resume-database-mirroring"></a>若要繼續資料庫鏡像  
  
1.  連接到任一個夥伴的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 。  
  
2.  在標準列中，按一下 **[新增查詢]** 。  
  
3.  發出下列 Transact-SQL 陳述式：  
  
     ALTER DATABASE *database_name* SET PARTNER RESUME  
  
     其中 *database_name* 是您要繼續其工作階段的鏡像資料庫。  
  
     下列範例會暫停 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 範例資料庫。  
  
    ```  
    ALTER DATABASE AdventureWorks2012 SET PARTNER RESUME;  
    ```  
  
##  <a name="follow-up-after-pausing-or-resuming-database-mirroring"></a><a name="FollowUp"></a> 後續操作：暫停或繼續資料庫鏡像之後  
  
-   **暫停資料庫鏡像之後**  
  
     在主要資料庫上，採取預防措施來避免交易記錄變滿。 如需詳細資訊，請參閱 [交易記錄 &#40;SQL Server&#41;](../../relational-databases/logs/the-transaction-log-sql-server.md)。  
  
-   **繼續資料庫鏡像之後**  
  
     繼續資料庫鏡像會使鏡像資料庫處於 SYNCHRONIZING 狀態。 若安全性層級為 FULL，則鏡像會追趕上主體，且鏡像資料庫會進入 SYNCHRONIZED 狀態。 此時即有可能發生容錯移轉。 若見證存在並處於 ON 的狀態，就有可能發生自動容錯移轉。 若見證不存在，則有可能發生手動容錯移轉。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [移除資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/remove-database-mirroring-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)  
  
  
