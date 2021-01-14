---
title: 檔案位置
description: SQL Server 執行個體有自己的程式和資料檔案。 它可與 SQL Server 的其他執行個體共用一般檔案。 本文包含檔案位置。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
ms.assetid: 463c570e-9f75-4653-b3b8-4d61753b0013
author: cawrites
ms.author: chadam
ms.openlocfilehash: e109b68dbf9b2e2b1d119f2f676896f5fe682aaf
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172700"
---
# <a name="file-locations-for-default-and-named-instances-of-sql-server"></a>SQL Server 的預設和具名執行個體的檔案位置
[!INCLUDE [SQL Server Windows Only - ASDBMI ](../../includes/applies-to-version/sql-windows-only-asdbmi.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的安裝是由一個或多個不同的執行個體所組成。 不論是預設或具名，執行個體都有自己的一組程式和資料檔案，以及在電腦上所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之間共用的一組共同檔案。  
  
 如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體包括 [!INCLUDE[ssDE](../../includes/ssde-md.md)]、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]和 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]，則每一個元件都有一組完整的資料檔案和可執行檔，以及所有元件共用的共同檔案。  
  
 為了隔離每一個元件的安裝位置，會針對給定的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體內的每一個元件產生唯一的執行個體識別碼。  
  
> [!IMPORTANT]  
>  程式檔和資料檔案不能安裝在抽取式磁碟機以及使用壓縮的檔案系統上、不能安裝在系統檔案所在的目錄中，也不能安裝在容錯移轉叢集執行個體上的共用磁碟機。  
>  
>  您可能需要設定掃描軟體，例如防毒和反間諜軟體應用程式，以排除 SQL Server 資料夾和檔案類型。 如需詳細資訊，請參閱下列支援文章：[Antivirus software on computers running SQL Server](https://support.microsoft.com/kb/309422) (執行 SQL Server 之電腦上的防毒軟體)。
> 
>  系統資料庫 (Master、Model、MSDB 和 TempDB) 與 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 使用者資料庫可以當做儲存選項與伺服器訊息區塊 (SMB) 檔案伺服器一起安裝。 這同時適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 獨立安裝和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 容錯移轉叢集安裝 (FCI)。 如需詳細資訊，請參閱 [將 SQL Server 與 SMB Fileshare 當做儲存選項一起安裝](../../database-engine/install-windows/install-sql-server-with-smb-fileshare-as-a-storage-option.md)。  
>   
>  請勿刪除下列任何一個目錄或是其內容：Binn、Data、Ftdata、HTML 或 1033。 必要時，您可以刪除其他目錄。不過，如果您沒有解除安裝後再重新安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的話，可能會無法擷取任何遺失的功能或資料。 請勿刪除或修改 HTML 目錄中的任何 .htm 檔。 這些檔案是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 工具得以正常運作所不可或缺的要素。  
  
## <a name="shared-files-for-all-instances-of-ssnoversion"></a>所有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]  
 在單一電腦上，所有執行個體所使用的通用檔案會安裝在資料夾 [!INCLUDE[ssInstallPathVar](../../includes/ssinstallpathvar-md.md)]。 \<*drive*> 是安裝元件的磁碟機代號。 預設通常是磁碟機 C。_nnn_ 會識別版本。 下表識別了路徑的版本。 \{nn} 是在執行個體識別碼及登錄路徑中使用的版本值。 

|版本|\*nnn*|{nn}|
|-----|-----|--------|
|[!INCLUDE[ssqlv15](../../includes/sssqlv15-md.md)]| 150| 15| 
|[!INCLUDE[ssqlv14](../../includes/sssqlv14-md.md)]| 140| 14| 
|[!INCLUDE[ssqlv13](../../includes/sssql16-md.md)]| 130| 13 | 
|[!INCLUDE[ssqlv12](../../includes/sssql14-md.md)]  | 120|12 | 
|[!INCLUDE[sssql11](../../includes/sssql11-md.md)] | 110|11 | 
  
## <a name="file-locations-and-registry-mapping"></a>檔案位置和登錄對應  
 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝期間，系統會為每一個伺服器元件產生一個執行個體識別碼。 這個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中的伺服器元件是 [!INCLUDE[ssDE](../../includes/ssde-md.md)]、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]和 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]。  
  
 預設執行個體識別碼是使用以下格式建構的：  
  
-   [!INCLUDE[ssDE](../../includes/ssde-md.md)]的 MSSQL，後面接著主要版本號碼、底線、次要版本 (如果適用的話) 和句點，再接著執行個體名稱。  
  
-   [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]的 MSAS，後面接著主要版本號碼、底線、次要版本 (如果適用的話) 和句點，再接著執行個體名稱。  
  
-   [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]的 MSRS，後面接著主要版本號碼、底線、次要版本 (如果適用的話) 和句點，再接著執行個體名稱。  
  
 此 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中的預設執行個體識別碼範例如下：  
  
-   [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 預設執行個體的 MSSQL\{nn}.MSSQLSERVER。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Analysis Services 預設執行個體的 MSAS\{nn}.MSSQLSERVER。  
  
-   名為 "MyInstance" [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 具名執行個體的 MSSQL\{nn}.MyInstance。  
  

 包含 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 和 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 而且已安裝到預設目錄之 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]具名執行個體 "MyInstance" 的目錄結構將會如下所示：  
  
-   C:\Program Files\Microsoft SQL Server\MSSQL\{nn}.MyInstance\  
  
-   C:\Program Files\Microsoft SQL Server\MSAS\{nn}.MyInstance\  
  
 您可以為執行個體識別碼指定任何值，但是請避免特殊字元和保留關鍵字。  
  
 您可以在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝期間指定非預設的執行個體識別碼。 如果使用者選擇變更預設安裝目錄，則可改用 \<custom path>\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，而不使用 \\{Program Files}\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 請注意，不支援以底線 (_) 為開頭或是包含數字符號 (#) 或貨幣符號 ($) 的執行個體識別碼。  
  
> [!NOTE]  
>  [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 和用戶端元件都不會感知執行個體，因此，也不會被指派執行個體識別碼。 根據預設，系統會將非執行個體感知的元件安裝到單一目錄： [!INCLUDE[ssInstallPathVar](../../includes/ssinstallpathvar-md.md)]。 變更某個共用元件的安裝路徑也會變更其他共用元件的安裝路徑。 後續安裝會將非執行個體感知的元件安裝到與原始安裝相同的目錄。  
  
 安裝之後，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 是唯一支援執行個體重新命名的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件。 如果 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 的執行個體已重新命名，執行個體識別碼將不會變更。 當執行個體重新命名完成之後，目錄和登錄機碼將會繼續使用安裝期間所建立的執行個體識別碼。  
  
 執行個體感知元件的登錄區會建立在 HKLM\Software\\[!INCLUDE[msCoName](../../includes/msconame-md.md)]\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\\<*Instance_ID*> 之下。 例如，  
  
-   HKLM\Software\\[!INCLUDE[msCoName](../../includes/msconame-md.md)]\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSSQL\{nn}.MyInstance  
  
-   HKLM\Software\\[!INCLUDE[msCoName](../../includes/msconame-md.md)]\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSAS\{nn}.MyInstance  
  
-   HKLM\Software\\[!INCLUDE[msCoName](../../includes/msconame-md.md)]\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSRS\{nn}.MyInstance  
  
 登錄也會維護執行個體識別碼到執行個體名稱的對應。 執行個體識別碼到執行個體名稱的對應維護如下：  
  
-   [HKEY_LOCAL_MACHINE\Software\\[!INCLUDE[msCoName](../../includes/msconame-md.md)]\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\Instance Names\SQL] "\<InstanceName>"="MSSQL\{nn}"  
  
-   [HKEY_LOCAL_MACHINE\Software\\[!INCLUDE[msCoName](../../includes/msconame-md.md)]\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\Instance Names\OLAP] "\<InstanceName>"="MSAS\{nn}"  
  
-   [HKEY_LOCAL_MACHINE\Software\\[!INCLUDE[msCoName](../../includes/msconame-md.md)]\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\Instance Names\RS] "\<InstanceName>"="MSRS\{nn}"  
  
## <a name="specifying-file-paths"></a>指定檔案路徑  
 在安裝期間，您可以變更下列功能的安裝路徑：  
  
 只有具有使用者可設定目的資料夾的功能，其安裝路徑才會顯示在安裝程式中：  
  
|元件|預設路徑|可設定或固定路徑|  
|---------------|------------------|--------------------------------|  
|[!INCLUDE[ssDE](../../includes/ssde-md.md)] 伺服器元件|\Program Files\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSSQL\{nn}.\<InstanceID>\ |可設定|  
|[!INCLUDE[ssDE](../../includes/ssde-md.md)] 資料檔|\Program Files\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSSQL\{nn}.\<InstanceID>\ |可設定|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 伺服器|\Program Files\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSAS\{nn}.\<InstanceID>\ |可設定|  
|[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 資料檔|\Program Files\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSAS\{nn}.\<InstanceID>\ |可設定|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 報表伺服器|\Program Files\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSRS\{nn}.\<InstanceID>\Reporting Services\ReportServer\Bin\ |可設定|  
|[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 報表管理員|\Program Files\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSRS\{nn}.\<InstanceID>\Reporting Services\ReportManager\ |固定路徑|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|\<Install Directory>\nnn\DTS\\ <sup>1</sup> |可設定 |  
|用戶端元件 (bcp.exe 和 sqlcmd.exe 除外)|\<Install Directory>\nnn\Tools\\ <sup>1</sup> |可設定 |  
|用戶端元件 (bcp.exe 和 sqlcmd.exe)|\<Install Directory>\Client SDK\ODBC\nnn\Tools\Binn|固定路徑|  
|複寫和伺服器端 COM 物件|[!INCLUDE[ssInstallPathVar](../../includes/ssinstallpathvar-md.md)]COM\\ <sup>2</sup> |固定路徑|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 資料轉換執行階段引擎的元件 DLL、資料轉換管線引擎和 **dtexec** 命令提示字元公用程式|[!INCLUDE[ssInstallPathVar](../../includes/ssinstallpathvar-md.md)]DTS\Binn|固定路徑|  
|對 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]|[!INCLUDE[ssInstallPathVar](../../includes/ssinstallpathvar-md.md)]DTS\Connections|固定路徑|  
|[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 支援的每一種類型之列舉值的 DLL|[!INCLUDE[ssInstallPathVar](../../includes/ssinstallpathvar-md.md)]DTS\ForEachEnumerators|固定路徑|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Browser 服務，WMI 提供者|[!INCLUDE[ssInstallPathVar](../../includes/ssinstallpathvar-md.md)]Shared\\ |固定路徑|  
|的所有執行個體之間共用的元件 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]|[!INCLUDE[ssInstallPathVar](../../includes/ssinstallpathvar-md.md)]Shared\\ |固定路徑|  
  
> [!WARNING]
> 請確認 \Program Files\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\ 資料夾受到有限權限的保護。  
  
請注意，檔案位置的預設磁碟機是 *systemdrive*，通常是磁碟機 C。子功能的安裝路徑由父功能的安裝路徑來決定。  
  
<sup>1</sup> 在 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 與用戶端元件之間共用單一安裝路徑。 變更一個元件的安裝路徑也會變更其他元件的安裝路徑。 後續安裝會將元件安裝到與原始安裝相同的位置。  
  
<sup>2</sup> 此目錄供電腦上 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的所有執行個體使用。 如果您將更新項目套用至電腦的任何執行個體，則此資料夾之檔案若有任何變更，電腦上所有執行個體都會受到影響。 將功能加入至現有的安裝時，您不能變更先前安裝之功能的位置，也不能指定新功能的位置。 您必須將其他功能安裝到安裝程式所建立的目錄中，或解除安裝後再重新安裝本產品。  
  
> [!NOTE]  
>  如果是叢集組態，您必須選取該叢集的每一個節點上可用的本機磁碟機。  
  
 當您在安裝期間指定伺服器元件或資料檔案的安裝路徑時，除了程式和資料檔案的指定位置之外，安裝程式還會使用執行個體識別碼。 安裝程式不會使用工具和其他共用檔案的執行個體識別碼。 安裝程式也不會使用 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 程式和資料檔案的任何執行個體識別碼，但是它會使用 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] 儲存機制的執行個體識別碼。  
  
 如果您設定了 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 功能的安裝路徑， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式會使用該路徑做為該安裝作業之所有執行個體特定資料夾的根目錄，包括 SQL 資料檔案在內。 在此情況下，如果將根目錄設定為 "C:\Program Files\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSSQL\{nn}.\<InstanceName>\MSSQL\\"，系統就會在該路徑的結尾新增執行個體特定目錄。  
  
 選擇在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝精靈 (安裝程式 UI 模式) 中使用 USESYSDB 升級功能的客戶，很容易讓自己進入這樣的情況：產品會安裝到遞迴的資料夾結構。 例如，\<*SQLProgramFiles*>\MSSQL14\MSSQL\MSSQL10_50\MSSQL\Data\\。 若要改用 USESYSDB 功能，請設定 SQL 資料檔案功能 (而非 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 功能) 的安裝路徑。  
  
> [!NOTE]
>  您應該可以在 Data 子目錄中找到資料檔案。 例如，如果是在 C:\Program Files\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSSQL\{nn}.\<InstanceName>\MSSQL\Data 底下找到資料檔案，則請在升級時指定 C:\Program Files\\[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]\MSSQL\{nn}.\<InstanceName>\ 以指定系統資料庫的資料目錄根路徑。  
  
## <a name="see-also"></a>另請參閱  
 [Database Engine 組態 - 資料目錄](../../database-engine/install-windows/install-sql-server.md)   
 [Analysis Services 組態 - 資料目錄](../../database-engine/install-windows/install-sql-server.md)  
  
