---
title: DataAdapter 和 DataReader
description: 了解 Microsoft SqlClient Data Provider for SQL Server 中，從資料庫擷取資料的 DataReader，以及從資料來源擷取資料，並填入 DataSet 的 DataAdapter。
ms.date: 11/30/2020
ms.assetid: cc952ca2-ec19-46ab-9189-15174b52cb74
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 3a252ca44683c062e531081f0b625a5e2359d236
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771379"
---
# <a name="dataadapters-and-datareaders"></a>DataAdapter 和 DataReader

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

您可以使用 Microsoft SqlClient Data Provider for SQL Server **DataReader**，從資料庫擷取順向唯讀資料流。 執行查詢時會傳回結果，並一直儲存於用戶端上的網路緩衝區中，直到您使用 **DataReader** 的 **Read** 方法對其加以要求為止。 使用 **DataReader** 可以提高應用程式的效能，方法是立即擷取可用的資料，及 (依預設) 一次只將一個資料列儲存到記憶體中，進而減少系統負荷。

<xref:System.Data.Common.DataAdapter> 可用於從資料來源擷取資料，並填入 <xref:System.Data.DataSet> 內的資料表。 `DataAdapter` 亦可將對 `DataSet` 所做的變更解析回資料來源。 `DataAdapter` 會使用 Microsoft SqlClient Data Provider for SQL Server 的 `Connection` 物件來連線到資料來源，並使用 `Command` 物件從資料來源擷取資料，並解析變更。

.NET 具有 <xref:System.Data.Common.DbDataReader> 及 <xref:System.Data.Common.DbDataAdapter> 物件：Microsoft SqlClient Data Provider for SQL Server 包含 <xref:Microsoft.Data.SqlClient.SqlDataReader> 及 <xref:Microsoft.Data.SqlClient.SqlDataAdapter> 物件。

## <a name="in-this-section"></a>本節內容

[由 DataReader 擷取的資料](retrieve-data-by-datareader.md)  
說明 ADO.NET **DataReader** 物件，以及如何將該物件用於從資料來源傳回結果資料流。

[從 DataAdapter 擴展資料集](populate-dataset-from-dataadapter.md)  
說明如何使用 `DataSet` 來以資料表、資料行及資料列填入 `DataAdapter`。

[DataAdapter 參數](dataadapter-parameters.md)  
說明如何搭配使用參數與 `DataAdapter` 的命令屬性，包括如何將 `DataSet` 中資料行的內容對應至命令參數。

[將現有條件約束加入至資料集](add-existing-constraints-to-dataset.md)  
說明如何將現有條件約束加入 `DataSet`。

[DataAdapter、DataTable 與 DataColumn 對應](dataadapter-datatable-datacolumn-mappings.md)  
說明如何設定 `DataTableMappings` 的 `ColumnMappings` 和 `DataAdapter`。

[逐頁查看查詢結果](paging-through-query-result.md)  
提供以資料分頁形式檢視查詢結果的範例。

[使用 DataAdapter 更新資料來源](update-data-sources-with-dataadapters.md)  
說明如何使用 `DataAdapter`，將 `DataSet` 中的變更解析回資料庫。

[處理 DataAdapter 事件](handle-dataadapter-events.md)  
說明 `DataAdapter` 事件以及如何使用它們。

[使用 DataAdapter 執行批次作業](batch-operations-using-dataadapters.md)  
說明在套用來自 `DataSet` 的更新時，如何藉由減少與 SQL Server 之間的往返次數，提高應用程式效能。

## <a name="see-also"></a>請參閱

- [連線到資料來源](connecting-to-data-source.md)
- [命令與參數](commands-parameters.md)
- [異動和並行存取](transactions-and-concurrency.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
