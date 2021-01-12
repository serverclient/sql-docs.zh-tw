---
title: 向主機守護者服務註冊電腦
description: 向主機守護者服務註冊 SQL Server 電腦，以用於具有安全記憶體保護區的 Always Encrypted。
ms.custom: ''
ms.date: 11/15/2019
ms.prod: sql
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
author: rpsqrd
ms.author: ryanpu
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 5d1b2a7209de25b1ce5c988ec9a46b77369dcf70
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101821"
---
# <a name="register-computer-with-host-guardian-service"></a>向主機守護者服務註冊電腦

[!INCLUDE [sqlserver2019-windows-only](../../../includes/applies-to-version/sqlserver2019-windows-only.md)]

本文說明如何註冊 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦，以便使用主機守護者服務 (HGS) 進行證明。

在開始之前，請確認您已部署至少一部 HGS 電腦，並已設定證明服務。
如需詳細資訊，請參閱[部署 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 的主機守護者服務](./always-encrypted-enclaves-host-guardian-service-deploy.md)。

## <a name="step-1-install-the-attestation-client-components"></a>步驟 1:安裝證明用戶端元件

若要允許 SQL 用戶端確認其與可信任的 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦連絡，[!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦必須成功使用主機守護者服務證明。
證明程序是由稱為 HGS 用戶端的選用 Windows 元件所管理。
下列步驟將協助您安裝此元件並開始證明。

1. 請確認 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦符合 [HGS 規劃文件中所述的必要條件](./always-encrypted-enclaves-host-guardian-service-plan.md#prerequisites)。

2. 在提高權限的 PowerShell 主控台中執行下列命令，安裝包含 HGS 用戶端和證明元件的主機守護者 Hyper-V 支援功能。

    ```powershell
    Enable-WindowsOptionalFeature -Online -FeatureName HostGuardian -All
    ```

3. 重新開機以完成安裝。

## <a name="step-2-verify-virtualization-based-security-is-running"></a>步驟 2:確認虛擬化型安全性正在執行

當您安裝主機守護者 Hyper-V 支援功能時，虛擬化型安全性 (VBS) 會自動設定並啟用。
[!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] Always Encrypted 的記憶體保護區會在 VBS 環境內執行，並受到該環境保護。
如果電腦沒有安裝並啟用 IOMMU 裝置，VBS 可能會無法啟動。
若要檢查 VBS 是否正在執行，請執行 `msinfo32.exe`，開啟 [系統資訊] 工具，並尋找 [系統摘要] 底部的 `Virtualization-based security` 項目。

![顯示虛擬化型安全性狀態和設定的系統資訊螢幕擷取畫面](./media/always-encrypted-enclaves/msinfo32-vbs-status.png)

要檢查的第一個項目是 `Virtualization-based security`，此項目可能具有下列三種值：

- `Running` 表示 VBS 設定正確，且能夠順利啟動。 如果電腦顯示此狀態，您可以跳至步驟 3。
- `Enabled but not running` 表示 VBS 已設定為執行，但是硬體不具備執行 VBS 的最低安全性需求。 您可能需要在 BIOS 或 UEFI 中變更硬體設定，以便啟用如 IOMMU 的選用處理器功能；而如果硬體確實不支援必要的功能，您可能需要降低 VBS 安全性需求。 請繼續閱讀本節，深入了解。
- `Not enabled` 表示 VBS 未設定為執行。 主機守護者 Hyper-V 支援功能會自動啟用 VBS，因此如果您看到此狀態，建議您重複步驟 1。

如果 VBS 未在電腦上執行，請檢查 `Virtualization-based security` 屬性。 比較 `Required Security Properties` 與 `Available Security Properties` 項目中的值。
必要屬性必須等於可用安全性屬性，或為可用安全性屬性的子集，以供 VBS 執行。

在證明 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 記憶體保護區的內容中，安全性屬性具有下列重要性：

- `Base virtualization support` 一律為必要，因為其代表執行虛擬程式所需的最低硬體功能。
- 建議使用 `Secure Boot`，但對於 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] Always Encrypted 來說並非必要。 安全開機會要求 Microsoft 簽署的開機載入器在 UEFI 初始化完成之後立即執行，藉以防止 Rootkit。 如果您使用信賴平台模組 (TPM) 證明，則不論 VBS 是否設定為需要安全開機，都會測量安全開機的啟用並予以強制執行。
- 建議使用 `DMA Protection`，但對於 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] Always Encrypted 來說並非必要。 DMA 保護使用 IOMMU 來保護 VBS，同時保護記憶體保護區的記憶體免於受到直接記憶體存取攻擊。 在生產環境中，您應該一律使用具備 DMA 保護的電腦。 在開發/測試環境中，DMA 保護的需求則可移除。 如果 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 執行個體已虛擬化，您很可能會沒有可用的 DMA 保護，並且必須移除此需求才能執行 VBS。 如需在 VM 中執行時降低安全性保證的相關資訊，請檢閱[信任模型](./always-encrypted-enclaves-host-guardian-service-plan.md#trust-model)。

在降低 VBS 所需的安全性功能之前，請洽詢您的 OEM 或雲端服務提供者，確認是否有方法能在 UEFI 或 BIOS 中啟用遺失的平台需求 (例如啟用安全開機、Intel VT-d 或 AMD IOV)。

若要變更 VBS 所需的平台安全性功能，請在提高權限的 PowerShell 主控台中執行下列命令：

```powershell
# Value 0 = No security features required (use this for Azure VMs)
# Value 1 = Only Secure Boot is required
# Value 2 = Only DMA protection is required (default configuration)
# Value 3 = Both Secure Boot and DMA protection are required
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\DeviceGuard -Name RequirePlatformSecurityFeatures -Value 0
```

變更登錄之後，請將 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦重新開機，並檢查 VBS 是否再次執行。

如果電腦是由您的公司所管理，則群組原則或 Microsoft 端點管理員可能會在重新開機之後，覆寫您對這些登錄機碼所做的任何變更。
請洽詢您的 IT 支援人員，確認他們是否有部署原則來管理您的 VBS 設定。

## <a name="step-3-configure-the-attestation-url"></a>步驟 3：設定證明 URL

接下來，您將使用 HGS 證明服務的 URL 來設定 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦。

在提高權限的 PowerShell 主控台中更新並執行下列命令，設定證明 URL。

- 將 `hgs.bastion.local` 取代為 HGS 叢集名稱
- 您可以在任意 HGS 電腦上執行 `Get-HgsServer`，取得叢集名稱
- 證明 URL 應一律以 `/Attestation` 結尾
- SQL Server 不會利用 HGS 的金鑰保護功能，因此請提供虛設 URL (例如 `http://localhost` 到 `-KeyProtectionServerUrl`)

```powershell
Set-HgsClientConfiguration -AttestationServerUrl "https://hgs.bastion.local/Attestation" -KeyProtectionServerUrl "http://localhost"
```

除非您先前已向 HGS 註冊這部電腦，否則此命令會回報證明失敗。 此結果是正常的。

Cmdlet 輸出中的 `AttestationMode` 欄位指出 HGS 所使用的證明模式。

請繼續進行[步驟 4A](#step-4a-register-a-computer-in-tpm-mode)，在 TPM 模式中註冊電腦，或進行[步驟 4B](#step-4b-register-a-computer-in-host-key-mode)，在主機金鑰模式中註冊電腦。

## <a name="step-4a-register-a-computer-in-tpm-mode"></a>步驟 4A：在 TPM 模式中註冊電腦

在此步驟中，您將收集電腦 TPM 狀態的相關資訊，並向 HGS 註冊電腦。

如果 HGS 證明服務設定為使用主機金鑰模式，請跳到[步驟 4B](#step-4b-register-a-computer-in-host-key-mode)。

請確認您使用 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦的已知良好設定，再開始收集 TPM 測量。
電腦須已安裝所有必要的硬體，並已套用最新的韌體和軟體更新。
HGS 會在證明時根據此基準來測量電腦，因此在收集 TPM 測量時，將電腦保持在最安全且符合預期的狀態十分重要。

測量時會收集三個資料檔案以用於 TPM 證明，如果您的電腦設定完全相同，其中某些檔案可以重複使用。

| 證明成品 | 測量的目標 | 唯一性 |
| -------------------- | ---------------- | ---------- |
| 平台識別項  | 電腦 TPM 中的公開簽署金鑰，以及 TPM 製造商的簽署金鑰憑證。 | 每部電腦 1 個 |
| TPM 基準 | TPM 中的平台控制項暫存器 (PCR)，用於測量在開機程序期間載入的韌體和 OS 設定。 例如安全開機狀態，以及損毀傾印是否受到加密。 | 每個唯一的電腦設定都有一個基準 (相同的硬體和軟體可以使用相同基準) |
| 程式碼完整性原則 | 您信任的 [Windows Defender 應用程式控制](/windows/security/threat-protection/windows-defender-application-control/windows-defender-application-control)原則，用於保護電腦 | 每個部署至電腦的唯一 CI 原則都有一個。 |

您可以在 HGS 上設定超過一個證明成品，以支援硬體和軟體混合組合。
HGS 只需要電腦證明在每個原則類別中符合一個原則。
例如，如果您在 HGS 上註冊了三個 TPM 基準，只要電腦測量符合其中任何一個基準，即可滿足原則需求。

### <a name="configure-a-code-integrity-policy"></a>設定程式碼完整性原則

HGS 要求在 TPM 模式中證明的每一部電腦都套用 Windows Defender 應用程式控制 (WDAC) 原則。
WDAC 程式碼完整性原則會檢查每個嘗試對受信任發行者和檔案雜湊清單執行程式碼的處理序，來限制哪些軟體可在電腦上執行。
針對 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 使用案例，記憶體保護區受到虛擬化型安全性保護而無法從主機 OS 修改，因此，WDAC 原則的嚴謹度不會影響加密查詢的安全性。
因此，建議您將簡單的稽核模式原則部署到 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦，在不對系統造成額外限制的情況下滿足證明需求。

如果您已經在電腦上使用自訂 WDAC 程式碼完整性原則來強化 OS 設定，您可以跳到[收集 TPM 證明資訊](#collect-tpm-attestation-information)。

1. 每個 Windows Server 2019、Windows 10 1809 版和更新版本的作業系統，都提供預先建立的範例原則。 `AllowAll` 原則可讓任何軟體在電腦上執行，不受任何限制。 將原則轉換成 OS 和 HGS 能了解的二進位格式，才能夠使用。 在提高權限的 PowerShell 主控台中，執行下列命令以編譯 `AllowAll` 原則：

    ```powershell
    # We are changing the policy to disable enforcement and user mode code protection before compiling
    $temppolicy = "$HOME\Desktop\allowall_edited.xml"
    Copy-Item -Path "$env:SystemRoot\schemas\CodeIntegrity\ExamplePolicies\AllowAll.xml" -Destination $temppolicy
    Set-RuleOption -FilePath $temppolicy -Option 0 -Delete
    Set-RuleOption -FilePath $temppolicy -Option 3

    ConvertFrom-CIPolicy -XmlFilePath $temppolicy -BinaryFilePath "$HOME\Desktop\allowall_cipolicy.bin"
    ```

2. 遵循 [Windows Defender 應用程式控制部署指南](/windows/security/threat-protection/windows-defender-application-control/windows-defender-application-control-deployment-guide)中的指導，以使用[群組原則](/windows/security/threat-protection/windows-defender-application-control/deploy-windows-defender-application-control-policies-using-group-policy)將 `allowall_cipolicy.bin` 檔案部署到 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦。 針對工作群組電腦，請使用本機群組原則編輯器 (`gpedit.msc`)，遵循相同程序。

3. 在 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦上執行 `gpupdate /force` 以設定新的程式碼完整性原則，然後將電腦重新開機以套用原則。

### <a name="collect-tpm-attestation-information"></a>收集 TPM 證明資訊

請為每部將使用 HGS 來進行證明的 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦重複下列步驟：

1. 當電腦處於已知的良好狀態時，請在 PowerShell 中執行下列命令來收集 TPM 證明資訊：

    ```powershell
    # Collects the TPM EKpub and EKcert
    $name = $env:computername
    $path = "$HOME\Desktop"
    (Get-PlatformIdentifier -Name $name).Save("$path\$name-EK.xml")

    # Collects the TPM baseline (current PCR values)
    Get-HgsAttestationBaselinePolicy -Path "$path\$name.tcglog" -SkipValidation

    # Collects the applied CI policy, if one exists
    Copy-Item -Path "$env:SystemRoot\System32\CodeIntegrity\SIPolicy.p7b" -Destination "$path\$name-CIpolicy.bin"
    ```

2. 將這三個證明檔案複製到 HGS 伺服器。

3. 在 HGS 伺服器上，在提升權限的 PowerShell 主控台中執行下列命令來註冊 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦：

    ```powershell
    # TIP: REMEMBER TO CHANGE THE FILENAMES
    # Registers the unique TPM with HGS (required for every computer)
    Add-HgsAttestationTpmHost -Path "C:\temp\SQL01-EK.xml"

    # Registers the TPM baseline (required ONCE for each unique hardware and software configuration)
    Add-HgsAttestationTpmPolicy -Name "MyHWSoftwareConfig" -Path "C:\temp\SQL01.tcglog"

    # Registers the CI policy (required ONCE for each unique CI policy)
    Add-HgsAttestationCiPolicy -Name "AllowAll" -Path "C:\temp\SQL01-CIpolicy.bin"
    ```

    > [!TIP]
    > 如果您在嘗試註冊唯一的 TPM 識別碼時遇到錯誤，請確認您是否已在您使用的 HGS 電腦上[匯入 TPM 中繼憑證和根憑證](./always-encrypted-enclaves-host-guardian-service-deploy.md#switch-to-tpm-attestation)。

除了平台識別碼、TPM 基準和程式碼完整性原則以外，您可能也需要變更由 HGS 所設定及施行的內建原則。
這些內建原則會以您從伺服器收集的 TPM 基準來測量，也代表了應啟用以保護電腦的各種安全性設定。
如果您有任何電腦不具備可防範 DMA 攻擊的 IOMMU (例如 VM)，您將必須停用 IOMMU 原則。

若要停用 IOMMU 需求，請在 HGS 伺服器上執行下列命令：

```powershell
Disable-HgsAttestationPolicy Hgs_IommuEnabled
```

> [!NOTE]
> 如果您停用 IOMMU 原則，任何使用 HGS 來證明的電腦都將不需要 IOMMU。
> 您無法只停用一部電腦的 IOMMU 原則。

您可以使用下列 PowerShell 命令來檢查已註冊的 TPM 主機和原則清單：

```powershell
Get-HgsAttestationTpmHost
Get-HgsAttestationTpmPolicy
```

## <a name="step-4b-register-a-computer-in-host-key-mode"></a>步驟 4B：在主機金鑰模式中註冊電腦

此步驟將引導您完成為主機產生唯一金鑰，並向 HGS 註冊該金鑰的程序。
如果 HGS 證明服務設定為使用 TPM 模式，請改為遵循[步驟 4A](#step-4a-register-a-computer-in-tpm-mode) 中的指導。

主機金鑰證明的運作方式是在 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦上產生非對稱金鑰組，並為 HGS 提供該金鑰的公開部分。
若要產生金鑰組，請在提高權限的 PowerShell 主控台中執行下列命令：

```powershell
Set-HgsClientHostKey
Get-HgsClientHostKey -Path "$HOME\Desktop\$env:computername-key.cer"
```

如果您已建立主機金鑰，並希望產生新的金鑰組，請改為使用下列命令：

```powershell
Remove-HgsClientHostKey
Set-HgsClientHostKey
Get-HgsClientHostKey -Path "$HOME\Desktop\$env:computername-key.cer"
```

產生主機金鑰後，請將憑證檔案複製到 HGS 伺服器，然後在提高權限的 PowerShell 主控台中執行下列命令以註冊 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦：

```powershell
Add-HgsAttestationHostKey -Name "YourComputerName" -Path "C:\temp\yourcomputername.cer"
```

為每一部將使用 HGS 進行證明的 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦重複步驟 4B。

## <a name="step-5-confirm-the-host-can-attest-successfully"></a>步驟 5：確認主機可成功證明

在您向 HGS 註冊 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 電腦後 ([步驟 4A](#step-4a-register-a-computer-in-tpm-mode) 用於 TPM 模式，[步驟 4B](#step-4b-register-a-computer-in-host-key-mode) 用於主機金鑰模式)，應確認電腦能成功證明。

您可以檢查 HGS 證明用戶端的設定，並隨時使用 [Get-HgsClientConfiguration](/powershell/module/hgsclient/get-hgsclientconfiguration) 來執行證明嘗試。
命令的輸出類似如下範例：

```
PS C:\> Get-HgsClientConfiguration


IsHostGuarded                  : True
Mode                           : HostGuardianService
KeyProtectionServerUrl         : http://localhost
AttestationServerUrl           : http://hgs.bastion.local/Attestation
AttestationOperationMode       : HostKey
AttestationStatus              : Passed
AttestationSubstatus           : NoInformation
FallbackKeyProtectionServerUrl :
FallbackAttestationServerUrl   :
IsFallbackInUse                : False
```

輸出中最重要的兩個欄位之一是 `AttestationStatus`，此欄位會告訴您電腦是否通過證明；另一個是 `AttestationSubStatus`，說明電腦在電腦未通過證明時，哪一項原則失敗。

`AttestationStatus` 中可能出現的最常見值如下所述：

| `AttestationStatus` | 說明 |
| ----------------- | ----------- |
| 已過期 | 主機先前已通過證明，但簽發給該主機的健康情況憑證已過期。 請確認主機和 HGS 時間為同步。 |
| `InsecureHostConfiguration` | 電腦未滿足在 HGS 伺服器上設定的一或多個證明原則。 如需詳細資訊，請參閱 `AttestationSubStatus`。 |
| NotConfigured | 電腦未設定證明 URL。 [設定證明 URL](#step-3-configure-the-attestation-url) |
| 已通過 | 電腦已通過證明，並受到信任可執行 [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] 記憶體保護區。 |
| `TransientError` | 證明嘗試因暫時性錯誤而失敗。 此錯誤通常表示在網路上連絡 HGS 時發生問題。 請檢查網路連線，並確認電腦可解析並路由傳送至 HGS 服務名稱。 |
| `TpmError` | 電腦的 TPM 裝置在證明嘗試期間回報錯誤。 請檢查 TPM 記錄檔，取得詳細資訊。 清除 TPM 可能可以解決此問題，但在清除 TPM 前請先暫止依賴 TPM 的 BitLocker 和其他服務。 |
| `UnauthorizedHost` | HGS 無法識別主機金鑰。 遵循[步驟 4B](#step-4b-register-a-computer-in-host-key-mode) 中的指示，向 HGS 註冊電腦。 |

當 `AttestationStatus` 顯示 `InsecureHostConfiguration`時，`AttestationSubStatus` 欄位將會填入一或多個失敗的原則名稱。
下表說明最常見的值及如何補救錯誤。

| AttestationSubStatus | 代表的意義及應採取的動作 |
| -------------------- | ---------------------------- |
| CodeIntegrityPolicy | 電腦上的程式碼完整性原則未向 HGS 註冊，「或」  電腦目前未使用程式碼完整性原則。 請參閱[設定程式碼完整性原則](#configure-a-code-integrity-policy)的指導。 |
| DumpsEnabled | 電腦已設定為允許損毀傾印，但 Hgs_DumpsEnabled 原則不允許傾印。 請停用這部電腦上的傾印，或停用 Hgs_DumpsEnabled 原則以繼續。 |
| FullBoot | 電腦從睡眠狀態或休眠中繼續，導致 TPM 測量變更。 請將電腦重新開機以產生全新的 TPM 測量。 |
| HibernationEnabled | 電腦已設定為允許使用未加密的休眠檔案進入休眠。 請停用電腦上的休眠以解決此問題。 |
| HypervisorEnforcedCodeIntegrityPolicy | 電腦未設定為使用程式碼完整性原則。 請檢查 [群組原則] 或 [本機群組原則] > [電腦設定] > [系統管理範本] > [系統] > [Device Guard] > [開啟虛擬化型安全性] > [虛擬化型程式碼完整性保護]。 此原則項目應該是「已啟用 (不含 UEFI 鎖定)」。 |
| Iommu | 這部電腦未啟用 IOMMU 裝置。 如果是實體電腦，請在 UEFI 設定功能表中啟用 IOMMU。 如果是虛擬機器且無法使用 IOMMU，請在 HGS 伺服器上執行 `Disable-HgsAttestationPolicy Hgs_IommuEnabled`。 |
| SecureBoot | 這部電腦未啟用安全開機。 在 UEFI 設定功能表中啟用安全開機以解決此錯誤。 |
| VirtualSecureMode | 虛擬化型安全性未在這部電腦上執行。 請遵循[步驟2：確認 VBS 正在電腦上執行](#step-2-verify-virtualization-based-security-is-running)的指導。 |