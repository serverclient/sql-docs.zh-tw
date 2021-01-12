---
title: SQL Server Integration Services DevOps 概觀 | Microsoft Docs
description: 了解如何使用 SSIS DevOps 工具建置 SSIS CICD。
ms.date: 12/06/2019
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: chugugrace
ms.author: chugu
ms.openlocfilehash: ce752b03e085ab70b033876d5c30ae3ff96d777c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091535"
---
# <a name="sql-server-integration-services-ssis-devops-tools-azure-devops-extension"></a>SQL Server Integration Services (SSIS) DevOps 工具 Azure DevOps 延伸模組

[SSIS DevOps 工具](https://marketplace.visualstudio.com/items?itemName=SSIS.ssis-devops-tools)延伸模組可在 **Azure DevOps** Marketplace 中取得。

如果您沒有 **Azure DevOps** 組織，請先註冊 [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops&preserve-view=true)，然後按照 [步驟](/azure/devops/marketplace/overview?tabs=browser&view=azure-devops&preserve-view=true#add-an-extension)新增 **SSIS DevOps 工具** 延伸模組。

**SSIS DevOps 工具** 包括 **SSIS 建置** 工作、**SSIS 部署** 發行工作，以及 **SSIS 目錄組態工作**。

- **[SSIS 建置](#ssis-build-task)** 工作支援在專案部署模型或套件部署模型中建置 dtproj 檔案。

- **[SSIS 部署](#ssis-deploy-task)** 工作支援將單一或多個 ispac 檔案部署到內部部署 SSIS 目錄和 Azure-SSIS IR，或將 SSISDeploymentManifest 檔案及其相關聯的檔案部署到內部部署或 Azure 檔案共用。

- **[SSIS 目錄組態](#ssis-catalog-configuration-task)** 工作支援使用 JSON 格式的組態檔來設定 SSIS 目錄資料夾/專案/環境。 這項工作支援下列情節：
    - 資料夾
        - 建立資料夾。
        - 更新資料夾描述。
    - 隨附此逐步解說的專案
        - 設定參數的值，支援 literal 值和 referenced 值。
        - 新增環境參考。
    - 環境
        - 建立環境。
        - 更新環境描述。
        - 建立或更新環境變數。

## <a name="ssis-build-task"></a>SSIS 建置工作

![建置工作](media/ssis-build-task.png)

### <a name="properties"></a>屬性

#### <a name="project-path"></a>專案路徑

要建置之專案資料夾或檔案的路徑。 如果指定了資料夾路徑，SSIS 建置工作會以遞迴方式搜尋此資料夾下的所有 dtproj 檔案，並建置所有檔案。

專案路徑不能是 *空的*，設定為 **.** 以從存放庫的根資料夾建立。

#### <a name="project-configuration"></a>專案組態

要用於建置的專案組態名稱。 如果未提供，則會預設為每個 dtproj 檔案中第一個定義的專案組態。

#### <a name="output-path"></a>輸出路徑

儲存建置結果的個別資料夾路徑，可以透過[發行組建成品工作](/azure/devops/pipelines/tasks/utility/publish-build-artifacts?view=azure-devops&preserve-view=true) \(英文\) 發行為組建成品。

### <a name="limitations-and-known-issues"></a>限制與已知問題

- SSIS 建置工作依賴 Visual Studio 和 SSIS 設計工具，這在建置代理程式上是必要的。 因此，若要在管線中執行 SSIS 建置工作，您必須針對 Microsoft 裝載的代理程式選擇 **vs2017-win2016**，或在自我裝載的代理程式上安裝 Visual Studio 和 SSIS 設計工具 (VS2017 + SSDT2017，或者 VS2019 + SSIS 專案延伸模組)。

- 若要使用任何現成可用的元件 (包括 SSIS Azure Feature Pack 和其他協力廠商元件) 來建置 SSIS 專案，必須在執行管線代理程式的電腦上安裝這些現成可用的元件。  針對 Microsoft 裝載的代理程式，使用者可以在執行 SSIS 建置工作之前，新增 [PowerShell 指令碼工作](/azure/devops/pipelines/tasks/utility/powershell?view=azure-devops&preserve-view=true) \(英文\) 或[命令列指令碼工作](/azure/devops/pipelines/tasks/utility/command-line?view=azure-devops&preserve-view=true) \(英文\) 下載並安裝元件。 面下面是可用於安裝 Azure Feature Pack 的範例 PowerShell 指令碼： 

```powershell
wget -Uri https://download.microsoft.com/download/E/E/0/EE0CB6A0-4105-466D-A7CA-5E39FA9AB128/SsisAzureFeaturePack_2017_x86.msi -OutFile AFP.msi

start -Wait -FilePath msiexec -Args "/i AFP.msi /quiet /l* log.txt"

cat log.txt
```

- 在 SSIS 建置工作中不支援 **EncryptSensitiveWithPassword** 和 **EncryptAllWithPassword** 的保護層級。 確定程式碼基底中的所有 SSIS 專案都不是使用這兩個保護層級，否則 SSIS 建置工作會在執行期間停止回應並逾時。

## <a name="ssis-deploy-task"></a>SSIS 部署工作

![部署工作](media/ssis-deploy-task.png)

### <a name="properties"></a>屬性

#### <a name="source-path"></a>來源路徑

您想要部署的來源 ISPAC 或 SSISDeploymentManifest 檔案的路徑。 這個路徑可以是資料夾路徑或檔案路徑。

#### <a name="destination-type"></a>目的地類型

目的地類型。 目前的 SSIS 部署工作支援兩種類型：

- *檔案系統*：將 SSISDeploymentManifest 檔案及其相關聯的檔案部署至指定的檔案系統。 支援內部部署和 Azure 檔案共用。
- *SSISDB*：將 ISPAC 檔案部署到指定的 SSIS 目錄，其可裝載於內部部署 SQL Server 或 Azure-SSIS Integration Runtime 上。

#### <a name="destination-server"></a>目的地伺服器

目的地 SQL Server 的名稱。 其可以是內部部署 SQL Server、Azure SQL Database 或 Azure SQL 受控執行個體的名稱。 只有當目的地類型為 SSISDB 時，才會顯示此屬性。

#### <a name="destination-path"></a>目的地路徑

將在其中部署來源檔案的目的地資料夾路徑。 例如：

- /SSISDB/\<folderName\>
- \\\\\<machineName\>\\\<shareFolderName\>\\\<optionalSubfolderName\>

SSIS 部署工作將會建立資料夾與子資料夾 (如果不存在)。

#### <a name="authentication-type"></a>驗證類型

用來存取指定目的地伺服器的驗證類型。 只有當目的地類型為 SSISDB 時，才會顯示此屬性。 一般支援下列驗證類型：

- Windows 驗證
- SQL Server 驗證
- Active Directory - 密碼
- Active Directory - 整合式

但是，是否支援特定驗證類型取決於目的地伺服器類型和代理程式類型。 詳細資料支援矩陣會列在下表中。

|目的地伺服器類型|Microsoft 裝載的代理程式|自我裝載的代理程式|
|---------|---------|---------|
|SQL Server 內部部署或 VM |N/A|Windows 驗證|
|Azure SQL|SQL Server 驗證 <br> Active Directory - 密碼|SQL Server 驗證 <br> Active Directory - 密碼 <br> Active Directory - 整合式|

#### <a name="domain-name"></a>網域名稱

用來存取指定檔案系統的網域名稱。 只有當目的地類型為 [檔案系統] 時，才會顯示此屬性。
當執行自我裝載代理程式的使用者帳戶已被授與指定目的地路徑的讀取/寫入存取權時，您可以將它保留空白。

#### <a name="username"></a>使用者名稱

存取指定檔案系統或 SSISDB 的使用者名稱。 當目的地類型為 [檔案系統] 或驗證類型為 [SQL Server 驗證] 或 [Active Directory - 密碼] 時，就會顯示此屬性。
當目的地類型為 [檔案系統]，而執行自我裝載代理程式的使用者帳戶已被授與指定目的地路徑的讀取/寫入存取權時，您可以將它保留空白。

#### <a name="password"></a>密碼

用來存取指定檔案系統或 SSISDB 的密碼。 當目的地類型為 [檔案系統] 或驗證類型為 [SQL Server 驗證] 或 [Active Directory - 密碼] 時，就會顯示此屬性。
當目的地類型為 [檔案系統]，且執行自我裝載代理程式的使用者帳戶已被授與指定目的地路徑的讀取/寫入存取權時，您可以將它保留空白。

#### <a name="overwrite-existing-projects-or-ssisdeploymentmanifest-files-of-the-same-names"></a>覆寫相同名稱的現有專案或 SSISDeploymentManifest 檔案

指定是否覆寫相同名稱的現有專案或 SSISDeploymentManifest 檔案。 如果為 [否]，SSIS 部署工作會略過部署這些專案或檔案。

#### <a name="continue-deployment-when-error-occurs"></a>發生錯誤時繼續部署

指定在發生錯誤時，是否繼續部署剩餘的專案或檔案。 如果為 [否]，則會在發生錯誤時立即停止 SSIS 部署工作。

### <a name="limitations-and-known-issues"></a>限制與已知問題

SSIS 部署工作目前不支援下列情節：

- 在 SSIS 目錄中設定環境。
- 將 ispac 部署至 Azure SQL Server 或 Azure SQL 受控執行個體，這只允許多重要素驗證 (MFA)。
- 將套件部署到 MSDB 或 SSIS 封裝存放區。

## <a name="ssis-catalog-configuration-task"></a>SSIS 目錄組態工作

![目錄組態工作](media/ssis-catalog-configuation-task.png)

### <a name="properties"></a>屬性

#### <a name="configuration-file-source"></a>組態檔來源

SSIS 目錄組態 JSON 檔案的來源。 可以是「檔案路徑」或「內嵌」。

請參閱以下有關如何[定義組態 JSON](#define-configuration-json) 的詳細資料：

- 參閱[範例內嵌組態 JSON](#a-sample-inline-configuration-json)。
- 查看 [JSON 結構描述](#json-schema)。

#### <a name="configuration-json-file-path"></a>組態 JSON 檔案路徑

SSIS 目錄組態 JSON 檔案的路徑。 只有當選取 [檔案路徑] 作為組態檔來源時，才會顯示此屬性。

若要在組態 JSON 檔案中使用[管線變數](/azure/devops/pipelines/process/variables)，則必須在這項工作之前新增[檔案轉換工作](/azure/devops/pipelines/tasks/utility/file-transform?view=azure-devops&preserve-view=true)，以將組態值替代為管線變數。 如需詳細資訊，請參閱 [JSON 變數替代](/azure/devops/pipelines/tasks/transforms-variable-substitution?tabs=Classic&view=azure-devops&preserve-view=true#json-variable-substitution) (英文)。

#### <a name="inline-configuration-json"></a>內嵌組態 JSON

SSIS 目錄組態的內嵌 JSON。 只有當選取 [內嵌] 作為組態檔來源時，才會顯示此屬性。 可直接使用管線變數。

#### <a name="roll-back-configuration-when-error-occurs"></a>發生錯誤時復原組態

發生錯誤時是否復原這項工作所做的組態。

#### <a name="target-server"></a>目標伺服器

目標 SQL Server 的名稱。 其可以是內部部署 SQL Server、Azure SQL Database 或 Azure SQL 受控執行個體的名稱。

#### <a name="authentication-type"></a>驗證類型

用來存取指定目標伺服器的驗證類型。 一般支援下列驗證類型：

- Windows 驗證
- SQL Server 驗證
- Active Directory - 密碼
- Active Directory - 整合式

但是，是否支援特定驗證類型取決於目的地伺服器類型和代理程式類型。 詳細資料支援矩陣會列在下表中。

|目的地伺服器類型|Microsoft 裝載的代理程式|自我裝載的代理程式|
|---------|---------|---------|
|SQL Server 內部部署或 VM |N/A|Windows 驗證|
|Azure SQL|SQL Server 驗證 <br> Active Directory - 密碼|SQL Server 驗證 <br> Active Directory - 密碼 <br> Active Directory - 整合式|

#### <a name="username"></a>使用者名稱

用來存取目標 SQL Server 的使用者名稱。 只有當驗證類型為 [SQL Server 驗證] 或 [Active Directory - 密碼] 時，才會顯示此屬性。

#### <a name="password"></a>密碼

用來存取目標 SQL Server 的密碼。 只有當驗證類型為 [SQL Server 驗證] 或 [Active Directory - 密碼] 時，才會顯示此屬性。

### <a name="define-configuration-json"></a>定義組態 JSON

組態 JSON 結構描述有三個層級：

- catalog
- folder
- 專案和環境

![目錄組態結構描述](media/catalog-configuration-schema.png)

#### <a name="a-sample-inline-configuration-json"></a>範例內嵌組態 JSON

```json
{
  "folders": [
    {
      "name": "devopsdemo",
      "description": "devops demo folder",
      "projects": [
        {
          "name": "catalog devops",
          "parameters": [
            {
              "name": "password",
              "container": "Package.dtsx",
              "value": "passwd",
              "valueType": "referenced"
            },
            {
              "name": "serverName",
              "container": "catalog devops",
              "value": "localhost",
              "valueType": "literal"
            }
          ],
          "references": [
            {
              "environmentName": "test",
              "environmentFolder": "devopsdemo"
            },
            {
              "environmentName": "test",
              "environmentFolder": "."
            }
          ]
        }
      ],
      "environments": [
        {
          "name": "test",
          "description": "test",
          "variables": [
            {
              "name": "passwd",
              "type": "string",
              "description": "",
              "value": "$(SSISDBServerAdminPassword)",
              "sensitive": true
            },
            {
              "name": "serverName",
              "type": "string",
              "description": "",
              "value": "$(TargetServerName)",
              "sensitive": false
            }
          ]
        }
      ]
    }
  ]
}
```

#### <a name="json-schema"></a>JSON 結構描述

##### <a name="catalog-attributes"></a>目錄屬性

|屬性  |描述  |注意  |
|---------|---------|---------|
|資料夾  |資料夾物件的陣列。 每個物件都包含目錄資料夾的組態資訊。|如需資料夾物件的結構描述，請參閱＜資料夾屬性＞。|

##### <a name="folder-attributes"></a>資料夾屬性

|屬性  |描述  |注意  |
|---------|---------|---------|
|NAME  |目錄資料夾的名稱。|如果資料夾不存在，則會加以建立。|
|description|目錄資料夾的描述。|將略過 *null* 值。|
|projects|專案物件的陣列。 每個物件都包含專案的組態資訊。|如需專案物件的結構描述，請參閱＜專案屬性＞。|
|environments|環境物件的陣列。 每個物件都包含環境的組態資訊。|如需環境物件的結構描述，請參閱＜環境屬性＞。|

##### <a name="project-attributes"></a>專案屬性

|屬性  |描述  |注意  |
|---------|---------|---------|
|NAME|專案的名稱。 |如果父資料表中不存在專案，則會略過專案物件。|
|參數|參數物件的陣列。 每個物件都包含參數的組態資訊。|如需參數物件的結構描述，請參閱＜參數屬性＞。|
|參考|參考物件的陣列。 每個物件都代表目標專案的一個環境參考。|如需參考物件的結構描述，請參閱＜參考屬性＞。|

##### <a name="parameter-attributes"></a>參數屬性

|屬性  |描述  |注意  |
|---------|---------|---------|
|NAME|參數的名稱。|<li>參數可以是專案參數或套件參數。 <li>如果參數不存在，則予以略過。 <li>如果參數是連線管理員屬性，則名稱格式應為 **CM.\<Connection Manager Name>.\<Property Name>** 。 |
|容器|參數的容器。|<li>如果參數是專案參數，則 *container* 應該是專案名稱。 <li>如果是套件參數，則 *container* 應該是副檔名為 **.dtsx** 的套件名稱。|
|value|參數的值。|<li>當 *valueType* 是 *referenced* 時：此值是 *string* 類型的環境變數參考。 <li> 當 *valueType* 是 *literal* 時：此屬性支援任何有效的 *boolean*、*number* 和 *string* JSON 值。 <li> 系統會將此值轉換成目標參數類型。 如果無法轉換，則會發生錯誤。<li> *null* 值無效。 該工作將略過此參數物件，並發出警告。|
|valueType|參數值類型。|有效類型包括： <br> *literal*：*value* 屬性代表常值。 <br> *referenced*：*value* 屬性代表環境變數參考。|

##### <a name="reference-attributes"></a>參考屬性

|屬性  |描述  |注意  |
|---------|---------|---------|
|environmentFolder|環境的資料夾名稱。|如果資料夾不存在，則會加以建立。 <br> 值可以是 "."，其代表參考環境的專案父資料夾。|
|environmentName|參考環境的名稱。|如果指定的環境不存在，則會加以建立。|

##### <a name="environment-attributes"></a>環境屬性

|屬性  |描述  |注意  |
|---------|---------|---------|
|NAME|環境的名稱。|如果環境不存在，則會加以建立。|
|description|環境的描述。|將略過 *null* 值。|
|variables|變數物件的陣列。|每個物件都包含環境變數的組態資訊。如需變數物件的結構描述，請參閱＜變數屬性＞。|

##### <a name="variable-attributes"></a>變數屬性

|屬性  |描述  |注意  |
|---------|---------|---------|
|NAME|環境變數的名稱。|如果環境變數不存在，則會加以建立。|
|type|環境變數的資料類型。|有效類型包括： <br> *boolean* <br> *byte* <br> *datetime* <br> decimal <br> *double* <br> *int16* <br> *int32* <br> *int64* <br> *sbyte* <br> *single* <br> *string* <br> *uint32* <br> *uint64*|
|description|環境變數的描述。|將略過 *null* 值。|
|value|環境變數的值。|此屬性支援任何有效的 boolean、number 和 string JSON 值。<br> 系統會將此值轉換成 **type** 屬性指定的類型。 如果轉換失敗，則會發生錯誤。<br>*null* 值無效。 該工作將略過此環境變數物件，並發出警告。|
|sensitive|環境變數的值是否具敏感性。|有效輸入包括： <br> *true* <br> *false*|

## <a name="release-notes"></a>版本資訊

### <a name="version-103"></a>1\.0.3 版

發行日期：2020 年 10 月 21 日

- 允許為 SSIS 部署工作和 SSIS 目錄設定工作指定連接字串尾碼。

### <a name="version-102"></a>1\.0.2 版

發行日期：2020 年 5 月 26 日

- 已修正在設定工作完成後，SSIS 目錄設定工作可能會失敗的問題。

### <a name="version-101"></a>1\.0.1 版

發行日期：2020 年 5 月 9 日

- 已修正即使只將單一 dtproj 檔案檔案指定為專案路徑，SSIS 建置工作還是一律會建立整個解決方案的問題。

### <a name="version-100"></a>1\.0.0 版

發行日期：2020 年 5 月 8 日

- 正式發行 (GA) 版本。
- 已在代理程式上新增 .NET Framework 最低版本的限制。 目前的最低版本是 .NET Framework 4.6.2。
- SSIS 組建工作和 SSIS 部署工作的精簡描述。

### <a name="version-020-preview"></a>0\.2.0 版預覽

發行日期：2020 年 3 月 31 日

- 新增 SSIS 目錄組態工作。

### <a name="version-013-preview"></a>0\.1.3 版預覽

發行日期：2020 年 1 月 19 日

- 已修正當 ispac 的原始檔案名稱變更時即無法部署的問題。

### <a name="version-012-preview"></a>0\.1.2 版預覽

發行日期：2020 年 1 月 13 日

- 新增了更詳細的例外狀況資訊，當目的地類型為 SSISDB 時，會顯示在 SSIS 部署工作記錄中。
- 修正了 SSIS 部署工作中，目的地路徑屬性說明文字的範例目的地路徑。

### <a name="version-011-preview"></a>0\.1.1 版預覽

發行日期：2020 年 1 月 6 日

- 新增了最低代理程式版本需求的限制。 目前，此產品的最低代理程式版本是 2.144.0。
- 修正了 SSIS 部署工作的部分錯誤顯示文字。
- 修正部分錯誤訊息。

### <a name="version-010-preview"></a>0\.1.0 版預覽

發行日期：2019 年 12 月 5 日

SSIS DevOps 工具的初始版本。 這是預覽版本。

## <a name="next-steps"></a>後續步驟

- 取得 [SSIS DevOps 延伸模組](https://marketplace.visualstudio.com/items?itemName=SSIS.ssis-devops-tools)
