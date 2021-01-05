---
title: 將次要資料庫從可用性群組移除
description: 使用 Transact-SQL (T-SQL)、PowerShell 或 SQL Server Management Studio 將次要資料庫從 Always On 可用性群組移除的步驟。
ms.custom: seodec18
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.availabilitygroup.unjoindb.f1
helpviewer_keywords:
- secondary databases [SQL Server], in availability group
- Availability Groups [SQL Server], removing
- Availability Groups [SQL Server], databases
ms.assetid: 4e51a570-58d7-4f01-9390-4198f3602576
author: cawrites
ms.author: chadam
ms.openlocfilehash: 03d286ae75d9bfeeb1304633b4ddf1f790979fdf
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642465"
---
# <a name="remove-a-secondary-database-from-an-availability-group-sql-server"></a>將次要資料庫從可用性群組移除 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]、 [!INCLUDE[tsql](../../../includes/tsql-md.md)]或 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]中的 PowerShell，將次要資料庫從 AlwaysOn 可用性群組中移除。  
   
  
##  <a name="prerequisites-and-restrictions"></a><a name="Prerequisites"></a> 必要條件和限制  
  
-   只有在次要複本上才支援這個工作。 您必須連接到裝載要從中移除資料庫之次要複本的伺服器執行個體。  
  
 
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要資料庫的 ALTER 權限。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 **若要從可用性群組中移除次要資料庫**  
  
1.  在 [物件總管] 中，連接到裝載您要從中移除一個或多個次要資料庫之次要複本的伺服器執行個體，然後展開伺服器樹狀目錄。  
  
2.  依序展開 [Always On 高可用性]  節點和 [可用性群組]  節點。  
  
3.  選取可用性群組，然後展開 **[可用性資料庫]** 節點。  
  
4.  此步驟取決於您要移除多個資料庫群組或只要移除一個資料庫，如下所示：  
  
    -   若要移除多個資料庫，請使用 **[物件總管詳細資料]** 窗格檢視及選取您要移除的所有資料庫。 如需詳細資訊，請參閱[使用物件總管詳細資料監視可用性群組 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-object-explorer-details-to-monitor-availability-groups.md)。  
  
    -   若要移除單一資料庫，請在 **[物件總管]** 窗格或 **[物件總管詳細資料]** 窗格中選取它。  
  
5.  以滑鼠右鍵按一下選取的一或多個資料庫，然後在命令功能表中選取 [移除次要資料庫]  。  
  
6.  在 **[從可用性群組移除資料庫]** 對話方塊中，若要移除所有列出的可用性資料庫，請按一下 **[確定]** 。 如果您不要移除所有列出的資料庫，請按一下 **[取消]** 。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
 **若要從可用性群組中移除次要資料庫**  
  
1.  連接到裝載次要複本的伺服器執行個體。  
  
2.  使用 [ALTER DATABASE 陳述式的 SET HADR 子句](../../../t-sql/statements/alter-database-transact-sql-set-hadr.md) ，如下所示：  
  
     ALTER DATABASE *database_name* SET HADR OFF  
  
     其中 *database_name* 是次要資料庫的名稱，您要從其所屬的可用性群組移除此資料庫。  
  
     下列範例會將本機次要資料庫 *MyDb2* 從其可用性群組中移除。  
  
    ```  
    ALTER DATABASE MyDb2 SET HADR OFF;  
    GO  
    ```  
  
##  <a name="using-powershell"></a><a name="PowerShellProcedure"></a> 使用 PowerShell  
 **若要從可用性群組中移除次要資料庫**  
  
1.  將目錄切換到 (**cd**) 裝載次要複本的伺服器執行個體。  
  
2.  使用 **Remove-SqlAvailabilityDatabase** 指令程式，指定要從可用性群組移除的可用性資料庫名稱。 連接到裝載次要複本的伺服器執行個體時，只會從可用性群組移除本機次要資料庫。  
  
     例如，下列命令會從伺服器執行個體 `MyDb8` 所裝載的次要複本中移除次要資料庫 `SecondaryComputer\Instance`。 已移除之次要資料庫的資料同步處理會停止。 此命令不會影響主要資料庫或任何其他次要資料庫。  
  
    ```  
    Remove-SqlAvailabilityDatabase `  
    -Path SQLSERVER:\Sql\SecondaryComputer\InstanceName\AvailabilityGroups\MyAg\Databases\MyDb8  
    ```  
  
    > [!NOTE]  
    >  若要檢視 Cmdlet 的語法，請在 **PowerShell 環境中使用** Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Cmdlet。 如需詳細資訊，請參閱 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)。  
  
 **若要設定和使用 SQL Server PowerShell 提供者**  
  
-   [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md)  
  
##  <a name="follow-up-after-removing-a-secondary-database-from-an-availability-group"></a><a name="FollowUp"></a> 待處理：從可用性群組中移除次要資料庫之後  
 次要資料庫已移除時，它不再聯結至可用性群組，而且可用性群組會將移除之次要資料庫的所有相關資訊捨棄。 移除的次要資料庫處於 RESTORING 狀態。  
  
> [!TIP]  
>  在移除次要資料庫後的短暫時間內，您可能可以透過將次要資料庫重新聯結至可用性群組，在資料庫上重新啟動 AlwaysOn 資料同步處理。 如需詳細資訊，請參閱 [將次要資料庫聯結至可用性群組 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/join-a-secondary-database-to-an-availability-group-sql-server.md)。  
  
 此時有替代方法可處理移除的次要資料庫：  
  
-   如果您不再需要此次要資料庫，可將它卸除。  
  
     如需詳細資訊，請參閱 [DROP DATABASE &#40;Transact-SQL &#41;](../../../t-sql/statements/drop-database-transact-sql.md) 或[刪除資料庫](../../../relational-databases/databases/delete-a-database.md)。  
  
-   從可用性群組中移除次要資料庫之後，若要存取移除的次要資料庫，可以復原資料庫。 不過，如果復原移除的次要資料庫，線上將會有兩個名稱相同但內容不同的獨立資料庫。 您必須確認用戶端只可以存取目前的主要資料庫。  
  
     如需詳細資訊，請參閱[復原資料庫而不還原資料 &#40;Transact-SQL&#41;](../../../relational-databases/backup-restore/recover-a-database-without-restoring-data-transact-sql.md)。  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [將主要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-primary-database-from-an-availability-group-sql-server.md)  
  
