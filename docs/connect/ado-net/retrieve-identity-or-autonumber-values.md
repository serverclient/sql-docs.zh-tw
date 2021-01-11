---
title: 擷取識別或自動編號值
description: 了解如何在 SQL Server 中擷取主要金鑰的識別與自動編號值，以及如何在 ADO.NET 中合併新的識別值。
ms.date: 12/04/2020
dev_langs:
- csharp
ms.assetid: d6b7f9cb-81be-44e1-bb94-56137954876d
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 4ca2199686359235ff1d2c834b0b19a88296030d
ms.sourcegitcommit: 4419e99d77ee2c73f9da1559c7944f7702f2de30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744344"
---
# <a name="retrieve-identity-or-autonumber-values"></a>擷取識別或自動編號值

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

關聯式資料庫中的主索引鍵是永遠包含唯一值的資料行或資料行組合。 瞭解主索引鍵值可讓您找出包含此值的資料列。 關聯式 Database Engine (例如 SQL Server、Oracle 和 Microsoft Access/Jet) 支援建立可指定為主索引鍵的自動遞增資料行。 上述的值會在資料列加入至資料表時由伺服器產生。 在 SQL Server 中，您可以設定資料行的識別屬性。在 Oracle 中，您可以建立序列 (Sequence)。在 Microsoft Access 中，您可以建立 AutoNumber 資料行。

您也可以將 <xref:System.Data.DataColumn> 屬性設定為 true，讓 <xref:System.Data.DataColumn.AutoIncrement%2A> 用來產生自動遞增的值。 不過，如果多個用戶端應用程式都獨立產生自動遞增的值，您可能會造成 <xref:System.Data.DataTable> 的個別執行個體 (Instance) 具有重複的值。 如果讓伺服器產生自動遞增的值，就能排除因允許每位使用者針對每個插入的資料列自行擷取產生的值所可能發生的衝突。

在呼叫 `Update` 的 `DataAdapter` 方法期間，資料庫可以傳送資料回 ADO.NET 應用程式當做輸出參數，或當做與 INSERT 陳述式在相同批次中執行之 SELECT 陳述式結果集的第一個傳回資料錄。 Microsoft SqlClient Data Provider for SQL Server 可以擷取這些值，並在要更新的 <xref:System.Data.DataRow> 中更新相對應的資料行。

> [!NOTE]
> 使用自動遞增值的替代方式是使用 <xref:System.Guid.NewGuid%2A> 的 <xref:System.Guid> 方法，在用戶端電腦上產生插入每個新資料列時可複製到伺服器的 GUID (全域唯一識別項)。 `NewGuid` 方法會產生 16 個位元組的二進位值，而此值是使用盡可能確保沒有任何值會重複的演算法所建立的。 在 SQL Server 資料庫中，GUID 會儲存在 SQL Server 可以使用 Transact-SQL `uniqueidentifier` 函式來自動產生的 `NEWID()` 資料行中。 使用 GUID 當做主索引鍵可能會對效能造成不良影響。 SQL Server 提供 `NEWSEQUENTIALID()` 函式的支援，而此函式會產生循序 GUID (雖然不保證是全域唯一的，但是可更有效率地進行索引)。

## <a name="retrieve-sql-server-identity-column-values"></a>擷取 SQL Server identity 資料行值

使用 Microsoft SQL Server 時，您可以建立含有輸出參數的預存程序，以便傳回插入資料列的識別值。 下表將描述 SQL Server 中三個可用來擷取識別資料行值的 Transact-SQL 函式。

|函式|描述|
|--------------|-----------------|
|SCOPE_IDENTITY|傳回目前執行範圍內的最後一個識別值。 建議您針對大部分案例使用 SCOPE_IDENTITY。|
|@@IDENTITY|包含目前工作階段 (Session) 中於任何資料表內產生的最後一個識別值。 @@IDENTITY 可能會受到觸發程序的影響，而且可能無法傳回您所預期的識別值。|
|IDENT_CURRENT|傳回在任何工作階段和任何範圍中針對特定資料表所產生的最後一個識別值。|

下列預存程序將示範如何將資料列插入 **Categories** 資料表，並使用輸出參數來傳回 Transact-SQL SCOPE_IDENTITY() 函式所產生的新識別值。

```sql
CREATE PROCEDURE dbo.InsertCategory
  @CategoryName nvarchar(15),
  @Identity int OUT
AS
INSERT INTO Categories (CategoryName) VALUES(@CategoryName)
SET @Identity = SCOPE_IDENTITY()
```

然後，您可以將此預存程序指定為 <xref:Microsoft.Data.SqlClient.SqlDataAdapter.InsertCommand%2A> 物件之 <xref:Microsoft.Data.SqlClient.SqlDataAdapter> 的來源。 <xref:Microsoft.Data.SqlClient.SqlCommand.CommandType%2A> 的 <xref:Microsoft.Data.SqlClient.SqlDataAdapter.InsertCommand%2A> 屬性必須設定為 <xref:System.Data.CommandType.StoredProcedure>。 識別輸出的擷取方式是建立 <xref:Microsoft.Data.SqlClient.SqlParameter> 為 <xref:System.Data.ParameterDirection> 的 <xref:System.Data.ParameterDirection.Output>。 如果您將插入命令的 <xref:Microsoft.Data.SqlClient.SqlCommand.UpdatedRowSource%2A> 屬性設定為 `UpdateRowSource.OutputParameters` 或 `UpdateRowSource.Both`，當 `InsertCommand` 經過處理之後，系統就會傳回自動遞增的識別值並將其放置在目前資料列的 **CategoryID** 資料行中。

如果您的插入命令執行了同時包含 INSERT 陳述式和 SELECT 陳述式而且傳回新識別值的批次，您就可以將插入命令的 `UpdatedRowSource` 屬性設定為 `UpdateRowSource.FirstReturnedRecord`，藉以擷取新的值。

[!code-csharp[DataWorks SqlClient.RetrieveIdentityStoredProcedure#1](~/../sqlclient/doc/samples/SqlDataAdapter_RetrieveIdentityStoredProcedure.cs#1)]

## <a name="merge-new-identity-values"></a>合併新的識別值

常見的案例是呼叫 `GetChanges` 的 `DataTable` 方法來建立僅包含變更資料列的複本，以及在呼叫 `Update` 的 `DataAdapter` 方法時使用新的複本。 當您必須將變更的資料列封送處理至執行更新的個別元件時，這種做法特別有用。 更新之後，該複本可以包含新的識別值，而這些值之後必須合併回原始的 `DataTable` 中。 新的識別值可能會與 `DataTable` 中的原始值不同。 若要完成合併，您必須保留複本中 **AutoIncrement** 資料行的原始值，才能找出並更新原始 `DataTable` 中的現有資料列，而非附加包含新識別值的新資料列。 不過，根據預設，這些原始值在呼叫 `Update` 的 `DataAdapter` 方法之後都會遺失，因為系統會針對每個更新的 `AcceptChanges` 隱含地呼叫 `DataRow`。

目前有兩種方式可以在 `DataColumn` 更新期間保留 `DataRow` 中 `DataAdapter` 的原始值。

- 第一種保留原始值的方法是將 `AcceptChangesDuringUpdate` 的 `DataAdapter` 屬性設定為 `false`。 此設定會影響正在更新之 `DataTable` 中的每一個 `DataRow`。 如需詳細資訊和程式碼範例，請參閱 <xref:System.Data.Common.DataAdapter.AcceptChangesDuringUpdate%2A>。

- 第二種方法是在 `RowUpdated` 的 `DataAdapter` 事件處理常式中撰寫程式碼，以便將 <xref:System.Data.Common.RowUpdatedEventArgs.Status%2A> 設定為 <xref:System.Data.UpdateStatus.SkipCurrentRow>。 如此一來，雖然系統會更新 `DataRow`，卻會保留每個 `DataColumn` 的原始值。 這種方法可讓您保留某些資料列的原始值，而不保留其他資料列的原始值。 例如，您的程式碼可以先檢查 <xref:System.Data.Common.RowUpdatedEventArgs.StatementType%2A> 並僅針對 <xref:System.Data.Common.RowUpdatedEventArgs.Status%2A> 為 <xref:System.Data.UpdateStatus.SkipCurrentRow> 的資料列將 `StatementType` 設定為 `Insert`，藉以保留已加入資料列的原始值，而不保留已編輯或刪除資料列的原始值。

當您使用這些方法的其中之一在 `DataAdapter` 更新期間保留 `DataRow` 中的原始值時，Microsoft SqlClient Data Adapter for SQL Server 會執行一系列的動作，將 `DataRow` 的目前值設定為輸出參數所傳回的新值，或設定為結果集第一個傳回的資料列所傳回的新值，同時仍然保留每個 `DataColumn` 中的原始值。 首先，系統會呼叫 `AcceptChanges` 的 `DataRow` 方法來保留目前的值當做原始值，然後指派新的值。 進行這些動作之後，已將 `DataRows` 屬性設定為 <xref:System.Data.DataRow.RowState%2A> 的 <xref:System.Data.DataRowState.Added> 會將其 `RowState` 屬性設定為 <xref:System.Data.DataRowState.Modified> (可能無法預期)。

命令結果套用至正在更新之每個 <xref:System.Data.DataRow> 的方式是由每個 <xref:System.Data.Common.DbCommand.UpdatedRowSource%2A> 的 <xref:System.Data.Common.DbCommand> 屬性所決定。 這個屬性會設定為 `UpdateRowSource` 列舉類型 (Enumeration) 中的值。

下表將描述 `UpdateRowSource` 列舉值如何影響已更新資料列的 <xref:System.Data.DataRow.RowState%2A> 屬性。

|成員名稱|說明|
|-----------------|-----------------|
|<xref:System.Data.UpdateRowSource.Both>|系統會呼叫 `AcceptChanges`，而且輸出參數值和 (或) 任何傳回之結果集中第一個資料列的值都會放置於正在更新的 `DataRow` 中。 如果沒有任何要套用的值，`RowState` 將成為 <xref:System.Data.DataRowState.Unchanged>。|
|<xref:System.Data.UpdateRowSource.FirstReturnedRecord>|如果傳回了一個資料列，系統就會呼叫 `AcceptChanges`，而且該資料列會對應至 `DataTable` 中的已變更資料列，並將 `RowState` 設定為 `Modified`。 如果沒有傳回任何資料列，系統就不會呼叫 `AcceptChanges` 而且 `RowState` 會維持 `Added`。|
|<xref:System.Data.UpdateRowSource.None>|系統會忽略任何傳回的參數或資料列。 系統不會呼叫 `AcceptChanges` 而且 `RowState` 會維持 `Added`。|
|<xref:System.Data.UpdateRowSource.OutputParameters>|系統會呼叫 `AcceptChanges`，而且任何輸出參數都會對應至 `DataTable` 中的已變更資料列，並將 `RowState` 設定為 `Modified`。 如果沒有任何輸出參數，`RowState` 將成為 `Unchanged`。|

### <a name="example"></a>範例

這則範例會示範如何從 `DataTable` 中擷取已變更的資料列，以及使用 <xref:Microsoft.Data.SqlClient.SqlDataAdapter> 來更新資料來源並擷取新的識別資料行值。 <xref:Microsoft.Data.SqlClient.SqlDataAdapter.InsertCommand%2A> 會執行兩個 Transact-SQL 陳述式：第一個陳述式是 INSERT 陳述式，而第二個陳述式是使用 SCOPE_IDENTITY 函式來擷取識別值的 SELECT 陳述式。

```sql
INSERT INTO dbo.Shippers (CompanyName)
VALUES (@CompanyName);
SELECT ShipperID, CompanyName FROM dbo.Shippers
WHERE ShipperID = SCOPE_IDENTITY();
```

插入命令的 `UpdatedRowSource` 屬性會設定為 `UpdateRowSource.FirstReturnedRow` 而 <xref:System.Data.MissingSchemaAction> 的 `DataAdapter` 屬性會設定為 `MissingSchemaAction.AddWithKey`。 此時，`DataTable` 已填入內容而且程式碼會將新的資料列加入至 `DataTable`。 然後，系統會將已變更的資料列擷取至新的 `DataTable` 中，然後再傳遞給更新伺服器的 `DataAdapter`。

[!code-csharp[DataWorks SqlClient.MergeIdentity#1](~/../sqlclient/doc/samples/SqlDataAdapter_MergeIdentity.cs#1)]

`OnRowUpdated` 事件處理常式會檢查 <xref:System.Data.Common.RowUpdatedEventArgs.StatementType%2A> 的 <xref:Microsoft.Data.SqlClient.SqlRowUpdatedEventArgs> 來判斷資料列是否為插入項目。 如果是的話，<xref:System.Data.Common.RowUpdatedEventArgs.Status%2A> 屬性就會設定為 <xref:System.Data.UpdateStatus.SkipCurrentRow>。 如此一來，雖然資料列會更新，卻會保留資料列中的原始值。 在此程序的主要本文中，系統會呼叫 <xref:System.Data.DataSet.Merge%2A> 方法，將新的識別值合併至原始的 `DataTable` 中，最後再呼叫 `AcceptChanges`。

[!code-csharp[DataWorks SqlClient.MergeIdentity#2](~/../sqlclient/doc/samples/SqlDataAdapter_MergeIdentity.cs#2)]

### <a name="retrieve-identity-values"></a>擷取識別值

當資料行中的值必須是唯一時，通常會將資料行設定為識別資料行。 此外，有時也需要新資料的識別值。 這個範例示範如何擷取識別值：

- 建立預存程序插入資料並傳回識別值。

- 執行命令插入新資料並顯示結果。

- 使用 <xref:Microsoft.Data.SqlClient.SqlDataAdapter> 插入新資料並顯示結果。

編譯及執行範例之前，您必須使用下列指令碼建立範例資料庫：

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
CREATE procedure [dbo].[CourseExtInfo] @CourseId int
as
select c.CourseID,c.Title,c.Credits,d.Name as DepartmentName
from Course as c left outer join Department as d on c.DepartmentID=d.DepartmentID
where c.CourseID=@CourseId

GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
create procedure [dbo].[DepartmentInfo] @DepartmentId int,@CourseCount int output
as
select @CourseCount=Count(c.CourseID)
from course as c
where c.DepartmentID=@DepartmentId

select d.DepartmentID,d.Name,d.Budget,d.StartDate,d.Administrator
from Department as d
where d.DepartmentID=@DepartmentId

GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
Create PROCEDURE [dbo].[GetDepartmentsOfSpecifiedYear]
@Year int,@BudgetSum money output
AS
BEGIN
        SELECT @BudgetSum=SUM([Budget])
  FROM [MySchool].[dbo].[Department]
  Where YEAR([StartDate])=@Year

SELECT [DepartmentID]
      ,[Name]
      ,[Budget]
      ,[StartDate]
      ,[Administrator]
  FROM [MySchool].[dbo].[Department]
  Where YEAR([StartDate])=@Year

END
GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE PROCEDURE [dbo].[GradeOfStudent]
-- Add the parameters for the stored procedure here
@CourseTitle nvarchar(100),@FirstName nvarchar(50),
@LastName nvarchar(50),@Grade decimal(3,2) output
AS
BEGIN
select @Grade=Max(Grade)
from [dbo].[StudentGrade] as s join [dbo].[Course] as c on
s.CourseID=c.CourseID join [dbo].[Person] as p on s.StudentID=p.PersonID
where c.Title=@CourseTitle and p.FirstName=@FirstName
and p.LastName= @LastName
END
GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE PROCEDURE [dbo].[InsertPerson]
-- Add the parameters for the stored procedure here
@FirstName nvarchar(50),@LastName nvarchar(50),
@PersonID int output
AS
BEGIN
    insert [dbo].[Person](LastName,FirstName) Values(@LastName,@FirstName)

    set @PersonID=SCOPE_IDENTITY()
END
Go

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

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[Person]([PersonID] [int] IDENTITY(1,1) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[HireDate] [datetime] NULL,
[EnrollmentDate] [datetime] NULL,
[Picture] [varbinary](max) NULL,
 CONSTRAINT [PK_School.Student] PRIMARY KEY CLUSTERED
(
[PersonID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]

GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[StudentGrade]([EnrollmentID] [int] IDENTITY(1,1) NOT NULL,
[CourseID] [nvarchar](10) NOT NULL,
[StudentID] [int] NOT NULL,
[Grade] [decimal](3, 2) NOT NULL,
 CONSTRAINT [PK_StudentGrade] PRIMARY KEY CLUSTERED
(
[EnrollmentID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]) ON [PRIMARY]

GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
create view [dbo].[EnglishCourse]
as
select c.CourseID,c.Title,c.Credits,c.DepartmentID
from Course as c join Department as d on c.DepartmentID=d.DepartmentID
where d.Name=N'English'

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
SET IDENTITY_INSERT [dbo].[Person] ON

INSERT [dbo].[Person] ([PersonID], [LastName], [FirstName], [HireDate], [EnrollmentDate]) VALUES (1, N'Hu', N'Nan', NULL, CAST(0x0000A0BF00000000 AS DateTime))
INSERT [dbo].[Person] ([PersonID], [LastName], [FirstName], [HireDate], [EnrollmentDate]) VALUES (2, N'Norman', N'Laura', NULL, CAST(0x0000A0BF00000000 AS DateTime))
INSERT [dbo].[Person] ([PersonID], [LastName], [FirstName], [HireDate], [EnrollmentDate]) VALUES (3, N'Olivotto', N'Nino', NULL, CAST(0x0000A0BF00000000 AS DateTime))
INSERT [dbo].[Person] ([PersonID], [LastName], [FirstName], [HireDate], [EnrollmentDate]) VALUES (4, N'Anand', N'Arturo', NULL, CAST(0x0000A0BF00000000 AS DateTime))
INSERT [dbo].[Person] ([PersonID], [LastName], [FirstName], [HireDate], [EnrollmentDate]) VALUES (5, N'Jai', N'Damien', NULL, CAST(0x0000A0BF00000000 AS DateTime))
INSERT [dbo].[Person] ([PersonID], [LastName], [FirstName], [HireDate], [EnrollmentDate]) VALUES (6, N'Holt', N'Roger', CAST(0x000097F100000000 AS DateTime), NULL)
INSERT [dbo].[Person] ([PersonID], [LastName], [FirstName], [HireDate], [EnrollmentDate]) VALUES (7, N'Martin', N'Randall', CAST(0x00008B1A00000000 AS DateTime), NULL)
SET IDENTITY_INSERT [dbo].[Person] OFF
SET IDENTITY_INSERT [dbo].[StudentGrade] ON

INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (1, N'C1045', 1, CAST(3.50 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (2, N'C1045', 2, CAST(3.00 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (3, N'C1045', 3, CAST(2.50 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (4, N'C1045', 4, CAST(4.00 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (5, N'C1045', 5, CAST(3.50 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (6, N'C1061', 1, CAST(4.00 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (7, N'C1061', 3, CAST(3.50 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (8, N'C1061', 4, CAST(2.50 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (9, N'C1061', 5, CAST(1.50 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (10, N'C2021', 1, CAST(2.50 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (11, N'C2021', 2, CAST(3.50 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (12, N'C2021', 4, CAST(3.00 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (13, N'C2021', 5, CAST(3.00 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (14, N'C2042', 1, CAST(2.00 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (15, N'C2042', 2, CAST(3.50 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (16, N'C2042', 3, CAST(4.00 AS Decimal(3, 2)))
INSERT [dbo].[StudentGrade] ([EnrollmentID], [CourseID], [StudentID], [Grade]) VALUES (17, N'C2042', 5, CAST(3.00 AS Decimal(3, 2)))
SET IDENTITY_INSERT [dbo].[StudentGrade] OFF
ALTER TABLE [dbo].[Course]  WITH CHECK ADD  CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
REFERENCES [dbo].[Department] ([DepartmentID])
GO
ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department]
GO
ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
REFERENCES [dbo].[Person] ([PersonID])
GO
ALTER TABLE [dbo].[StudentGrade] CHECK CONSTRAINT [FK_StudentGrade_Student]
GO
```

後面接著程式碼清單：

[!code-csharp[SqlClient_RetrieveIdentity#1](~/../sqlclient/doc/samples/SqlClient_RetrieveIdentity.cs#1)]

## <a name="see-also"></a>請參閱

- [在 ADO.NET 中擷取及修改資料](retrieving-modifying-data.md)
- [DataAdapter 和 DataReader](dataadapters-datareaders.md)
- [使用 DataAdapter 更新資料來源](update-data-sources-with-dataadapters.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
