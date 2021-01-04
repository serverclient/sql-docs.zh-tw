---
title: 設定進階資料安全性
titleSuffix: Azure Arc
description: 為已啟用 Azure Arc 的 SQL Server 執行個體設定進階資料安全性
author: anosov1960
ms.author: sashan
ms.reviewer: mikeray
ms.date: 09/10/2020
ms.topic: conceptual
ms.prod: sql
ms.openlocfilehash: e0c68752d21de5945c06433af8c08f6208322f7c
ms.sourcegitcommit: 18e2f0706e03d0b2b6324845244fbafaa077a8dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97103165"
---
# <a name="configure-advanced-data-security-for-azure-arc-enabled-sql-server-instance"></a>為已啟用 Azure Arc 的 SQL Server 執行個體設定進階資料安全性

您可以遵循下列步驟，為內部部署 SQL Server 執行個體啟用進階資料安全性。

## <a name="prerequisites"></a>必要條件

* 您的 SQL Server 執行個體會上線至已啟用 Arc 的 SQL Server。 遵循這些指示，[將 SQL Server 執行個體上架至已啟用 Arc 的 SQL Server](connect.md)。

* 系統會為您的使用者帳戶指派其中一個[資訊安全中心角色 (RBAC)](/azure/security-center/security-center-permissions)

## <a name="create-a-log-analytics-workspace"></a>建立 Log Analytics 工作區

1. 搜尋 [Log Analytics 工作區] 資源類型，並透過建立刀鋒視窗新增一個。

   ![建立新的工作區](media/configure-advanced-data-security/create-new-log-analytics-workspace.png)

   > [!NOTE]
   > 您可以使用任何區域中的 Log Analytics 工作區，因此如果您已經有一個工作區，則可加以使用。 但我們建議您在 [電腦 - Azure Arc] 資源建立所在的相同區域中建立工作區。

1. 移至 Log Analytics 工作區資源的概觀頁面，然後選取 [Windows、Linux 及其他來源]。 複製工作區識別碼和主要金鑰以便稍後使用。

   ![Log Analytics 工作區刀鋒視窗](media/configure-advanced-data-security/log-analytics-workspace-blade.png)

## <a name="install-microsoft-monitoring-agent-mma"></a>安裝 Microsoft Monitoring Agent (MMA)

只有尚未在遠端電腦上設定 MMA 代理程式時，才需要執行下一個步驟。

1. 針對 SQL Server 執行個體安裝所在的虛擬或實體伺服器選取 [電腦 - Azure Arc] 資源，然後使用 [延伸模組] 功能新增 [Microsoft Monitoring Agent - Azure Arc] 延伸模組。 當系統要求您設定 Log Analytics 工作區時，請使用您在上一個步驟中儲存的工作區識別碼和主要金鑰。

   ![安裝 MMA](media/configure-advanced-data-security/install-mma-extension.png)

1. 驗證成功之後，按一下 [建立] 以啟動 MMA Arc 延伸模組部署工作流程。 當部署完成時，狀態將會更新為 [成功]。

1. 如需詳細資訊，請參閱[使用 Azure Arc 進行延伸模組管理](/azure/azure-arc/servers/manage-vm-extensions)。

## <a name="enable-azure-defender"></a>啟用 Azure Defender

接下來，您必須啟用適用於 SQL Server 執行個體的 Azure Defender。

1. 移至資訊安全中心，並從資訊看板開啟 [定價和設定] 頁面。

1. 選取您在上一個步驟中為 MMA 延伸模組設定的工作區。

1. 選取 [開啟 Azure Defender]。 確定已開啟 [機器上的 SQL 伺服器] 選項。

   ![升級工作區](media/configure-advanced-data-security/enable-azure-defender.png)

 > [!NOTE]
   > 第一次產生弱點評估的掃描，會在啟用進階資料安全性之後 24 小時內發生。 之後，每週會在星期日執行自動掃描。

## <a name="explore"></a>探索

在 Azure 資訊安全中心探索安全性異常和威脅。

1. 開啟您的 SQL Server – Azure Arc 資源，然後選取左側功能表中的 [安全性]。 查看該執行個體的建議和警示。

   ![選取安全性標題](media/configure-advanced-data-security/security-heading-sql-server-arc.png)

1. 按一下任何建議，以查看資訊安全中心中的弱點詳細資料。

   ![弱點報告](media/configure-advanced-data-security/vulnerabilities-report.png)

1. 按一下任何安全性警示以取得完整詳細資料，並在 [Azure Sentinel](/azure/sentinel/overview) 中進一步探索攻擊。 下圖是暴力密碼破解警示的範例。

   ![暴力密碼破解警示](media/configure-advanced-data-security/brute-force-alert.png)

1. 按一下 [採取動作] 以減輕警示。

   ![警示防護](media/configure-advanced-data-security/brute-force-alert-mitigation.png)

> [!NOTE]
> 頁面頂端的一般 [資訊安全中心] 連結不會使用預覽入口網站 URL，因此您的 [SQL Server - Azure Arc] 資源不會顯示在該處。 我們建議您遵循個別建議或警示的連結。

## <a name="next-steps"></a>下一步

您可以使用 [Azure Sentinel](/azure/sentinel/overview)，進一步調查安全性警示和攻擊。 遵循這些指示來[使 Azure Sentinel 上線](/azure/sentinel/connect-data-sources)。