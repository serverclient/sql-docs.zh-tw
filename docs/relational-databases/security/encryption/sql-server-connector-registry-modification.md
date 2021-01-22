---
title: SQL Server 連接器錯誤及資訊記錄
description: 本文說明如何藉由修改登錄項目來啟用 SQL Server 連接器的錯誤及記錄
ms.date: 10/08/2020
ms.localizationpriority: medium
ms.prod: sql
ms.reviewer: vanto
ms.technology: security
ms.topic: how-to
author: rupp29
ms.author: arupp
ms.openlocfilehash: 8d3f89803711e96cb1e37adc9792c54bceb259c4
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596506"
---
# <a name="sql-server-connector-error-and-information-logging"></a>SQL Server 連接器錯誤及資訊記錄

本文說明如何修改登錄項目，以啟用 SQL Server 連接器的錯誤及資訊記錄。

## <a name="sql-server-connector-for-microsoft-azure-key-vault"></a>適用於 Microsoft Azure 金鑰保存庫的 SQL Server 連接器

[適用於 Microsoft Azure Key Vault 的 SQL Server 連接器](https://www.microsoft.com/download/details.aspx?id=45344)可讓 SQL Server 加密使用 Microsoft Azure Key Vault 作為可延伸金鑰管理 (EKM) 提供者，以保護其加密金鑰。

此[下載內容](https://www.microsoft.com/download/details.aspx?id=45344)包含 SQL Server 連接器及範例指令碼，可讓 SQL Server 系統管理員了解如何設定 SQL Server 連接器及啟用 SQL Server 加密案例。 如需詳細資訊，請參閱[使用 Key Vault 進行可延伸金鑰管理 (SQL Server)](./extensible-key-management-using-azure-key-vault-sql-server.md)。

使用 [Azure Key Vault 論壇](https://social.msdn.microsoft.com/Forums/AzureKeyVault)提出問題、共用見解，並討論 SQL Server 連接器。

> [!NOTE]
> 在正常執行期間，SQL Server 連接器 DLL 會以動態方式建立登錄項目，以建立與 Azure Key Vault 的連線，從而建立金鑰 `[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\SQL Server Cryptographic Provider]`。 SQL Server 啟動帳戶必須為本機系統管理員，或其服務帳戶必須為 **NT SERVICE\MSSQLSERVER**

## <a name="upgrade-sql-server-connector-to-the-latest-version"></a>將 SQL Server 連接器升級至最新版本

如需將 SQL Server 連接器 (版本：1.0.5.0，日期為 2020 年 9 月) 升級至最新版本的 DLL 密碼編譯提供者，請遵循以下步驟。

### <a name="upgrade"></a>升級

1. 使用 SQL Server 組態管理員來停止 SQL Server 服務
1. 使用 **Control Panel\Programs\Programs and Features** 解除安裝舊版本
    1. 應用程式名稱：適用於 Microsoft Azure Key Vault 的 SQL Server 連接器
    1. 版本：15.0.300.96
    1. DLL 檔案日期：2018 年 1 月 30 日下午 3:00
1. 安裝 (升級) 新的適用於 Microsoft Azure Key Vault 的 SQL Server 連接器
    1. 版本：15.0.2000.367
    1. DLL 檔案日期：2020 年 9 月 11 日 ‏‎ 上午 5:17
1. 啟動 SQL Server 服務
1. 測試加密資料庫是否可供存取

### <a name="rollback"></a>復原

1. 使用 SQL Server 組態管理員來停止 SQL Server 服務
1. 使用 **Control Panel\Programs\Programs and Features** 解除安裝新版本
    1. 應用程式名稱：適用於 Microsoft Azure Key Vault 的 SQL Server 連接器
    1. 版本：15.0.2000.367
    1. DLL 檔案日期：2020 年 9 月 11 日 ‏‎ 上午 5:17
1. 安裝適用於 Microsoft Azure Key Vault 的 SQL Server 連接器的舊版本
    1. 版本：15.0.300.96
    1. DLL 檔案日期：2018 年 1 月 30 日下午 3:00
1. 啟動 SQL Server 服務
1. 測試加密的資料庫是否可供存取

> [!NOTE]
> - SQL Server 連接器 1.0.0.440 版及較舊版本皆已取代，且實際執行環境中也不再支援。 如需如何針對 SQL Server 連接器問題進行疑難排解的詳細資訊，請參閱 [SQL Server 連接器維護及疑難排解](../../../relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting.md)。
> - 從 1.0.3.0 版開始，SQL Server 連接器會相關的錯誤訊息報告至 Windows 事件記錄，以供疑難排解之用。
> - 從 [1.0.4.0 版：(13.0.811.168)](https://download.microsoft.com/download/8/0/9/809494F2-BAC9-4388-AD07-7EAF9745D77B/SQLServerConnectorforMicrosoftAzureKeyVault.msi) 開始支援私人 Azure 雲端，包括 Azure 中國、Azure 德國及 Azure Government。
> - 1\.0.5.0 版中的憑證指紋演算法有重大變更。 您在升級至 1.0.5.0 之後可能會遇到資料庫還原失敗。 如需詳細資訊，請參閱[知識庫文章 447099](https://support.microsoft.com/help/4470999/db-backup-problems-to-sql-server-connector-for-azure-1-0-5-0)。
> - **從 1.0.5.0 版 (檔案日期為 2020 年 9 月) 開始，SQL Server 連接器支援篩選訊息及網路要求重試邏輯。**
> - 舊版 SQL Server 連接器也是版本：[1.0.5.0 (15.0.300.96 版) - 檔案日期 2018 年 1 月](https://download.microsoft.com/download/8/0/9/809494F2-BAC9-4388-AD07-7EAF9745D77B/ENU/SQLServerConnectorforMicrosoftAzureKeyVault.msi)。 如果您遇到任何問題，請升級至最新的 SQL Server 連接器。

**系統需求** - 支援的 SQL Server 版本：

- SQL Server 2019 RTM Enterprise 64 位元
- SQL Server 2017 RTM Enterprise 64 位元
- SQL Server 2016 RTM Enterprise 64 位元
- SQL Server 2014 RTM Enterprise 64 位元
- SQL Server 2012 SP2 Enterprise 64 位元
- SQL Server 2012 SP1 CU6 Enterprise 64 位元
- SQL Server 2008 R2 SP2 CU8 Enterprise 64 位元

在低於以上所列的 SQL Server 2008 和 2012 版本上，必須安裝下列知識庫文章中指定的修補程式：[https://support2.microsoft.com/kb/2859713](https://support2.microsoft.com/kb/2859713)。

適用於 Microsoft Azure Key Vault 的 SQL Server 連接器，也需要在 Azure 的 Microsoft SQL Server 虛擬機器上具備 .NET 4.5.1 版。 安裝 SQL Server 連接器之前，應該先安裝此程式庫。

根據您執行的 SQL Server 版本來安裝適當版本的 Visual Studio C++ 可轉散發套件：

- 針對 2008、2008 R2、2012 及 2014 版 SQL Server，請安裝 2013 Visual C++ 可轉散發套件。

- 針對 SQL Server 2016，請安裝 2015 Visual C++ 可轉散發套件。

## <a name="modify-windows-registry-steps"></a>修改 Windows 登錄步驟

修改登錄項目，以在 Windows 應用程式事件記錄檔中啟用 SQL Server 連接器記錄錯誤及資訊事件。

> [!CAUTION]
> 請仔細遵循本節中的步驟，並自行承擔風險。 若未正確修改登錄，可能會發生嚴重的問題。 在修改登錄之前，請先[備份登錄以供還原](https://support.microsoft.com/help/322756)，以防發生問題。

1. 有兩種方式可以在 Windows 10 中開啟登錄編輯程式：
    - 在工作列上的搜尋方塊中鍵入 **regedit**。 然後，選取登錄編輯程式 (桌面應用程式) 的第一筆結果。

    ![ekm regedit 開啟](../../../relational-databases/security/encryption/media/ekm-registry/ekm-regedit-open.png "ekm regedit 開啟")
    - 長按或以滑鼠右鍵按一下 [開始] 按鈕，然後選取 [執行]。 在對話方塊中輸入 **regedit**，然後選取 [確定]。

   ![ekm regedit 啟動](../../../relational-databases/security/encryption/media/ekm-registry/ekm-regedit-start.png "ekm regedit 啟動")

1. 巡覽至此登錄金鑰：

    **HKLM\SOFTWARE\Microsoft\SQL Server Cryptographic Provider\Azure Key Vault\\**

    ![ekm regedit akv](../../../relational-databases/security/encryption/media/ekm-registry/ekm-regedit-akv.png "ekm regedit akv")  

1. 在名為 `Log` 的 **Azure Key Vault** 下新增新的金鑰：

    **HKLM\SOFTWARE\Microsoft\SQL Server Cryptographic Provider\Azure Key Vault\\Log\\**

    ![ekm regedit akv 記錄](../../../relational-databases/security/encryption/media/ekm-registry/ekm-regedit-akv-log.png "ekm regedit akv log.png")  

1. 在 **記錄** 金鑰底下，新增名為 `Level` 的 DWORD (32 位元) 值：

    **HKLM\SOFTWARE\Microsoft\SQL Server Cryptographic Provider\Azure Key Vault\\Log\\**

    ![ekm regedit akv 記錄 DWORD](../../../relational-databases/security/encryption/media/ekm-registry/ekm-regedit-akv-log-dword.png "ekm regedit akv 記錄 DWORD")  

1. 將 DWORD 的值設為適當的記錄層級 (0、1、2)：
   1. 0 (資訊) - **預設**
   1. 1 (錯誤)
   1. 2 (無記錄)

   ![ekm regedit akv 記錄層級](../../../relational-databases/security/encryption/media/ekm-registry/ekm-regedit-akv-log-level.png "ekm regedit akv 記錄層級")  

本文中所述的登錄項目可在以下金鑰底下找到：

```console
\Computer
    \HKEY_LOCAL_MACHINE
       \SOFTWARE
          \Microsoft
             \SQL Server Cryptographic Provider
                \Azure Key Vault
                   \Log\
                      <Level>
```

您可以選擇性使用命令列來產生金鑰：

```cmd
--Create the logging parameter using (Administrator) Command Line:
REG ADD "HKLM\SOFTWARE\Microsoft\SQL Server Cryptographic Provider\Azure Key Vault\Log" /v Level /t REG_DWORD /d 1 

--Validate the new registry entry
REG QUERY "HKLM\SOFTWARE\Microsoft\SQL Server Cryptographic Provider\Azure Key Vault\Log" /v Level
```

使用登錄項目也可以修正遺失訊息的應用程式事件記錄檔項目。 事件記錄檔可包含 `The description for Event ID 0 from source SQL Server Connector for Microsoft Azure Key Vault cannot be found...` 的訊息。  

```cmd
--Create the registry entry to enable missing messages (this works with any version)
REG ADD "HKLM\SYSTEM\ControlSet001\Services\EventLog\Application\SQL Server Connector for Microsoft Azure Key Vault" /v EventMessageFile /t REG_EXPAND_SZ /d "C:\Program Files\SQL Server Connector for Microsoft Azure Key Vault\Microsoft.AzureKeyVaultService.EKM.dll"

--Validate the new registry entry
REG QUERY "HKLM\SYSTEM\ControlSet001\Services\EventLog\Application\SQL Server Connector for Microsoft Azure Key Vault" /v EventMessageFile
```

## <a name="related-articles"></a>相關文章

- 如需其他範例指令碼，請參閱 [SQL Server 透明資料加密和使用 Azure Key Vault 進行可延伸金鑰管理](https://techcommunity.microsoft.com/t5/sql-server/intro-sql-server-transparent-data-encryption-and-extensible-key/ba-p/1427549)部落格文章
- [可延伸金鑰管理 (EKM)](extensible-key-management-ekm.md)  
- [使用 Azure Key Vault 進行可延伸金鑰管理](extensible-key-management-using-azure-key-vault-sql-server.md)
- [SQL Server 連接器維護和疑難排解](../../../relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting.md)