---
title: 使用 Resource Governor 管理
description: 了解如何使用 Resource Governor 來管理 SQL Server 機器學習服務中 Python 與 R 工作負載的 CPU、實體 IO 與記憶體資源配置。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 08/06/2020
ms.topic: how-to
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15'
ms.openlocfilehash: 3e71b33eb08fd386232e992e9b47da7b5057aa32
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170720"
---
# <a name="manage-python-and-r-workloads-with-resource-governor-in-sql-server-machine-learning-services"></a>使用 SQL Server 機器學習服務中的 Resource Governor 管理 Python 與 R 工作負載
[!INCLUDE [SQL Server 2016 and later](../../includes/applies-to-version/sqlserver2016.md)]

了解如何使用 [Resource Governor](../../relational-databases/resource-governor/resource-governor.md) 來管理 SQL Server 機器學習服務中 Python 與 R 工作負載的 CPU、實體 IO 與記憶體資源配置。

Python 與 R 中的機器學習演算法需要大量計算。 視您的工作負載優先順序而定，您可能需要增加或減少機器學習服務可用的資源。

如需更多一般資訊，請參閱 [Resource Governor](../../relational-databases/resource-governor/resource-governor.md)。

> [!NOTE] 
> Resource Governor 是企業版功能。

## <a name="default-allocations"></a>預設配置

根據預設，機器學習的外部指令碼執行階段限制為不超過全部電腦記憶體的 20%。 這取決於您的系統，但一般而言，您可能會發現此限制太過嚴格而導致無法進行重要的機器學習工作，例如將模型定型或根據多列資料進行預測。 

## <a name="manage-resources-with-resource-governor"></a>使用 Resource Governor 管理資源
 
根據預設，外部處理序最多會在本機伺服器上使用 20% 的總主機記憶體。 您可以修改預設資源集區來進行全伺服器變更，其中 R 與 Python 處理序會利用您提供給外部處理序的任何容量。

您也可以選擇使用關聯的工作負載群組與分類器來建立自訂 **外部資源集區**，以針對來自特定程式、主機或您提供之其他準則的要求決定資源配置。 外部資源集區是 [!INCLUDE[sssql15-md](../../includes/sssql16-md.md)] 中引進的資源集區類型，可協助管理資料庫引擎外部的 R 與 Python 執行階段。

1. [啟用資源控管](../../relational-databases/resource-governor/enable-resource-governor.md) (預設為關閉)。

2. 執行 [CREATE EXTERNAL RESOURCE POOL](../../t-sql/statements/create-external-resource-pool-transact-sql.md) 以建立並設定資源集區，然後執行 [ALTER RESOURCE GOVERNOR](../../t-sql/statements/alter-resource-governor-transact-sql.md) 來加以實作。

3. 建立工作負載群組以進行細微的配置，例如在定型及評分之間。

4. 建立分類器來攔截外部處理的呼叫。

5. 使用您建立的物件來執行查詢與程序。

如需逐步解說，請參閱[建立 SQL Server 機器學習服務的資源集區](create-external-resource-pool.md)以取得逐步指示。

如需術語與一般概念的簡介，請參閱 [Resource Governor 資源集區](../../relational-databases/resource-governor/resource-governor-resource-pool.md)。

## <a name="processes-under-resource-governance"></a>資源治理下的處理序
  
 您可以使用「外部資源集區」來管理下列可執行檔在資料庫引擎執行個體上使用的資源：

+ Rterm.exe (從 SQL Server 本機呼叫，或使用 SQL Server 從遠端呼叫作為遠端計算內容時)
+ Rterm.exe (從 SQL Server 本機呼叫，或使用 SQL Server 從遠端呼叫作為遠端計算內容時)
+ BxlServer.exe 和附屬程序
+ LaunchPad 啟動的附屬處理序，例如 PythonLauncher.dll
  
> [!NOTE]
> 不支援使用 Resource Governor 直接管理 Launchpad 服務。 Launchpad 是受信任的服務，只能裝載 Microsoft 所提供的啟動器。 會明確設定受信任的啟動器，以避免過度使用資源。
  
## <a name="next-steps"></a>後續步驟

+ [建立機器學習的資源集區](create-external-resource-pool.md)
+ [Resource Governor 資源集區](../../relational-databases/resource-governor/resource-governor-resource-pool.md)