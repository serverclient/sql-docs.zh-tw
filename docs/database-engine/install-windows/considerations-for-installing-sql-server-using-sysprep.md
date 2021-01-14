---
title: 使用 SysPrep 安裝 SQL Server
description: SQL Server Sysprep 可供在電腦上準備 SQL Server 的獨立執行個體，並於稍後完成設定。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
ms.assetid: e1792eeb-2874-4653-b20e-3063f4eb4e5d
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 127d1a0bcac97d2b802460549b38c79012f71329
ms.sourcegitcommit: 3ec49252e82590de0fe559a8574606ae213f6f3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97975419"
---
# <a name="considerations-for-installing-sql-server-using-sysprep"></a>使用 SysPrep 安裝 SQL Server 的考量

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep 可讓您在電腦上準備 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的獨立執行個體，並於稍後完成設定。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep 需要使用包含兩個步驟的程序來取得已設定的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]獨立執行個體。 這些步驟包含以下內容：  
  
- [準備映像](#BKMK_PrepareImage)  
  
    這個步驟會在安裝產品二進位編碼檔案之後停止安裝程序，而不會針對正在準備的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體設定電腦、網路或帳戶特有的資訊。  
  
- [完成映像](#BKMK_CompleteImage)  
  
    這個步驟可讓您完成已備妥之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體的組態。 在此步驟中，您可以提供電腦、網路和帳戶特有的資訊。  
  
如需如何使用 SysPrep 安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的詳細資訊，請參閱[使用 SysPrep 安裝 SQL Server](../../database-engine/install-windows/install-sql-server-using-sysprep.md)。  
  
## <a name="common-uses-for-ssnoversion-sysprep"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep 的常見用法  
您可依照下列任何方法使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep 功能：  
  
- 您可以使用「準備映像」步驟，在同一部電腦上準備一個或多個未設定的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 您可以在同一部電腦上使用完整的映像步驟來設定這些備妥的執行個體。  
  
- 您可以擷取備妥之執行個體的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式組態檔，並將它用於準備多部電腦上的其他未設定的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體，以供日後組態設定使用。  
  
- 當結合 Windows 系統準備工具 (也稱為 Windows SysPrep) 時，您可以建立作業系統的映像，包括來源電腦上未設定的已備妥 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。 然後，您可以將作業系統映像部署到多部電腦上。 當您完成作業系統的組態之後，可以使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式的「完成映像」步驟來設定備妥的執行個體。  
  
    Windows SysPrep 工具可用來準備 Windows 作業系統映像， 其目的是為了擷取作業系統的自訂映像，以供整個組織部署。 如需 SysPrep 及其用途的詳細資訊，請參閱 [SysPrep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。  
  
## <a name="installation-media-considerations"></a>安裝媒體考量  
 如果您使用完整版的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，請考量以下事項：  
  
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的非 Express 版：  
  
    - 「準備映像」步驟會使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Evaluation Edition 來安裝產品二進位編碼檔案。 當此執行個體完成時， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的版本取決於「完成映像」步驟中所提供的產品識別碼。  
  
    - 如果您提供 Evaluation Edition 產品識別碼，評估期間將會在備妥的執行個體完成後的 180 天到期。  
  
- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Express 版：  
  
    - 若要準備 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Express 版本的執行個體，請使用 Express 安裝媒體。  
  
    - 您不能指定備妥之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Express 版本執行個體的產品識別碼。  
  
## <a name="supported-ssnoversion-installations"></a>支援的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝  
[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 的 SysPrep 支援所有功能，包括 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的工具。  
  
您可以針對 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 或之前版本的並存安裝準備多個執行個體。 這些執行個體的功能必須支援 SysPrep。  
  
當準備映像步驟結束時，將會自動安裝及完成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 當您準備 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體時，也會自動準備瀏覽器與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]寫入器。 當您在完成映像步驟中完成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，便會完成這兩個項目。  
  
如需支援的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本資訊，請參閱 [SQL Server 2017 的版本和支援功能](../../sql-server/editions-and-components-of-sql-server-2017.md)及 [SQL Server 2019](../../sql-server/editions-and-components-of-sql-server-version-15.md)  
  
您可以執行版本升級，同時設定備妥的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Express 版本不支援這個選項。  
  
從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]開始， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep 支援從命令列進行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集安裝。  
  
## <a name="ssnoversion-sysprep-limitations"></a>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] SysPrep 限制  
不支援修復備妥的執行個體。 如果安裝程式在準備映像或完成映像步驟期間失敗，您必須執行解除安裝。  
  
##  <a name="prepare-image"></a><a name="BKMK_PrepareImage"></a> 準備映像  
準備映像步驟會安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 產品和功能，但不會設定安裝。  
  
您可以在這個步驟中，指定要安裝的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 功能及 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 產品安裝檔的安裝位置。 若要準備 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體，可以在 [安裝中心] 的 [進階] 頁面上透過 [Image Preparation of a stand-alone instance for SysPrep deployment (準備 SysPrep 部署獨立執行個體的映像)] 或是從命令提示字元執行。  
  
- 您可以在同一部電腦上準備多個可於稍後完成的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。  
  
- 您可以從現有的已備妥 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體中，新增或移除 SysPrep 安裝所支援的功能。  
  
 當準備好執行個體之後，就可以使用 **[開始]** 功能表上的捷徑，讓您完成已備妥之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體的組態。  
  
##  <a name="complete-image"></a><a name="BKMK_CompleteImage"></a> 完成映像  
您可以使用下列其中一個方法，完成已備妥的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體：  
  
- 使用 [開始] 功能表上的捷徑。  
  
- 在 [安裝中心] 的 [進階] 頁面上存取 [Image completion of a prepared stand-alone instance (完成備妥之獨立執行個體的映像)] 步驟。  
  
## <a name="see-also"></a>另請參閱  
[規劃 SQL Server 安裝](../../sql-server/install/planning-a-sql-server-installation.md)