---
title: 已啟用 Azure Arc 的 SQL Server - 版本資訊
description: 最新版本資訊
author: anosov1960
ms.author: sashan
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: conceptual
ms.prod: sql
ms.openlocfilehash: 4d3890a29905057eb800fac823d27f149adb2ac0
ms.sourcegitcommit: 866554663ca3191748b6e4eb4d8d82fa58c4e426
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559250"
---
# <a name="release-notes---azure-arc-enabled-sql-server-preview"></a>版本資訊 - 已啟用 Azure Arc 的 SQL Server (預覽)

> [!NOTE]
> 作為預覽功能，本文所述的技術受限於 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="december-2020"></a>2020 年 12 月

### <a name="breaking-change"></a>重大變更

此版本引進了更新的[資源提供者](/azure/azure-resource-manager/management/azure-services-resource-providers)，稱為 `Microsoft.AzureArcData`。 您必須先註冊此資源提供者，才能繼續使用已啟用 Azure Arc 的 SQL Server。 請參閱[必要條件](connect.md#prerequisites)一節中的資源提供者註冊指示。

如果您具備現有的 SQL Server - Azure Arc 資源，請使用下列步驟將其移轉至 Microsoft.AzureArcData 命名空間。

1. 啟動 [Cloud Shell](https://shell.azure.com/)。 如需詳細資料，請[深入了解 Cloud Shell 中的 PowerShell](https://aka.ms/pscloudshell/docs)。

2. 使用下列命令，將指令碼上傳至介面：

    ```console
    curl https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/manage/azure-arc-enabled-sql-server/migrate-to-azure-arc-data.ps1 -o migrate-to-azure-arc-data.ps1
    ```
3. 執行指令碼。  

    ```console
   ./migrate-to-azure-arc-data.ps1
    ```

> [!NOTE]
> - 若要將命令貼入介面，請使用 Windows 上的 `Ctrl-Shift-V` 或 MacOS 上的 `Cmd-v`。
> - 指令碼會直接上傳到與 Cloud Shell 工作階段相關聯的主資料夾。
> - 當移轉完成時，指令碼會提示您輸入資源群組名稱，並列印訊息。

### <a name="other-changes"></a>其他變更

* [SQL Server - Azure Arc] 資源類型中的 *TCPPorts* 屬性已重新命名為 *TCPStaticPorts*
* 所需的權限不會像過去一樣廣泛。 如需詳細資料，請參閱[必要權限](overview.md#required-permissions)一節。

### <a name="known-issues"></a>已知問題

* *CreateTime* 屬性不會新增至 AzureArcData 命名空間中任何新建立的資源，包括 [SQL Server - Azure Arc] 資源。

## <a name="october-2020"></a>2020 年 10 月

10 月更新包含下列改善項目：

* [註冊已啟用 Azure Arc 的 SQL Server] 刀鋒視窗現在包含 [標籤] 索引標籤。這些標籤會包含在註冊指令碼中，並反映在 [SQL Server - Azure Arc] 資源中。 如需詳細資料，請參閱[將 SQL Server 連線到 Azure Arc](connect.md)。

* [Environment Health] \(環境健全狀況\) 項目現在支援藉由部署 *CustomScriptExtension*，以從入口網站啟用 [SQL 評定]。 如需詳細資料，請參閱[設定 SQL 評定](assess.md#run-on-demand-sql-assessment)。

### <a name="known-issues"></a>已知問題

下列問題適用於 10 月版本：

* 將 SQL Server 執行個體連線到 Azure Arc 需要具有一組廣泛權限的帳戶。 如需詳細資料，請參閱[所需的權限](overview.md#required-permissions)。

## <a name="september-2020"></a>2020 年 9 月

已啟用 Azure Arc 的 SQL Server 發行公開預覽。 已啟用 Azure Arc 的 SQL Server 將 Azure 服務擴充至 SQL Server 執行個體，該執行個體裝載於客戶資料中心的 Azure 外部、邊緣或多重雲端環境中。

如需詳細資料，請參閱[已啟用 Azure Arc 的 SQL Server 概觀](overview.md)

### <a name="known-issues"></a>已知問題

下列問題適用於 9 月版本：

* [註冊已啟用 Azure Arc 的 SQL Server] 刀鋒視窗不支援設定自訂標籤。 若要新增自訂標籤，請在註冊後開啟 [SQL Server - Azure Arc] 資源，並在 [概觀] 頁面中變更 [標籤]。

* 將 SQL Server 執行個體連線到 Azure Arc 需要具有一組廣泛權限的帳戶。 如需詳細資料，請參閱[所需的權限](overview.md#required-permissions)。

## <a name="next-steps"></a>後續步驟

**只想試試看嗎？**  請參閱[已啟用 Azure Arc 的 SQL Server 快速入門](https://aka.ms/AzureArcSqlServerJumpstart)來快速開始使用。
