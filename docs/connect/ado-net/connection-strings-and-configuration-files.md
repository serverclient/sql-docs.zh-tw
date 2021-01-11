---
title: 連接字串與組態檔
description: 了解如何將 ADO.NET 應用程式的連接字串儲存於應用程式組態檔，以作為安全性與維護的最佳做法。
ms.date: 11/13/2020
dev_langs:
- csharp
ms.assetid: 37df2641-661e-407a-a3fb-7bf9540f01e8
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 8f1e0ac5fc52576f0e47ccb8be4b8e90899c6e68
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771450"
---
# <a name="connection-strings-and-configuration-files"></a>連接字串與組態檔

[!INCLUDE[appliesto-netfx-netcore-xxxx-md](../../includes/appliesto-netfx-netcore-xxxx-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

在應用程式的程式碼中嵌入連接字串可能會導致安全性漏洞和維護問題。 編譯到應用程式原始程式碼中的未加密連接字串，可使用 [Ildasm.exe (IL 反組譯工具)](/dotnet/framework/tools/ildasm-exe-il-disassembler) 工具進行檢視。 此外，如果連接字串變更，應用程式就必須重新編譯。 基於上述理由，建議您將連接字串儲存在應用程式組態檔中。

## <a name="work-with-application-configuration-files"></a>使用應用程式組態檔

應用程式組態檔包含特定應用程式專屬的設定。 例如，ASP.NET 應用程式可能擁有一或多個 **web.config** 檔案，Windows 應用程式則可能具有選擇性的 **app.config** 檔案。 組態檔會共用通用的項目，但組態檔的名稱及位置則會根據應用程式的主機而不同。

### <a name="the-connectionstrings-section"></a>connectionStrings 區段

連接字串可以用索引鍵/值對的方式，儲存在應用程式組態檔 **configuration** 項目的 **connectionStrings** 區段中。 子項目包含 **add**、**clear** 和 **remove**。

下列組態檔片段示範儲存連接字串的結構描述和語法。 **name** 屬性是用於唯一識別連接字串的名稱，可藉此在執行階段擷取該連接字串。 **providerName** 是具有 Microsoft SqlClient Data Provider for SQL Server 的 **Microsoft.Data.SqlClient**。

```xml  
<?xml version='1.0' encoding='utf-8'?>  
  <configuration>  
    <connectionStrings>  
      <clear />  
      <add name="Name"
       providerName="Microsoft.Data.SqlClient"
       connectionString="Valid Connection String;" />  
    </connectionStrings>  
  </configuration>  
```  

> [!NOTE]
> 您可將部分連接字串儲存在組態檔中，然後在執行階段使用 <xref:System.Data.Common.DbConnectionStringBuilder> 類別 (Class) 加以完成。 在您無法預先知道連接字串的項目，或者不想將機密資訊儲存在組態檔中時，這種方法很有用。 如需詳細資訊，請參閱[連接字串建置器](connection-string-builders.md)。

### <a name="use-external-configuration-files"></a>使用外部組態檔

外部組態檔是包含組態檔片段 (由單一區段組成) 的個別檔案。 外部組態檔接著會由主組態檔來參考。 將 **connectionStrings** 區段儲存在實際分開的檔案中，對於在部署應用程式之後可能會編輯連接字串的情況很有用。 例如，標準的 ASP.NET 行為是在組態檔修改時重新啟動應用程式網域，而這可能導致狀態資訊遺失。 然而，修改外部組態檔並不會造成應用程式重新啟動。 外部組態檔並不僅限於 ASP.NET 才有，Windows 應用程式也可加以利用； 此外，也可以透過檔案存取安全性和權限，限制對外部組態檔的存取權。 執行階段的外部組態檔使用是透明的，而且不需要任何特殊的程式碼。

若要將連接字串儲存於外部組態檔，請建立僅包含 **connectionStrings** 區段的個別檔案。 請勿包含任何額外的項目、區段或屬性。 此範例說明外部組態檔的語法。

```xml  
<connectionStrings>  
  <add name="Name"
   providerName="Microsoft.Data.SqlClient"
   connectionString="Valid Connection String;" />  
</connectionStrings>  
```  

 在主應用程式組態檔中，可以使用 **configSource** 屬性來指定外部檔案的完整名稱及位置。 此範例會參考名為 `connections.config` 的外部組態檔。

```xml  
<?xml version='1.0' encoding='utf-8'?>  
<configuration>  
    <connectionStrings configSource="connections.config"/>  
</configuration>  
```  

## <a name="retrieving-connection-strings-at-run-time"></a>在執行階段擷取連接字串

.NET Framework 2.0 在 <xref:System.Configuration> 命名空間 (Namespace) 中導入了新的類別，可簡化在執行階段從組態檔中擷取連接字串的作業。 您可以透過程式設計的方式，依名稱或提供者名稱擷取連接字串。

> [!NOTE]
> **machine.config** 檔案也包含 **connectionStrings** 區段，後者則包含 Visual Studio 所使用的連接字串。 從 Windows 應用程式中的 **app.config** 檔案依提供者名稱擷取連接字串時，首先會載入 **machine.config** 中的連接字串，然後才會載入 **app.config** 的項目。如果在 **connectionStrings** 元素之後緊接著新增 **clear**，就會從記憶體中的資料結構移除所有繼承的參考，如此便只會考量本機 **app.config** 檔案中所定義的連接字串。

### <a name="work-with-the-configuration-files"></a>使用組態檔

從 .NET Framework 2.0 開始，在本機電腦上使用組態檔時，就會使用 <xref:System.Configuration.ConfigurationManager> 來取代已被取代的 <xref:System.Configuration.ConfigurationSettings>。 <xref:System.Web.Configuration.WebConfigurationManager> 則會用於搭配 ASP.NET 組態檔。 這是為了在 Web 伺服器上使用組態檔而設計，可透過程式設計的方式存取 **system.web** 之類的組態檔區段。

> [!NOTE]
> 您必須為呼叫端授與權限，才能在執行階段存取組態檔；所需的權限則根據應用程式類型、組態檔以及位置而有所不同。 如需詳細資訊，請參閱[使用組態類別](/previous-versions/aspnet/ms228063(v=vs.100))和 <xref:System.Web.Configuration.WebConfigurationManager> (適用於 ASP.NET 應用程式) 以及 <xref:System.Configuration.ConfigurationManager> (適用於 Windows 應用程式)。

您可以利用 <xref:System.Configuration.ConnectionStringSettingsCollection> 從應用程式組態檔擷取連接字串。 此項目包含 <xref:System.Configuration.ConnectionStringSettings> 物件的集合，而其中每個物件都代表 **connectionStrings** 區段中的單一項目。 其屬性會對應至連接字串屬性，讓您可以藉由指定名稱或提供者名稱而擷取連接字串。

|屬性|描述|  
|--------------|-----------------|  
|<xref:System.Configuration.ConnectionStringSettings.Name%2A>|連接字串的名稱。 對應至 **name** 屬性。|  
|<xref:System.Configuration.ConnectionStringSettings.ProviderName%2A>|完整的提供者名稱。 對應至 **providerName** 屬性。|  
|<xref:System.Configuration.ConnectionStringSettings.ConnectionString%2A>|連接字串。 對應至 **connectionString** 屬性。|  

### <a name="example-listing-all-connection-strings"></a>範例：列出所有連接字串

此範例會逐一查看 <xref:System.Configuration.ConnectionStringSettingsCollection>，並在主控台視窗中顯示 <xref:System.Configuration.ConnectionStringSettings.Name%2A?displayProperty=nameWithType>、<xref:System.Configuration.ConnectionStringSettings.ProviderName%2A?displayProperty=nameWithType> 與 <xref:System.Configuration.ConnectionStringSettings.ConnectionString%2A?displayProperty=nameWithType> 屬性。

> [!NOTE]
> System.Configuration.dll 並未包含在所有專案類型中，您可能需要設定其參考，才能使用該組態類別。 特定應用程式組態檔的名稱和位置會根據應用程式類型及裝載處理序而不同。

[!code-csharp[DataWorks ConnectionStringSettings.RetrieveFromConfig#1](~/../SqlClient/doc/samples/ConnectionStringSettings_RetrieveFromConfig.cs#1)]

### <a name="example-retrieving-a-connection-string-by-name"></a>範例：依名稱擷取連接字串

此範例示範如何藉由指定連接字串的名稱，從組態檔擷取連接字串。 程式碼會建立 <xref:System.Configuration.ConnectionStringSettings> 物件，並將提供的輸入參數與 <xref:System.Configuration.ConfigurationManager.ConnectionStrings%2A> 名稱進行比對。 如果沒有找到符合的名稱，函式就會傳回 `null` (在 Visual Basic 中則為 `Nothing`)。

[!code-csharp[DataWorks ConnectionStringSettings.RetrieveFromConfigByName#1](~/../SqlClient/doc/samples/ConnectionStringSettings_RetrieveFromConfigByName.cs#1)]

### <a name="example-retrieving-a-connection-string-by-provider-name"></a>範例：依提供者名稱擷取連接字串

此範例示範如何透過指定 *Microsoft.Data.SqlClient* 格式的提供者名稱來擷取連接字串。 程式碼會逐一查看 <xref:System.Configuration.ConnectionStringSettingsCollection>，並針對第一個找到的 <xref:System.Configuration.ConnectionStringSettings.ProviderName%2A> 傳回連接字串。 如果找不到提供者名稱，函式就會傳回 `null` (在 Visual Basic 中則為 `Nothing`)。

[!code-csharp[DataWorks ConnectionStringSettings.RetrieveFromConfigByProvider#1](~/../SqlClient/doc/samples/ConnectionStringSettings_RetrieveFromConfigByProvider.cs#1)]

## <a name="encrypt-configuration-file-sections-using-protected-configuration"></a>使用受保護的組態來加密組態檔區塊

ASP.NET 2.0 導入一項稱為「受保護的組態」的新功能，可用於加密組態檔中的機密資訊。 雖然主要是針對 ASP.NET 所設計，但這項功能仍可用來加密 Windows 應用程式中的組態檔區段。 如需受保護之組態功能的詳細描述，請參閱[使用受保護的組態加密組態資訊](/previous-versions/aspnet/53tyfkaw(v=vs.100))。

下列組態檔片段顯示加密之後的 **connectionStrings** 區段。 **configProtectionProvider** 會指定用於加密及解密連接字串的受保護組態提供者。 **EncryptedData** 區段包含加密文字。

```xml  
<connectionStrings configProtectionProvider="DataProtectionConfigurationProvider">  
  <EncryptedData>  
    <CipherData>  
      <CipherValue>AQAAANCMnd8BFdERjHoAwE/Cl+sBAAAAH2... </CipherValue>  
    </CipherData>  
  </EncryptedData>  
</connectionStrings>  
```  

在執行階段擷取加密的連接字串時，.NET Framework 會使用指定的提供者對 **CipherValue** 進行解密，並將其提供給應用程式使用。 您不需要撰寫任何額外的程式碼來管理解密程序。

### <a name="protected-configuration-providers"></a>受保護的組態提供者

受保護的組態提供者會在本機電腦上登錄於 **machine.config** 檔案的 **configProtectedData** 區段，如下列片段所示，此片段顯示 .NET Framework 所提供的兩個受保護的組態提供者。 為了便於讀取，這裡顯示的值已經過刪減。

```xml  
<configProtectedData defaultProvider="RsaProtectedConfigurationProvider">  
  <providers>  
    <add name="RsaProtectedConfigurationProvider"
      type="System.Configuration.RsaProtectedConfigurationProvider" />  
    <add name="DataProtectionConfigurationProvider"
      type="System.Configuration.DpapiProtectedConfigurationProvider" />  
  </providers>  
</configProtectedData>  
```  

您可以將額外的受保護組態提供者新增至 **machine.config** 檔案來進行設定。 也可以從 <xref:System.Configuration.ProtectedConfigurationProvider> 抽象基底類別 (Base Class) 繼承而建立自己的受保護組態提供者。 下表說明 .NET Framework 所隨附的兩個組態檔。

|提供者|描述|  
|--------------|-----------------|  
|<xref:System.Configuration.RsaProtectedConfigurationProvider>|使用 RSA 加密演算法來加密及解密資料。 RSA 演算法可用於公開金鑰 (Public Key) 加密及數位簽章。 這種演算法也稱為「公開金鑰」或非對稱加密，因為它會使用兩種不同的金鑰。 您可以使用 [ASP.NET IIS 註冊工具 (Aspnet_regiis.exe)](/previous-versions/dotnet/netframework-3.5/k6h9cz8h(v=vs.90)) 來加密 Web.config 檔案中的區段並管理加密金鑰。 ASP.NET 會在處理檔案時對組態檔進行解密。 ASP.NET 應用程式的識別必須可以讀取用於對區段進行加密及解密的加密金鑰。|  
|<xref:System.Configuration.DpapiProtectedConfigurationProvider>|使用 Windows Data Protection API (DPAPI) 來加密組態區段。 DPAPI 使用 Windows 內建的密碼編譯服務，可以針對電腦特定或使用者帳戶特定的保護進行設定。 電腦特定的保護特別適用於相同伺服器上需要共用資訊的多個應用程式。 使用者特定的保護則可用於使用特定使用者識別執行的服務，例如共用的裝載環境。 每個應用程式都會以個別的身分識別來執行，其會限制對檔案與資料庫等資源的存取。|

這兩種提供者都提供高度加密的資料。 不過，如果您打算在多個伺服器 (例如 Web 伺服陣列) 上使用相同的加密組態檔，則只有使用 <xref:System.Configuration.RsaProtectedConfigurationProvider> 才能匯出用於加密資料的加密金鑰並將其匯入另一個伺服器。 如需詳細資訊，請參閱[匯入和匯出受保護的組態 RSA 金鑰容器](/previous-versions/aspnet/yxw286t2(v=vs.100))。

### <a name="use-the-configuration-classes"></a>使用組態類別

<xref:System.Configuration> 命名空間 (Namespace) 提供類別 (Class)，以透過程式設計的方式使用組態設定。 <xref:System.Configuration.ConfigurationManager> 類別可用於存取電腦、應用程式及使用者組態檔。 如果要建立 ASP.NET 應用程式，您可以使用 <xref:System.Web.Configuration.WebConfigurationManager> 類別，此類別會提供相同功能，同時還允許您存取 ASP.NET 應用程式獨有的設定，例如可在 **\<system.web>** 中找到的設定。

> [!NOTE]
> <xref:System.Security.Cryptography> 命名空間包含可為資料加密及解密提供額外選項的類別。 如果需要無法使用受保護組態而提供的密碼編譯服務，請使用這些類別。 這其中某些類別是 Unmanaged Microsoft CryptoAPI 的包裝函式，某些則純粹是 Managed 實作 (Implementation)。 如需詳細資訊，請參閱[密碼編譯服務](/previous-versions/visualstudio/visual-studio-2008/93bskf9z(v=vs.90))。

### <a name="appconfig-example"></a>App.config 範例

此範例示範如何在 Windows 應用程式的 **app.config** 檔案中切換 **connectionStrings** 區段的加密。 在此範例中，程序會採用應用程式的名稱做為引數，例如 "MyApplication.exe"。 接下來會加密 **app.config** 檔案，並將其複製到 "MyApplication.exe.config" 名稱下包含可執行檔的資料夾。

> [!NOTE]
> 連接字串只能在當初進行加密的電腦上進行解密。

程式碼會使用 <xref:System.Configuration.ConfigurationManager.OpenExeConfiguration%2A> 方法來開啟 **app.config** 檔案進行編輯，<xref:System.Configuration.ConfigurationManager.GetSection%2A> 方法則會傳回 **connectionStrings** 區段。 接著程式碼會檢查 <xref:System.Configuration.SectionInformation.IsProtected%2A> 屬性，並呼叫 <xref:System.Configuration.SectionInformation.ProtectSection%2A> 來加密區段 (如果尚未加密)， 然後再叫用 <xref:System.Configuration.SectionInformation.UnprotectSection%2A> 方法來對區段進行解密。 <xref:System.Configuration.Configuration.Save%2A> 方法則會完成作業並儲存變更。

> [!NOTE]
> 您必須在專案中設定 `System.Configuration.dll` 的參考，程式碼才能執行。

[!code-csharp[DataWorks ConnectionStrings.Encrypt#1](~/../SqlClient/doc/samples/ConnectionStrings_Encrypt.cs#1)]

### <a name="webconfig-example"></a>Web.config 範例

此範例使用 <xref:System.Web.Configuration.WebConfigurationManager.OpenWebConfiguration%2A> 的 `WebConfigurationManager` 方法。 請注意，在此例中可以藉由波狀符號提供 **Web.config** 檔案的相對路徑。 程式碼需要 `System.Web.Configuration` 類別的參考。  
  
[!code-csharp[DataWorks ConnectionStringsWeb.Encrypt#1](~/../SqlClient/doc/samples/ConnectionStringsWeb_Encrypt.cs#1)]

如需保護 ASP.NET 應用程式的詳細資訊，請參閱[保護 ASP.NET 網站](/previous-versions/aspnet/91f66yxt(v=vs.100))。

## <a name="see-also"></a>另請參閱

- [連接字串建立器](connection-string-builders.md)
- [保護連線資訊](protecting-connection-information.md)
- [使用組態類別](/previous-versions/visualstudio/visual-studio-2008/ms228063(v=vs.90))
- [設定應用程式](/dotnet/framework/configure-apps/index)
- [ASP.NET 網站管理](/previous-versions/aspnet/6hy1xzbw(v=vs.100))
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
