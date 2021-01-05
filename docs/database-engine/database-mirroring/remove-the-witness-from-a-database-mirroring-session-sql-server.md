---
title: 移除資料庫鏡像見證
description: 描述如何使用 SQL Server Management Studio (SSMS) 或 Transact-SQL (T-SQL) 從資料庫鏡像工作階段移除見證。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- witness [SQL Server], turning off
- witness [SQL Server], removing
- database mirroring [SQL Server], witness
ms.assetid: f3ce7afc-8936-4d35-80ce-d0f8fbc318d3
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 8653d60228d2b1a72b5a25001d2f1212824b2608
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641319"
---
# <a name="remove-the-witness-from-a-database-mirroring-session-sql-server"></a>從資料庫鏡像工作階段移除見證 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 或 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] ，在 [!INCLUDE[tsql](../../includes/tsql-md.md)]中移除資料庫鏡像工作階段內的見證。 在資料庫鏡像工作階段期間，資料庫擁有者隨時可以關閉資料庫鏡像工作階段的見證。  
  
 **本主題內容**  
  
-   **開始之前：**  
  
     [安全性](#Security)  
  
-   **若要取代移除見證，請使用：**  
  
     [Transact-SQL](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
-   **後續操作：** [移除見證之後](#FollowUp)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要資料庫的 ALTER 權限。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
  
#### <a name="to-remove-the-witness"></a>移除見證  
  
1.  連接到主體伺服器執行個體，在 **[物件總管]** 窗格中按一下伺服器名稱，以展開伺服器樹狀目錄。  
  
2.  展開 **[資料庫]** ，然後選取要移除見證的資料庫。  
  
3.  以滑鼠右鍵按一下資料庫，選取 [工作]  ，然後按一下 [鏡像]  。 這將會開啟在 **[資料庫屬性]** 對話方塊中的 **[鏡像]** 頁面。  
  
4.  若要移除見證，請從 **[見證]** 欄位刪除其伺服器網路位址。  
  
    > [!NOTE]  
    >  如果從具有自動容錯移轉的高安全性模式切換到高效能模式，則會自動清除 [見證]  欄位。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
  
#### <a name="to-remove-the-witness"></a>移除見證  
  
1.  連接到任一夥伴伺服器執行個體上的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 。  
  
2.  在標準列中，按一下 **[新增查詢]** 。  
  
3.  發出下列陳述式：  
  
     [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql-database-mirroring.md) 資料庫名稱  SET WITNESS OFF  
  
     其中 *database_name* 是鏡像資料庫的名稱。  
  
     下列範例會從 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫中移除見證。  
  
    ```  
    ALTER DATABASE AdventureWorks2012 SET WITNESS OFF ;  
    ```  
  
##  <a name="follow-up-after-removing-the-witness"></a><a name="FollowUp"></a> 後續操作：移除見證之後  
 關閉見證會根據交易安全性設定來變更 [作業模式](../../database-engine/database-mirroring/database-mirroring-operating-modes.md)：  
  
-   如果交易安全性設定為 FULL (預設值)，則工作階段會使用高安全性的同步模式，而且不包含自動容錯移轉。  
  
-   如果交易安全性設定為 OFF，則工作階段會以非同步方式作業 (以高效能模式)，而且不需要仲裁。 每當交易安全性關閉時，我們也強烈建議您關閉見證。  
  
> [!TIP]  
>  資料庫的交易安全性設定會記錄在每個夥伴之 **mirroring_safety_level** 和 **mirroring_safety_level_desc** 資料行的 [sys.database_mirroring](../../relational-databases/system-catalog-views/sys-database-mirroring-transact-sql.md) 目錄檢視中。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [使用 Windows 驗證新增資料庫鏡像見證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/add-a-database-mirroring-witness-using-windows-authentication-transact-sql.md)  
  
-   [新增或取代資料庫鏡像見證 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/add-or-replace-a-database-mirroring-witness-sql-server-management-studio.md)  
  
## <a name="see-also"></a>另請參閱  
 [ALTER DATABASE 資料庫鏡像 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-database-mirroring.md)   
 [變更資料庫鏡像工作階段中的交易安全性 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/change-transaction-safety-in-a-database-mirroring-session-transact-sql.md)   
 [使用 Windows 驗證新增資料庫鏡像見證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/add-a-database-mirroring-witness-using-windows-authentication-transact-sql.md)   
 [資料庫鏡像見證](../../database-engine/database-mirroring/database-mirroring-witness.md)  
  
  
