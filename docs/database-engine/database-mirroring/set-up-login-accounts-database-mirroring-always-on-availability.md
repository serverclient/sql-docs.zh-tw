---
title: 設定登入帳戶 (鏡像與可用性群組)
description: 設定登入帳戶來存取資料庫鏡像或 Always On 可用性群組的資料庫鏡像端點。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], deployment
- logins [SQL Server], database mirroring
ms.assetid: e9f5287b-1325-4cda-88a6-19eaaa52a652
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: ea62f1f934527ce40e2b5fe2f03b8b6c1b8e49bc
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641978"
---
# <a name="set-up-login-accounts---database-mirroring-always-on-availability"></a>設定登入帳戶 - 資料庫鏡像 AlwaysOn 可用性
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  若要讓兩個伺服器執行個體連接到對方的 [資料庫鏡像端點](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md) ，這兩個執行個體的登入帳戶都必須要有對方的存取權。 而且，這兩個登入帳戶都必須要有連接權限來連接到對方的資料庫鏡像端點。  
  
 此需求的影響視伺服器執行個體是否以相同網域使用者帳戶執行而定：  
  
-   如果伺服器執行個體是以相同的網域使用者帳戶執行，兩個 **master** 資料庫中都會自動存在正確的使用者登入。 這樣可簡化資料庫鏡像和 Always On 可用性群組的安全性組態。  
  
-   如果伺服器執行個體是以不同使用者帳戶執行，則裝載主體伺服器或主要複本的伺服器執行個體上的使用者登入必須以手動方式重新產生在裝載鏡像伺服器的伺服器執行個體或裝載次要複本的每個伺服器執行個體上。 如需詳細資訊，請參閱本主題稍後的 [＜為不同的帳戶建立登入＞](#CreateLogin) 和 [＜授與連接權限＞](#GrantConnect)。  
  
    > [!IMPORTANT]  
    >  若要建立更安全的環境，請考慮針對每個伺服器執行個體使用不同的網域帳戶。  
  
##  <a name="create-a-login-for-a-different-account"></a><a name="CreateLogin"></a> ＜為不同的帳戶建立登入＞  
 如果兩個伺服器執行個體是以不同的帳戶執行，系統管理員必須使用 CREATE LOGIN [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，為每個伺服器執行個體之遠端執行個體的啟動服務帳戶建立登入。 如需詳細資訊，請參閱 [CREATE LOGIN &#40;Transact-SQL&#41;](../../t-sql/statements/create-login-transact-sql.md)。  
  
> [!IMPORTANT]  
>  如果您是在非網域帳戶之下執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，則必須使用憑證。 如需詳細資訊，請參閱[使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)。  
  
 例如，若要讓伺服器執行個體 sqlA (在 loginA 之下執行) 連接到伺服器執行個體 sqlB (在 loginB 之下執行)，則 loginA 必須在 sqlB 中，而 loginB 必須在 sqlA 中。 此外，若資料庫鏡像工作階段中包含見證伺服器執行個體 (sqlC)，而且其中的三個伺服器執行個體都是在不同的網域帳戶下執行，則必須建立下列登入：  
  
|在執行個體|建立登入及授與連接權限給...|  
|--------------------|--------------------------------------------------------------|  
|sqlA|sqlB 和 sqlC|  
|sqlB|sqlA 和 sqlC|  
|sqlC|sqlA 與 sqlB|  
  
> [!NOTE]  
>  您也可以利用電腦帳戶代替網域使用者，與網路服務帳戶連接。 如果使用電腦帳戶，則必須將該帳戶新增為其他個伺服器執行個體的使用者。  
  
##  <a name="grant-connect-permission"></a><a name="GrantConnect"></a> ＜授與連接權限＞  
 一旦在伺服器執行個體上建立登入，就必須授權該登入來連接伺服器執行個體的資料庫鏡像端點。 系統管理員可使用 GRANT [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式來授與連接權限。 如需詳細資訊，請參閱 [GRANT &#40;Transact-SQL&#41;](../../t-sql/statements/grant-transact-sql.md)的相關資訊。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [建立登入](../../relational-databases/security/authentication-access/create-a-login.md)  
  
-   [使用 Windows 驗證允許資料庫鏡像端點的網路存取 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-allow-network-access-windows-authentication.md)。  
  
-   [使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)   
 [為資料庫鏡像組態進行疑難排解 &#40;SQL Server&#41; &#40;SQL Server&#41;](../../database-engine/database-mirroring/troubleshoot-database-mirroring-configuration-sql-server.md)   
 [疑難排解 AlwaysOn 可用性群組組態 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/troubleshoot-always-on-availability-groups-configuration-sql-server.md)  
  
  
