---
title: 已啟用 Azure Arc 的 SQL Server
titleSuffix: ''
description: 管理已啟用 Azure Arc 之 SQL Server 的 SQL Server 執行個體
author: anosov1960
ms.author: sashan
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: conceptual
ms.prod: sql
ms.custom: references_regions
ms.openlocfilehash: c1ba7f7552b5050e3c1fa7bc765acfa431f3df30
ms.sourcegitcommit: 18e2f0706e03d0b2b6324845244fbafaa077a8dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97103143"
---
# <a name="azure-arc-enabled-sql-server-preview"></a>已啟用 Azure Arc 的 SQL Server (預覽)

已啟用 Azure Arc 的 SQL Server 是「適用於伺服器的 Azure Arc」的一部分。 它將 Azure 服務擴充至在 Azure 外部裝載於客戶資料中心、邊緣或多雲端環境中的 SQL Server 執行個體。

若要啟用 Azure 服務，必須使用 Azure 入口網站和註冊指令碼，向 Azure Arc 註冊正在執行的 SQL Server 執行個體。 註冊之後，執行個體會在 Azure 上顯示為 __SQL Server – Azure Arc__ 資源。 此資源屬性會反映 SQL Server 組態設定的子集。

SQL Server 可以安裝在執行 Windows 或 Linux 的虛擬或實體機器上，並透過 Connected Machine 代理程式連線至 Azure Arc。 安裝此代理程式後，機器就會自動註冊為 SQL Server 執行個體註冊的一部分。 Connected Machine 代理程式會透過 TCP 連接埠 443，安全地將訊息輸出到 Azure Arc。 如果機器連線至防火牆或 HTTP Proxy 伺服器以透過網際網路通訊，請檢閱 [Connected Machine 代理程式的網路設定需求](/azure/azure-arc/servers/agent-overview#prerequisites)。

「已啟用 Azure Arc 的 SQL Server」公開預覽支援一組解決方案，需要安裝 Microsoft Monitoring Agent (MMA) 伺服器延伸模組並連線至 Azure Log analytics 工作區，以進行資料收集和報告。 這些解決方案包括使用 Azure 資訊安全中心和 Azure Sentinel 的進階資料安全性，以及使用隨選 SQL 評定功能的 SQL 環境健康情況檢查。

下圖顯示「已啟用 Azure Arc 的 SQL Server」架構。

![公開預覽架構](media/overview/pubic-preview-architecture.png)

## <a name="prerequisites"></a>必要條件

### <a name="supported-sql-versions-and-operating-systems"></a>支援的 SQL 版本和作業系統

已啟用 Azure Arc 的 SQL Server 支援在下列其中一個 Windows 或 Linux 作業系統版本上執行的 SQL Server 2012 或更新版本：

- Windows Server 2012 R2 及更高版本
- Ubuntu 16.04 和 18.04 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64) 
- SUSE Linux Enterprise Server (SLES) 15 (x64)

### <a name="required-permissions"></a>所需的權限

若要將 SQL Server 執行個體和主控電腦連線至 Azure Arc，您必須具備有權執行下列動作的帳戶：
   * Microsoft.AzureArcData/sqlServerInstances/read
   * Microsoft.AzureArcData/sqlServerInstances/write
   * Microsoft.HybridCompute/machines/read
   * Microsoft.HybridCompute/machines/write
   * Microsoft.GuestConfiguration/guestConfigurationAssignments/read

為了達到最佳安全性，建議您在 Azure 中建立具有前述最低權限的自訂角色。 若要進一步了解如何在 Azure 中建立具有這些權限的自訂角色，請參閱[自訂角色概觀](/azure/active-directory/users-groups-roles/roles-custom-overview)。 若要新增角色指派，請參閱[使用 Azure 入口網站來新增或移除角色指派](/azure/role-based-access-control/role-assignments-portal)或[使用 Azure RBAC 和 Azure CLI 來新增或移除角色指派](/azure/role-based-access-control/role-assignments-cli)。

### <a name="azure-subscription-and-service-limits"></a>Azure 訂用帳戶與服務限制

使用 Azure Arc 設定您的 SQL Server 執行個體之前，您應先檢閱 Azure Resource Manager 的[訂用帳戶限制](/azure/azure-resource-manager/management/azure-subscription-service-limits#subscription-limits)和[資源群組限制](/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits)，以規劃要連線的機器數目。

### <a name="networking-configuration-and-resource-providers"></a>網路設定和資源提供者

檢閱 Connected Machine 代理程式所需的[網路設定、傳輸層安全性和資源提供者](/azure/azure-arc/servers/agent-overview#prerequisites)。

需要有資源提供者 `Microsoft.AzureArcData`，才能將 SQL Server 執行個體連線到 Azure Arc。請參閱[必要條件](connect.md#prerequisites)一節中的資源提供者註冊指示。

如果您已經有連線到 Azure Arc 的 SQL Server 執行個體，請遵循下列步驟，將現有的 [SQL Server - Azure Arc] 資源移轉至新的命名空間。

### <a name="supported-azure-regions"></a>支援的 Azure 區域

公開預覽適用於下列區域：
- 美國東部
- 美國東部 2
- 美國西部 2
- 澳大利亞東部
- 東南亞
- 北歐
- 西歐
- 英國南部

## <a name="next-steps"></a>後續步驟

- [將 SQL Server 連線到 Azure Arc](connect.md)
- [使用隨選 SQL 評定來設定您的 SQL Server 執行個體以進行定期環境健康情況檢查](assess.md)
- [設定 SQL Server 執行個體的進階資料安全性](configure-advanced-data-security.md)