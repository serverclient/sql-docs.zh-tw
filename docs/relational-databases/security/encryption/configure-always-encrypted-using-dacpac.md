---
description: 使用 Always Encrypted 與 DAC 套件設定資料行加密
title: 使用 Always Encrypted 與 DAC 套件設定資料行加密 | Microsoft Docs
ms.custom: ''
ms.date: 06/26/2019
ms.prod: sql
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- Always Encrypted, configure with SSMS
ms.assetid: 29816a41-f105-4414-8be1-070675d62e84
author: jaszymas
ms.author: jaszymas
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 506abf476e04ab1a40059d04031949fa80b31acf
ms.sourcegitcommit: 866554663ca3191748b6e4eb4d8d82fa58c4e426
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559320"
---
# <a name="configure-column-encryption-using-always-encrypted-with-a-dac-package"></a>使用 Always Encrypted 與 DAC 套件設定資料行加密 
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]

[資料層應用程式 (DAC) 套件](../../data-tier-applications/data-tier-applications.md) (也稱為 DACPAC) 是 SQL Server 資料庫部署的可移植單位，其定義所有 SQL Server 物件，包括資料表及資料表內的資料行。 當您將 DACPAC 發佈至資料庫時 (當您使用 DACPAC 升級資料庫時)，目標資料庫的結構描述會更新以符合 DACPAC 中結構描述。 您可以使用 SQL Server Management Studio 中的 [[升級資料層應用程式精靈]](../../data-tier-applications/upgrade-a-data-tier-application.md#UsingDACUpgradeWizard)、[PowerShell](../../data-tier-applications/upgrade-a-data-tier-application.md#UpgradeDACPowerShell) 或 [sqlpackage](../../../tools/sqlpackage/sqlpackage-publish.md) 來發佈 DACPAC。

本文說明當 DACPAC 或/和目標資料庫包含以 [Always Encrypted](always-encrypted-database-engine.md) 所保護的資料行時，升級資料庫的特殊考量。 如果 DACPAC 中資料行與目標資料庫中現有資料行的加密配置不同，則發佈 DACPAC 會導致加密、解密或重新加密儲存在資料行中的資料。 如需詳細資訊，請參閱下表。

| 條件|動作|
|:---|:---|
|資料行已在 DACPAC 中加密，但未在資料庫中加密。| 將會加密資料行中的資料。|
|資料行未在 DACPAC 中加密，但在資料庫中加密。| 將會解密資料行中的資料 (將會移除資料行的加密)。|
| 資料行已在 DACPAC 和資料庫中進行加密，但是 DACPAC 中的資料行會使用與資料庫中對應資料行不同的加密類型和/或不同的資料行加密金鑰。|將會解密資料行中的資料，然後重新加密以符合 DACPAC 中的加密組態。|

部署 DAC 套件也可能會導致建立或移除 Always Encrypted 資料行主要金鑰或資料行加密金鑰的中繼資料物件。

## <a name="performance-considerations"></a>效能考量
若要執行密碼編譯作業，您用來部署 DACPAC 的工具必須將資料移出資料庫。 此工具會在資料庫中建立具有所需加密設定的新資料表、從原始資料表載入所有資料、執行所要求的密碼編譯作業、將資料上傳至新的資料表，然後交換原始資料表與新的資料表。 執行密碼編譯作業可能需要很長的時間。 在這段期間，資料庫無法寫入交易。 

::: moniker range=">=sql-server-ver15"

> [!NOTE]
> 如果您使用 [!INCLUDE [sssqlv15-md](../../../includes/sssqlv15-md.md)] 且 SQL Server 執行個體是以安全記憶體保護區進行設定，您可以就地執行密碼編譯作業，而不需要將資料移出資料庫。 請參閱[使用具有安全記憶體保護區的 Always Encrypted 就地設定資料行加密](always-encrypted-enclaves-configure-encryption.md)。 請注意，就地加密不適用於 DACPAC 部署。

::: moniker-end

## <a name="permissions-for-publishing-a-dac-package-if-always-encrypted-is-set-up"></a>設定 Always Encrypted 時發佈 DAC 套件的權限

若要在 DACPAC 或/和目標資料庫中設定 Always Encrypted 時發佈DAC 套件，您可能需要有下列部分或所有權限 (取決於 DACPAC 中結構描述與目標資料庫結構描述之間的差異)。

*ALTER ANY COLUMN MASTER KEY*、 *ALTER ANY COLUMN ENCRYPTION KEY*、 *VIEW ANY COLUMN MASTER KEY DEFINITION*、 *VIEW ANY COLUMN ENCRYPTION KEY DEFINITION*

如果升級作業觸發資料加密作業，則您也需要可以存取針對受影響資料行所設定的資料行主要金鑰︰

- **憑證存放區 - 本機電腦** - 您必須具有當成資料行主要金鑰使用之憑證的讀取權，或為電腦上的系統管理員。
- **Azure Key Vault** - 您需要有包含資料行主要金鑰之保存庫的 *create*、*get*、*unwrapKey*、*wrapKey*、*sign* 和 *verify* 權限。
- **金鑰存放區提供者 (CNG)** - 使用金鑰存放區或金鑰時，系統可能會提示您提供必要權限和認證 (取決於存放區和 KSP 設定)。
- **密碼編譯服務提供者 (CAPI)** - 使用金鑰存放區或金鑰時，系統可能會提示您提供必要權限和認證 (取決於存放區和 CSP 設定)。

如需詳細資訊，請參閱 [建立及儲存資料行主要金鑰 (永遠加密)](../../../relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted.md)。 

 
## <a name="next-steps"></a>後續步驟
- [使用 Always Encrypted 開發應用程式](always-encrypted-client-development.md)
- [使用 Always Encrypted 與 SQL Server Management Studio 查詢資料行](always-encrypted-query-columns-ssms.md)

## <a name="see-also"></a>另請參閱  
 - [一律加密](../../../relational-databases/security/encryption/always-encrypted-database-engine.md)
 - [Always Encrypted 的金鑰管理概觀](overview-of-key-management-for-always-encrypted.md) 
 - [使用 SQL Server Management Studio 設定 Always Encrypted](configure-always-encrypted-using-sql-server-management-studio.md)
 - [使用 [Always Encrypted 精靈] 設定資料行加密](always-encrypted-wizard.md)
 - [使用 Always Encrypted 與 PowerShell 設定資料行加密](configure-column-encryption-using-powershell.md)
 
