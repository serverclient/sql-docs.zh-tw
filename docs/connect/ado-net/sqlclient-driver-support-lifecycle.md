---
title: SqlClient 驅動程式支援生命週期
description: 包含產品支援週期資訊的頁面。
ms.date: 01/04/2020
dev_langs:
- csharp
- vb
ms.assetid: 6f5ff56a-a57e-49d7-8ae9-bbed697e42e3
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-jizho2
ms.openlocfilehash: 200e600448f1f93cca3d566be10d6065ffd537e7
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878581"
---
# <a name="sqlclient-driver-support-lifecycle"></a>SqlClient 驅動程式支援生命週期

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

Microsoft.Data.SqlClient 程式庫會遵循適用於所有版本的最新 .NET Core 支援原則。

[檢視 .NET Core 支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) \(英文\)

## <a name="microsoftdatasqlclient-release-cadence"></a>Microsoft.Data.SqlClient 發行步調

從 1.2 版開始，每隔六個月就會定期發行穩定的新 (GA) 發行版本，期間還會提供 2 到 3 個預覽版本。 專案關係人和維護人員將根據一些資格和客戶回應，來選擇長期支援 (LTS) 版本。

### <a name="actively-supported-releases"></a>主動支援的發行版本

| 版本 | 正式發行日期 | 最新修補程式版本 | 修補程式發行日期 | 支援層級  | 結束支援 |
| -- | -- | -- | -- | -- | -- |
| 2.1 | 2020 年 11 月 19 日 | 2.1.1 | 2020 年 12 月 18 日 | 目前 | |
| 2.0 | 2020 年 6 月 16 日 | 2.0.1 | 2020 年 8 月 25 日 | 目前 | 2021 年 2 月 19 日 |
| 1.1 | 2019 年 11 月 20 日 | 1.1.3 | 2020 年 5 月 15 日 | LTS | 2022 年 11 月 21 日 |

### <a name="out-of-support-releases"></a>支援終止的發行版本

| 版本 | 最新的修補程式發行日期 | 最新修補程式版本 | 支援已結束 |
| -- | -- | -- | -- |
| 1.0 | 2019 年 9 月 26 日 | 1.0.19269.1 | 2020 年 2 月 20 日 |

### <a name="long-term-support-lts-releases"></a>長期支援 (LTS) 發行版本

LTS 版本會在初始版本之後的三年內受到支援。

### <a name="current-releases"></a>目前發行版本

目前版本會在後續的目前或 LTS 版本之後的三個月內受到支援。

## <a name="sql-version-compatibility-with-microsoftdatasqlclient"></a>SQL 版本與 Microsoft.Data.SqlClient 的相容性

|資料庫版本&nbsp;&#8594;<br />&#8595; 驅動程式版本|Azure SQL Database|Azure Synapse Analytics|Azure SQL 受控執行個體|SQL Server 2019|SQL Server 2017|SQL Server 2016|SQL Server 2014|SQL Server 2012|
|---|---|---|---|---|---|---|---|---|
|2.1|是|是|是|是|是|是|是|是|
|2.0|是|是|是|是|是|是|是|是|
|1.1|是|是|是|是|是|是|是|是|
|1.0|是|是|是|是|是|是|是|是|

## <a name="supported-os-versions"></a>支援的 OS 版本

### <a name="support-for-net-framework-applications"></a>針對 .NET Framework 應用程式的支援

Microsoft.Data.SqlClient 支援 .NET Framework v4.6 與更新版本所支援的所有作業系統。

[.NET Framework 系統需求](/dotnet/framework/get-started/system-requirements)。

### <a name="support-for-net-core-applications"></a>針對 .NET Core 應用程式的支援

Microsoft.Data.SqlClient 支援 .NET Core v2.1 與更新版本所支援的所有作業系統。

[.NET Core 所支援的 OS 生命週期原則](https://github.com/dotnet/core/blob/master/os-lifecycle-policy.md) \(英文\)。

> [!NOTE]
> 目前不支援全球化不區分模式。
