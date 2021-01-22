---
description: MSSQLSERVER_6522
title: MSSQLSERVER_6522
ms.custom: ''
ms.date: 12/25/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, vencher, tejasaks, docast
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 6522 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: f48534e104139ee7cbd4eb7602fce2a8e51eeb73
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98597142"
---
# <a name="mssqlserver_6522"></a>MSSQLSERVER_6522
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|6522|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|SQLCLR_UDF_EXEC_FAILED|
|訊息文字|執行使用者自訂常式或彙總 "%.*ls" 時，發生 .NET Framework 錯誤: %ls。|
||

## <a name="explanation"></a>說明

請考慮下列案例。

### <a name="scenario-1"></a>案例 1

您建立參考 Microsoft .NET Framework 組件的通用語言執行平台 (CLR) 常式。 .NET Framework 組件未記錄於 [922672](/troubleshoot/sql/database-design/policy-untested-net-framework-assemblies) 中。 然後，您安裝以 .NET Framework 3.5 或 .NET Framework 2.0 為基礎的 Hotfix。

### <a name="scenario-2"></a>案例 2

您建立組件，然後在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫中註冊該組件。 然後，您在全域組件快取 (GAC) 中安裝不同版本的組件。

當您在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中執行 CLR 常式或使用上述任一案例的組件時，您會收到如下所示的錯誤訊息：

> 伺服器：訊息 6522，層級 16，狀態 2，第 1 行  
執行使用者自訂常式或彙總 'getsid' 時，發生 .NET Framework 錯誤:
>
> System.IO.FileLoadException:無法載入檔案或組件 'System.DirectoryServices, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' 或其相依性的其中之一。 主機存放區中組件的簽章與 GAC 中的組件不同。 (發生例外狀況於 HRESULT:0x80131050)

## <a name="possible-cause"></a>可能的原因

當 CLR 載入組件時，CLR 會驗證 GAC 中是否有相同的組件。 如果 GAC 中有相同的組件，則 CLR 會驗證這些組件的模組版本識別碼 (MVID) 是否相符。 如果這些組件的 MVID 不相符，您會收到[說明](#explanation)一節提及的錯誤訊息。

重新編譯組件時，組件的 MVID 會變更。 因此，如果您更新 .NET Framework，.NET Framework 組件會擁有不同的 MVID，因為那些組件已重新編譯。 此外，如果您更新自己的組件，該組件會重新編譯。 因此，該組件也會有不同的 MVID。

## <a name="user-action"></a>使用者動作

### <a name="action-1"></a>Action 1

若要解決[說明](#explanation)一節中案例 1 的問題，您必須手動更新 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的 .NET Framework 組件。 若要這麼做，請使用 `ALTER ASSEMBLY` 陳述式來指向下列資料夾中 .NET Framework 組件的新版本：

`%Windir%\Microsoft.NET\Framework\Version`

> [!NOTE]
> **Version** 代表您已安裝或更新的 .NET Framework 版本。

### <a name="action-2"></a>動作 2

若要解決[說明](#explanation)一節中案例 2 的問題，請使用 `ALTER ASSEMBLY` 陳述式來更新資料庫中的組件。

如果在您這麼做之後問題仍存在，請從資料庫卸除組件，然後在資料庫中註冊新版本的組件。

## <a name="more-information"></a>詳細資訊

建議您不要使用未記錄於[支援的 SQL Server CLR 裝載環境中未經測試的.NET Framework 組件的原則](/troubleshoot/sql/database-design/policy-untested-net-framework-assemblies)中的 .NET Framework 組件。 此文章列出在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] CLR 主控環境中經過測試的組件。

### <a name="description-of-clr-routines"></a>CLR 常式的描述

CLR 常式包括下列使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 與 .NET Framework CLR 的整合實作的物件：

- 純量值的使用者定義函數 (純量 UDF)
- 資料表值使用者定義函數 (TVF)
- 使用者定義程序 (UDP)
- 使用者定義觸發程序
- 使用者定義資料類型
- 使用者自訂彙總

### <a name="assemblies-to-update-after-you-install-the-net-framework-35"></a>安裝 .NET Framework 3.5 後需更新的組件

安裝 .NET Framework 3.5 後，您必須使用 ALTER ASSEMBLY 陳述式來更新下列組件：

- Accessibility.dll
- AspNetMMCExt.dll
- Cscompmgd.dll
- IEExecRemote.dll
- IEHost.dll
- IIEHost.dll
- Microsoft.Build.Conversion.dll
- Microsoft.Build.Engine.dll
- Microsoft.Build.Framework.dll
- Microsoft.Build.Tasks.dll 
- Microsoft.Build.Utilities.dll 
- Microsoft.CompactFramework.Build.Tasks.dll 
- Microsoft.JScript.dll 
- Microsoft.VisualBasic.Vsa.dll 
- Microsoft.Vsa.dll 
- Microsoft.Vsa.Vb.CodeDOMProcessor.dll 
- Microsoft_VsaVb.dll 
- Sysglobl.dll 
- System.Configuration.Install.dll 
- System.Design.dll 
- System.DirectoryServices.dll 
- System.DirectoryServices.Protocols.dll 
- System.Drawing.dll 
- System.Drawing.Design.dll 
- System.EnterpriseServices.dll 
- System.Management.dll 
- System.Messaging.dll 
- System.Runtime.Serialization.Formatters.Soap.dll 
- System.ServiceProcess.dll 
- System.Web.dll 
- System.Web.Mobile.dll 
- System.Web.RegularExpressions.dll 

這些組件位於下列資料夾：

`%Windir%\Microsoft.NET\Framework\v2.0.50727`

### <a name="how-to-preserve-the-data-from-user-defined-data-types-after-you-drop-an-assembly"></a>如何在卸除組件後保留來自使用者定義資料類型的資料

如果您卸除來自 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的使用者定義資料類型所使用的組件，您可以使用下列其中一種方法來保留資料。

假設有下列案例：

- 您建立名為 *MyAssembly.dll* 的組件。
- MyAssembly 組件會參考 `System.DirectoryServices.dll` 組件。
- 您擁有名為 *MyDateTime* 的使用者定義資料類型。
- *MyDateTime* 資料類型會使用 *MyAssembly .dll* 組件。
- 您建立名為 *MyTable* 的資料表。
- *MyTable* 資料表包含 *MyDateTime* 資料類型的資料。

#### <a name="method-1-use-the-bcpexe-utility"></a>方法 1：使用 bcp.exe 公用程式

1. 搭配 -n 參數使用 Bcp.exe 公用程式，將資料從 MyTable 資料表複製到檔案中。 例如，在命令提示字元中執行下列命令：

    ```console
    bcp MyDatabase.dbo.MyTable out C:\MyFile.bcp -n -SSQLServerName -T
    ```

2. 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Management Studio 中，請遵循下列步驟：
   1. 卸除 MyTable 資料表。
   2. 卸除 MyDateTime 資料類型。
   3. 卸除 `System.DirectoryServices.dll` 組件。
   4. 卸除 MyAssembly 組件。
3. 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Management Studio 中，請遵循下列步驟：

   1. 註冊 `System.DirectoryServices.dll` 組件。
   2. 註冊 MyAssembly 組件。
   3. 建立 MyDateTime 資料類型。
   4. 建立具有與 MyTable 資料表相同資料表結構的新資料表。
4. 搭配 -n 參數使用 Bcp.exe 公用程式，將資料從檔案匯入 MyTable 資料表中。 例如，在命令提示字元中執行下列命令：
  
    ```console
    bcp MyDatabase.dbo.MyTable in C:\MyFile.bcp -n -SSQLServerName -T
    ```

#### <a name="method-2-use-the-insert--select-statement"></a>方法 2：使用 INSERT ...SELECT 陳述式

假設 MyDateTime 資料類型在儲存體中佔用 9 個位元組。

1. 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Management Studio 中，執行下列陳述式以建立包含 `VARBINARY(9)` 資料類型之資料行的新資料表：

    ```sql
    CREATE TABLE TempTable (c1 VARBINARY(9));
    ```

2. 執行下列 INSERT ...SELECT 陳述式來填入 TempTable 資料表：

    ```sql
    INSERT INTO TempTable SELECT CAST(c1 as VARBINARY(9)) FROM MyTable;
    ```

3. 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Management Studio 中，請遵循下列步驟：
   1. 卸除 MyTable 資料表。
   2. 卸除 MyDateTime 資料類型。
   3. 卸除 System.DirectoryServices.dll 組件。
   4. 卸除 MyAssembly 組件。
4. 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Management Studio 中，請遵循下列步驟：
   1. 註冊 System.DirectoryServices.dll 組件。
   2. 註冊 MyAssembly 組件。
   3. 建立 MyDateTime 資料類型。
   4. 建立具有與 MyTable 資料表相同資料表結構的新資料表。
5. 執行下列 INSERT ...SELECT 陳述式來填入 MyTable 資料表：

    ```sql
    INSERT INTO MyTable SELECT c1 FROM TempTable;
    ```

## <a name="references"></a>參考資料

- 如需有關組件版本的詳細資訊，請參閱[已淘汰的 Visual Studio 2005 文件](https://www.microsoft.com/download/details.aspx?id=55984)。
- 如需有關如何更新組件的詳細資訊，請參閱 [ALTER ASSEMBLY (Transact-SQL)](../../t-sql/statements/alter-assembly-transact-sql.md)。
- 如需有關如何卸除組件的詳細資訊，請參閱 [DROP ASSEMBLY (Transact-SQL)](../../t-sql/statements/drop-assembly-transact-sql.md)。
- 如需有關如何在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫中註冊組件的詳細資訊，請參閱 [CREATE ASSEMBLY (Transact-SQL)](../../t-sql/statements/create-assembly-transact-sql.md)。
- 如需有關 Bcp.exe 公用程式的詳細資訊，請參閱 [https://msdn2.microsoft.com/library/ms162802.aspx](../../tools/bcp-utility.md)。