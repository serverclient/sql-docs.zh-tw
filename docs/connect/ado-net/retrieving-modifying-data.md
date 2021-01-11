---
title: 擷取及修改資料
description: 在 .NET 中，Microsoft SqlClient Data Provider for SQL Server 可作為應用程式與資料來源之間的橋樑以讀取及更新資料。
ms.date: 11/30/2020
ms.assetid: 722e7f87-3691-46c6-87e8-7d159722d675
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 4275b7de0f31d03aa36ef31d8801fcdc0e9ec853
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771535"
---
# <a name="retrieving-and-modifying-data-in-adonet"></a>在 ADO.NET 中擷取及修改資料

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

任何資料庫應用程式都有一個主要功能，那就是連接到資料來源並擷取其內含的資料。 SqlClient 資料提供者可作為應用程式與資料來源之間的橋樑，讓您能夠執行命令，以及使用 **DataReader** 或 **DataAdapter** 來擷取資料。 任何資料庫應用程式都有一個主要功能，那就是更新資料庫中儲存的資料。 在 Microsoft SqlClient Data Provider for SQL Server 中，更新資料牽涉到使用 **DataAdapter** 與 <xref:System.Data.DataSet>，以及 **Command** 物件；而且也可能涉及使用異動。

## <a name="in-this-section"></a>本節內容

[連線到資料來源](connecting-to-data-source.md)  
說明如何建立資料來源的連接，以及如何使用連接事件。

[連接字串](connection-strings.md)  
包含一些主題，其中說明連接字串 (包含連接字串關鍵字、安全性資訊) 的使用、儲存和擷取的各種層面。

[連接共用](connection-pooling.md)  
描述適用於 Microsoft SqlClient Data Provider for SQL Server 的連接共用。

[命令和參數](commands-parameters.md)  
包含一些主題，其中說明如何建立命令和命令產生器、設定參數，以及執行命令來擷取和修改資料。

[DataAdapter 和 DataReader](dataadapters-datareaders.md)  
包含一些主題，其中說明 DataReader、DataAdapter、參數、處理 DataAdapter 事件，以及執行批次作業。

[異動和並行存取](transactions-and-concurrency.md)  
包含一些主題，其中說明如何執行本機異動、分散式異動，以及使用開放式並行存取 (Optimistic Concurrency)。

[擷取資料庫結構描述資訊](retrieving-database-schema-information.md)  
說明如何取得資料來源的可用資料庫或目錄、資料庫中的資料表和檢視表、資料表的條件約束，以及其他結構描述資訊。

[DbProviderFactory](dbproviderfactories.md)  
說明提供者 Factory 模型並示範如何使用 `System.Data.Common` 命名空間 (Namespace) 中的基底類別 (Base Class)。  

[擷取識別或自動編號值](retrieve-identity-or-autonumber-values.md)  
提供範例，說明如何將針對 SQL Server 資料表中的 **identity** 資料行所產生的值，對應至資料表中插入資料列的資料行。 討論如何在 `DataTable` 中合併識別值。  
  
[擷取二進位資料](retrieve-binary-data.md)  
說明如何使用 `CommandBehavior`.`SequentialAccess` 來擷取二進位資料或大型資料結構 以修改 `DataReader` 的預設行為。  
  
[使用預存程序修改資料](modify-data-with-stored-procedures.md)  
說明如何使用預存程序 (Stored Procedure) 輸入參數和輸出參數，將資料列插入資料庫中，並傳回新的識別值。  

[SqlClient 中的資料追蹤](data-tracing.md)  
說明 Microsoft SqlClient Data Provider for SQL Server 如何提供內建的資料追蹤功能。  
  
[SqlClient 中的效能計數器](performance-counters.md)  
描述可供 Microsoft SqlClient Data Provider for SQL Server 使用的效能計數器。  
  
[非同步程式設計](asynchronous-programming.md)  
描述 Microsoft SqlClient Data Provider for SQL Server 對非同步程式設計的支援。  
  
[SqlClient 串流支援](sqlclient-streaming-support.md)  
討論如何撰寫從 SQL Server 串流資料而不需將它完全載入記憶體的應用程式。  

## <a name="see-also"></a>請參閱

- [ADO.NET 中的資料類型對應](data-type-mappings-ado-net.md)
- [SQL Server and ADO.NET](./sql/index.md) (SQL Server 和 ADO.NET)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
