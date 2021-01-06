---
title: 設定安全性精靈：見證伺服器執行個體
description: '說明「設定資料庫鏡像安全性精靈」的 [見證伺服器執行個體] 頁面。 '
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
f1_keywords:
- sql13.swb.configdbmsecurwiz.witnsrvr.f1
ms.assetid: b5763663-984a-473b-93a3-6cd3322ad41c
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 2b75b77304b4a8f5cc450e82466882d07a1c82d8
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643524"
---
# <a name="witness-server-instance-configure-database-mirroring-security-wizard"></a>見證伺服器執行個體 (設定資料庫鏡像安全性精靈)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  使用此頁面來指定有關做為工作階段見證之伺服器執行個體的資訊。  
  
> [!NOTE]
>  並非所有 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本都可使用見證伺服器執行個體。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本支援的功能清單，請參閱 [SQL Server 2016 版本支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)。  
  
 **若要使用 SQL Server Management Studio 設定資料庫鏡像**  
  
-   [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)  
  
-   [啟動設定資料庫鏡像安全性精靈 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-the-configuring-database-mirroring-security-wizard.md)  
  
## <a name="options"></a>選項。  
 **見證伺服器執行個體**  
 如果已經指定見證伺服器執行個體 (在 [資料庫屬性]  對話方塊的 [鏡像]  頁面上)，就會顯示該執行個體 (如需詳細資訊，請參閱[資料庫屬性 &#40;鏡像頁面&#41;](../../relational-databases/databases/database-properties-mirroring-page.md))。  
  
 否則，此清單方塊會顯示目前伺服器的名稱。 請注意，見證伺服器執行個體不可以與主體或鏡像伺服器執行個體相同。  
  
 **[連接]**  
 如果未指定見證伺服器執行個體，請按一下 [連接]  。 這會顯示 **[連接到伺服器]** 對話方塊，您可以在其中指定伺服器執行個體並建立連接。  
  
 如果已指定執行個體，但精靈缺少具備檢查端點是否存在之權限的連接，請按一下 **[連接]** 。 隨即顯示已預先選取伺服器執行個體的 [連接到伺服器]  對話方塊，且無法變更。 指定具備足夠權限的網域帳戶，然後連接到伺服器執行個體。  
  
> [!NOTE]  
>  連接到伺服器執行個體時，「設定資料庫鏡像安全性精靈」會使用 **[連接到伺服器]** 對話方塊中提供的認證。 這些認證與鏡像工作階段的認證不同，後者會使用以服務方式執行伺服器執行個體之啟動帳戶的認證。  
  
 **接聽程式連接埠**  
 此選項的行為會視此伺服器執行個體的鏡像端點是否存在，如下：  
  
-   如果伺服器執行個體沒有接聽程式通訊埠，在 [通訊埠]  文字方塊中就會顯示通訊埠編號 5022。 您可以輸入任何可用的通訊埠編號，例如 7022。  
  
-   當鏡像端點已經存在時，會顯示來自端點的通訊埠編號。 如果您需要變更該通訊埠，請使用 ALTER ENDPOINT 陳述式。 如需詳細資訊，請參閱 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)。  
  
    > [!NOTE]  
    >  需要通訊埠編號。  
  
 **端點名稱**  
 如果這個伺服器執行個體存在有鏡像端點，該端點名稱會顯示於此處。 如果端點不存在，您可以指定端點名稱。  
  
 **透過此端點傳送的資料要加密**  
 依預設，加密為已啟用。 當已啟用時，加密為必須的 (不只是支援的)，並且所有的加密選項都使用預設值。 如需詳細資訊，請參閱 [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/create-endpoint-transact-sql.md)的相關資訊。  
  
 若要停用加密，請清除該核取方塊。 若要重新啟用加密，請選取該核取方塊。  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)   
 [資料庫屬性 &#40;鏡像頁面&#41;](../../relational-databases/databases/database-properties-mirroring-page.md)   
 [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)   
 [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)   
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)   
 [資料庫鏡像見證](../../database-engine/database-mirroring/database-mirroring-witness.md)  
  
  
