---
title: 由 DataReader 擷取的資料
description: 了解如何使用 ADO.NET 中的 DataReader 和此範例程式碼來擷取資料。 DataReader 提供無緩衝的資料流。
ms.date: 11/30/2020
dev_langs:
- csharp
ms.assetid: 97afc121-fb8b-465b-bab3-6d844420badb
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: dfbe41fe29a3dadc69a2a428f1bf8b606a2a7050
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771327"
---
# <a name="retrieve-data-by-a-datareader"></a>由 DataReader 擷取的資料

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

若要使用 **DataReader** 擷取資料，請建立 **Command** 物件的執行個體，再藉由呼叫 **Command.ExecuteReader** 擷取資料來源的資料列，建立 **DataReader**。 **DataReader** 提供無緩衝的資料流，可使程序邏輯有效地循序處理來自資料來源的結果。

> [!NOTE]
> 需要擷取大量資料時，**DataReader** 是很好的選擇，因為資料不會快取至記憶體。

下列範例將說明如何使用 **DataReader**，其中 `reader` 代表有效的 DataReader，而 `command` 代表有效的 Command 物件。  

```csharp
reader = command.ExecuteReader();  
```

使用 **DataReader.Read** 方法，從查詢結果取得資料列。 您可以將資料行的名稱或循序編號傳遞給 **DataReader**，以存取傳回資料列的每個資料行。 不過，為了達到最佳效能，**DataReader** 也提供了一系列方法，讓您以資料行的原生資料類型 (**GetDateTime**、**GetDouble**、**GetGuid**、**GetInt32** 等等) 存取資料行的值。 如需資料提供者特有 **DataReaders** 具類型存取子方法的清單，請參閱 <xref:Microsoft.Data.SqlClient.SqlDataReader>。 若您已知基礎資料類型，請使用具類型的存取子方法，以減少擷取資料行值時所需的類型轉換量。  

下列範例會在 **DataReader** 物件中逐一查看，並從每個資料列傳回兩個資料行。  

[!code-csharp[DataWorks SqlClient.HasRows#1](~/../sqlclient/doc/samples/SqlDataReader_HasRows.cs#1)]

## <a name="close-the-datareader"></a>關閉 DataReader  

當 `DataReader` 物件使用完畢之後，請務必呼叫 `Close()` 方法。

> [!NOTE]
> 如果您的 **Command** 包含輸出參數或傳回值，則必須等到 **DataReader** 關閉後才能使用這些值。  

> [!IMPORTANT]
> 在 **DataReader** 開啟期間，**Connection** 只能供該 **DataReader** 使用。 必須等到原始 **DataReader** 關閉後，才能執行 **Connection** 的任何命令 (包括建立其他 **DataReader**)。  

> [!NOTE]
> 請不要在 **Connection** 上呼叫 **Close** 或 **Dispose**、呼叫 **DataReader**，或您類別之 **Finalize** 方法中的任何其他 Managed 物件。 在完成項中，只需釋放類別直接擁有的 Unmanaged 資源。 如果類別未擁有任何 Unmanaged 資源，請不要在類別定義中包含 **Finalize** 方法。 如需詳細資訊，請參閱[記憶體回收](/dotnet/standard/garbage-collection/index)。
 
## <a name="retrieve-multiple-result-sets-using-nextresult"></a>使用 NextResult 擷取多個結果集

如果 **DataReader** 傳回多個結果集，請呼叫 **NextResult** 方法，依序逐一查看結果集。 下列範例顯示 <xref:Microsoft.Data.SqlClient.SqlDataReader> 使用 <xref:Microsoft.Data.SqlClient.SqlCommand.ExecuteReader%2A> 方法，處理兩個 SELECT 陳述式的結果。  

[!code-csharp[DataWorks SqlClient.NextResult#1](~/../sqlclient/doc/samples/SqlDataReader_NextResult.cs#1)]

## <a name="get-schema-information-from-the-datareader"></a>從 DataReader 取得結構描述資訊  

**DataReader** 開啟期間，您可以使用 **GetSchemaTable** 方法擷取目前結果集的結構描述資訊。 **GetSchemaTable** 會傳回 <xref:System.Data.DataTable> 物件，並填入內含目前結果集之結構描述資訊的資料列和資料行。 **DataTable** 將針對結果集的每個資料行包含一個資料列。 結構描述資料表的每個資料行，皆會對應至結果集資料列內所傳回的資料行屬性，其中 **ColumnName** 等於屬性名稱，而資料行值則等於屬性的值。 下列範例會列出 **DataReader** 的結構描述資訊。  

[!code-csharp[DataWorks SqlClient.GetSchemaTable#1](~/../sqlclient/doc/samples/SqlDataReader_GetSchemaTable.cs#1)]

## <a name="see-also"></a>請參閱

- [DataAdapter 和 DataReader](dataadapters-datareaders.md)
- [命令與參數](commands-parameters.md)
- [擷取資料庫結構描述資訊](retrieving-database-schema-information.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
