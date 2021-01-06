---
title: 主體伺服器執行個體 (設定資料庫鏡像安全性精靈)
description: SQL Server Management Studio 中 [設定資料庫鏡像安全性] 精靈的 [主體伺服器執行個體] 頁面的描述。
ms.custom: seo-lt-2019
ms.date: 03/07/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
f1_keywords:
- sql13.swb.configdbmsecurwiz.principalsrvr.f1
ms.assetid: 58af27d7-c5dd-4669-be6b-b472bc2c8ef4
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: fc2a6f30a4832fb19856b19eb47dde63e449bd20
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644228"
---
# <a name="principal-server-instance-configure-database-mirroring-security-wizard"></a>主體伺服器執行個體 (設定資料庫鏡像安全性精靈)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  使用此頁面來指定有關主體資料庫之伺服器執行個體的資訊。 主體資料庫是開始鏡像工作階段的資料庫副本。 工作階段開始之後，主體資料庫是接受使用者變更的資料庫副本。 (發生容錯移轉時，主體與鏡像角色會交換，所以初始的主體資料庫可能不再是主體資料庫)。  
  
 **若要使用 SQL Server Management Studio 設定資料庫鏡像**  
  
-   [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)  
  
-   [啟動設定資料庫鏡像安全性精靈 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-the-configuring-database-mirroring-security-wizard.md)  
  
## <a name="options"></a>選項。  
 **主體伺服器執行個體**  
 因為 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 中的資料庫鏡像一律從主體伺服器設定，所以目前的伺服器執行個體一律為主體伺服器執行個體。  
  
 **接聽程式連接埠**  
 此選項的行為會視此伺服器執行個體的鏡像端點是否存在，如下：  
  
-   如果這個伺服器執行個體沒有接聽程式通訊埠，在 **[通訊埠]** 文字方塊中就會顯示通訊埠編號 5022。 您可以使用任何可用的通訊埠編號，例如 7022。  
  
-   當鏡像端點已經存在時，會顯示來自端點的通訊埠編號。 如果您需要變更通訊埠，請使用 ALTER ENDPOINT 命令。 如需詳細資訊，請參閱 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)。  
  
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
  
  
