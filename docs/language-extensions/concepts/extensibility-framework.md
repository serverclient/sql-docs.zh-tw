---
title: SQL Server 語言延伸模組中的擴充性架構
titleSuffix: ''
description: 了解用於「SQL Server 語言延伸模組」的擴充性架構，此架構可讓您在 SQL Server 中執行外部程式碼。 SQL Server 2019 支援 Java、Python 和 R。 程式碼會在語言執行階段環境中執行，作為核心資料庫引擎的延伸模組。
author: dphansen
ms.author: davidph
ms.date: 11/05/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: language-extensions
monikerRange: '>=sql-server-ver15||>=sql-server-linux-ver15'
ms.openlocfilehash: 344e5f35e35202b6a44a4932db8a27424fdb8753
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97471799"
---
# <a name="extensibility-architecture-in-sql-server-language-extensions"></a>SQL Server 語言延伸模組中的擴充性架構

[!INCLUDE [SQL Server 2019 and later](../../includes/applies-to-version/sqlserver2019.md)]

了解用於「SQL Server 語言延伸模組」的擴充性架構，此架構可讓您在 SQL Server 中執行外部程式碼。 SQL Server 2019 支援 Java、Python 和 R。 程式碼會在語言執行階段環境中執行，作為核心資料庫引擎的延伸模組。

## <a name="background"></a>背景

擴充性架構其用途是提供 SQL Server 和外部語言之間的介面。 藉由在受 SQL Server 管理的安全架構中執行信任的語言，資料庫管理員可以維護安全性，同時允許資料科學家存取企業資料。

<!-- We need to get a diagram like the one below.
The following diagram visually describes opportunities and benefits of the extensible architecture.

  ![Goals of integration with SQL Server](../media/ml-service-value-add.png "Machine Learning Services Value Add")
-->

您可以藉由呼叫預存程序來執行任何支援的外部語言，而結果會以表格式結果直接傳回 SQL Server。 這可讓您輕鬆地從任何可傳送 SQL 查詢並處理結果的應用程式使用外部語言。

## <a name="architecture-diagrams"></a>架構圖表

此架構的設計，是為了讓外部程式碼在 SQL Server 的個別處理序中執行，而不是在 SQL Server 上於內部管理資料和作業要求鏈的元件中執行。 
  
  **Windows 中的元件架構：** *_

  ![Windows 上的元件架構](../media/generic-architecture-windows.png "Windows 上的元件架構")
  
  _*_Linux 中的元件架構：_*_
  
  ![Linux 上的元件架構](../media/generic-architecture-linux.png "Linux 上的元件架構")
  
元件包含 _ *Launchpad** 服務，用於叫用外部執行階段和程式庫特定邏輯 (例如 Java)，以載入解譯器和程式庫。

<a name="launchpad"></a>

## <a name="launchpad"></a>Launchpad

[!INCLUDE[rsql_launchpad_md](../../includes/rsql-launchpad-md.md)] 是一項服務，可管理負責執行指令碼之外部處理序的存留時間、資源和安全性界限。 這如同全文索引及查詢服務啟動個別的主機來處理全文查詢。 啟動控制板服務只會啟動由 Microsoft 發行之信任的啟動器，或是經 Microsoft 認證符合效能和資源管理要求的啟動器。

[!INCLUDE[rsql_launchpad_md](../../includes/rsql-launchpad-md.md)] 服務會以使用 [AppContainers](/windows/desktop/secauthz/appcontainer-isolation) 執行隔離的 **SQLRUserGroup** 執行。

系統會針對您已新增 SQL Server 機器學習語言延伸模組的每個資料庫引擎執行個體，建立個別的 [!INCLUDE[rsql_launchpad_md](../../includes/rsql-launchpad-md.md)] 服務。 每個資料庫引擎執行個體都有一個啟動控制板服務，因此如果您有多個支援外部指令碼的執行個體，就會每個都有一個啟動控制板服務。 資料庫引擎執行個體會繫結至為它建立的啟動控制板。 預存程序或 T-SQL 中的所有外部指令碼叫用，都會導致 SQL Server 服務呼叫為相同執行個體建立的啟動控制板服務。

若要以特定受支援的語言執行工作，啟動控制板會從集區取得安全的背景工作帳戶，並啟動附屬處理序來管理外部執行階段。 每個附屬處理序都會繼承啟動控制板的使用者帳戶，並在指令碼執行期間使用該背景工作帳戶。 如果指令碼使用平行處理序，則會以相同的單一背景工作帳戶建立它們。

## <a name="communication-channels-between-components"></a>元件之間的通訊通道

本節描述元件和資料平台之間的通訊協定。

+ **TCP/IP**

  根據預設，SQL Server 和 SQL Satellite 之間的內部通訊是使用 TCP/IP。

+ **ODBC**

  外部資料科學用戶端和遠端 SQL Server 執行個體之間的通訊是使用 ODBC。 將指令碼工作傳送到 SQL Server 的帳戶必須有連線到執行個體及執行外部指令碼的權限。

  此外，取決於工作，帳戶可能需要下列權限：

  + 讀取由作業使用的資料
  + 將資料寫入至資料表：例如，將結果儲存至資料表時
  + 建立資料庫物件：例如，如果將外部指令碼儲存為新預存程序的一部分。

  當 SQL Server 作為從遠端用戶端執行指令碼的計算內容，且可執行檔必須從外部來源取得資料時，ODBC 會用於回寫。 SQL Server 會將發出遠端命令的使用者身分識別，對應到目前執行個體上的使用者身分識別，並使用該使用者的認證來執行 ODBC 命令。 執行此 ODBC 呼叫所需的連接字串可從用戶端程式碼取得。

+ **其他通訊協定**

  可能需要以「區塊」運作或將資料傳輸回遠端用戶端的處理序也可以使用 [XDF 檔案格式](/machine-learning-server/r/concept-what-is-xdf) \(英文\)。 實際的資料傳輸是透過已編碼的 Blob 來進行。

## <a name="next-steps"></a>後續步驟

+ [什麼是語言延伸模組？](../language-extensions-overview.md)