---
title: 手動容錯移轉資料庫鏡像工作階段 (SQL Server Management Studio) | Microsoft Docs
description: 了解如何使用 SQL Server Management Studio，來起始手動容錯移轉至鏡像伺服器。 鏡像資料庫之後會變成主體資料庫。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- failover [SQL Server], database mirroring
- manual failover [SQL Server]
- database mirroring [SQL Server], failover
ms.assetid: 4ecf9c63-b3a4-4c54-b553-5bc37973232b
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 8f01c967758f0603fe044e7449c972a6933f0e30
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642298"
---
# <a name="manually-fail-over-a-database-mirroring-session-sql-server-management-studio"></a>手動容錯移轉資料庫鏡像工作階段 (SQL Server Management Studio)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  鏡像資料庫已同步處理時 (亦即，資料庫為 SYNCHRONIZED 狀態時)，資料庫擁有者可以初始化至鏡像伺服器的手動容錯移轉。  
  
 在手動容錯移轉期間，會針對發生容錯移轉的資料庫互換主體伺服器和鏡像伺服器角色。 鏡像資料庫變成主體資料庫，而主體資料庫變成鏡像。 例如，下表顯示手動容錯移轉如何互換這兩個鏡像夥伴 ( `SQLDBENGINE0_1` 和 `SQLDBENGINE0_2`) 的角色。  
  
|伺服器|容錯移轉前|容錯移轉後|  
|------------|---------------------|--------------------|  
|`SQLDBENGINE0_1`|PRINCIPAL|MIRROR|  
|`SQLDBENGINE0_2`|MIRROR|PRINCIPAL|  
  
 請注意，其他資料庫鏡像工作階段的伺服器角色不受影響。 如需詳細資訊，請參閱 [資料庫鏡像工作階段期間的角色切換 &#40;SQL Server&#41;](../../database-engine/database-mirroring/role-switching-during-a-database-mirroring-session-sql-server.md)版本都可使用見證伺服器執行個體。  
  
### <a name="to-manually-fail-over-database-mirroring"></a>若要手動容錯移轉資料庫鏡像  
  
1.  連接到主體伺服器執行個體，在 **[物件總管]** 窗格中按一下伺服器名稱，以展開伺服器樹狀目錄。  
  
2.  展開 [資料庫]，然後選取要容錯移轉的資料庫。  
  
3.  以滑鼠右鍵按一下資料庫，選取 [工作]，然後按一下 [鏡像]。 這將會開啟在 **[資料庫屬性]** 對話方塊中的 **[鏡像]** 頁面。  
  
4.  按一下 [容錯移轉]。  
  
     確認方塊隨即顯示。  主體伺服器一開始會使用 Windows 驗證來嘗試連接至鏡像伺服器。 如果 Windows 驗證沒有用，主體伺服器就會顯示 [連接到伺服器] 對話方塊。 如果鏡像伺服器使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證，請在 [驗證] 方塊中選取 [SQL Server 驗證]。 在 [登入] 文字方塊中，指定要用來連接至鏡像伺服器的登入帳戶，然後在 [密碼] 文字方塊中，指定該帳戶的密碼。  
  
     如果容錯移轉成功，[資料庫屬性] 對話方塊就會關閉。 鏡像資料庫變成主體資料庫，而主體資料庫變成鏡像。  
  
     如果容錯移轉失敗，則會顯示錯誤訊息，而且對話方塊會保持開啟狀態。  
  
    > [!IMPORTANT]  
    >  如果您在開啟 [鏡像] 頁面後修改了任何屬性，將不會儲存那些變更。  
  
     對話方塊會自動關閉。  
  
## <a name="see-also"></a>另請參閱  
 [資料庫屬性 &#40;鏡像頁面&#41;](../../relational-databases/databases/database-properties-mirroring-page.md)   
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)   
 [手動容錯移轉資料庫鏡像工作階段 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/manually-fail-over-a-database-mirroring-session-transact-sql.md)   
 [暫停或繼續資料庫鏡像工作階段 &#40;SQL Server&#41;](../../database-engine/database-mirroring/pause-or-resume-a-database-mirroring-session-sql-server.md)   
 [移除資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/remove-database-mirroring-sql-server.md)  
  
  
