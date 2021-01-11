---
title: 什麼是 PolyBase？ | Microsoft Docs
description: PolyBase 可讓 SQL Server 執行個體處理會從外部資料來源 (例如 Hadoop 和 Azure Blob 儲存體) 讀取資料的 Transact-SQL 查詢。
ms.date: 12/14/2019
ms.prod: sql
ms.technology: polybase
ms.topic: overview
f1_keywords:
- PolyBase
- PolyBase, guide
helpviewer_keywords:
- PolyBase
- PolyBase, overview
- Hadoop import
- Hadoop export
- Hadoop export, PolyBase overview
- Hadoop import, PolyBase overview
ms.custom: contperf-fy21q2
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: ''
monikerRange: '>=sql-server-2016||>=sql-server-linux-2017||>=aps-pdw-2016||=azure-sqldw-latest'
ms.openlocfilehash: afcc848a169ec6a9c9dd02ecee50718d7e1508c1
ms.sourcegitcommit: 81f06a754fcaf4b72136859ccb18c82693f24780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97811533"
---
# <a name="what-is-polybase"></a>什麼是 PolyBase？

[!INCLUDE[appliesto-ss-xxxx-asdw-pdw-md](../../includes/appliesto-ss-xxxx-asdw-pdw-md.md)]

PolyBase 可讓您的 SQL Server 執行個體處理會從外部資料來源讀取資料的 Transact-SQL 查詢。 相同的查詢也可以存取您 SQL Server 執行個體中的關聯式資料表。 PolyBase 也可以讓相同的查詢聯結來自外部來源與 SQL Server 的資料。

若要使用 PolyBase，請在 SQL Server 的執行個體中：

1. [在 Windows 上安裝 PolyBase](polybase-installation.md)
1. 建立[外部資料來源](../../t-sql/statements/create-external-data-source-transact-sql.md)
1. 建立[外部資料表](../../t-sql/statements/create-external-table-transact-sql.md)

這些會一起提供針對外部資料來源的連線。

SQL Server 2016 推出了支援針對 Hadoop 和 Azure Blob 儲存體連線的 PolyBase。

SQL Server 2019 推出了額外的連接器，包括 SQL Server、Oracle、Teradata 和 MongoDB。

![PolyBase 邏輯](../../relational-databases/polybase/media/polybase-logical.png "PolyBase 邏輯")

PolyBase 會將某些計算推送到外部來源來將整體查詢最佳化。 PolyBase 外部存取並不限於 Hadoop。 也支援其他非結構化資料表，例如分隔文字檔。

外部連接器的範例包括：

- [SQL Server](polybase-configure-sql-server.md)
- [Oracle](polybase-configure-oracle.md)
- [Teradata](polybase-configure-teradata.md)
- [MongoDB](polybase-configure-mongodb.md)

### <a name="supported-sql-products-and-services"></a>支援的 SQL 產品和服務

PolyBase 為下列 Microsoft SQL 產品提供這些相同的功能：

- SQL Server 2016 和更新版本 (僅限 Windows)
- Analytics Platform System (先前稱為「平行處理資料倉儲」)
- Azure Synapse Analytics

### <a name="azure-integration"></a>Azure 整合

有了 PolyBase 在後面的幫助，T-SQL 查詢也可以從 Azure Blob Storage 匯入及匯出資料。 此外，PolyBase 可讓 Azure Synapse Analytics 從 Azure Data Lake Store 與 Azure Blob 儲存體匯入及匯出資料。

## <a name="why-use-polybase"></a>為何要使用 PolyBase？

PolyBase 可讓您將來自 SQL Server 執行個體的資料與外部資料聯結。 在 PolyBase 之前，若要將資料聯結到外部資料來源，您可以：

- 傳輸一半的資料，來使所有的資料集中於一處。
- 同時查詢兩個資料來源，然後撰寫自訂查詢邏輯以在用戶端層級聯結並整合資料。

PolyBase 可讓您直接使用 Transact-SQL 來聯結資料。

PolyBase 不需要您在 Hadoop 環境中安裝其他軟體。 您可以使用與用來查詢資料庫資料表相同的 T-SQL 語法來查詢外部資料。 由 PolyBase 所實作的支援動作都同時自動發生。 查詢作者不需要具備關於外部來源的任何知識。

### <a name="polybase-uses"></a>PolyBase 用途

PolyBase 可在 SQL Server 中啟用下列情節：

- **從 SQL Server 執行個體或 PDW 查詢儲存在 Hadoop 中的資料。** 使用者必須將資料儲存於符合成本效益的分散式且可擴充的系統中，例如 Hadoop。 PolyBase 可讓您輕鬆地使用 T-SQL 來查詢資料。

- **查詢 Azure Blob 儲存體中所儲存的資料。** Azure Blob 儲存體是儲存資料以供 Azure 服務使用的便利位置。  PolyBase 可讓您輕鬆地使用 T-SQL 來存取資料。

- **從 Hadoop、Azure Blob 儲存體或 Azure Data Lake Store 匯入資料。** 將 Hadoop 或 Azure Blob 儲存體或 Azure Data Lake Store中的資料匯入至關聯式資料表，以利用 Microsoft SQL 資料行存放區技術和分析功能的速度。 個別 ETL 或匯入工具則不需要。

- **將資料匯出至 Hadoop、Azure Blob 儲存體或 Azure Data Lake Store。** 將資料封存至 Hadoop、Azure Blob 儲存體或 Azure Data Lake Store 以達成符合成本效益的儲存體，並讓它持續上線，方便進行存取。

- **與 BI 工具整合。** 使用 PolyBase 搭配 Microsoft 的商務智慧和分析堆疊，或使用與 SQL Server 相容的任何協力廠商工具。

## <a name="performance"></a>效能

- **將計算推送到 Hadoop。** 查詢最佳化工具會做出成本型決策，以將計算推送到 Hadoop (在那麼做可以改善查詢效能的前提下)。  查詢最佳化工具會使用外部資料表上的統計資料來做出成本型決策。 推送計算會建立 MapReduce 工作，並利用 Hadoop 的分散式運算資源。

- **調整計算資源。** 若要改善查詢效能，您可以使用 SQL Server [PolyBase 向外延展群組](../../relational-databases/polybase/polybase-scale-out-groups.md)。 這可啟用 SQL Server 執行個體與 Hadoop 節點之間的平行資料傳輸，而且會加入在外部資料上運作的計算資源。

## <a name="next-steps"></a>後續步驟

使用 PolyBase 之前，您必須[安裝 PolyBase 功能](polybase-installation.md)。 然後請參閱下列設定指南 (視您的資料來源) 而定：

- [Hadoop](polybase-configure-hadoop.md)
- [Azure Blob 儲存體](polybase-configure-azure-blob-storage.md)
- [SQL Server](polybase-configure-sql-server.md)
- [Oracle](polybase-configure-oracle.md)
- [Teradata](polybase-configure-teradata.md)
- [MongoDB](polybase-configure-mongodb.md)
- [ODBC 泛型型別](polybase-configure-odbc-generic.md)