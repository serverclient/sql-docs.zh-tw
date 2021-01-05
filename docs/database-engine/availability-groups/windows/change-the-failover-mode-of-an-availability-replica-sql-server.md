---
title: 變更可用性群組的複本容錯移轉模式
description: 描述如何使用 Transact-SQL (T-SQL)、PowerShell 或 SQL Server Management Studio，變更 Always On 可用性群組內複本的容錯移轉模式。
ms.custom: seo-lt-2019
ms.date: 06/30/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- failover modes [SQL Server]
- Availability Groups [SQL Server], deploying
- Availability Groups [SQL Server], failover modes
- Availability Groups [SQL Server], configuring
ms.assetid: 619a826f-8e65-48eb-8c34-39497d238279
author: cawrites
ms.author: chadam
ms.openlocfilehash: e24bd2b51945f69d86fe887b2c4a4c1c5e17c773
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643111"
---
# <a name="change-the-failover-mode-for-a-replica-within-an-always-on-availability-group"></a>變更 Always On 可用性群組內複本的容錯移轉模式
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 、 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]或 PowerShell，在 [!INCLUDE[tsql](../../../includes/tsql-md.md)]中變更 AlwaysOn 可用性群組內可用性複本的容錯移轉模式。 容錯移轉模式是複本屬性，用於判斷以同步認可可用性模式下執行之複本的容錯移轉模式。 如需詳細資訊，請參閱 [容錯移轉及容錯移轉模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md) 和 [可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)。  
  
## <a name="prerequisites-and-restrictions"></a><a name="Prerequisites"></a> 必要條件和限制  
  
-   只有在主要複本上才支援這個工作。 您必須連接到裝載主要複本的伺服器執行個體。  
  
-   SQL Server 容錯移轉叢集執行個體 (FCI) 不支援依照可用性群組進行自動容錯移轉，因此任何由 FCI 裝載的可用性複本只能設定為手動容錯移轉。  
  

##  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要可用性群組的 ALTER AVAILABILITY GROUP 權限、CONTROL AVAILABILITY GROUP 權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 **若要變更可用性複本的容錯移轉模式**  
  
1.  在 [物件總管] 中，連接到裝載主要複本的伺服器執行個體，然後展開伺服器樹狀目錄。  
  
2.  依序展開 [Always On 高可用性]  節點和 [可用性群組]  節點。  
  
3.  按一下要變更複本的可用性群組。  
  
4.  以滑鼠右鍵按一下複本，然後按一下 [屬性]  。  
  
5.  在 **[可用性複本屬性]** 對話方塊中，使用 **[容錯移轉模式]** 下拉式清單來變更此複本的容錯移轉模式。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
 **若要變更可用性複本的容錯移轉模式**  
  
1.  連接到裝載主要複本的伺服器執行個體。  
  
2.  使用 [ALTER AVAILABILITY GROUP](../../../t-sql/statements/alter-availability-group-transact-sql.md) 陳述式，如下所示：

    ```syntaxsql
    ALTER AVAILABILITY GROUP *group_name* MODIFY REPLICA ON '*server_name*'  
       WITH ( {  
             AVAILABILITY_MODE = { SYNCHRONOUS_COMMIT | ASYNCHRONOUS_COMMIT }
                | FAILOVER_MODE = { AUTOMATIC | MANUAL }
             }  )
    ```
    
    在前一段指令碼中：

    - *group_name* 是可用性群組的名稱。  
  
    - <伺服器名稱>  是電腦名稱或容錯移轉叢集網路名稱。 具名執行個體請新增 `\instance_name'。 使用裝載想要修改複本的名稱。
  
如需這些參數的詳細資訊，請參閱 [ALTER AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-availability-group-transact-sql.md)。  
  
下列範例 (在 *MyAG* 可用性群組的主要複本上輸入) 會針對位於 *COMPUTER01* 電腦的預設伺服器執行個體上的可用性複本，將容錯移轉模式變更為自動容錯移轉。  
  
```sql
ALTER AVAILABILITY GROUP MyAG MODIFY REPLICA ON 'COMPUTER01' WITH  
    (FAILOVER_MODE = AUTOMATIC);  
```  
  
##  <a name="using-powershell"></a><a name="PowerShellProcedure"></a> 使用 PowerShell  
 **若要變更可用性複本的容錯移轉模式**  
  
1.  變更目錄 (**cd**) 為裝載主要複本的伺服器執行個體。  
  
2.  使用 **Set-SqlAvailabilityReplica** Cmdlet 搭配 **FailoverMode** 參數。 將複本設定為自動容錯移轉時，您可能需要使用 **AvailabilityMode** 參數將複本變更成同步認可的可用性模式。  
  
    例如，下列命令會將可用性群組 `MyReplica` 中的複本 `MyAg` 修改成使用同步認可的可用性模式並且支援自動容錯移轉。  
  
    ```powershell
    Set-SqlAvailabilityReplica -AvailabilityMode "SynchronousCommit" -FailoverMode "Automatic" `   
    -Path SQLSERVER:\Sql\PrimaryServer\InstanceName\AvailabilityGroups\MyAg\Replicas\MyReplica  
    ```  
  
    > [!NOTE]  
    >  若要檢視 Cmdlet 的語法，請在 **PowerShell 環境中使用** Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Cmdlet。 如需詳細資訊，請參閱 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)。  
  
 **若要設定和使用 SQL Server PowerShell 提供者**  
  
-   [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [可用性模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/availability-modes-always-on-availability-groups.md)   
 [容錯移轉及容錯移轉模式 &#40;AlwaysOn 可用性群組&#41;](../../../database-engine/availability-groups/windows/failover-and-failover-modes-always-on-availability-groups.md)  
  
