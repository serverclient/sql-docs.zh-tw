---
title: 設定資料庫鏡像 (SQL Server) | Microsoft Docs
description: 了解在 SQL Server 中設定資料庫鏡像的先決條件、建議與步驟，包括資料庫鏡像工作階段的概觀。
ms.custom: ''
ms.date: 05/17/2016
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], deployment
ms.assetid: da45efed-55eb-4c71-be34-ac2589dfce8d
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: b2166452c4b1c6f87b907c2329a897e9c9041b75
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643204"
---
# <a name="setting-up-database-mirroring-sql-server"></a>設定資料庫鏡像 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  本節描述的是設定資料庫鏡像的必要條件、建議事項及步驟。 如需資料庫鏡像的簡介，請參閱 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)。  
  
> [!IMPORTANT]  
>  我們建議您在離峰時間設定資料庫鏡像，因為組態會影響效能。  
  
  
##  <a name="preparing-a-server-instance-to-host-a-mirror-server"></a><a name="PrepareInstances"></a> 準備伺服器執行個體以裝載鏡像伺服器  
 在資料庫鏡像工作階段中：  
  
1.  主體伺服器、鏡像伺服器和見證 (如果有的話) 必須是由位於個別主機系統上的個別伺服器執行個體所裝載。 每一個伺服器執行個體都需要資料庫鏡像端點。 如果您需要建立資料庫鏡像端點，請確定其他伺服器執行個體能夠存取它。  
  
     伺服器執行個體用於資料庫鏡像的驗證格式，是其資料庫鏡像端點的屬性。 有兩種傳輸安全性可用於資料庫鏡像：Windows 驗證或以憑證為基礎的驗證。 如需詳細資訊，請參閱[資料庫鏡像和 AlwaysOn 可用性群組的傳輸安全性 &#40;SQL Server&#41;](../../database-engine/database-mirroring/transport-security-database-mirroring-always-on-availability.md)。  
  
     網路存取的需求與驗證形式相關，如下所示：  
  
    -   **如果使用 Windows 驗證**  
  
         如果伺服器執行個體正在不同的網域使用者帳戶下執行，每個執行個體都會需要登入其他執行個體的 **master** 資料庫。 如果登入不存在，您就必須自行建立。 如需詳細資訊，請參閱 [使用 Windows 驗證允許資料庫鏡像的網路存取 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-allow-network-access-windows-authentication.md)。  
  
    -   **如果使用憑證**  
  
         若要啟用某伺服器執行個體上資料庫鏡像的憑證驗證，系統管理員必須設定每一個伺服器執行個體，才能同時在傳出和傳入的連接使用憑證。 您必須先設定傳出連接。 如需詳細資訊，請參閱[使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)。  
  
2.  確定所有資料庫使用者的登入都存在於鏡像伺服器上。 如需詳細資訊，請參閱 [設定資料庫鏡像或 AlwaysOn 可用性群組的登入帳戶 &#40;SQL Server&#41;](../../database-engine/database-mirroring/set-up-login-accounts-database-mirroring-always-on-availability.md)。  
  
3.  在即將裝載鏡像資料庫的伺服器執行個體上，設定鏡像資料庫所需的其餘環境。 如需詳細資訊，請參閱 [在另一個伺服器執行個體上提供可用的資料庫時，管理中繼資料 &#40;SQL Server&#41;](../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)。  
  
##  <a name="overview-establishing-a-database-mirroring-session"></a><a name="EstablishUsingWinAuthentication"></a> 概觀：建立資料庫鏡像工作階段  
 建立鏡像工作階段的基本步驟如下：  
  
1.  在每項還原作業上使用 RESTORE WITH NORECOVERY，透過還原下列備份來建立鏡像資料庫：  
  
    1.  在確定建立備份時主體資料庫已經使用完整復原模式之後，還原主體資料庫最近的完整資料庫備份。 鏡像資料庫必須擁有與主體資料庫相同的名稱。  
  
    2.  如果自還原完整備份之後您已經建立任何差異資料庫備份，請還原最近的差異備份。  
  
    3.  還原自從完整或差異資料庫備份後進行的所有記錄備份。  
  
     如需詳細資訊，請參閱 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)。  
  
    > [!IMPORTANT]  
    >  進行主體資料庫的備份後，請儘快完成剩下的設定步驟。 在夥伴上啟動鏡像之前，您應該在原始資料庫上建立目前的記錄備份，並將它還原到未來的鏡像資料庫。  
  
2.  您可以使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或「資料庫鏡像精靈」來設定鏡像。 如需詳細資訊，請參閱下列其中之一：  
  
    -   [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-establish-session-windows-authentication.md)  
  
    -   [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)  
  
3.  依預設，工作階段會設定為完整交易安全性 (SAFETY 設定為 FULL)，它會以不含自動容錯移轉的同步高安全性模式啟動工作階段。 您可以依照下列方式，將工作階段重新設定為在具有自動容錯移轉的高安全性模式下執行，或在非同步的高效能模式下執行：  
  
    -   **具有自動容錯移轉的高安全性模式**  
  
         如果您想讓高安全性模式工作階段支援自動容錯移轉，請加入見證伺服器執行個體。  
  
         **加入見證**  
  
        -   [使用 Windows 驗證新增資料庫鏡像見證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/add-a-database-mirroring-witness-using-windows-authentication-transact-sql.md)  
  
        -   [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)  
  
        > [!NOTE]  
        >  資料庫擁有者可以隨時關閉資料庫的見證。 見證關閉後相當於沒有見證，不會發生自動容錯移轉。  
  
    -   **高效能模式**  
  
         或者，如果您不想進行自動容錯移轉，而且較注重效能而非可用性，請關閉交易安全性。 如需詳細資訊，請參閱[在資料庫鏡像工作階段中變更交易安全性 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/change-transaction-safety-in-a-database-mirroring-session-transact-sql.md)。  
  
        > [!NOTE]  
        >  在高效能模式中，WITNESS 需要設定為 OFF。 如需詳細資訊，請參閱[仲裁：見證如何影響資料庫可用性 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/quorum-how-a-witness-affects-database-availability-database-mirroring.md)。  
  
> [!NOTE]  
>  如需使用 Microsoft Windows 驗證以 [!INCLUDE[tsql](../../includes/tsql-md.md)] 設定資料庫鏡像的範例，請參閱[範例：使用 Windows 驗證設定資料庫鏡像 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql.md)。  
>   
>  如需使用以憑證為基礎的安全性以 [!INCLUDE[tsql](../../includes/tsql-md.md)] 設定資料庫鏡像範例，請參閱[範例：使用憑證設定資料庫鏡像 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/example-setting-up-database-mirroring-using-certificates-transact-sql.md)。  
  
  
##  <a name="in-this-section"></a><a name="InThisSection"></a> 本節內容  
 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)  
 摘要說明在繼續進行暫停的工作階段之前，建立鏡像資料庫或準備鏡像資料庫的步驟。 同時提供如何主題的連結。  
  
 [指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)  
 描述伺服器網路位址的語法、位址如何識別伺服器執行個體的資料庫鏡像端點，以及如何找出系統的完整網域名稱。  
  
 [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)  
 描述如何使用設定資料庫鏡像安全性精靈，在資料庫上啟動資料庫鏡像。  
  
 [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-establish-session-windows-authentication.md)  
 描述設定資料庫鏡像的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 步驟。  
  
 [範例：使用 Windows 驗證設定資料庫鏡像 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql.md)  
 包含一則範例，內容說明使用 Windows 驗證建立具有見證之資料庫鏡像工作階段的所有必要階段。  
  
 [範例：使用憑證設定資料庫鏡像 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/example-setting-up-database-mirroring-using-certificates-transact-sql.md)  
 包含一則範例，內容說明使用以憑證為基礎的驗證建立具有見證之資料庫鏡像工作階段的所有必要階段。  
  
 [設定資料庫鏡像或 AlwaysOn 可用性群組的登入帳戶 &#40;SQL Server&#41;](../../database-engine/database-mirroring/set-up-login-accounts-database-mirroring-always-on-availability.md)  
 描述針對使用與本機伺服器執行個體不同之帳戶的遠端伺服器執行個體，建立登入。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **SQL Server Management Studio**  
  
-   [啟動設定資料庫鏡像安全性精靈 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-the-configuring-database-mirroring-security-wizard.md)  
  
-   [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)  
  
 **Transact-SQL**  
  
-   [使用 Windows 驗證允許資料庫鏡像的網路存取 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-allow-network-access-windows-authentication.md)  
  
-   [允許資料庫鏡像端點使用輸出連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-outbound-connections.md)  
  
-   [允許資料庫鏡像端點使用輸入連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-inbound-connections.md)  
  
-   [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)  
  
-   [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-establish-session-windows-authentication.md)  
  
-   [使用 Windows 驗證新增資料庫鏡像見證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/add-a-database-mirroring-witness-using-windows-authentication-transact-sql.md)  
  
-   [設定鏡像資料庫以使用 Trustworthy 屬性 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/set-up-a-mirror-database-to-use-the-trustworthy-property-transact-sql.md)  
  
 **Transact-SQL/SQL Server Management Studio**  
  
-   [升級鏡像執行個體](../../database-engine/database-mirroring/upgrading-mirrored-instances.md)  
  
-   [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)  
  
-   [疑難排解資料庫鏡像組態 &#40;SQL Server&#41;](../../database-engine/database-mirroring/troubleshoot-database-mirroring-configuration-sql-server.md)  
  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)   
 [資料庫鏡像：互通性與共存性 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-interoperability-and-coexistence-sql-server.md)   
 [資料庫鏡像和 Always On 可用性群組的傳輸安全性 &#40;SQL Server&#41;](../../database-engine/database-mirroring/transport-security-database-mirroring-always-on-availability.md)   
 [指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)  
  
  
