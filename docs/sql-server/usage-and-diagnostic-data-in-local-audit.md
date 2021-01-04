---
title: 本機稽核使用狀況與診斷資料收集
description: 了解 SQL Server 用於收集使用方式與診斷資料並傳送給 Microsoft 的本機審核。
ms.custom: seo-lt-2019
ms.date: 03/27/2019
ms.prod: sql
ms.prod_service: security
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- Local Audit
ms.assetid: a0665916-7789-4f94-9086-879275802cf3
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: f927e003673cb4397250fe532d57452ddb4e6445
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97474559"
---
# <a name="local-audit-for-sql-server-usage-and-diagnostic-data-collection-ceip"></a>SQL Server 使用狀況和診斷資料收集的本機稽核 (CEIP)

[!INCLUDE [SQL Server - Windows only ASDBMI  ](../includes/applies-to-version/sql-windows-only-asdbmi.md)]

## <a name="introduction"></a>簡介

Microsoft SQL Server 包含一些啟用網際網路的功能，而這些功能可能會收集並傳送電腦或裝置的相關資訊。 這稱為「標準電腦資訊」  。 [SQL Server 使用方式和診斷資料收集](usage-and-diagnostic-data-configuration-for-sql-server.md) \(英文\) 的本機稽核元件會將服務收集的資料寫入至指定的資料夾，代表將傳送給 Microsoft 的資料 (記錄)。 本機稽核的目的是要讓客戶看到 Microsoft 以此功能收集的所有資料，以用於相容性、法規或隱私權驗證的理由。  

若為 SQL Server 2016 CU2 和 CU3，則可在 SQL Server 資料庫引擎和 Analysis Services (SSAS) 的執行個體層級設定本機稽核。 若為 SQL Server 2016 CU4、2016 SP1 和更新版本，則也會啟用 SQL Server Integration Services (SSIS) 的本機稽核。 安裝程式執行期間所安裝的其他 SQL Server 元件，以及在安裝程式執行之後下載或安裝的 SQL Server 工具，沒有使用方式和診斷資料收集的本機稽核功能。

## <a name="remarks"></a>備註

 - 不支援移除或停用 SQL CEIP 服務。 
 - 不支援從叢集群組移除 SQL CEIP 資源。 

若要退出資料收集，請參閱[開啟或關閉本機稽核](#turning-local-audit-on-or-off)

## <a name="prerequisites"></a>Prerequisites 

若要在每個 SQL Server 執行個體上啟用本機稽核，下列是必要條件︰ 

1. 執行個體已修補為 SQL Server 2016 RTM CU2 或更新版本。 若為 Integration Services，則執行個體會修補至 SQL 2016 RTM CU4、SQL 2016 SP1 或更新版本。

1. 使用者必須是系統管理員或具有存取權可新增和修改登錄機碼、建立資料夾、管理資料夾安全性及停止/啟動 Windows 服務的角色。  

## <a name="pre-configuration-steps-prior-to-turning-on-local-audit"></a>開啟本機稽核前的預先設定步驟

開啟本機稽核之前，系統管理員必須︰

1. 知道 SQL Server 執行個體名稱和 SQL Server CEIP 服務登入帳戶。 

1. 設定本機稽核檔案的新資料夾。

1. 授與 SQL Server CEIP 服務登入帳戶的權限。

1. 建立登錄機碼設定來設定本機稽核目標目錄。 


### <a name="get-the-sql-server-ceip-service-logon-account"></a>取得 SQL Server CEIP 服務登入帳戶 

執行下列步驟，以取得 SQL Server CEIP 服務登入帳戶
 
1. 啟動 [服務]  主控台。 若要這樣做，請在鍵盤上選取 **Windows 鍵 + R** 來開啟 [執行]  對話方塊。 接下來，在文字欄位中鍵入 *services.msc*，並選取 [確定]  來啟動 [服務]  主控台。  

2. 瀏覽至適當的服務。 例如，針對資料庫引擎，請找出 **SQL Server CEIP 服務** **(您的執行個體名稱  )** 。 針對 Analysis Services，請找出 **SQL Server Analysis Services CEIP** **(您的執行個體名稱  )** 。 若為 Integration Services，則找出 **SQL Server Integration Services CEIP 服務**。

3. 以滑鼠右鍵按一下服務並選擇 [屬性]  。 

4. 選取 [登入]  索引標籤。登入帳戶列在 [這個帳戶]  。 

### <a name="configure-a-new-folder-for-the-local-audit-files"></a>設定本機稽核檔案的新資料夾。    

建立新資料夾 (本機稽核目錄) 供本機稽核寫入記錄檔。 例如，資料庫引擎的預設執行個體的本機稽核目錄完整路徑會是︰*C:\\SQLCEIPAudit\\MSSQLSERVER\\DB\\* 。 
 
  >[!NOTE] 
  >請在 SQL Server 安裝路徑以外設定本機稽核的目錄路徑，以避免允許稽核功能和修補作業造成潛在的 SQL Server 問題。

|設計決策|建議|  
|-----------------|----------|  
|空間可用性 |針對大約 10 個資料庫的一般工作負載，請規劃每個資料庫的每個執行個體大約有 2 MB 的磁碟空間。|  
|個別的目錄 | 為每個執行個體建立目錄。 例如，針對名為 `MSSQLSERVER` 的 SQL Server 執行個體，使用 *C:\\SQLCEIPAudit\\MSSQLSERVER\\DB\\*。 這可簡化檔案管理。
|個別的資料夾 |針對每個服務使用特定的資料夾。 例如，針對給定的執行個體名稱，為資料庫引擎使用一個資料夾。 如果 Analysis Services 的執行個體使用相同的執行個體名稱，請為 Analysis Services 建立個別的資料夾。 資料庫引擎和 Analysis Services 執行個體設定為相同的資料夾將會造成所有本機稽核從兩個執行個體寫入相同的記錄檔。| 
|授與 SQL Server CEIP 服務登入帳戶的權限|啟用 [列出資料夾內容]、[讀取] 和 [寫入] 存取給 SQL Server CEIP 服務登入帳戶|


### <a name="grant-permissions-to-the-sql-server-ceip-service-logon-account"></a>授與 SQL Server CEIP 服務登入帳戶的權限
  
1. 在檔案總管裡，巡覽至新的資料夾所在的位置。

1. 以滑鼠右鍵按一下新資料夾，然後選擇 [屬性]。 

1. 在 [安全性] 索引標籤上，選取 [編輯] 管理權限。

1. 選取 [新增]，並鍵入 SQL Server CEIP 服務的認證。 例如 `NT Service\SQLTELEMETRY` 。

1. 選取 [檢查名稱] 驗證您提供的名稱，然後選取 [確定]。

1. 在 [權限] 對話方塊中，選擇 SQL Server CEIP 服務的登入帳戶，並選取 [列出資料夾內容]、[讀取] 和 [寫入]。

1. 選取 [確定] 立即套用權限變更。 
  
### <a name="create-a-registry-key-setting-to-configure-local-audit-target-directory"></a>建立登錄機碼設定來設定本機稽核目標目錄

1. 啟動 regedit。

1. 巡覽至適當的 CPE 路徑：

   | 版本 | <資料庫引擎>*_ - 登錄機碼 |
   | :------ | :----------------------------- |
   | 2016    | HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server\\MSSQL_ *13**.* Your-Instance-Name*\\CPE |
   | 2017    | HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server\\MSSQL **14**.<您的執行個體名稱>\\CPE |
   | 2019    | HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server\\MSSQL **15**.<您的執行個體名稱>\\CPE |
   | &nbsp; | &nbsp; |

   | 版本 | ***Analysis Services** _ - 登錄機碼 |
   | :------ | :------------------------------- |
   | 2016    | HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server\\MSAS_ *13**.* Your-Instance-Name*\\CPE |
   | 2017    | HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server\\MSAS **14**.<您的執行個體名稱>\\CPE |
   | 2019    | HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server\\MSAS **15**.<您的執行個體名稱>\\CPE |  
   | &nbsp; | &nbsp; |

   | 版本 | ***Integration Services** _ - 登錄機碼 |
   | :------ | :---------------------------------- |
   | 2016    | HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server\\_ *130** |
   | 2017    | HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server\\**140** |
   | 2019    | HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server\\**150** |
   | &nbsp; | &nbsp; |

1. 以滑鼠右鍵按一下 CPE 路徑，然後選擇 [新增]。 選取 [字串值]。

1. 將新的登錄機碼命名為 `UserRequestedLocalAuditDirectory`。 
 
## <a name="turning-local-audit-on-or-off"></a>開啟或關閉本機稽核

完成預先設定步驟之後，您可以開啟本機稽核。 若要這樣做，請使用系統管理員帳戶或具有具有存取權可修改登錄機碼的類似角色，遵循以下步驟來開啟或關閉本機稽核。 

1. 啟動 **regedit**。  

1. 巡覽至適當的 CPE [路徑](#create-a-registry-key-setting-to-configure-local-audit-target-directory)。 

1. 以滑鼠右鍵按一下 **UserRequestedLocalAuditDirectory**，然後選取 [修改]。 

1. 若要開啟本機稽核，請鍵入本機稽核路徑，例如 *C:\\SQLCEIPAudit\\MSSQLSERVER\\DB\\* 。
 
    若要關閉本機稽核，請清空 **UserRequestedLocalAuditDirectory** 中的值。

1. 關閉 **regedit**。 

如果服務已在執行中，SQL Server CEIP 應該會立即辨識本機稽核設定。 若要啟動 SQL Server CEIP 服務，系統管理員或具有啟動或停止 Windows 服務之存取權的任何人，可以遵循下列步驟： 

1. 啟動 [服務] 主控台。 若要這樣做，請在鍵盤上選取 **Windows 鍵 + R** 來開啟 [執行] 對話方塊。 接下來，在文字欄位中鍵入 *services.msc*，並選取 [確定] 來啟動 [服務] 主控台。  

1. 瀏覽至適當的服務。 

    - 若為資料庫引擎，請使用 **SQL Server CEIP 服務 (「您的執行個體名稱」)**。     
    - 若為 Analysis Services，請使用 **SQL Server Analysis Services CEIP (「您的執行個體名稱」)**。
    - 若為 Integration Services， 
        - 如果是 SQL 2016，請使用「SQL Server Integration Services CEIP 服務 13.0」。
        - 如果是 SQL 2017，請使用「SQL Server Integration Services CEIP 服務 14.0」。
    - 針對 SQL 2019，請使用「SQL Server Integration Services CEIP 服務 15.0」。

1. 以滑鼠右鍵按一下服務並選擇 [重新啟動]。 

1. 確定服務的狀態為 **執行中**。 

本機稽核每天會產生一個記錄檔。 記錄檔格式將為 `<YYYY-MM-DD>.json`。 例如， *2016-07-12.json*。 如果指定目錄中已有當天的檔案，本機稽核將附加到其中。 否則，它會建立當天的新檔案。 

  >[!NOTE]
  > 啟用本機稽核之後，可能需要 5 分鐘，才會第一次寫入記錄檔。 

## <a name="maintenance"></a>維護 

1. 若要限制本機稽核寫入檔案的磁碟空間使用量，請設定原則或定期作業來清除本機稽核目錄，移除較舊且不需要的檔案。  

2. 保護本機稽核目錄路徑，讓它只能由適當的人員存取。 請注意，記錄檔包含[如何設定 SQL Server 2016 將意見反應傳送給 Microsoft](https://support.microsoft.com/kb/3153756)中所述的資訊。 此檔案的存取權應該避免大部分組織成員讀取它。  

## <a name="data-dictionary-of-local-audit-output-data-structure"></a>本機稽核輸出資料結構的資料字典 

- 本機稽核記錄檔為 JSON 格式，其中包含一組物件 (資料列)，代表在 **emitTime** 時傳回給 Microsoft 的資料點。
- 每個資料列會遵循特定的結構描述，如 **schemaVersion** 所識別。
- 每個資料列是 SQLCEIP 服務工作階段的輸出，如 **sessionID** 所識別。
- 資料列會依 **sequence** 所識別的順序發出。
- 每個資料點資料列包含 **queryIdentifier** 的輸出，這可以是 T-SQL 查詢、XE 工作階段或與某種追蹤相關的訊息，識別為 **traceName**。
- **queryIdentifiers** 會與 **querySetVersion** 一起分組並建立版本。
- **data** 包含對應查詢執行的輸出，此執行花費時間為 **queryTimeInTicks**。
- T-SQL 查詢的 **queryIdentifiers** 會具有查詢中所儲存的 T-SQL 查詢定義。

| 邏輯本機稽核資訊階層 | 相關的資料行 |
| ------ | -------|
| 標頭 | emitTime, schemaVersion 
| 電腦 | operatingSystem 
| 執行個體 | instanceUniqueID、correlationID、clientVersion 
| 工作階段 | sessionID, traceName 
| 查詢 | sequence, querySetVersion, queryIdentifier, query, queryTimeInTicks 
| 資料 |  data 

### <a name="namevalue-pairs-definition-and-examples"></a>名稱/值配對的定義和範例 

下面所列的資料行代表本機稽核檔案輸出的順序。 使用 SHA 256 的單向雜湊用於底下許多資料行的匿名值。  

| 名稱 | 描述 | 範例值
|-------|--------| ----------|
|instanceUniqueID| 匿名的執行個體識別碼 | 888770C4D5A8C6729F76F33D472B28883AE518C92E1999888B171A085059FD 
|schemaVersion| SQLCEIP 結構描述版本 |  3 
|emitTime |UTC 的資料點發出時間 | 2016-09-08T17:20:22.1124269Z 
|sessionID | 要服務 SQLCEIP 服務用的工作階段識別碼 | 89decf9a-ad11-485c-94a7-fefb3a02ed86 
|correlationId | 其他識別碼的預留位置 | 0 
|sequence | 工作階段內傳送的資料點序號 | 15 
|clientVersion | SQL Server 執行個體版本 | 13.0.2161.3 ((SQL16_RTM_QFE-CU).160907-1223) 
|operatingSystem | 安裝 SQL Server 執行個體的作業系統版本 | Microsoft Windows Server 2012 R2 Datacenter 
|querySetVersion | 一組查詢定義的版本 | 1.0.0.0 
|traceName | 追蹤的類別：(SQLServerXeQueries, SQLServerPeriodicQueries, SQLServerOneSettingsException) | SQLServerPeriodicQueries 
|queryIdentifier | 查詢的識別碼 | SQLServerProperties.002 
|data   | 所收集到 queryIdentifier 相關資訊的輸出，它作為 T-SQL 查詢、XE 工作階段或應用程式的輸出 |  [{"Collation": "SQL_Latin1_General_CP1_CI_AS","SqlFTinstalled": "0" "SqlIntSec": "1","IsSingleUser": "0","SqlFilestreamMode": "0","SqlPbInstalled": "0","SqlPbNodeRole": "","SqlVersionMajor": "13","SqlVersionMinor": "0","SqlVersionBuild": "2161","ProductBuildType": "","ProductLevel": "RTM","ProductUpdateLevel": "CU2","ProductUpdateReference": "KB3182270","ProductRevision": "3","SQLEditionId": "-1534726760","IsClustered": "0","IsHadrEnabled": "0","SqlAdvAInstalled": "0","PacketReceived": "1210","Version": "Microsoft SQL Server 2016 (RTM-CU2) (KB3182270) - 13.0.2161.3 (X64) \n\tSep  7 2016 14:24:16 \n\tCopyright (c) Microsoft Corporation\n\tStandard Edition (64-bit) on Windows Server 2012 R2 Datacenter 6.3 \u003cX64\u003e (Build 9600: ) (Hypervisor)\n"}],
|查詢| 如果適用的話，此為與產生資料之 queryIdentifier 相關的 T-SQL 查詢定義。        此元件不會由 SQL Server CEIP 服務上傳。 它包含在本機稽核中，僅供客戶參考之用。| SELECT\n      SERVERPROPERTY(\u0027Collation\u0027) AS [Collation],\n      SERVERPROPERTY(\u0027IsFullTextInstalled\u0027) AS [SqlFTinstalled],\n      SERVERPROPERTY(\u0027IsIntegratedSecurityOnly\u0027) AS [SqlIntSec],\n      SERVERPROPERTY(\u0027IsSingleUser\u0027) AS [IsSingleUser],\n      SERVERPROPERTY (\u0027FileStreamEffectiveLevel\u0027) AS [SqlFilestreamMode],\n      SERVERPROPERTY(\u0027IsPolyBaseInstalled\u0027) AS [SqlPbInstalled],\n      SERVERPROPERTY(\u0027PolyBaseRole\u0027) AS [SqlPbNodeRole],\n      SERVERPROPERTY(\u0027ProductMajorVersion\u0027) AS [SqlVersionMajor],\n      SERVERPROPERTY(\u0027ProductMinorVersion\u0027) AS [SqlVersionMinor],\n      SERVERPROPERTY(\u0027ProductBuild\u0027) AS [SqlVersionBuild],\n      SERVERPROPERTY(\u0027ProductBuildType\u0027) AS ProductBuildType,\n      SERVERPROPERTY(\u0027ProductLevel\u0027) AS ProductLevel,\n      SERVERPROPERTY(\u0027ProductUpdateLevel\u0027) AS ProductUpdateLevel,\n      SERVERPROPERTY(\u0027ProductUpdateReference\u0027) AS ProductUpdateReference,\n      RIGHT(CAST(SERVERPROPERTY(\u0027ProductVersion\u0027) AS NVARCHAR(30)),CHARINDEX(\u0027.\u0027, REVERSE(CAST(SERVERPROPERTY(\u0027ProductVersion\u0027) AS NVARCHAR(30)))) - 1) AS ProductRevision,\n      SERVERPROPERTY(\u0027EditionID\u0027) AS SQLEditionId,\n      SERVERPROPERTY(\u0027IsClustered\u0027) AS IsClustered,\n      SERVERPROPERTY(\u0027IsHadrEnabled\u0027) AS IsHadrEnabled,\n      SERVERPROPERTY(\u0027IsAdvancedAnalyticsInstalled\u0027) AS [SqlAdvAInstalled],\n      @@PACK_RECEIVED AS PacketReceived,\n      @@VERSION AS Version
|queryTimeInTicks | 具有下列追蹤類別的查詢執行所需的持續時間：(SQLServerXeQueries, SQLServerPeriodicQueries) |  0 
 
### <a name="trace-categories"></a>追蹤類別 
目前，我們會收集下列追蹤類別︰ 

- **SQLServerXeQueries**︰包含透過擴充事件工作階段所收集的資料點。
- **SQLServerPeriodicQueries**︰包含透過在 SQL Server 執行個體中定期執行的查詢所收集的資料點。
- **SQLServerPerDBPeriodicQueries**︰包含透過在 SQL Server 執行個體中的最多 30 個資料庫定期執行的查詢所收集的資料點。
- **SQLServerOneSettingsException**︰包含與更新結構描述和 (或) 查詢集相關的例外狀況訊息。
- **DigitalProductID**︰包含用於彙總 SQL Server 執行個體匿名 (SHA-256) 雜湊數位產品識別碼的資料點。 

### <a name="local-audit-file-examples"></a>本機稽核檔案範例



以下是本機稽核的 JSON 檔案輸出摘錄。

```JSON
[
  {
    "instanceUniqueId": "888770C4D5A8C6729F76F33D472B28883AE518C92E1999888B171A085059FD",
    "isSSEIInstance": "0",
    "schemaVersion": "5",
    "emitTime": "2018-05-04T15:27:59.7031518Z",
    "sessionId": "c3cd1b56-ab61-462f-8363-8881779aa223",
    "correlationId": 0,
    "sequence": 18,
    "clientVersion": "14.0.3025.34 ((SQLServer2017-CU6).180410-0033)",
    "isInternalMachine": "1",
    "operatingSystem": "Microsoft Windows 10 Enterprise",
    "querySetVersion": "14.0.3025.34",
    "traceName": "SQLServerPeriodicQueries",
    "queryIdentifier": "SQLServerProperties.002",
    "data": [
      {
        "Collation": "SQL_Latin1_General_CP1_CI_AS",
        "SqlFTinstalled": "0",
        "SqlIntSec": "1",
        "IsSingleUser": "0",
        "SqlFilestreamMode": "2",
        "SqlPbInstalled": "1",
        "SqlPbNodeRole": "Head",
        "SqlVersionMajor": "14",
        "SqlVersionMinor": "0",
        "SqlVersionBuild": "3025",
        "ProductBuildType": "",
        "ProductLevel": "RTM",
        "ProductUpdateLevel": "CU6",
        "ProductUpdateReference": "KB4101464",
        "ProductRevision": "34",
        "SQLEditionId": "1872460670",
        "IsClustered": "0",
        "IsHadrEnabled": "0",
        "SqlAdvAInstalled": "1",
        "PacketReceived": "422",
        "Version": "Microsoft SQL Server 2017 (RTM-CU6) (KB4101464) - 14.0.3025.34 (X64) \n\tApr  9 2018 18:00:41 \n\tCopyright (C) 2017 Microsoft Corporation\n\tEnterprise Edition: Core-based Licensing (64-bit) on Windows 10 Enterprise 10.0 <X64> (Build 16299: )\n"
      }
    ],
    "query": "SELECT\n      SERVERPROPERTY('Collation') AS [Collation],\n      SERVERPROPERTY('IsFullTextInstalled') AS [SqlFTinstalled],\n      SERVERPROPERTY('IsIntegratedSecurityOnly') AS [SqlIntSec],\n      SERVERPROPERTY('IsSingleUser') AS [IsSingleUser],\n      SERVERPROPERTY ('FileStreamEffectiveLevel') AS [SqlFilestreamMode],\n      SERVERPROPERTY('IsPolyBaseInstalled') AS [SqlPbInstalled],\n      SERVERPROPERTY('PolyBaseRole') AS [SqlPbNodeRole],\n      SERVERPROPERTY('ProductMajorVersion') AS [SqlVersionMajor],\n      SERVERPROPERTY('ProductMinorVersion') AS [SqlVersionMinor],\n      SERVERPROPERTY('ProductBuild') AS [SqlVersionBuild],\n      SERVERPROPERTY('ProductBuildType') AS ProductBuildType,\n      SERVERPROPERTY('ProductLevel') AS ProductLevel,\n      SERVERPROPERTY('ProductUpdateLevel') AS ProductUpdateLevel,\n      SERVERPROPERTY('ProductUpdateReference') AS ProductUpdateReference,\n      RIGHT(CAST(SERVERPROPERTY('ProductVersion') AS NVARCHAR(30)),CHARINDEX('.', REVERSE(CAST(SERVERPROPERTY('ProductVersion') AS NVARCHAR(30)))) - 1) AS ProductRevision,\n      SERVERPROPERTY('EditionID') AS SQLEditionId,\n      SERVERPROPERTY('IsClustered') AS IsClustered,\n      SERVERPROPERTY('IsHadrEnabled') AS IsHadrEnabled,\n      SERVERPROPERTY('IsAdvancedAnalyticsInstalled') AS [SqlAdvAInstalled],\n      @@PACK_RECEIVED AS PacketReceived,\n      @@VERSION AS Version",
    "queryTimeInTicks": 0
  },
  {
    "instanceUniqueId": "8884F770C4D5A8C6729F76F33D472B28883AE518C92E1999888B171A085059FD",
    "isSSEIInstance": "0",
    "schemaVersion": "5",
    "emitTime": "2018-05-04T15:28:00.9025999Z",
    "sessionId": "c3cd1b56-ab61-462f-8363-8881779aa223",
    "correlationId": 0,
    "sequence": 23,
    "clientVersion": "14.0.3025.34 ((SQLServer2017-CU6).180410-0033)",
    "isInternalMachine": "1",
    "operatingSystem": "Microsoft Windows 10 Enterprise",
    "querySetVersion": "14.0.3025.34",
    "traceName": "SQLServerPeriodicQueries",
    "queryIdentifier": "OsSysInfo.003",
    "data": [
      {
        "LogicalCPUCount": "8",
        "HyperthreadRatio": "8",
        "PhysicalMemoryMB": "32710.902343",
        "SQLServerStartTime": "05/04/2018 08:22:30",
        "AffinityTypeDesc": "AUTO",
        "VirtualMachineType": "0",
        "SocketCount": "1",
        "CoresPerSocket": "4",
        "NumaNodeCount": "1",
        "ContainerType": "0",
        "ContainerDescription": "NONE"
      }
    ],
    "query": "SELECT\n      cpu_count AS LogicalCPUCount,\n      hyperthread_ratio AS HyperthreadRatio,\n      physical_memory_kb/1024.0 AS PhysicalMemoryMB,\n      sqlserver_start_time AS SQLServerStartTime,\n      affinity_type_desc AS AffinityTypeDesc,\n      virtual_machine_type AS VirtualMachineType,\n      socket_count as SocketCount,\n      cores_per_socket as CoresPerSocket,\n      numa_node_count as NumaNodeCount,\n      container_type as ContainerType,\n      container_type_desc as ContainerDescription\n      FROM sys.dm_os_sys_info WITH(nolock)",
    "queryTimeInTicks": 0
  }
]
```
## <a name="frequently-asked-questions"></a>常見問題集

**DBA 如何讀取本機稽核記錄檔？**
這些記錄檔是以 JSON 格式撰寫。 每一行都是一個 JSON 物件，代表一段上傳至 Microsoft 的使用方式 / 診斷資料。 欄位名稱應該一目了然。

**DBA 若停用使用狀況與診斷資料收集會有何影響？**
將不會寫入本機稽核檔案。

**防火牆後面沒有網際網路連線/電腦的話會有何影響？**
SQL Server 的使用方式和診斷資料將不會傳送給 Microsoft。 它仍然會嘗試寫入本機稽核記錄檔，如果已正確設定的話。

**DBA 如何停用本機稽核？**
請移除 UserRequestedLocalAuditDirectory 登錄機碼項目。

**誰能夠讀取本機稽核記錄檔？**
您組織中可以存取本機稽核目錄的任何人。

**DBA 如何管理寫入指定目錄的記錄檔？**
DBA 必須自行管理目錄的檔案清除工作，以避免耗用太多磁碟空間。

**是否有用戶端或工具，可用於讀取此 JSON 輸出？**
可以使用 [記事本]、Visual Studio 或您選擇的任何 JSON 讀取器來讀取輸出。
或者，您可以如下所示，在 SQL Server 執行個體中讀取 JSON 檔案並分析資料。 如需如何在 SQL Server 中讀取 JSON 檔案的詳細資訊，請瀏覽 [Importing JSON files into SQL Server using OPENROWSET (BULK) and OPENJSON (Transact-SQL)](/archive/blogs/sqlserverstorageengine/bulk-importing-json-files-into-sql-server)(使用 OPENROWSET (BULK) 和 OPENJSON (Transact-SQL) 將 JSON 檔案匯入到 SQL Server)。

```Transact-SQL
DECLARE @JSONFile AS VARCHAR(MAX)

-- Read the JSON file into variable 
SELECT @JSONFile = BulkColumn 
FROM OPENROWSET (BULK 'C:\SQLCEIPAudit\MSSQLSERVER\2016-09-08.json', SINGLE_CLOB) MyFile 

-- Check if the JSON file has been read properly and if it's in a JSON format
SELECT 
    @JSONFile LocalAuditOutput, 
    ISJSON(@JSONFile) IsFileInJSONFormat

-- Get the query identifier, query and the data (output of the query)   
SELECT 
    sequence,
    queryIdentifier,
    query,
    data
FROM OPENJSON(@JSONFile) 
    WITH (sessionId VARCHAR(64)
         ,sequence INT
         ,queryIdentifier VARCHAR(128)
         ,query VARCHAR(MAX)
         ,data NVARCHAR(MAX) AS JSON)
-- Get specific details about the output of "DatabaseProperties.001" query  
SELECT 
    QueryIdentifier,
    DatabaseID,
    CompatibilityLevel,
    IsQueryStoreOn
FROM OPENJSON(@JSONFile) 
    WITH (sessionId VARCHAR(64)
         ,sequence INT
         ,queryIdentifier VARCHAR(128)
         ,query VARCHAR(MAX)
         ,data NVARCHAR(MAX) AS JSON) 
    CROSS APPLY OPENJSON(data) 
        WITH (   DatabaseID varchar(128) '$.database_id'
                ,CompatibilityLevel varchar(128) '$.compatibility_level'
                ,IsQueryStoreOn varchar(128) '$.QS'
             )
WHERE queryIdentifier = 'DatabaseProperties.001'
```

## <a name="see-also"></a>另請參閱
[SSMS 使用狀況和診斷資料收集的本機稽核](../ssms/sql-server-management-studio-telemetry-ssms.md)