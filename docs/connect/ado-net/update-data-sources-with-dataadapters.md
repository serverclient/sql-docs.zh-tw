---
title: 使用 DataAdapter 更新資料來源
description: 了解 DataAdapter 的更新方法如何將 DataSet 的變更，解析回 ADO.NET 應用程式中的資料來源。
ms.date: 11/30/2020
dev_langs:
- csharp
ms.assetid: d1bd9a8c-0e29-40e3-bda8-d89176b72fb1
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 6f2feb876d0f232f4d7951de8ee1cc84587e6486
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771328"
---
# <a name="update-data-sources-with-dataadapters"></a>使用 DataAdapter 更新資料來源

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

呼叫 `Update` 的 <xref:System.Data.Common.DataAdapter> 方法，可將 <xref:System.Data.DataSet> 的變更解析回資料來源。 `Update` 方法類似 `Fill` 方法，會將 `DataSet` 的執行個體以及選擇性 (Optional) <xref:System.Data.DataTable> 物件或 `DataTable` 名稱做為引數。 `DataSet` 執行個體是包含已進行之變更的 `DataSet`，而 `DataTable` 則識別要從中擷取變更的資料表。 如果沒有指定任何 `DataTable`，就會使用 `DataTable` 中的第一個 `DataSet`。

當您呼叫 `Update` 方法時，`DataAdapter` 會分析已進行的變更，並執行適當的命令 (INSERT、UPDATE 或 DELETE)。 當 `DataAdapter` 發現 <xref:System.Data.DataRow> 的變更時，它會使用 <xref:Microsoft.Data.SqlClient.SqlDataAdapter.InsertCommand%2A>、<xref:Microsoft.Data.SqlClient.SqlDataAdapter.UpdateCommand%2A> 或 <xref:Microsoft.Data.SqlClient.SqlDataAdapter.DeleteCommand%2A> 來處理變更。

這些屬性可讓您在設計階段指定命令語法，並在可能的情況下使用預存程序，將 ADO.NET 應用程式的效能最大化。 您必須在呼叫 `Update` 之前明確地設定命令。 如果呼叫 `Update` 之後，特定更新沒有適當命令可用 (例如，刪除的資料列沒有 `DeleteCommand`)，便會擲回例外狀況 (Exception)。

> [!IMPORTANT]
> 如果您搭配 `DataAdapter` 使用 SQL Server 的預存程序來編輯或刪除資料，請確定您沒有在預存程序定義中使用 `SET NOCOUNT ON`。 因為這樣會讓傳回的受影響資料列計數成為零，而 `DataAdapter` 會將它解譯為並行衝突。 在此事件中，系統會擲回 <xref:System.Data.DBConcurrencyException>。

命令參數可用來針對 `DataSet` 內每個經過修改的資料列指定 SQL 陳述式或預存程序的輸入和輸出值。 如需詳細資訊，請參閱 [DataAdapter 參數](dataadapter-parameters.md) (機器翻譯)。

> [!NOTE]
> 請務必瞭解刪除 <xref:System.Data.DataTable> 中的資料列與移除資料列之間的差異。 當您呼叫 `Remove` 或 `RemoveAt` 方法時，系統會立即移除資料列。 如果您接著將 `DataTable` 或 `DataSet` 傳遞至 `DataAdapter` 並呼叫 `Update`，則後端資料來源中對應的資料列皆 **不會受到影響**。 當您使用 `Delete` 方法時，資料列會保留在 `DataTable` 中並標示為待刪除。 如果您接著將 `DataTable` 或 `DataSet` 傳遞至 `DataAdapter` 並呼叫 `Update`，則會 **刪除** 後端資料來源中對應的資料列。

如果您的 `DataTable` 對應至或產生自單一資料庫資料表，則可以利用 <xref:System.Data.Common.DbCommandBuilder> 物件來自動產生 `DeleteCommand`、`InsertCommand` 以及 `UpdateCommand` 的 `DataAdapter` 物件。 如需詳細資訊，請參閱[使用 CommandBuilder 產生命令](generate-commands-with-commandbuilders.md) (機器翻譯)。

## <a name="use-updatedrowsource-to-map-values-to-a-dataset"></a>使用 UpdatedRowSource 將值對應至 DataSet

您可以使用 <xref:Microsoft.Data.SqlClient.SqlCommand> 物件的 <xref:Microsoft.Data.SqlClient.SqlCommand.UpdatedRowSource%2A> 屬性，在呼叫 `DataAdapter` 的 <xref:System.Data.Common.DbDataAdapter.Update%2A> 方法之後，控制從資料來源傳回的值要如何對應回 `DataTable`。 您可以透過將 `UpdatedRowSource` 屬性設為其中一個 <xref:System.Data.UpdateRowSource> 列舉值，控制要忽略 `DataAdapter` 命令所傳回的輸出參數，還是要將這些參數套用至 `DataSet` 中已變更的資料列。 您還能夠指定是否要將第一個傳回的資料列 (如果存在) 套用至 `DataTable` 中已變更的資料列。

下列表格說明 `UpdateRowSource` 列舉型別的各種值，以及這些值如何影響與 `DataAdapter` 搭配使用之命令的行為。

|UpdatedRowSource 列舉型別|描述|
|----------------------------------|-----------------|
|<xref:System.Data.UpdateRowSource.Both>|輸出參數和傳回結果集的第一個資料列都會對應至 `DataSet` 中已變更的資料列。|
|<xref:System.Data.UpdateRowSource.FirstReturnedRecord>|只有傳回結果集之第一個資料列內的資料會對應至 `DataSet` 中已變更的資料列。|
|<xref:System.Data.UpdateRowSource.None>|將忽略任何輸出參數或傳回結果集的資料列。|
|<xref:System.Data.UpdateRowSource.OutputParameters>|只有輸出參數會對應至 `DataSet` 中已變更的資料列。|

`Update` 方法會將您的變更解析回資料來源，但是自從您上次填入 `DataSet` 之後，其他用戶端可能也已經修改資料來源的資料。 若要以目前的資料重新整理 `DataSet`，請使用 `DataAdapter` 和 `Fill` 方法。 如此新資料列會加入資料表，而更新資訊也會合併入現有資料列。

`Fill` 方法會藉由檢查 `DataSet` 中資料列的主索引鍵值以及 `SelectCommand` 所傳回的資料列，決定要加入新資料列或更新現有資料列。 如果 `Fill` 方法發現 `DataSet` 中之資料列的主索引鍵值符合 `SelectCommand` 所傳回結果中之資料列的主索引鍵值，它就會以 `SelectCommand` 所傳回之資料列中的資訊更新現有資料列，並將現有資料列的 <xref:System.Data.DataRow.RowState%2A> 設為 `Unchanged`。 如果 `SelectCommand` 傳回之資料列的主索引鍵值與 `DataSet` 中資料列的任何主索引鍵值都不相符，`Fill` 方法就加入 `RowState` 設定為 `Unchanged` 的新資料列。

> [!NOTE]
> 如果 `SelectCommand` 傳回 **外部聯結** 的結果，則 `DataAdapter` 不會為產生的 `DataTable` 設定 `PrimaryKey` 值。 您必須自己定義 `PrimaryKey`，確保正確解析重複的資料列。

若要處理呼叫 `Update` 方法時可能會發生的例外狀況，您可以使用 `RowUpdated` 事件在發生例外狀況時回應資料列更新錯誤 (請參閱[處理 DataAdapter 事件](handle-dataadapter-events.md) (英文))，也可以在呼叫 `Update` 之前將 <xref:System.Data.Common.DataAdapter.ContinueUpdateOnError%2A> 設定為 `true`，然後在更新完成時回應儲存於特定資料列 `RowError` 屬性中的錯誤資訊。

> [!NOTE]
> 在 `DataSet`、`DataTable` 或 `DataRow` 上呼叫 `AcceptChanges` 會導致 `DataRow` 的所有 `Original` 值都以 `DataRow` 的 `Current` 值覆寫。 如果識別資料列為唯一的欄位值已經被修改，則在呼叫 `AcceptChanges` 之後，`Original` 值就不會再與資料來源內的值相符。 呼叫 `DataAdapter` 的 `Update` 方法時，會為每個資料列自動呼叫 `AcceptChanges`。 您可以先將 `AcceptChangesDuringUpdate` 的 `DataAdapter` 屬性設定為 false，或針對 `RowUpdated` 事件建立事件處理常式並將 <xref:System.Data.Common.RowUpdatedEventArgs.Status%2A> 設定為 <xref:System.Data.UpdateStatus.SkipCurrentRow>，藉以在呼叫 Update 方法期間保留原始值。 如需詳細資訊，請參閱[處理 DataAdapter 事件](handle-dataadapter-events.md) (英文)。

下列範例示範如何明確設定 `DataAdapter` 的 `UpdateCommand` 並呼叫其 `Update` 方法，藉以更新已修改的資料列。

> [!NOTE]
> 在 `UPDATE statement` 的 `WHERE clause` 中所指定的參數會設定為使用 `SourceColumn` 的 `Original` 值。 這一點相當重要，因為 `Current` 值可能已經修改，而不符合資料來源中的值。 `Original` 值是用來填入資料來源之 `DataTable` 的值。

[!code-csharp[SqlDataAdapter_Update#1](~/../sqlclient/doc/samples/SqlDataAdapter_Update.cs#1)]

## <a name="autoincrement-columns"></a>自動遞增資料行

如果來自資料來源的資料表具有自動遞增的資料行，您就可以將這些資料行填入 `DataSet` 中，方法包括：將自動遞增值當成預存程序的輸出參數傳回並將它對應至資料表的資料行、傳回結果集 (由預存程序或 SQL 陳述式所傳回) 之第一個資料列中的自動遞增值，或使用 `RowUpdated` 的 `DataAdapter` 事件來執行其他 SELECT 陳述式。 如需詳細資訊與範例，請參閱[擷取識別或自動編號值](retrieve-identity-or-autonumber-values.md)。

## <a name="ordering-of-inserts-updates-and-deletes"></a>插入、更新與刪除的順序

在許多情況下，以何種順序將透過 `DataSet` 所進行的變更傳送給資料來源是很重要的。 例如，如果更新了現有資料列的主索引鍵值，也加入了以新主索引鍵值當做外部索引鍵的資料列，則先進行更新再執行插入是很重要的。

您可以使用 `Select` 的 `DataTable` 方法來傳回僅參考具有特定 `DataRow` 之資料列的 `RowState` 陣列。 接著，您可以將傳回的 `DataRow` 陣列傳遞給 `Update` 的 `DataAdapter` 方法來處理已修改的資料列。 您可指定要更新的資料列子集，藉以控制插入、更新和刪除的處理順序。

## <a name="example"></a>範例

例如，下列程式碼確保先處理資料表的已刪除資料列，接著處理已更新資料列，然後再處理已插入資料列。

[!code-csharp[SqlDataAdapter_Update#2](~/../sqlclient/doc/samples/SqlDataAdapter_Update.cs#2)]

## <a name="use-a-dataadapter-to-retrieve-and-update-data"></a>使用 DataAdapter 來擷取和更新資料

您可以使用 DataAdapter 擷取和更新資料。

- 此範例會使用 `DataAdapter.AcceptChangesDuringFill` 來複製資料庫中的資料。 如果將屬性設定為 **false**，則在填滿資料表時不會呼叫 **AcceptChanges**，而且新增的資料列會被視為插入的資料列。 因此，範例會使用這些資料列，將新資料列插入資料庫。

- 這些範例會使用 `DataAdapter.TableMappings` 來定義來源資料表與 DataTable 之間的對應。

- 此範例會使用 `DataAdapter.FillLoadOption` 來決定配接器如何在 **DbDataReader** 中填入 **DataTable**。 當您建立 DataTable 時，您只能透過將屬性設定為 **LoadOption.Upsert** 或 **LoadOption.PreserveChanges**，來將資料從資料庫寫入目前版本或原始版本。

- 此範例也會使用 `DbDataAdapter.UpdateBatchSize` 來更新資料表，以執行批次作業。

在編譯和執行範例之前，您必須建立範例資料庫：

```sql
USE [master]
GO

CREATE DATABASE [MySchool]

GO

USE [MySchool]
GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Course]([CourseID] [nvarchar](10) NOT NULL,
[Year] [smallint] NOT NULL,
[Title] [nvarchar](100) NOT NULL,
[Credits] [int] NOT NULL,
[DepartmentID] [int] NOT NULL,
 CONSTRAINT [PK_Course] PRIMARY KEY CLUSTERED
(
[CourseID] ASC,
[Year] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]) ON [PRIMARY]

GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Department]([DepartmentID] [int] IDENTITY(1,1) NOT NULL,
[Name] [nvarchar](50) NOT NULL,
[Budget] [money] NOT NULL,
[StartDate] [datetime] NOT NULL,
[Administrator] [int] NULL,
 CONSTRAINT [PK_Department] PRIMARY KEY CLUSTERED
(
[DepartmentID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]) ON [PRIMARY]

GO

INSERT [dbo].[Course] ([CourseID], [Year], [Title], [Credits], [DepartmentID]) VALUES (N'C1045', 2012, N'Calculus', 4, 7)
INSERT [dbo].[Course] ([CourseID], [Year], [Title], [Credits], [DepartmentID]) VALUES (N'C1061', 2012, N'Physics', 4, 1)
INSERT [dbo].[Course] ([CourseID], [Year], [Title], [Credits], [DepartmentID]) VALUES (N'C2021', 2012, N'Composition', 3, 2)
INSERT [dbo].[Course] ([CourseID], [Year], [Title], [Credits], [DepartmentID]) VALUES (N'C2042', 2012, N'Literature', 4, 2)

SET IDENTITY_INSERT [dbo].[Department] ON

INSERT [dbo].[Department] ([DepartmentID], [Name], [Budget], [StartDate], [Administrator]) VALUES (1, N'Engineering', 350000.0000, CAST(0x0000999C00000000 AS DateTime), 2)
INSERT [dbo].[Department] ([DepartmentID], [Name], [Budget], [StartDate], [Administrator]) VALUES (2, N'English', 120000.0000, CAST(0x0000999C00000000 AS DateTime), 6)
INSERT [dbo].[Department] ([DepartmentID], [Name], [Budget], [StartDate], [Administrator]) VALUES (4, N'Economics', 200000.0000, CAST(0x0000999C00000000 AS DateTime), 4)
INSERT [dbo].[Department] ([DepartmentID], [Name], [Budget], [StartDate], [Administrator]) VALUES (7, N'Mathematics', 250024.0000, CAST(0x0000999C00000000 AS DateTime), 3)
SET IDENTITY_INSERT [dbo].[Department] OFF

ALTER TABLE [dbo].[Course]  WITH CHECK ADD  CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
REFERENCES [dbo].[Department] ([DepartmentID])
GO
ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department]
GO
```

[!code-csharp[SqlDataAdapter_Properties#1](~/../sqlclient/doc/samples/SqlDataAdapter_Properties.cs#1)]

## <a name="see-also"></a>請參閱

- [DataAdapter 和 DataReader](dataadapters-datareaders.md)
- [擷取識別或自動編號值](retrieve-identity-or-autonumber-values.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
