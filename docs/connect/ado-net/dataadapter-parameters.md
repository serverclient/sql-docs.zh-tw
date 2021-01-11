---
title: DataAdapter 參數
description: 了解從資料來源傳回資料，以及管理資料來源變更的 DbDataAdapter 屬性。
ms.date: 11/30/2020
dev_langs:
- csharp
ms.assetid: f21e6aba-b76d-46ad-a83e-2ad8e0af1e12
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 90985f155392a78da408ffbda3e1d2c0b12d4648
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771600"
---
# <a name="dataadapter-parameters"></a>DataAdapter 參數

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

<xref:System.Data.Common.DbDataAdapter> 具有四個屬性，可用來擷取資料來源的資料，以及將資料更新至資料來源：<xref:System.Data.Common.DbDataAdapter.SelectCommand%2A> 屬性可傳回資料來源的資料，而 <xref:System.Data.Common.DbDataAdapter.InsertCommand%2A>、<xref:System.Data.Common.DbDataAdapter.UpdateCommand%2A> 和 <xref:System.Data.Common.DbDataAdapter.DeleteCommand%2A> 屬性可用來管理在資料來源的變更。

> [!NOTE]
> 在您呼叫 `SelectCommand` 的 `Fill` 方法前，必須先設定 `DataAdapter` 屬性。 您必須先設定 `InsertCommand`、`UpdateCommand` 或 `DeleteCommand` 屬性，然後再呼叫 `Update` 的 `DataAdapter` 方法，端視針對 <xref:System.Data.DataTable> 中的資料進行哪些變更而定。 例如，如果已經加入資料列，則必須先設定 `InsertCommand`，才能呼叫 `Update`。 `Update` 正在處理已插入、已更新或已刪除的資料列時，`DataAdapter` 會使用個別的 `Command` 屬性來處理這項動作。 已修改資料列的目前資訊會透過 `Command` 集合傳遞給 `Parameters` 物件。

當您更新資料來源的資料列時，您會呼叫 UPDATE 陳述式，此陳述式會使用唯一識別碼，來識別待更新資料表中的資料列。 唯一的識別項一般是主索引鍵欄位的值。 UPDATE 陳述式所使用的參數包含唯一的識別項，以及要更新的資料行和值，如下列 Transact-SQL 陳述式所示。

```sql
UPDATE Customers SET CompanyName = @CompanyName
  WHERE CustomerID = @CustomerID  
```  

> [!NOTE]
> 參數預留位置的語法會隨資料來源而有所不同。 此範例將說明 SQL Server 資料來源的保留字元。

在此範例中，會針對 `CustomerID` 等於 `@CustomerID` 參數值的資料列，以 `@CompanyName` 參數的值來更新 `CompanyName` 欄位。 這些參數會使用 <xref:Microsoft.Data.SqlClient.SqlParameter> 物件的 <xref:Microsoft.Data.SqlClient.SqlParameter.SourceColumn%2A> 屬性，從已修改的資料列擷取資訊。 下列是前述範例 UPDATE 陳述式的參數。 程式碼會假設變數 `adapter` 表示有效的 <xref:Microsoft.Data.SqlClient.SqlDataAdapter> 物件。

[!code-csharp[Classic WebData SqlDataAdapter.SqlDataAdapter#2](~/../sqlclient/doc/samples/SqlDataAdapter_SqlDataAdapter.cs#2)]

`Add` 集合的 `Parameters` 方法會擷取參數的名稱、資料型別、大小 (如果此型別有大小)，以及來自 <xref:System.Data.Common.DbParameter.SourceColumn%2A> 的 `DataTable` 名稱。 請注意，<xref:System.Data.Common.DbParameter.SourceVersion%2A> 參數的 `@CustomerID` 會設定為 `Original`。 如此一來，如果已修改的 <xref:System.Data.DataRow> 內識別欄位的值有所變更，便可確保資料來源內的現有資料列也已經更新。 在這種情況下，`Original` 資料列值會與資料來源中的目前值相符，而 `Current` 資料列值會包含已更新的值。 `SourceVersion` 參數的 `@CompanyName` 並未設定，因此會使用預設的 `Current` 資料列值。

> [!NOTE]
> 針對 `DataAdapter` 的 `Fill` 作業與 `DataReader` 的 `Get` 方法，會以 Microsoft SqlClient Data Provider for SQL Server 所傳回的類型推斷 .NET 類型。 由 Microsoft SQL Server 資料類型所推斷的 .NET 類型與存取子方法，會在 [ADO.NET 中的資料類型對應](data-type-mappings-ado-net.md)一文中詳述。

## <a name="parametersourcecolumn-parametersourceversion"></a>Parameter.SourceColumn、Parameter.SourceVersion

`SourceColumn` 和 `SourceVersion` 可當做引數傳遞給 `Parameter` 建構函式 (Constructor)，或設定為現有 `Parameter` 的屬性。 `SourceColumn` 是將在其中擷取 <xref:System.Data.DataColumn> 值之 <xref:System.Data.DataRow> 的 `Parameter` 名稱。 `SourceVersion` 會指定 `DataRow` 用來擷取值的 `DataAdapter` 版本。

下表顯示可與 <xref:System.Data.DataRowVersion> 搭配使用的 `SourceVersion` 列舉值。

|DataRowVersion 列舉型別|描述|  
|--------------------------------|-----------------|  
|`Current`|這個參數會使用資料行目前的值。 此為預設值。|  
|`Default`|此參數會使用資料行的 `DefaultValue`。|  
|`Original`|這個參數會使用資料行的原始值。|  
|`Proposed`|這個會參數使用建議值。|  

下一區段中的 `SqlClient` 程式碼範例會定義 <xref:System.Data.Common.DbDataAdapter.UpdateCommand%2A> 的參數，其中 `CustomerID` 資料行將做為兩個參數的 `SourceColumn` 使用：`@CustomerID` (`SET CustomerID = @CustomerID`) 和 `@OldCustomerID` (`WHERE CustomerID = @OldCustomerID`)。 `@CustomerID` 參數是用來將 **CustomerID** 資料行更新為 `DataRow` 中目前的值。 因此，會使用 `SourceVersion` 為 `Current` 的 `CustomerID` `SourceColumn`。 `@OldCustomerID` 參數是用來識別資料來源中的目前資料列。 因為在資料列的 `Original` 版本中找到相符的資料行值，所以會使用 `SourceColumn` 為 `CustomerID` 的同一個 `SourceVersion` (`Original`)。

## <a name="work-with-sqlclient-parameters"></a>使用 SqlClient 參數

下列範例將示範如何建立 <xref:Microsoft.Data.SqlClient.SqlDataAdapter> 並將 <xref:System.Data.Common.DataAdapter.MissingSchemaAction%2A> 設定為 <xref:System.Data.MissingSchemaAction.AddWithKey>，以便從資料庫中擷取其他結構描述資訊。 <xref:Microsoft.Data.SqlClient.SqlDataAdapter.SelectCommand%2A>、<xref:Microsoft.Data.SqlClient.SqlDataAdapter.InsertCommand%2A>、<xref:Microsoft.Data.SqlClient.SqlDataAdapter.UpdateCommand%2A> 和 <xref:Microsoft.Data.SqlClient.SqlDataAdapter.DeleteCommand%2A> 屬性已設定而且其對應的 <xref:Microsoft.Data.SqlClient.SqlParameter> 物件已加入至 <xref:Microsoft.Data.SqlClient.SqlCommand.Parameters%2A> 集合。 此方法會傳回 `SqlDataAdapter` 物件。

[!code-csharp[Classic WebData SqlDataAdapter.SqlDataAdapter#1](~/../sqlclient/doc/samples/SqlDataAdapter_SqlDataAdapter.cs#1)]

## <a name="see-also"></a>請參閱

- [DataAdapter 和 DataReader](dataadapters-datareaders.md)
- [命令與參數](commands-parameters.md)
- [使用 DataAdapter 更新資料來源](update-data-sources-with-dataadapters.md)
- [使用預存程序修改資料](modify-data-with-stored-procedures.md)
- [ADO.NET 中的資料類型對應](data-type-mappings-ado-net.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
