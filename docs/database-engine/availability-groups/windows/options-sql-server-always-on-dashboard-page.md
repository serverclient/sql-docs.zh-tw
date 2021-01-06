---
title: SSMS 中的可用性群組儀表板
description: 了解可在 SQL Server Management Studio 中的 [SQL Server Always On] 儀表板上找到的 [選項] 頁面。
ms.custom: seodec18
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- VS.ToolsOptionsPages.Alwayson.Dashboard
ms.assetid: 4369b588-e982-4b57-80a1-beb2e879ce0b
author: cawrites
ms.author: chadam
ms.openlocfilehash: b465e35420035a9c6c10bd8db53a680baf57a4da
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643564"
---
# <a name="options-sql-server-always-on-dashboard-page"></a>選項 (SQL Server AlwaysOn、儀表板頁面)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  您可以使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] [選項] 對話方塊的 [SQL Server AlwaysOn 儀表板] 頁面來設定 AlwaysOn 儀表板。  
  
 **若要存取此頁面：**  
  
 在 [工具]  功能表上按一下 [選項]  ，展開 [SQL Server Always]  資料夾，然後按一下 [儀表板]  。  
  
## <a name="on-this-page"></a>在此頁面上  
 **開啟自動重新整理**  
 按一下以啟用自動重新整理。 可用選項包括：  
  
-   [重新整理間隔 (秒)]  欄位顯示儀表板將會重新整理的間隔秒數。 預設值是 30。 已啟用自動重新整理時，您可以編輯此欄位變更重新整理間隔。  
  
-   **[連線重試次數]** 顯示儀表板嘗試連接到 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體的次數，此執行個體裝載儀表板正在監視之可用性群組的可用性複本。 預設值為 65535。 已啟用自動重新整理時，您可以編輯此欄位變更連線重試次數。  
  
 **啟用您的使用者定義 AlwaysOn 原則。**  
 如果您已經定義自己的 AlwaysOn 原則，請按一下此選項以啟用您的原則。  
  
## <a name="see-also"></a>另請參閱  
 [使用 AlwaysOn 儀表板 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-always-on-dashboard-sql-server-management-studio.md)  
  
  
