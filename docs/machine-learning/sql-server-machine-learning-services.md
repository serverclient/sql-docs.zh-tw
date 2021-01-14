---
title: 什麼是 SQL Server 機器學習服務 (Python 和 R)？
titleSuffix: ''
description: 機器學習服務是 SQL Server 內的一項功能，能夠使用關聯式資料執行 Python 和 R 指令碼。 您可以使用開放原始碼套件和架構，以及 Microsoft Python 和 R 套件，來進行預測性分析與機器學習。 指令碼會在資料庫中執行，不需在 SQL Server 外部或透過網路來移動資料。 本文說明 SQL Server 機器學習服務的基本概念，以及如何開始。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 11/10/2020
ms.topic: overview
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 98e9dd928f2639bfd1df3e6782bcc9aaed60f31e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102859"
---
# <a name="what-is-sql-server-machine-learning-services-with-python-and-r"></a>什麼是使用 Python 和 R 的 SQL Server 機器學習服務？
[!INCLUDE [SQL Server 2017 SQL MI](../includes/applies-to-version/sqlserver2017-asdbmi.md)]

機器學習服務是 SQL Server 內的一項功能，能夠使用關聯式資料執行 Python 和 R 指令碼。 您可以使用開放原始碼套件和架構，以及 [Microsoft Python 和 R 套件](#packages)，來進行預測性分析與機器學習。 指令碼會在資料庫中執行，不需在 SQL Server 外部或透過網路來移動資料。 本文說明 SQL Server 機器學習服務的基本概念，以及如何開始。

如需其他 SQL 平台上的機器學習，請參閱 [SQL 機器學習文件](index.yml)。

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
> [!NOTE]
> 若要在 SQL Server 中執行 Java，請參閱 [Java 語言延伸模組文件](../language-extensions/java-overview.md)。
::: moniker-end

## <a name="execute-python-and-r-scripts-in-sql-server"></a>在 SQL Server 中執行 Python 和 R 指令碼

SQL Server 機器學習服務，可讓您在資料庫中執行 Python 和 R 指令碼。 您可以使用此服務來準備和清除資料、進行特徵工程，然後在資料庫內訓練、評估，以及部署機器學習模型。 此功能可讓您在資料所在的位置執行指令碼，而無須透過網路將資料傳輸至另一部伺服器。

您可以使用預存程序 [sp_execute_external_script](../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)，在 SQL Server 執行個體上執行 Python 和 R 指令碼。

機器學習服務包含了 Python 和 R 的基本發行版。 除了 Microsoft 套件以外，您還可以安裝並使用開放原始碼套件和架構，例如 PyTorch、TensorFlow 和 scikit-learn。

機器學習服務使用擴充性架構，以在 SQL Server 中執行 Python 和 R 指令碼。 深入了解運作方式：

+ [擴充性架構](concepts/extensibility-framework.md)
+ [Python 延伸模組](concepts/extension-python.md)
+ [R 延伸模組](concepts/extension-r.md)

## <a name="get-started-with-machine-learning-services"></a>開始使用機器學習服務

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
1. 在 [Windows](install/sql-machine-learning-services-windows-install.md) 或 [Linux](../linux/sql-server-linux-setup-machine-learning.md?toc=/sql/machine-learning/toc.json) 上安裝 SQL Server 機器學習服務。 您也可以使用[巨量資料叢集的機器學習服務](../big-data-cluster/machine-learning-services.md)和 [Azure SQL 受控執行個體中的機器學習服務 ](/azure/azure-sql/managed-instance/machine-learning-services-overview)。

1. 設定您的開發工具。 您可以在 [Azure Data Studio 筆記本](install/sql-machine-learning-azure-data-studio.md)中使用和執行 Python 和 R 指令碼。 也可以在 [Azure Data Studio](../azure-data-studio/what-is-azure-data-studio.md) 中執行 T-SQL。

1. 撰寫您的第一個 Python 或 R 指令碼。

   + [SQL 機器學習的 Python 教學課程](tutorials/python-tutorials.md)
   + [SQL 機器學習的 R 教學課程](tutorials/r-tutorials.md)
::: moniker-end

::: moniker range="=azuresqldb-mi-current"
+ 撰寫您的第一個 Python 或 R 指令碼。

   + [SQL 機器學習的 Python 教學課程](tutorials/python-tutorials.md)
   + [SQL 機器學習的 R 教學課程](tutorials/r-tutorials.md)
::: moniker-end

::: moniker range="=sql-server-2017"
1. [在 Windows 上安裝 SQL Server 機器學習服務](install/sql-machine-learning-services-windows-install.md)。

1. 設定您的開發工具。 您可以在 [Azure Data Studio 筆記本](install/sql-machine-learning-azure-data-studio.md)中使用和執行 Python 和 R 指令碼。 也可以在 [Azure Data Studio](../azure-data-studio/what-is-azure-data-studio.md) 中使用 T-SQL。

1. 撰寫您的第一個 Python 或 R 指令碼。

   + [SQL 機器學習的 Python 教學課程](tutorials/python-tutorials.md)
   + [SQL 機器學習的 R 教學課程](tutorials/r-tutorials.md)
::: moniker-end

<a name="versions"></a>

## <a name="python-and-r-versions"></a>Python 和 R 版本

以下列出機器學習服務中所包含的 Python 與 R 版本。

| SQL Server 版本 | Python 版本 | R 版本 |
|-|-|-|
| SQL Server 2017 | 3.5.2 | 3.3.3 |
| SQL Server 2019 | 3.7.3 | 3.5.2 |

若為 SQL Server 2016 中的 R 版本，請參閱[什麼是 R 服務中的 R 版本一節](r/sql-server-r-services.md?view=sql-server-2016&preserve-view=true#version)

<a name="packages"></a>

## <a name="python-and-r-packages"></a>Python 和 R 套件

除了 Microsoft 的企業套件之外，您還可以使用開放原始碼套件和架構。 最常見的開放原始碼 Python 和 R 套件都已預先安裝在機器學習服務中。 此外，也包含下列來自 Microsoft 的 Python 和 R 套件：

| Language | Package | 描述 |
|-|-|-|
| Python | [revoscalepy](python/ref-py-revoscalepy.md) | 可調整 Python 的主要套件。 資料轉換與操作、統計摘要、視覺化，以及許多模型化形式。 此外，此套件中的函式會自動在可用的核心之間分配工作負載來進行平行處理。 |
| Python | [microsoftml](python/ref-py-microsoftml.md) | 新增機器學習演算法來建立自訂模型，以進行文字分析、影像分析及情感分析。 | 
| R | [RevoScaleR](r/ref-r-revoscaler.md) | 可調整 R 的主要套件。資料轉換與操作、統計摘要、視覺化，以及許多模型化形式。 此外，此套件中的函式會自動在可用的核心之間分配工作負載來進行平行處理。 |
| R | [MicrosoftML (R)](r/ref-r-microsoftml.md) | 新增機器學習演算法來建立自訂模型，以進行文字分析、影像分析及情感分析。 |
| R | [olapR](r/ref-r-olapr.md) | 用於針對 SQL Server Analysis Services OLAP cube 執行 MDX 查詢的 R 函式。 |
| R | [sqlrutils](r/ref-r-sqlrutils.md) | 一種機制，可在 T-SQL 預存程序中使用 R 指令碼、向資料庫註冊該預存程序，然後從 [R 開發環境](r/set-up-a-data-science-client.md)中執行該預存程序。 |
| R | [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open (MRO) 是 Microsoft 所提供的 R 加強發行版。 其為一個適用於統計分析與資料科學的完整開放原始碼平台， 並以 R 為基礎而且與其 100% 相容，且包含可提升效能和重現性的額外功能。 |

如需機器學習服務安裝了哪些套件，以及如何安裝其他套件的詳細資訊，請參閱：

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
+ [取得 Python 套件資訊](package-management/python-package-information.md)
+ [使用 sqlmlutils 安裝 Python 套件](package-management/install-additional-python-packages-on-sql-server.md)
+ [取得 R 套件資訊](package-management/r-package-information.md)
+ [使用 sqlmlutils 安裝新的 R 套件](package-management/install-additional-r-packages-on-sql-server.md)。
::: moniker-end
::: moniker range="=sql-server-2017"
+ [取得 Python 套件資訊](package-management/python-package-information.md)
+ [在 SQL Server 上使用 Python 工具來安裝套件](package-management/install-python-packages-standard-tools.md)
+ [取得 R 套件資訊](package-management/r-package-information.md)
+ [使用 T-SQL (CREATE EXTERNAL LIBRARY) 在 SQL Server 上安裝 R 套件](package-management/install-r-packages-with-tsql.md)。
::: moniker-end

## <a name="next-steps"></a>後續步驟

+ 在 [Windows](install/sql-machine-learning-services-windows-install.md) 或 [Linux](../linux/sql-server-linux-setup-machine-learning.md?toc=/sql/machine-learning/toc.json) 上安裝 SQL Server 機器學習服務
+ [SQL 機器學習的 Python 教學課程](tutorials/python-tutorials.md)
+ [SQL 機器學習的 R 教學課程](tutorials/r-tutorials.md)