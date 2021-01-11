---
title: 從 DataAdapter 擴展資料集
description: 了解如何從 ADO.NET 中的 DataAdapter 填入 DataSet，ADO.NET 可為您提供獨立於資料來源，且具有一致性的關聯式程式設計模型。
ms.date: 11/30/2020
dev_langs:
- csharp
ms.assetid: 3fa0ac7d-e266-4954-bfac-3fbe2f913153
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: e6c50bf7255dc77edfd0b93e03dedeec83ed4c4d
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771500"
---
# <a name="populate-a-dataset-from-a-dataadapter"></a>從 DataAdapter 擴展資料集

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

ADO.NET <xref:System.Data.DataSet> 是記憶體駐留型資料表示法，可提供獨立於資料來源，且具有一致性的關聯式程式設計模型。 `DataSet` 表示一組完整的資料，包括資料表、條件約束和資料表間的關係。 因為 `DataSet` 與資料來源無關，所以 `DataSet` 可包含應用程式的本機資料，以及來自多個資料來源的資料。 而您與現有資料來源的互動則是透過 `DataAdapter`。

`SelectCommand` 的 `DataAdapter` 屬性為 `Command` 物件，可用於從資料來源擷取資料。 `InsertCommand`的 `UpdateCommand`、 `DeleteCommand` 和 `DataAdapter` 屬性為 `Command` 物件，可根據對 `DataSet`內資料的修改，管理對資料來源內資料的更新作業。 [使用 Dataadapter 更新資料來源](update-data-sources-with-dataadapters.md)一文中會更詳細地說明這些屬性。

`Fill` 的 `DataAdapter` 方法用於以 `DataSet` 之 `SelectCommand` 的結果填入 `DataAdapter`。 `Fill` 把下列各項當成引數：要填入的 `DataSet` 、 `DataTable` 物件，或是以 `DataTable` 傳回資料列填入的 `SelectCommand`名稱。

> [!NOTE]
> 使用 `DataAdapter` 擷取所有的資料表要花很多時間，特別是資料表包含許多資料列時。 這是因為存取資料庫，尋找和處理資料，然後再將資料傳輸至用戶端的過程非常耗時。 在將所有資料表提取到用戶端時，所有在伺服器上的資料列都會鎖定。 若要提升效能，您可以使用 `WHERE` 子句大幅減少傳回用戶端的資料列數目。 您也可以藉由在 `SELECT` 陳述式中明確列出所需的資料行，以減少傳回用戶端的資料數量。 另一個不錯的解決方法就是以批次方式擷取資料列 (例如一次擷取數百個資料列)，同時僅在用戶端完成目前的批次作業後，才擷取下一批次。

`Fill` 方法會隱含地使用 `DataReader` 物件傳回用於在 `DataSet`內建立資料表的資料行名稱和型別，以及用於填入 `DataSet`內資料表資料列的資料。 資料表和資料行只有在不存在時才會建立；否則 `Fill` 會使用現有的 `DataSet` 結構描述。 資料行類型是根據 [ADO.NET 中資料類型對應](data-type-mappings-ado-net.md)的資料表，以 .NET Framework 類型的形式建立。 但不會建立主索引鍵，除非索引鍵已存在於資料來源中，並且 `DataAdapter` **.** `MissingSchemaAction` 設定為 `MissingSchemaAction` **.** `AddWithKey`。 如果 `Fill` 發現主索引鍵已在資料表中，它就會以資料列 (其主索引鍵的資料行值，符合從資料來源傳回的資料列對應值) 的資料來源資料，覆寫 `DataSet` 中的資料。 如果沒發現主索引鍵，則資料會附加在 `DataSet`的資料表內。 當您填入 `DataSet` 時，`Fill` 會使用任何可能存在的對應 (請參閱 [DataAdapter、DataTable 及 DataColumn 對應](dataadapter-datatable-datacolumn-mappings.md) (英文))。

> [!NOTE]
> 如果 `SelectCommand` 傳回 OUTER JOIN 的結果，則 `DataAdapter` 便不會為產生的 `PrimaryKey` 設定 `DataTable`值。 您必須自己定義 `PrimaryKey` ，確保正確解析重複的資料列。

下列程式碼範例會建立 <xref:Microsoft.Data.SqlClient.SqlDataAdapter> 的執行個體，它使用 <xref:Microsoft.Data.SqlClient.SqlConnection> 連接至 Microsoft SQL Server `Northwind` 資料庫，並以客戶清單填入 <xref:System.Data.DataTable> 中的 `DataSet` 。 SQL 陳述式和傳遞給 <xref:Microsoft.Data.SqlClient.SqlConnection> 建構函式的 <xref:Microsoft.Data.SqlClient.SqlDataAdapter> 引數，可用於建立 <xref:Microsoft.Data.SqlClient.SqlDataAdapter.SelectCommand%2A> 的 <xref:Microsoft.Data.SqlClient.SqlDataAdapter>屬性。

## <a name="example"></a>範例

[!code-csharp[SqlDataAdapter_FillDataSet#1](~/../sqlclient/doc/samples/SqlDataAdapter_FillDataSet.cs#1)]

> [!NOTE]
> 這個範例所顯示的程式碼並未明確開啟和關閉 `Connection`。 `Fill` 方法若發現連接尚未開啟，會隱含開啟 `Connection` 正在使用的 `DataAdapter` 。 如果 `Fill` 開啟了連接，則當 `Fill` 結束時也會一併關閉連接。 如此便可在處理單一作業時 (例如 `Fill` 或 `Update`)，簡化您的程式碼。 但是，如果您要執行需要開啟連接的多項作業，您可明確呼叫 `Open` 的 `Connection`方法，針對資料來源執行作業，然後呼叫 `Close` 的 `Connection`方法，如此即可改善應用程式的效能。 您應該儘量減少與資料來源之間的連接，以釋放資源給其他用戶端應用程式使用。

## <a name="multiple-result-sets"></a>多個結果集

如果 `DataAdapter` 發現多個結果集，它便會在 `DataSet`內建立多個資料表。 資料表會指定 Table *N* 的遞增預設名稱，從 "Table" 開始 (Table0)。 如果資料表名稱做為引數傳遞至 `Fill` 方法，則資料表會獲得開頭為 "TableName"，格式為 TableName *N* 的遞增預設名稱，例如 TableName0。  
  
## <a name="populate-a-dataset-from-multiple-dataadapters"></a>從多個 DataAdapter 填入 DataSet  

不論 `DataAdapter` 物件的數量多寡，都可以搭配 `DataSet` 使用。 每個 `DataAdapter` 都可以用以填滿一個或多個 `DataTable` 物件，並將更新解析回相關的資料來源。 `DataRelation` 與 `Constraint` 物件可以新增至本機的 `DataSet` ，這可讓您將來自不同資料來源的資料關聯。 例如， `DataSet` 包含的資料可來自 Microsoft SQL Server 資料庫、透過 OLE DB 公開的 IBM DB2 資料庫和產生 XML 資料流的資料來源。 與每個資料來源的通訊可以由一個或多個 `DataAdapter` 物件處理。  
  
### <a name="example"></a>範例  

下列程式碼範例會從 Microsoft SQL Server 上的 `Northwind` 資料庫填入客戶清單，並從 Microsoft Access 中的 `Northwind` 資料庫填入訂單清單。 填入的資料表和 `DataRelation`有關，之後客戶清單便會顯示該客戶的訂貨。

[!code-csharp[SqlDataAdapter_FillDataSet#2](~/../sqlclient/doc/samples/SqlDataAdapter_FillDataSet.cs#2)]

## <a name="sql-server-decimal-type"></a>SQL Server Decimal 類型

根據預設，`DataSet` 會使用 .NET 資料類型來儲存資料。 對大部分的應用程式而言，這些型別可便於表示資料來源資訊， 但是當資料來源中的資料型別為 SQL Server decimal 或 numeric 資料型別時，這種表示可能會造成問題。 .NET `decimal` 資料類型最多允許 28 位有效位數，而 SQL Server `decimal` 資料類型則允許 38 位有效位數。 在 `SqlDataAdapter` 作業期間，如果 `Fill` 判斷 SQL Server `decimal` 欄位的精確度超過 28 個字元，則目前的資料列便不會加入 `DataTable`。 反而會發生 `FillError` 事件，讓您判斷是否會失去精確度，並做出適當回應。 如需 `FillError` 事件的詳細資訊，請參閱[處理 DataAdapter 事件](handle-dataadapter-events.md) (英文)。 若要取得 SQL Server `decimal` 值，您也可以使用 <xref:Microsoft.Data.SqlClient.SqlDataReader> 物件並呼叫 <xref:Microsoft.Data.SqlClient.SqlDataReader.GetSqlDecimal%2A> 方法。

ADO.NET 也包含對 `DataSet` 中 <xref:System.Data.SqlTypes> 的增強支援。 如需詳細資訊，請參閱 [SqlTypes and the DataSet](./sql/sqltypes-dataset.md)。

## <a name="see-also"></a>請參閱

- [DataAdapter 和 DataReader](dataadapters-datareaders.md)
- [ADO.NET 中的資料類型對應](data-type-mappings-ado-net.md)
- [Multiple Active Result Set (MARS)](./sql/multiple-active-result-sets-mars.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
