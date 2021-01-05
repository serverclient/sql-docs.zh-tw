---
title: PowerShell：可用性群組資料庫鏡像端點
description: 描述如何使用 PowerShell 針對 Always On 可用性群組建立資料庫鏡像端點。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], server instance
- Availability Groups [SQL Server], deploying
- Availability Groups [SQL Server], endpoint
ms.assetid: 6197bbe7-67d4-446d-ba5f-cabfa5df77f1
author: cawrites
ms.author: chadam
ms.openlocfilehash: b9b1be7e6cbfead5bf0fa14bf60f294ad852d9ad
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643230"
---
# <a name="create-a-database-mirroring-endpoint-for-an-availability-group-using-powershell"></a>使用 PowerShell 針對可用性群組建立資料庫鏡像端點
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  此主題描述如何使用 PowerShell，在 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 中建立 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 所用的資料庫鏡像端點。  
  

  
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要 CREATE ENDPOINT 權限或系統管理員 (sysadmin) 固定伺服器角色的成員資格。 如需詳細資訊，請參閱 [GRANT 端點權限 &#40;Transact-SQL&#41;](../../../t-sql/statements/grant-endpoint-permissions-transact-sql.md)。  

> [!IMPORTANT]  
>  RC4 演算法已被取代。 [!INCLUDE[ssNoteDepFutureDontUse](../../../includes/ssnotedepfuturedontuse-md.md)] 我們建議您改用 AES。  
  
##  <a name="using-powershell"></a><a name="PowerShellProcedure"></a> 使用 PowerShell  
 **若要建立資料庫鏡像端點**  
  
1.  將目錄 (**cd**) 變更為您要建立資料庫鏡像端點的伺服器執行個體。  
  
2.  使用 **New-SqlHadrEndpoint** Cmdlet 建立端點，然後使用 **Set-SqlHadrEndpoint** 啟動端點。  
  
###  <a name="example-powershell"></a><a name="PShellExample"></a> 範例 (PowerShell)  
 下列 PowerShell 命令會在 SQL Server 執行個體 (機器  \\執行個體  ) 上建立資料庫鏡像端點。 此端點使用通訊埠 5022。  
  
> [!IMPORTANT]  
>  這個範例只適用於目前缺少資料庫鏡像端點的伺服器執行個體。  
  
```  
# Create the endpoint.  
$endpoint = New-SqlHadrEndpoint MyMirroringEndpoint -Port 5022 -Path SQLSERVER:\SQL\Machine\Instance  
  
# Start the endpoint  
Set-SqlHadrEndpoint -InputObject $endpoint -State "Started"  
  
```  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要設定資料庫鏡像端點**  
  
-   [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)  
  
-   [使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)  
  
    -   [允許資料庫鏡像端點使用輸出連線的憑證 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/database-mirroring-use-certificates-for-outbound-connections.md)  
  
    -   [允許資料庫鏡像端點使用輸入連線的憑證 &#40;Transact-SQL&#41;](../../../database-engine/database-mirroring/database-mirroring-use-certificates-for-inbound-connections.md)  
  
-   [指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)  
  
-   [在新增或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)  
  
 **若要檢視有關資料庫鏡像端點的資訊**  
  
-   [sys.database_mirroring_endpoints &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md)  
  
## <a name="see-also"></a>另請參閱  
 [建立可用性群組 &#40;Transact-SQL&#41;](../../../database-engine/availability-groups/windows/create-an-availability-group-transact-sql.md)   
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)  
  
  
