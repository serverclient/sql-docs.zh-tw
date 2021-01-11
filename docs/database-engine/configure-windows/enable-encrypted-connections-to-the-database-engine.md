---
title: 啟用加密連線 | Microsoft Docs
ms.custom: contperf-fy20q4
ms.date: 08/29/2019
ms.prod: sql
ms.prod_service: security
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
description: 跨通訊通道加密資料。 使用 SQL Server 組態管理員來啟用 SQL Server 資料庫引擎執行個體的加密連線。
helpviewer_keywords:
- connections [SQL Server], encrypted
- SSL [SQL Server]
- Secure Sockets Layer (SSL)
- TLS [SQL Server]
- Transport Layer Security (TLS)
- encryption [SQL Server], connections
- cryptography [SQL Server], connections
- certificates [SQL Server], installing
- requesting encrypted connections
- installing certificates
- security [SQL Server], encryption
- TLS certificates
ms.assetid: e1e55519-97ec-4404-81ef-881da3b42006
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: b18a3131329e0485221a0ae2cdaafd0726a4f31c
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878952"
---
# <a name="enable-encrypted-connections-to-the-database-engine"></a>啟用資料庫引擎的加密連線

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  了解如何跨通訊通道加密資料。  您可啟用 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體的加密連線，並使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員來指定憑證。
 
 伺服器電腦必須已佈建憑證。 若要在伺服器電腦上佈建憑證，則需要[將其匯入至 Windows](#single-server)。 用戶端電腦必須設定為[信任憑證的根授權單位](#about)。  
  
> [!IMPORTANT]
> 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始，已中止使用安全通訊端層 (SSL)。 請改用傳輸層安全性 (TLS)。

## <a name="transport-layer-security-tls"></a>傳輸層安全性 (TLS)

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可以使用「傳輸層安全性」(TLS) 來加密在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體與用戶端應用程式之間的網路傳輸的資料。 TLS 加密會在通訊協定層內執行，且可供所有支援的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 用戶端使用。

TLS 可以在用戶端連線要求加密時用來驗證伺服器。 如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體在已取得公開憑證授權單位指派之憑證的電腦上執行，導向受信任之根授權單位的憑證鏈結便可擔保該電腦及 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的身分。 在這種伺服器驗證中，必須將執行用戶端應用程式的電腦設定成信任伺服器使用之憑證的根授權單位。 

可以搭配自我簽署憑證來使用加密，下節將做說明，但自我簽署憑證所提供的保護有限。
TLS 使用的加密層級 (40 位元或 128 位元) 視應用程式和資料庫電腦上執行的 Microsoft Windows 作業系統版本而定。

> [!WARNING]
> 使用 40 位元加密層級會被視為不安全。

> [!WARNING]
> 使用自我簽署憑證來加密的 TLS 連線不提供增強式安全性。 這種連線容易受到攔截式攻擊。 在生產環境或連線到網際網路的伺服器上，您不應該仰賴使用自我簽署憑證的 TLS。

對於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體與應用程式之間網路上傳輸的資料，啟用 TLS 加密可提高其安全性。 不過，使用 TLS 加密 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 與用戶端應用程式之間的所有流量時，需要下列額外的處理：
-  在連線時需要額外的網路往返作業。
-  從應用程式傳送到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的封包必須利用用戶端 TLS 堆疊來加密，並使用伺服器 TLS 堆疊來解密。
-  從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體傳送到應用程式的封包必須利用伺服器 TLS 堆疊來加密，並使用用戶端 TLS 堆疊來解密。

## <a name="about-certificates"></a><a name="about"></a> 關於憑證

 您必須針對 **伺服器驗證** 發出此憑證。 憑證的名稱必須是電腦的完整網域名稱 (FQDN)。  
  
 電腦上之使用者的憑證會儲存在本機中。 若要安裝憑證以供 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用，您必須搭配執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員與具有本機系統管理員權限的帳戶。

 用戶端必須可確認伺服器所使用之憑證的擁有權。 如果用戶端具有已簽署伺服器憑證之憑證授權單位的公開金鑰憑證，則不需要進一步進行組態設定。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 包含許多憑證授權單位的公開金鑰憑證。 如果伺服器憑證是由用戶端沒有公開金鑰憑證的公開或私人憑證授權單位所簽署，則您必須安裝已簽署伺服器憑證之憑證授權單位的公開金鑰憑證。  
  
> [!NOTE]  
> 若要在容錯移轉叢集中使用加密功能，請務必在容錯移轉叢集中的所有節點上，對於虛擬伺服器使用完整的 DNS 名稱來安裝伺服器憑證。 例如，假設您有一個雙節點的叢集，節點的名稱分別為 **_test1.\_\<your company>\*.com*** 與 **_test2.\_\<your company>\*.com***，且您有一部名為 **_virtsql_*_ 的虛擬伺服器，則需要同時在這兩個節點上安裝 _ *_virtsql.\_\<your company>\*.com*** 的憑證。 您可以在 [SQL Server 網路組態] 的 [virtsql 通訊協定] 屬性方塊中，將 **ForceEncryption** 選項的值設為 [是]。

> [!NOTE]
> 在 Azure VM 上建立 Azure 搜尋服務索引器到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的加密連線時，請參閱[在 Azure VM 上設定從 Azure 搜尋服務索引器到 SQL Server 的連線](/azure/search/search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers)。 

## <a name="certificate-requirements"></a>憑證需求

若要讓 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 載入 TLS 憑證，憑證必須符合下列條件：

- 憑證必須位於本機電腦憑證存放區或目前使用者憑證存放區。

- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶必須有存取 TLS 憑證的必要權限。

- 目前的系統時間必須在憑證的 [有效期限自] 屬性值之後，並且在憑證的 [有效期限至] 屬性值之前。

- 憑證必須是為了伺服器驗證而準備的。 這需要憑證的 [增強金鑰使用方法] 屬性指定 [伺服器驗證 (1.3.6.1.5.5.7.3.1)]。

- 憑證必須使用 **AT_KEYEXCHANGE** 的 **KeySpec** 選項來建立。 憑證的金鑰用法屬性 (**KEY_USAGE**) 通常也包括金鑰編密法 (**CERT_KEY_ENCIPHERMENT_KEY_USAGE**)。

- 憑證的 [主旨] 屬性必須指出一般名稱 (CN) 與伺服器電腦的主機名稱或完整網域名稱 (FQDN) 是相同的。 使用主機名稱時，必須在憑證中指定 DNS 尾碼。 如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 是在容錯移轉叢集上執行，則一般名稱必須符合虛擬伺服器的主機名稱或 FQDN，且容錯移轉叢集中的所有節點都必須提供憑證。

- [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)] 的原生用戶端 (SNAC) 支援萬用字元憑證。 SNAC 已淘汰，並已替換成 [Microsoft OLE DB Driver for SQL Server](../../connect/oledb/oledb-driver-for-sql-server.md) 和 [Microsoft ODBC Driver for SQL Server](../../connect/odbc/microsoft-odbc-driver-for-sql-server.md)。 其他用戶端可能不支援萬用字元憑證。 如需詳細資訊，請參閱用戶端文件和 [KB 258858](https://support.microsoft.com/kb/258858)。       
  使用 SQL Server 組態管理員不能選取萬用字元憑證。 若要使用萬用字元憑證，您必須編輯 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQLServer\SuperSocketNetLib` 登錄機碼，在 [憑證] 值輸入不含空格的憑證指紋。  

  > [!WARNING]  
  > [!INCLUDE[ssnoteregistry_md](../../includes/ssnoteregistry-md.md)]  

## <a name="install-on-single-server"></a><a name="single-server"></a>在單一伺服器上安裝

在 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 中，憑證管理已整合到 SQL Server 組態管理員。 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 的 SQL Server 組態管理員可以與舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 搭配使用。 請參閱[憑證管理 (SQL Server 組態管理員)](../../database-engine/configure-windows/manage-certificates.md)，在單一 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體上新增憑證。

如果使用 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 到 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]，且無法使用 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 的 SQL Server 組態管理員，請遵循下列步驟：

1. 在 [開始]  功能表中按一下 [執行] ，然後在 [開啟]  方塊中輸入 **MMC** ，然後按一下 [確定] 。  
  
2. 在 MMC 主控台的 [檔案] 功能表上，按一下 [新增/移除嵌入式管理單元]。  
  
3. 在 [新增/移除嵌入式管理單元] 對話方塊中，按一下 [新增]。  
  
4. 在 [新增獨立嵌入式管理單元] 對話方塊中，按一下 [憑證]，然後按 [新增]。  
  
5. 在 [憑證嵌入式管理單元] 對話方塊中，按一下 [電腦帳戶]，然後按 [完成]。  
  
6. 在 [新增獨立嵌入式管理單元] 對話方塊中，按一下 [關閉]。  
  
7. 在 [新增/移除嵌入式管理單元] 對話方塊中，按一下 [確定]。  
  
8. 在 [憑證] 嵌入式管理單元中，展開 [憑證] 後再展開 [個人]，然後以滑鼠右鍵按一下 [憑證]，接著指向 [所有工作]，再按 [匯入]。  

9. 以滑鼠右鍵按一下匯入的憑證，並指向 [所有工作]，然後按一下 [管理私密金鑰]。 在 [安全性] 對話方塊中，為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶所使用的使用者帳戶新增讀取權限。  
  
10. 完成 **[憑證匯入精靈]** 以加入憑證至電腦中，然後關閉 MMC 主控台。 如需新增憑證至電腦的詳細資訊，請參閱 Windows 文件集。  

> [!IMPORTANT]
> 針對生產環境，建議從憑證授權單位取得信任的憑證。    
> 基於測試目的，也可使用自我簽署憑證。 若要建立自我簽署憑證，請參閱 [Powershell Cmdlet New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) 或 [certreq 命令](/windows-server/administration/windows-commands/certreq_1)。
  
## <a name="install-across-multiple-servers"></a>跨多部伺服器安裝

在 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 中，憑證管理已整合到 SQL Server 組態管理員。 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 的 SQL Server 組態管理員可以與舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 搭配使用。 請參閱[憑證管理 (SQL Server 組態管理員)](../../database-engine/configure-windows/manage-certificates.md)，以在容錯移轉叢集設定或可用性群組設定中新增憑證。

如果使用 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 到 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)]，且無法使用 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 的 SQL Server 組態管理員，請針對每部伺服器遵循[在單一伺服器上佈建 (安裝) 憑證](#single-server)一節中的步驟。

## <a name="export-server-certificate"></a>匯出伺服器憑證  
  
1. 從 [憑證] 嵌入式管理單元的 [憑證] / [個人] 資料夾中找到憑證，以滑鼠右鍵按一下 [憑證]，並指向 [所有工作]，然後按一下 [匯出]。  
  
2. 完成 **[憑證匯出精靈]** ，並將憑證檔儲存在方便取得的位置。  
  
## <a name="configure-server"></a>設定伺服器

設定伺服器強制加密連線。

> [!IMPORTANT]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶必須擁有用來在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上強制加密的憑證讀取權限。 針對不具有特殊權限的服務帳戶，則必須將讀取權限新增至憑證。 若無法進行此操作，可能會導致 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務重新啟動失敗。
  
1. 在 [SQL Server 組態管理員] 中，展開 [SQL Server 網路組態]，然後以滑鼠右鍵按一下 [通訊協定] _\<server instance>_ ，然後選取 [屬性]。  
  
2. 在 [ _\<instance name>_ 屬性的通訊協定]  對話方塊的 [憑證] 索引標籤上，從 [憑證] 方塊的下拉式清單選取所需憑證，然後按一下 [確定]。  
  
3. 在 **[旗標]** 索引標籤的 **[ForceEncryption]** 方塊中選取 **[是]** ，然後按一下 **[確定]** 以關閉對話方塊。  
  
4. 重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務。  

> [!NOTE]
> 若要確保用戶端與伺服器之間的安全連線，請設定用戶端要求加密連線。 [本文稍後](#configure-client)會說明其他詳細資料。

## <a name="configure-client"></a><a name="configure-client"></a>設定用戶端

設定用戶端要求加密連線。

1. 將原始憑證或匯出的憑證檔複製到用戶端電腦。  
  
2. 在用戶端電腦上，使用 [憑證] 嵌入式管理單元安裝根憑證或匯出的憑證檔。  
  
3. 使用 SQL Server 組態管理員，以滑鼠右鍵按一下 [SQL Server Native Client 組態]，然後按一下 [屬性]。  
  
4. 在 **[旗標]** 頁面的 **[強制通訊協定加密]** 方塊中，按一下 **[是]** 。  
  
## <a name="use-sql-server-management-studio"></a>使用 SQL Server Management Studio
  
若要從 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 將連線加密：  

1. 在 [物件總管] 工具列上，按一下 **[連接]** ，再按一下 **[Database Engine]** 。  
  
2. 在 **[連接到伺服器]** 對話方塊中，完成連接資訊，然後按一下 **[選項]** 。  
  
3. 在 **[連接屬性]** 索引標籤上，按一下 **[加密連接]** 。  

## <a name="internet-protocol-security-ipsec"></a>網際網路通訊協定安全性 (IPSec)
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料可以利用 IPSec 在傳輸期間加密。 IPSec 是由用戶端和伺服器作業系統所提供，不需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態。 如需有關 IPSec 的詳細資訊，請參閱 Windows 或網路文件。

## <a name="next-steps"></a>後續步驟

+ [Microsoft SQL Server 的 TLS 1.2 支援](https://support.microsoft.com/kb/3135244)     
+ [設定 Windows 防火牆以允許 SQL Server 存取](../../sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access.md)     
+ [Powershell Cmdlet New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate)