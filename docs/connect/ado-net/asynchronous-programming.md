---
title: 非同步程式設計
description: 了解 Microsoft SqlClient Data Provider for SQL Server 中的非同步程式設計。
ms.date: 12/04/2020
ms.assetid: 85da7447-7125-426e-aa5f-438a290d1f77
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 6eec681974499219a1ca649f9a47449a27ff4002
ms.sourcegitcommit: 5b2c47ce88f7e56552fd415c32b319009d043b56
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804304"
---
# <a name="asynchronous-programming"></a>非同步程式設計

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

此主題會討論對 Microsoft SqlClient Data Provider for SQL Server (SqlClient) 中非同步程式設計的支援。

## <a name="legacy-asynchronous-programming"></a>舊版非同步程式設計

Microsoft SqlClient Data Provider for SQL Server 包含來自 **System.Data.SqlClient** 的方法，以針對移轉至 <xref:Microsoft.Data.SqlClient> 的應用程式維持回溯相容性。 不建議針對新的開發使用下列舊版非同步程式設計方法：

- <xref:Microsoft.Data.SqlClient.SqlCommand.BeginExecuteNonQuery%2A?displayProperty=nameWithType>

- <xref:Microsoft.Data.SqlClient.SqlCommand.BeginExecuteReader%2A?displayProperty=nameWithType>

- <xref:Microsoft.Data.SqlClient.SqlCommand.BeginExecuteXmlReader%2A?displayProperty=nameWithType>

> [!TIP]
> 在 Microsoft SqlClient Data Provider for SQL Server 中，這些舊版方法已不需要連接字串中的 `Asynchronous Processing=true`。

## <a name="asynchronous-programming-features"></a>非同步程式設計功能

這些非同步程式設計功能提供讓程式碼成為非同步的簡單技巧。

如需 .NET 中非同步程式設計的詳細資訊，請參閱：

- [C# 中的非同步程式設計](/dotnet/csharp/async)

- [使用 Async 和 Await 進行非同步程式設計 (Visual Basic)](/dotnet/visual-basic/programming-guide/concepts/async/index)

當您的使用者介面沒有回應或不能擴充伺服器時，您可能就需要使程式碼更加非同步。 傳統的非同步程式碼編寫涉及安裝回呼 (也稱為接續)，以表示非同步作業完成後發生的邏輯。 這會使非同步程式碼的結構比同步程式碼更複雜。

您可以在不使用回呼的情況下呼叫非同步方法，而不需要跨多個方法或 lambda 運算式分割您的程式碼。

`async` 修飾詞，指定方法為非同步方法。 呼叫 `async` 方法時會傳回工作。 當 `await` 運算子套用至工作時，目前的方法會立即結束。 當工作完成時，會以相同的方法繼續執行。

> [!TIP]
> 在 Microsoft SqlClient Data Provider for SQL Server 中，不需要進行非同步呼叫來設定 `Context Connection` 連接字串關鍵字。

呼叫 `async` 方法不會配置任何額外的執行緒。 它可能會在結尾處簡短地使用現有的 I/O 完成執行緒。

Microsoft SqlClient Data Provider for SQL Server 中的下列方法支援非同步程式設計：

- <xref:System.Data.Common.DbConnection.OpenAsync%2A?displayProperty=nameWithType>

- <xref:System.Data.Common.DbCommand.ExecuteDbDataReaderAsync%2A?displayProperty=nameWithType>

- <xref:System.Data.Common.DbCommand.ExecuteNonQueryAsync%2A?displayProperty=nameWithType>

- <xref:System.Data.Common.DbCommand.ExecuteReaderAsync%2A?displayProperty=nameWithType>

- <xref:System.Data.Common.DbCommand.ExecuteScalarAsync%2A?displayProperty=nameWithType>

- <xref:System.Data.Common.DbDataReader.GetFieldValueAsync%2A>

- <xref:System.Data.Common.DbDataReader.IsDBNullAsync%2A>

- <xref:System.Data.Common.DbDataReader.NextResultAsync%2A?displayProperty=nameWithType>

- <xref:System.Data.Common.DbDataReader.ReadAsync%2A?displayProperty=nameWithType>

- <xref:Microsoft.Data.SqlClient.SqlConnection.OpenAsync%2A?displayProperty=nameWithType>

- <xref:Microsoft.Data.SqlClient.SqlCommand.ExecuteNonQueryAsync%2A?displayProperty=nameWithType>

- <xref:Microsoft.Data.SqlClient.SqlCommand.ExecuteReaderAsync%2A?displayProperty=nameWithType>

- <xref:Microsoft.Data.SqlClient.SqlCommand.ExecuteScalarAsync%2A?displayProperty=nameWithType>

- <xref:Microsoft.Data.SqlClient.SqlCommand.ExecuteXmlReaderAsync%2A?displayProperty=nameWithType>

- <xref:Microsoft.Data.SqlClient.SqlDataReader.NextResultAsync%2A?displayProperty=nameWithType>

- <xref:Microsoft.Data.SqlClient.SqlDataReader.ReadAsync%2A?displayProperty=nameWithType>

- <xref:Microsoft.Data.SqlClient.SqlBulkCopy.WriteToServerAsync%2A?displayProperty=nameWithType>

其他非同步成員支援 [SqlClient 串流支援](sqlclient-streaming-support.md)。

> [!TIP]
> 非同步方法不需要連接字串中的 `Asynchronous Processing=true`。 而且此屬性在 Microsoft SqlClient Data Provider for SQL Server 中已淘汰。

### <a name="synchronous-to-asynchronous-connection-open"></a>同步至非同步連接開啟

您可以升級現有的應用程式，以使用非同步功能。 例如，假設應用程式具有同步連接演算法，並且會在每次連接至資料庫時封鎖 UI 執行緒，則一旦連接後，該應用程式就會呼叫預存程序，提示其他使用者方才有使用者登入。

[!code-csharp[SqlCommand_ExecuteNonQuery#1](~/../sqlclient/doc/samples/SqlCommand_ExecuteNonQuery.cs#1)]

轉換成使用非同步功能時，程式看起來會像這樣：

[!code-csharp[SqlCommand_ExecuteNonQueryAsync#1](~/../sqlclient/doc/samples/SqlCommand_ExecuteNonQueryAsync.cs#1)]

### <a name="add-the-asynchronous-feature-in-an-existing-application-mixing-old-and-new-patterns"></a>在現有的應用程式中新增非同步功能 (混用新舊模式)

您也可以新增非同步功能 (SqlConnection::OpenAsync)，而不需要變更現有的非同步邏輯。 例如，如果應用程式目前使用：

[!code-csharp[SqlConnection_OpenAsync_ContinueWith#1](~/../sqlclient/doc/samples/SqlConnection_OpenAsync_ContinueWith.cs#1)]

您可以開始使用非同步模式，而不需要大幅變更現有的演算法。

[!code-csharp[SqlConnection_OpenAsync_ContinueWith#2](~/../sqlclient/doc/samples/SqlConnection_OpenAsync_ContinueWith.cs#2)]

### <a name="use-the-base-provider-model-and-the-asynchronous-feature"></a>使用基底提供者模型與非同步功能

您可能需要建立能夠連接到不同的資料庫並執行查詢的工具。 您可以使用基底提供者模型與非同步功能。

您必須啟用伺服器上的「Microsoft 分散式異動控制器」(MSDTC)，才能使用分散式異動。 如需如何啟用 MSDTC 的詳細資訊，請參閱[如何在網頁伺服器上啟用 MSDTC](/previous-versions/commerce-server/dd327979(v=cs.90))。

[!code-csharp[SqlClient_AsyncProgramming_ProviderModel#1](~/../sqlclient/doc/samples/SqlClient_AsyncProgramming_ProviderModel.cs#1)]

### <a name="use-sql-transactions-and-the-new-asynchronous-feature"></a>使用 SQL 交易與新的非同步功能

[!code-csharp[SqlClient_AsyncProgramming_SqlTransaction#1](~/../sqlclient/doc/samples/SqlClient_AsyncProgramming_SqlTransaction.cs#1)]

### <a name="use-distributed-transactions-and-the-new-asynchronous-feature"></a>使用分散式交易與新的非同步功能

在企業應用程式中，您可能需要在某些案例中加入 **分散式交易**，以支援多部資料庫伺服器之間的交易。 您可以使用 System.Transactions 命名空間並登記分散式異動，如下所示：

[!code-csharp[SqlClient_AsyncProgramming_DistributedTransaction#1](~/../sqlclient/doc/samples/SqlClient_AsyncProgramming_DistributedTransaction.cs#1)]

### <a name="cancel-an-asynchronous-operation"></a>取消非同步作業

您可以使用 <xref:System.Threading.CancellationToken> 取消非同步要求。

[!code-csharp[SqlClient_AsyncProgramming_Cancellation#1](~/../sqlclient/doc/samples/SqlClient_AsyncProgramming_Cancellation.cs#1)]

### <a name="asynchronous-operations-with-sqlbulkcopy"></a>包含 SqlBulkCopy 的非同步作業

非同步功能也會在含 <xref:Microsoft.Data.SqlClient.SqlBulkCopy.WriteToServerAsync%2A?displayProperty=nameWithType> 的 <xref:Microsoft.Data.SqlClient.SqlBulkCopy?displayProperty=nameWithType> 中。

[!code-csharp[SqlClient_AsyncProgramming_SqlBulkCopy#1](~/../sqlclient/doc/samples/SqlClient_AsyncProgramming_SqlBulkCopy.cs#1)]

## <a name="asynchronously-use-multiple-commands-with-mars"></a>以非同步方式搭配 MARS 使用多個命令

此範例會開啟 **AdventureWorks** 資料庫的單一連線。 使用 <xref:Microsoft.Data.SqlClient.SqlCommand> 物件，即會建立 <xref:Microsoft.Data.SqlClient.SqlDataReader>。 使用讀取器時，會開啟第二個 <xref:Microsoft.Data.SqlClient.SqlDataReader>，並使用第一個 <xref:Microsoft.Data.SqlClient.SqlDataReader> 的資料作為第二個讀取器的 WHERE 子句輸入。

> [!NOTE]
> 下列範例使用 [**AdventureWorks** 範例資料庫](../../samples/adventureworks-install-configure.md)。 範例程式碼中提供的連接字串會假設資料庫安裝在本機電腦且可供使用。 請依據環境需求修改連接字串。

[!code-csharp[SqlClient_AsyncProgramming_MultipleCommands#1](~/../sqlclient/doc/samples/SqlClient_AsyncProgramming_MultipleCommands.cs#1)]

## <a name="asynchronously-read-and-update-data-with-mars"></a>搭配 MARS 以非同步方式讀取及更新資料

MARS 允許將連線用於含有一個以上擱置中作業的讀取作業與資料操作語言 (DML) 作業。 此功能讓應用程式不需要處理連線忙碌的錯誤。 此外，MARS 也可以取代伺服器端資料指標的使用，這通常會取用更多資源。 最後，因為多個作業可在單一連線上進行操作，所以可共用相同的交易內容，而無需使用 **sp_getbindtoken** 及 **sp_bindsession** 系統預存程序。

下列主控台應用程式示範如何使用兩個具有三個 <xref:Microsoft.Data.SqlClient.SqlCommand> 物件的 <xref:Microsoft.Data.SqlClient.SqlDataReader> 物件，以及已啟用 MARS 的單一 <xref:Microsoft.Data.SqlClient.SqlConnection> 物件。 第一個命令物件會擷取其信用評等為 5 的廠商清單。 第二個命令物件會使用 <xref:Microsoft.Data.SqlClient.SqlDataReader> 提供的廠商識別碼，來載入第二個 <xref:Microsoft.Data.SqlClient.SqlDataReader> 以及該特定廠商的所有產品。 第二個 <xref:Microsoft.Data.SqlClient.SqlDataReader> 會造訪每個產品記錄。 將會執行計算，以判斷新的 **OnOrderQty** 應該是什麼。 然後使用第三個命令物件，以新值來更新 **ProductVendor** 資料表。 這整個程序都會在單一交易內進行，並在結束時復原。

> [!NOTE]
> 下列範例使用 [**AdventureWorks** 範例資料庫](../../samples/adventureworks-install-configure.md)。 範例程式碼中提供的連接字串會假設資料庫安裝在本機電腦且可供使用。 請依據環境需求修改連接字串。

[!code-csharp[SqlClient_AsyncProgramming_MultipleCommandsEx#1](~/../sqlclient/doc/samples/SqlClient_AsyncProgramming_MultipleCommandsEx.cs#1)]

## <a name="see-also"></a>請參閱

- [在 ADO.NET 中擷取及修改資料](retrieving-modifying-data.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
