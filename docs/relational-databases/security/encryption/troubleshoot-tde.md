---
title: Azure Key Vault 中客戶管理金鑰的常見錯誤
description: 了解如何找出並解決 Azure Key Vault 中透明資料加密 (TDE) 與客戶管理金鑰的存取問題與一般錯誤。
ms.custom: seo-lt-2019
helpviewer_keywords:
- troublshooting, tde akv
- tde akv configuration, troubleshooting
- tde troubleshooting
author: jaszymas
ms.prod: sql
ms.technology: security
ms.reviewer: vanto
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: jaszymas
monikerRange: = azuresqldb-current || = azure-sqldw-latest
ms.openlocfilehash: b1725b11a5cc491c4624a7196240546a649f9afa
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102210"
---
# <a name="common-errors-for-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault"></a>在 Azure Key Vault 中使用客戶受控金鑰進行透明資料加密的常見錯誤

[!INCLUDE[asdb-asdbmi-asa](../../../includes/applies-to-version/asdb-asdbmi-asa.md)]

此文章說明如何找出並解決造成資料庫 (設定為使用 [Azure Key Vault 中的透明資料加密 (TDE) 搭配客戶管理金鑰](/azure/sql-database/transparent-data-encryption-byok-azure-sql)) 變得無法存取的 Azure Key Vault 金鑰存取問題。

## <a name="introduction"></a>簡介
當 TDE 設定為在 Azure Key Vault 中使用客戶管理金鑰時，資料庫必須持續存取此 TDE 保護裝置才能保持在線上。  如果邏輯 SQL Server 無法存取 Azure Key Vault 中的客戶管理 TDE 保護裝置，資料庫將會開始拒絕所有連線，而不會出現相應的錯誤訊息，並在 Azure 入口網站中將其狀態變更為「無法存取」。

在前 8 小時，如果基礎 Azure Key vault 金鑰存取問題已解決，資料庫將會自動修復並自動上線。 這表示在所有間歇性和暫時性網路中斷的情況下，使用者不需要採取動作，資料庫就會自動上線。 在大部分情況下，使用者必須採取動作，才能解決基礎金鑰保存庫的金鑰存取問題。 

如果無法存取的資料庫已經不再需要，可以立即將它刪除，以停止產生成本。 除非已還原對 Azure Key Vault 金鑰的存取權，且資料庫已重新上線，否則不允許資料庫上的其他所有動作。 當使用客戶管理金鑰加密的資料庫無法存取時，也無法將伺服器上的 TDE 選項從「客戶管理」變更為「服務管理」金鑰。 當 TDE 保護裝置遭撤銷時，這是保護資料免於未經授權存取的必要動作。 

在資料庫無法存取超過 8 小時之後，就不會再自動修復。 如果已經在該期間之後還原對必要 Azure Key Vault 金鑰的存取權，您必須手動重新驗證該金鑰的存取權，才能讓資料庫重新上線。 在此情況下，讓資料庫重新上線可能需要花很多時間，視資料庫大小而定。 一旦資料庫重新上線，則先前設定的設定，例如，[容錯移轉群組](/azure/sql-database/sql-database-auto-failover-group)、PITR 歷程記錄和所有標籤 **都會遺失**。 因此，建議使用[動作群組](/azure/azure-monitor/platform/action-groups) \(部分機器翻譯\) 實作通知系統，以便盡快察覺並解決基礎金鑰保存庫的金鑰存取問題。 

## <a name="common-errors-causing-databases-to-become-inaccessible"></a>導致資料庫變得無法存取的常見錯誤

搭配金鑰保存庫使用 TDE 時，出現的大多數問題都是由下列其中一個錯誤設定所造成：

### <a name="the-key-vault-is-unavailable-or-doesnt-exist"></a>金鑰保存庫無法使用或不存在

- 意外刪除金鑰保存庫。
- 已針對 Azure Key Vault 設定防火牆，但它不允許存取 Microsoft 服務。
- 間歇網路錯誤導致無法使用金鑰保存庫。

### <a name="no-permissions-to-access-the-key-vault-or-the-key-doesnt-exist"></a>沒有存取金鑰保存庫的權限或金鑰不存在

- 意外刪除、停用金鑰或金鑰已過期。
- 意外刪除邏輯 SQL Server 執行個體 AppId。
- 邏輯 SQL Server 執行個體已移至不同的訂用帳戶。 如果將邏輯伺服器移至不同的訂用帳戶，則必須建立新的 AppId。
- 針對金鑰授與 AppId 的權限不足 (它們不包括 Get、Wrap 和 Unwrap)。
- 已撤銷邏輯 SQL Server 執行個體 AppId 的權限。

## <a name="identify-and-resolve-common-errors"></a>識別及解決常見的錯誤

在此節中，我們列出了最常見錯誤的疑難排解步驟。

### <a name="missing-server-identity"></a>遺漏伺服器身分識別

**錯誤訊息**

「401 AzureKeyVaultNoServerIdentity - 伺服器上的伺服器識別設定不正確。  請連絡支援人員。」

**偵測**

使用下列 Cmdlet 或命令，以確保身分識別已指派給邏輯 SQL Server 執行個體：

- Azure PowerShell：[Get-AzureRMSqlServer](/powershell/module/AzureRM.Sql/Get-AzureRmSqlServer) \(英文\) 

- Azure CLI：[az-sql-server-show](/cli/azure/sql/server#az-sql-server-show) \(英文\)

**風險降低**

使用下列 Cmdlet 或命令，為邏輯 SQL Server 執行個體設定 Azure AD 身分識別 (AppId)：

- Azure PowerShell：具有 `-AssignIdentity` 選項的 [Set-AzureRmSqlServer](/powershell/module/azurerm.sql/set-azurermsqlserver) \(英文\)。

- Azure CLI：具有 `--assign_identity` 選項的 [az sql server update](/cli/azure/sql/server#az-sql-server-update) \(英文\)。

在 Azure 入口網站中，移至金鑰保存庫，然後移至 [存取原則]  。 完成下列步驟： 

 1. 使用 [新增]  按鈕，為上一個步驟中建立的伺服器新增 AppId。 
 1. 指派下列金鑰權限：取得、包裝及解除包裝 

若要深入了解，請參閱[將 Azure AD 身分識別指派給您的伺服器](/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure#assign-an-azure-ad-identity-to-your-server) \(部分機器翻譯\)。

> [!IMPORTANT]
> 如果在初始設定搭配 Key Vault 的 TDE 之後，邏輯 SQL Server 執行個體已移至新的租用戶，就必須重複設定 Azure AD 身分識別的步驟以建立新的 AppId。 然後，將 AppId 新增至金鑰保存庫，並為金鑰指派正確的權限。 
>

### <a name="missing-key-vault"></a>遺漏索金鑰保存庫

**錯誤訊息**

「503 AzureKeyVaultConnectionFailed - 因為嘗試連線至 Azure Key Vault 失敗，所以無法完成伺服器上的作業。」 

**偵測**

識別金鑰 URI 和金鑰保存庫：

1. 使用下列 Cmdlet 或命令來取得特定邏輯 SQL Server 執行個體的金鑰 URI：

    - Azure PowerShell：[Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey)

    - Azure CLI：[az-sql-server-tde-key-show](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-show) \(英文\) 

1. 使用金鑰 URI 來識別金鑰保存庫：

    - Azure PowerShell：您可以檢查 $MyServerKeyVaultKey 變數的屬性來取得金鑰保存庫的詳細資料。

    - Azure CLI：如需金鑰保存庫的詳細資料，請檢查傳回的伺服器加密保護裝置。

**風險降低**

確認金鑰保存庫可供使用：

- 確定金鑰保存庫可供使用且邏輯 SQL Server 執行個體可存取。
- 如果金鑰保存庫位於防火牆後方，請確定已核取要允許 Microsoft 服務存取金鑰保存庫的方塊。
- 如果意外刪除金鑰保存庫，您必須從頭完成該設定。


### <a name="missing-key"></a>遺漏金鑰

**錯誤訊息**

「404 ServerKeyNotFound - 目前訂用帳戶上找不到要求的伺服器金鑰。」  

「409 ServerKeyDoesNotExists - 伺服器金鑰不存在。」 

**偵測**

識別金鑰 URI 和金鑰保存庫：

- 使用[遺漏金鑰保存庫](#missing-key-vault)中的 Cmdlet 或命令來識別新增至邏輯 SQL Server 執行個體的金鑰 URI。 執行命令將傳回金鑰的清單。

**風險降低**

確認 Key Vault 中有 TDE 保護裝置：

1. 識別金鑰保存庫，並在 Azure 入口網站中移至該金鑰保存庫。
1. 確認有金鑰 URI 所識別的金鑰。

### <a name="missing-permissions"></a>遺漏權限

**錯誤訊息**

「401 AzureKeyVaultMissingPermissions - 伺服器缺少 Azure Key Vault 的必要權限。」 

**偵測**

識別金鑰 URI 和金鑰保存庫： 

- 使用[遺漏金鑰保存庫](#missing-key-vault)中的 Cmdlet 或命令來識別邏輯 SQL Server 執行個體所使用的金鑰保存庫。

**風險降低**

確認邏輯 SQL Server 執行個體具有金鑰保存庫的權限，以及存取金鑰的正確權限：

- 在 Azure 入口網站中，移至金鑰保存庫 > [存取原則]  。 尋找邏輯 SQL Server 執行個體的 AppId。  
- 如果 AppId 已存在，請確定 AppID 擁有下列金鑰權限：取得、包裝及解除包裝。
- 如果 AppId 不存在，請使用 [新增]  按鈕加以新增。 

## <a name="getting-tde-status-from-the-activity-log"></a>從活動記錄中取得 TDE 狀態

為了允許監視因 Azure Key Vault 金鑰存取問題而造成的資料庫狀態，系統將會根據 Azure Resource Manager URL 與 Subscription+Resourcegroup+ServerName+DatabaseName，將下列事件記錄到資源識別碼的[活動記錄](/azure/service-health/alerts-activity-log-service-notifications) \(部分機器翻譯\) 中： 

**當服務失去 Azure Key Vault 金鑰存取權時的事件**

EventName：MakeDatabaseInaccessible 

狀態：Started 

描述：資料庫已失去 Azure 金鑰保存庫金鑰的存取權，現在無法存取資料庫：<error message>   

 

**當 8 小時的自我修復等候時間開始時的事件** 

EventName：MakeDatabaseInaccessible 

狀態：InProgress 

描述：資料庫正在等候使用者於 8 小時內重新建立 Azure 金鑰保存庫金鑰的存取權。   

 

**當資料庫自動重新上線的事件**

EventName：MakeDatabaseAccessible 

狀態：成功 

描述：已重新建立資料庫的 Azure 金鑰保存庫金鑰存取權，且資料庫現在已上線。 

 

**當問題未在 8 小時內解決，且必須以手動方式驗證 Azure Key Vault 金鑰存取權時的事件** 

EventName：MakeDatabaseInaccessible 

狀態：成功 

描述：無法存取資料庫，而且需要使用者解決 Azure 金鑰保存庫錯誤，並使用「重新驗證」金鑰重新建立 Azure 金鑰保存庫金鑰存取權。 

 

**當資料庫在手動重新驗證金鑰後重新上線時的事件**

EventName：MakeDatabaseAccessible 

狀態：成功 

描述：已重新建立資料庫的 Azure 金鑰保存庫金鑰存取權，且資料庫現在已上線。 

 

**當 Azure Key Vault 金鑰存取權的重新驗證已成功，且資料庫正在上線時的事件**

EventName：MakeDatabaseAccessible 

狀態：Started 

描述：已開始還原資料庫的 Azure 金鑰保存庫金鑰存取權。 

 

**當 Azure Key Vault 金鑰存取權的重新驗證失敗時的事件**

EventName：MakeDatabaseAccessible 

狀態：失敗 

描述：還原資料庫的 Azure 金鑰保存庫金鑰存取權失敗。 


## <a name="next-steps"></a>後續步驟

- 深入了解[Azure 資源健康狀態](/azure/service-health/resource-health-overview) \(部分機器翻譯\)。
- 設定[動作群組](/azure/azure-monitor/platform/action-groups) \(部分機器翻譯\)，以根據您的偏好來接收通知和警示，例如，電子郵件/簡訊/推播/語音、邏輯應用程式、Webhook、ITSM 或自動化 Runbook。