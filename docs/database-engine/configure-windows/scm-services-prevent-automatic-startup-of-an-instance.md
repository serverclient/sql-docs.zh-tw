---
title: SCM 服務 - 避免自動啟動執行個體 | Microsoft Docs
description: 了解如何防止 SQL Server 的執行個體自動啟動。 查看如何將啟動模式設定為手動完成此工作。
ms.custom: ''
ms.date: 01/06/2016
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- automatic SQL Server startup
- SQL Server, stopping
- SQL Server, automatic startup
- canceling automatic startup
- stopping SQL Server
- preventing automatic startups [SQL Server]
ms.assetid: 782663cf-f3d7-4cc6-b621-21e4550f0322
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 643c21ea1e854dd7fa65798505b6f7d2225da62a
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170950"
---
# <a name="scm-services---prevent-automatic-startup-of-an-instance"></a>SCM 服務 - 避免自動啟動執行個體
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 SQL Server 組態管理員，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中防止 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 執行個體自動啟動。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 通常會設定為自動啟動。 不過您也可以將該執行個體的啟動模式改設為手動。  
  
##  <a name="using-sql-server-configuration-manager"></a><a name="SSMSProcedure"></a> 使用 SQL Server 組態管理員  
  
#### <a name="to-prevent-automatic-startup-of-an-instance-of-sql-server"></a>避免自動啟動 SQL Server 的執行個體  
  
1.  指向 **[開始]** 功能表上的 **[所有程式]** ，然後依序指向 [ [!INCLUDE[ssCurrentUI](../../includes/sscurrentui-md.md)]] 和 **[組態工具]** ，再按一下 **[SQL Server 組態管理員]** 。  
  
    > [!NOTE]  
    >  因為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員是 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 管理主控台程式的嵌入式管理單元，而不是獨立的程式，所以 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員在較新版本的 Windows 中不會作為應用程式出現。  
    >   
    >  -   **Windows 10**：  
    >          若要開啟 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員，請在 **起始頁** 上輸入 SQLServerManager13.msc (適用於 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)])。 若為舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，則以較小的數字取代 13。 按一下 SQLServerManager13.msc 即可開啟組態管理員。 若要將組態管理員釘選到起始頁或工作列，請以滑鼠右鍵按一下 SQLServerManager13.msc，然後按一下 [開啟檔案位置]。 在 Windows 檔案總管中，以滑鼠右鍵按一下 SQLServerManager13.msc，然後按一下 [釘選到 [開始] 功能表] 或 [釘選到工作列]。  
    > -   **Windows 8**：  
    >          若要開啟 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員，請在 [搜尋] 常用鍵的 [應用程式] 底下，輸入 **SQLServerManager\<version>.msc** (例如 **SQLServerManager13.msc**)，然後按 **Enter**。  
  
2.  在 [SQL Server 組態管理員] 中展開 [服務]，然後按一下 [SQL Server]。  
  
3.  在 [詳細資料] 窗格中，以滑鼠右鍵按一下 [MSSQLServer]，然後按一下 [屬性]。  
  
4.  在 [SQL Server \<**_instancename_**> 屬性] 對話方塊之 [服務] 索引標籤的 [一般] 方塊中，將 [啟動模式] 的值設定為 [手動]。  
  
5.  按一下 [確定] 以關閉 [SQL Server \<**_instancename_**> 屬性] 對話方塊，然後關閉 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員。  
  
## <a name="see-also"></a>另請參閱  
 [啟動、停止、暫停、繼續、重新啟動 Database Engine、SQL Server Agent 或 SQL Server Browser 服務](../../database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services.md)  
  
  
