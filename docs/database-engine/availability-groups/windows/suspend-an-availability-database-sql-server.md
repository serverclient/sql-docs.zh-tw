---
title: 暫止可用性資料庫
description: 了解如何使用 SQL Server Management Studio、Transact-SQL 或 SQL PowerShell，將 Always On 可用性群組內資料庫的資料移動暫止。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.availabilitygroup.suspenddatamove.f1
helpviewer_keywords:
- secondary databases [SQL Server], in availability group
- primary databases [SQL Server], in availability group
- Availability Groups [SQL Server], suspending a database
- Availability Groups [SQL Server], databases
ms.assetid: 86858982-6af1-4e80-9a93-87451f0d7ee9
author: cawrites
ms.author: chadam
ms.openlocfilehash: f918daa031b25ba8f1110dc9f2aaeaebef0a9361
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642959"
---
# <a name="suspend-an-availability-database-sql-server"></a>暫止可用性資料庫 (SQL Server)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  您可以使用 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 、 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]或 [!INCLUDE[tsql](../../../includes/tsql-md.md)]的 PowerShell，暫停 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]中的可用性資料庫。 請注意，暫停命令必須在裝載要暫停或回復之資料庫的伺服器執行個體上發出。  
  
 暫停命令的效果取決於您暫停的是次要資料庫或主要資料庫，如下所示：  
  
|暫停的資料庫|暫停命令的效果|  
|------------------------|-------------------------------|  
|次要資料庫|只有本機次要資料庫會暫停，而且其同步處理狀態會變成 NOT SYNCHRONIZING。 其他次要資料庫不受影響。 暫停的資料庫會停止接收和套用資料 (記錄檔記錄)，並且開始落後於主要資料庫。 可讀取次要複本上的現有連接會保持可用狀態。 在資料移動繼續執行之前，不允許可讀取次要複本上已暫停之資料庫的新連接。 只有在使用接聽程式和唯讀路由開啟連線時，此行為才適用。<br /><br /> 主要資料庫仍然可用。 如果您暫停每個對應的次要資料庫，則主要資料庫會公開執行。<br /><br /> **\*\* 重要事項 \*\*** 次要資料庫暫停期間，所對應之主要資料庫的傳送佇列將會累積未傳送的交易記錄檔記錄。 次要複本的連接會傳回在資料移動暫停時可用的資料。|  
|主要資料庫|主要資料庫會停止將資料移動到每個連接的次要資料庫。 主要資料庫會繼續以公開模式執行。 主要資料庫仍然可供用戶端使用，而可讀取次要資料庫上的現有連接仍然可以使用，並且可以建立新連接。|  
  
> [!NOTE]  
>  暫停 AlwaysOn 次要資料庫不會直接影響主要資料庫的可用性， 但暫停次要資料庫可能會影響其備援及容錯移轉功能。 這與資料庫鏡像相反，在資料庫鏡像中，鏡像狀態會在鏡像資料庫和主體資料庫上暫停。 暫停 AlwaysOn 主要資料庫會暫停所有對應次要資料庫上的資料移動，而且該資料庫的備援和容錯移轉功能也會停止，直到主要資料庫繼續為止。  
  
-   **開始之前：**  
  
     [限制事項](#Restrictions)  
  
     [先決條件](#Prerequisites)  
  
     [建議](#Recommendations)  
  
     [安全性](#Security)  
  
-   **若要使用下列項目暫停資料庫：**  
  
-   [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
     [PowerShell](#PowerShellProcedure)  
  
-   **後續操作：** [避免填滿交易記錄](#FollowUp)  
  
-   [相關工作](#RelatedTasks)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項  
 一旦裝載目標資料庫的複本接受 SUSPEND 命令之後，就會將其傳回，但暫停資料庫實際上是以非同步方式進行。  
  
###  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
 您必須連接到裝載要暫停之資料庫的伺服器執行個體。 若要暫停主要資料庫和對應的次要資料庫，請連接到裝載主要複本的伺服器執行個體。 若要暫停次要資料庫，同時保留主要資料庫可用狀態，請連接到次要複本。  
  
###  <a name="recommendations"></a><a name="Recommendations"></a> 建議  
 出現瓶頸時，短暫暫停一個或多個次要資料庫，可能有助於暫時改善主要複本的效能。 只要次要資料庫保持暫停狀態，對應主要資料庫的交易記錄便無法截斷。 這會導致記錄檔記錄在主要資料庫上累積。 因此，我們建議您盡快恢復 (或移除) 暫停的次要資料庫。 如需詳細資訊，請參閱本主題稍後的[後續操作：避免填滿交易記錄](#FollowUp)。  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要資料庫的 ALTER 權限。  
  
 需要可用性群組的 ALTER AVAILABILITY GROUP 權限、CONTROL AVAILABILITY GROUP 權限、ALTER ANY AVAILABILITY GROUP 權限或 CONTROL SERVER 權限。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 **若要暫停資料庫**  
  
1.  在 [物件總管] 中，連接到裝載您要暫停資料庫之可用性複本的伺服器執行個體，然後展開伺服器樹狀目錄。 如需詳細資訊，請參閱本主題前面的＜ [必要條件](#Prerequisites)＞。  
  
2.  依序展開 [Always On 高可用性] 節點和 [可用性群組] 節點。  
  
3.  展開可用性群組。  
  
4.  展開 [可用性資料庫] 節點，以滑鼠右鍵按一下資料庫，然後按一下 [暫停進行資料移動]。  
  
5.  在 **[暫停資料移動]** 對話方塊中，按一下 **[確定]** 。  
  
     [物件總管] 會透過變更資料庫圖示以顯示暫停指標，指出資料庫已暫停。  
  
> [!NOTE]  
>  若要暫停此複本位置的其他資料庫，請針對每個資料庫重複步驟 4 和 5。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
 **若要暫停資料庫**  
  
1.  連接至裝載您要暫停其資料庫之複本的伺服器執行個體。 如需詳細資訊，請參閱本主題前面的＜ [必要條件](#Prerequisites)＞。  
  
2.  使用下列 [ALTER DATABASE](../../../t-sql/statements/alter-database-transact-sql-set-hadr.md) 陳述式暫停資料庫：  
  
     ALTER DATABASE *database_name* SET HADR SUSPEND;
  
##  <a name="using-powershell"></a><a name="PowerShellProcedure"></a> 使用 PowerShell  
 **若要暫停資料庫**  
  
1.  將目錄 (**cd**) 變更為裝載您要暫停其資料庫之複本的伺服器執行個體。 如需詳細資訊，請參閱本主題前面的＜ [必要條件](#Prerequisites)＞。  
  
2.  使用 **Suspend-SqlAvailabilityDatabase** Cmdlet 暫停可用性群組。  
  
     例如，下列命令會針對 `MyDb3` 伺服器執行個體上 `MyAg` 可用性群組中的 `Computer\Instance`可用性資料庫暫停資料同步處理。  
  
    ```  
    Suspend-SqlAvailabilityDatabase `   
    -Path SQLSERVER:\Sql\Computer\Instance\AvailabilityGroups\MyAg\Databases\MyDb3  
    ```  
  
    > [!NOTE]  
    >  若要檢視 Cmdlet 的語法，請在 **PowerShell 環境中使用** Get-Help [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Cmdlet。 如需詳細資訊，請參閱 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)。  
  
 **若要設定和使用 SQL Server PowerShell 提供者**  
  
-   [SQL Server PowerShell 提供者](../../../powershell/sql-server-powershell-provider.md)  
  
##  <a name="follow-up-avoiding-a-full-transaction-log"></a><a name="FollowUp"></a> 後續操作：避免填滿交易記錄  
 一般而言，在資料庫上執行自動檢查點時，交易記錄會在下一個記錄備份之後，截斷至該檢查點。 但在次要資料庫暫停時，所有目前的記錄檔記錄仍在主要資料庫作用中。 如果交易記錄已填滿 (因為已達到最大值，或者伺服器執行個體用盡空間)，資料庫就不能再執行其他更新。  
  
 若要避免這個問題，您應該執行下列其中一項工作：  
  
-   為主要資料庫增加更多的記錄空間。  
  
-   在日誌填滿之前恢復次要資料庫。 如需詳細資訊，請參閱本主題稍後的＜ [繼續可用性資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/resume-an-availability-database-sql-server.md)中的可用性資料庫。  
  
-   移除次要資料庫。 如需詳細資訊，請參閱[將次要資料庫從可用性群組移除 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server.md)。  
  
 **若要對寫滿交易記錄進行疑難排解**  
  
-   [寫滿交易記錄疑難排解 &#40;SQL Server 錯誤 9002&#41;](../../../relational-databases/logs/troubleshoot-a-full-transaction-log-sql-server-error-9002.md)  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [繼續可用性資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/resume-an-availability-database-sql-server.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [繼續可用性資料庫 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/resume-an-availability-database-sql-server.md)  
  
