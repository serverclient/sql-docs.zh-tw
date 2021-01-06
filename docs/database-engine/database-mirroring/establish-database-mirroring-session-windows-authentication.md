---
title: 設定資料庫鏡像 (Windows 驗證)
description: 了解如何使用 SQL Server Management Studio 設定使用 Windows 驗證的資料庫鏡像工作階段。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], sessions
ms.assetid: 7cb418d6-dce1-4a0d-830e-9c5ccfe3bd72
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: ece2afa5381b2692c3cb3b99358fc02891e75e97
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644301"
---
# <a name="establish-database-mirroring-session---windows-authentication"></a>建立資料庫鏡像工作階段 - Windows 驗證
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
    
> [!NOTE]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)] 請改用 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]。  
  
 若要建立資料庫鏡像工作階段，並修改資料庫的資料庫鏡像屬性，請使用 **[資料庫屬性]** 對話方塊的 **[鏡像]** 頁面。使用 **[鏡像]** 頁面來設定資料庫鏡像之前，請先確定您是否已經符合下列需求：  
  
-   主體和鏡像伺服器執行個體必須執行相同的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本 (Standard 或 Enterprise)。 此外，我們強烈建議您在可比較而且可以處理相同工作負載的系統上執行這些伺服器執行個體。  
  
    > [!NOTE]  
    >  並非所有 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本都可使用見證伺服器執行個體。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本支援的功能清單，請參閱 [SQL Server 2016 版本支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)。  
  
-   鏡像資料庫必須存在而且保持在最新狀態。  
  
     建立鏡像資料庫會需要在鏡像伺服器執行個體上還原最近的主體資料庫備份 (使用 WITH NORECOVERY)。 此外，在完整備份之後還需要建立一或多個記錄備份，並將這些記錄備份依序還原至鏡像資料庫 (使用 WITH NORECOVERY)。 如需詳細資訊，請參閱 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)。  
  
-   如果伺服器執行個體正在不同的網域使用者帳戶下執行，每個執行個體都會需要登入其他執行個體的 **master** 資料庫。 如果登入不存在，您必須先建立登入，然後再設定鏡像。 如需詳細資訊，請參閱 [使用 Windows 驗證允許資料庫鏡像的網路存取 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-allow-network-access-windows-authentication.md)。  
  
### <a name="to-configure-database-mirroring"></a>若要設定資料庫鏡像  
  
1.  連接到主體伺服器執行個體後，在 [物件總管] 中按一下伺服器名稱，以展開伺服器樹狀目錄。  
  
2.  展開 **[資料庫]** ，然後選取要鏡像的資料庫。  
  
3.  以滑鼠右鍵按一下資料庫，選取 [工作]  ，然後按一下 [鏡像]  。 這將會開啟在 **[資料庫屬性]** 對話方塊中的 **[鏡像]** 頁面。  
  
4.  若要開始設定鏡像，請按一下 **[設定安全性]** 按鈕，啟動「設定資料庫鏡像安全性精靈」。  
  
    > [!NOTE]  
    >  在資料庫鏡像工作階段中，您只能使用這個精靈來加入或變更見證伺服器執行個體。  
  
5.  「設定資料庫鏡像安全性精靈」會自動在每個伺服器執行個體上建立資料庫鏡像端點 (如果沒有端點的話)，並在伺服器執行個體角色 ( **[主體]** 、 **[鏡像]** 或 **[見證]** ) 的對應欄位中輸入其伺服器網路位址。  
  
    > [!IMPORTANT]  
    >  建立端點時，「設定資料庫鏡像安全性精靈」一定會使用 Windows 驗證。 鏡像端點必須已經設定成使用每個伺服器執行個體的憑證，然後您才能使用此精靈搭配以憑證為基礎的驗證。 此外，精靈之 **[服務帳戶]** 對話方塊中的所有欄位都必須保持空白。 如需建立資料庫鏡像端點以便使用憑證的相關資訊，請參閱 [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/create-endpoint-transact-sql.md)。  
  
6.  選擇性地變更作業模式。 有些作業模式的可用性，取決於您是否已指定見證的 TCP 位址。 選項如下：  
  
    |選項|見證？|說明|  
    |------------|--------------|-----------------|  
    |**高效能 (非同步)**|Null (若存在，則不使用，但工作階段需要仲裁)|為了將效能發揮到極致，鏡像資料庫的狀態總是會比主體資料庫有些延遲，永遠無法真正的同步。 然而，在資料庫之間的間距通常很小。 夥伴的遺失將具有下列結果：<br /><br /> 如果鏡像伺服器執行個體已無法使用，主體將繼續。<br /><br /> 如果主體伺服器執行個體無法使用，鏡像就會停止。但是，如果此工作階段沒有見證 (符合建議) 或者見證連接至鏡像伺服器，此鏡像伺服器就可以當做暖待命伺服器存取。然後，資料庫擁有者可以對鏡像伺服器執行個體進行強制服務 (有遺失資料的可能)。<br /><br /> <br /><br /> 如需詳細資訊，請參閱 [資料庫鏡像工作階段期間的角色切換 &#40;SQL Server&#41;](../../database-engine/database-mirroring/role-switching-during-a-database-mirroring-session-sql-server.md)版本都可使用見證伺服器執行個體。|  
    |**不具有自動容錯移轉的高安全性 (同步)**|否|保證會將所有已認可的交易都寫入鏡像伺服器上的磁碟。<br /><br /> 當夥伴相互連接，而且資料庫已同步處理時，可以進行手動容錯移轉。<br /><br /> 夥伴的遺失將具有下列結果：<br /><br /> 如果鏡像伺服器執行個體已無法使用，主體將繼續。<br /><br /> 如果主體伺服器執行個體無法使用，鏡像將停止，但可以當做暖待命伺服器存取。然後，資料庫擁有者可以對鏡像伺服器執行個體進行強制服務 (有遺失資料的可能)。<br /><br /> <br /><br /> 如需詳細資訊，請參閱 [資料庫鏡像工作階段期間的角色切換 &#40;SQL Server&#41;](../../database-engine/database-mirroring/role-switching-during-a-database-mirroring-session-sql-server.md)版本都可使用見證伺服器執行個體。|  
    |**具有自動容錯移轉的高安全性 (同步)**|是 (必要)|保證會將所有已認可的交易都寫入鏡像伺服器上的磁碟。<br /><br /> 經由納入見證伺服器執行個體來支援自動容錯移轉，藉以得到最大化的可用性。 請注意，您必須先指定見證伺服器位址，才能選取 **[具有自動容錯移轉的高安全性 (同步)]** 選項。<br /><br /> 當夥伴相互連接，而且資料庫已同步處理時，可以進行手動容錯移轉。<br /><br /> 在有見證的情況下，夥伴的遺失將具有下列結果：<br /><br /> 如果主體伺服器執行個體已無法使用，將發生自動容錯移轉。 鏡像伺服器執行個體將切換到主體的角色，並且提供它的資料庫做為主體資料庫。<br /><br /> 如果鏡像伺服器執行個體已無法使用，主體將繼續。<br /><br /> <br /><br /> 如需詳細資訊，請參閱 [資料庫鏡像工作階段期間的角色切換 &#40;SQL Server&#41;](../../database-engine/database-mirroring/role-switching-during-a-database-mirroring-session-sql-server.md)版本都可使用見證伺服器執行個體。<br /><br /> **&#42;&#42; 重要事項 &#42;&#42;** 如果見證中斷連線，則必須將夥伴相互連線，才能使用資料庫。 如需詳細資訊，請參閱[仲裁：見證如何影響資料庫可用性 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/quorum-how-a-witness-affects-database-availability-database-mirroring.md)。|  
  
7.  若下列條件均符合，請按一下 **[啟動鏡像]** 開始鏡像作業：  
  
    -   您目前連接到主體伺服器執行個體。  
  
    -   安全性已正確設定。  
  
    -   已指定主體及鏡像伺服器執行個體的完整 TCP 位址 (在 **[伺服器網路位址]** 區段中)。  
  
    -   如果作業模式設定為 **[具有自動容錯移轉的高安全性 (同步)]** ，也會指定見證伺服器執行個體的完整 TCP 位址。  
  
8.  在鏡像開始後，您可以變更作業模式，並且按一下 **[確定]** 以儲存變更。 請注意，您必須先指定見證伺服器位址，才能透過自動容錯移轉切換到高安全性模式。  
  
    > [!NOTE]  
    >  若要移除見證，請從 **[見證]** 欄位刪除其伺服器網路位址。 如果從具有自動容錯移轉的高安全性模式切換到高效能模式，則會自動清除 [見證]  欄位。  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像工作階段期間的角色切換 &#40;SQL Server&#41;](../../database-engine/database-mirroring/role-switching-during-a-database-mirroring-session-sql-server.md)   
 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)   
 [資料庫屬性 &#40;鏡像頁面&#41;](../../relational-databases/databases/database-properties-mirroring-page.md)   
 [暫停或繼續資料庫鏡像工作階段 &#40;SQL Server&#41;](../../database-engine/database-mirroring/pause-or-resume-a-database-mirroring-session-sql-server.md)   
 [設定鏡像資料庫以使用 Trustworthy 屬性 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/set-up-a-mirror-database-to-use-the-trustworthy-property-transact-sql.md)   
 [移除資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/remove-database-mirroring-sql-server.md)   
 [角色切換後針對登入和作業進行管理 &#40;SQL Server&#41;](../../sql-server/failover-clusters/management-of-logins-and-jobs-after-role-switching-sql-server.md)   
 [設定資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)   
 [在另一個伺服器執行個體上提供可用的資料庫時，管理中繼資料 &#40;SQL Server&#41;](../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)   
 [新增或取代資料庫鏡像見證 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/add-or-replace-a-database-mirroring-witness-sql-server-management-studio.md)  
  
  
