---
title: 資料庫需求
description: 使用 Master Data Services 組態管理員來建立和設定 Master Data Services 資料庫，以儲存所有主要資料。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: mds
ms.reviewer: ''
ms.technology: master-data-services
ms.topic: conceptual
ms.assetid: fe731839-c5c4-4884-bb6a-644eca28bb30
author: lrtoyou1223
ms.author: lle
ms.openlocfilehash: 21224e52ae9109f5404fc6a6ee11ad59e525995b
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171170"
---
# <a name="database-requirements-master-data-services"></a>資料庫需求 (Master Data Services)

[!INCLUDE [SQL Server Windows Only - ASDBMI ](../../includes/applies-to-version/sql-windows-only-asdbmi.md)]

  所有主要資料都儲存在 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] 資料庫中。 裝載這個資料庫的電腦必須執行的實例 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssDE](../../includes/ssde-md.md)] 。  
  
 使用 [!INCLUDE[ssMDScfgmgr](../../includes/ssmdscfgmgr-md.md)] 可在本機或遠端電腦上建立及設定 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] 資料庫。 如果您將資料庫從某個環境移到另一個環境，就可以將 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] Web 服務和 [!INCLUDE[ssMDSmdm](../../includes/ssmdsmdm-md.md)] 關聯至位於新位置的資料庫，藉以在新的環境中維護資訊。  
  
> [!NOTE]  
>  安裝 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] 元件的任何電腦都必須獲得授權。 如需詳細資訊，請參閱使用者授權合約 (EULA)。  
  
## <a name="requirements"></a>規格需求  
 建立 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] 資料庫之前，請確定是否符合下列需求。  
  
### <a name="sql-server-edition"></a>SQL Server 版本  
 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] 資料庫可以裝載在下列 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本上：  
  
 
-   [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] Enterprise (64 位元) x64  
  
-   [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] Developer (64 位元) x64  
  
-   [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] Business Intelligence (64 位元) x64  
  
-   [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] Enterprise (64 位元) x64  
  
-   [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] Developer (64 位元) x64  
  
-   [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] Business Intelligence (64 位元) x64  
  
-   [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] Enterprise (64 位元) x64 - 只能從 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] Enterprise 升級  
  
-   [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] Developer (64 位元) x64  
  
-   Microsoft SQL Server 2008 R2 Enterprise (64 位元) x64  
  
-   Microsoft SQL Server 2008 R2 Developer (64 位元) x64  
  
 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本支援的功能清單，請參閱 [SQL Server 2016 版本支援的功能](../../sql-server/editions-and-components-of-sql-server-2016.md)。 
  
### <a name="operating-system"></a>作業系統  
 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssDE](../../includes/ssde-md.md)] 支援的 Windows 作業系統和其他需求的相關資訊，請參閱[安裝 SQL Server 2016 的硬體與軟體需求](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)。  
  
### <a name="accounts-and-permissions"></a>帳戶和權限  
  
|類型|描述|  
|----------|-----------------|  
|使用者帳戶|在 [!INCLUDE[ssMDScfgmgr](../../includes/ssmdscfgmgr-md.md)]中，您可以使用 Windows 帳戶或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 帳戶連接到 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，來主控 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] 資料庫。 此使用者帳戶必須屬於  執行個體的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssDE](../../includes/ssde-md.md)]. 如需有關 **系統管理員** 角色的詳細資訊，請參閱 [伺服器層級角色](../../relational-databases/security/authentication-access/server-level-roles.md)。|  
|[!INCLUDE[ssMDSmdm](../../includes/ssmdsmdm-md.md)] 系統管理員帳戶|當您建立 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] 資料庫時，必須指定要成為 [!INCLUDE[ssMDSshort](../../includes/ssmdsshort-md.md)] 系統管理員的網域使用者帳戶。 對於所有與這個資料庫有關聯的 [!INCLUDE[ssMDSmdm](../../includes/ssmdsmdm-md.md)] Web 應用程式，這位使用者都可以更新所有功能區域中的所有模型和所有資料。 如需詳細資訊，請參閱系統 [管理員 &#40;Master Data Services&#41;](../../master-data-services/administrators-master-data-services.md)。|  
  
### <a name="database-backup"></a>資料庫備份  
 最佳做法是根據環境的需求，每天在活動量低的時間備份一次完整的資料庫，然後更頻繁地備份交易記錄。 如需資料庫備份的詳細資訊，請參閱[備份概觀 &#40;SQL Server&#41;](../../relational-databases/backup-restore/backup-overview-sql-server.md)。  
  
## <a name="see-also"></a>另請參閱  
 [安裝 Master Data Services](../../master-data-services/install-windows/install-master-data-services.md)   
 [建立 Master Data Services 資料庫](../../master-data-services/install-windows/create-a-master-data-services-database.md)   
 [Master Data Services 資料庫](../../master-data-services/master-data-services-database.md)   
 [[連接到 Master Data Services 資料庫] 對話方塊](../../master-data-services/connect-to-a-master-data-services-database-dialog-box.md)   
 [建立資料庫精靈 &#40;Master Data Services 組態管理員&#41;](../../master-data-services/create-database-wizard-master-data-services-configuration-manager.md)  
  
