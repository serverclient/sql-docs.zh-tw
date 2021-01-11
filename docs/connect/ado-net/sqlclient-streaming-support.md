---
title: SqlClient 串流支援
description: 討論如何撰寫應用程式，以串流來自 SQL Server 的資料，而不需將其完全載入記憶體。
ms.date: 12/04/2020
ms.assetid: c449365b-470b-4edb-9d61-8353149f5531
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: c5ae05856f3f1e01d5831a6e80338f19e3994966
ms.sourcegitcommit: 4419e99d77ee2c73f9da1559c7944f7702f2de30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744345"
---
# <a name="sqlclient-streaming-support"></a>SqlClient 串流支援

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

SQL Server 與應用程式之間的串流支援可支援伺服器上的非結構化資料 (文件、影像與媒體檔案)。 SQL Server 資料庫可以儲存二進位大型物件 (BLOB)，但擷取 BLOB 可能會佔用很多記憶體。

與 SQL Server 之間的串流支援，簡化了串流資料之應用程式的撰寫，不需將資料完全載入記憶體，因此可減少記憶體溢位例外狀況。

串流支援也可讓中介層應用程式以更好的方式進行擴縮，特別是在商務物件連線到 Azure SQL 以傳送、擷取及操作大型 Blob 的案例中。

> [!WARNING]
> 支援串流的成員可用來從查詢中擷取資料，並將參數傳遞給查詢與預存程序。 串流功能可處理基本 OLTP 與資料移轉案例，而且適用於內部部署與外部部署資料移轉環境。

## <a name="streaming-support-from-sql-server"></a>從 SQL Server 串流的支援

從 SQL Server 串流的支援在 <xref:System.Data.Common.DbDataReader> 與 <xref:Microsoft.Data.SqlClient.SqlDataReader> 類別中引進新功能，以取得 <xref:System.IO.Stream>、<xref:System.Xml.XmlReader> 與 <xref:System.IO.TextReader> 物件，並回應這些物件。 這些類別用於從查詢中擷取資料。 因此，從 SQL Server 串流的支援可處理 OLTP 案例，而且適用於內部部署與外部部署環境。

已在 <xref:Microsoft.Data.SqlClient.SqlDataReader> 中新增下列成員，以啟用從 SQL Server 串流的支援：

- <xref:Microsoft.Data.SqlClient.SqlDataReader.IsDBNullAsync%2A>

- <xref:Microsoft.Data.SqlClient.SqlDataReader.GetFieldValue%2A?displayProperty=nameWithType>

- <xref:Microsoft.Data.SqlClient.SqlDataReader.GetFieldValueAsync%2A>

- <xref:Microsoft.Data.SqlClient.SqlDataReader.GetStream%2A>

- <xref:Microsoft.Data.SqlClient.SqlDataReader.GetTextReader%2A>

- <xref:Microsoft.Data.SqlClient.SqlDataReader.GetXmlReader%2A>

已在 <xref:System.Data.Common.DbDataReader> 中新增下列成員，以啟用從 SQL Server 串流的支援：

- <xref:System.Data.Common.DbDataReader.GetFieldValue%2A>

- <xref:System.Data.Common.DbDataReader.GetStream%2A>

- <xref:System.Data.Common.DbDataReader.GetTextReader%2A>

## <a name="streaming-support-to-sql-server"></a>串流到 SQL Server 的支援

串流到 SQL Server 的支援位於 <xref:Microsoft.Data.SqlClient.SqlParameter> 類別，因此其可接受並回應 <xref:System.Xml.XmlReader>、<xref:System.IO.Stream> 與 <xref:System.IO.TextReader> 物件。 <xref:Microsoft.Data.SqlClient.SqlParameter> 用於將參數傳遞給查詢和預存程序。

> [!NOTE]
> 您必須取消所有資料流作業，才能處置 <xref:Microsoft.Data.SqlClient.SqlCommand> 物件或呼叫 <xref:Microsoft.Data.SqlClient.SqlCommand.Cancel%2A>。 如果應用程式傳送 <xref:System.Threading.CancellationToken>，就不保證能取消。

下列 <xref:Microsoft.Data.SqlClient.SqlParameter.SqlDbType%2A> 類型將接受 <xref:Microsoft.Data.SqlClient.SqlParameter.Value%2A> 的 <xref:System.IO.Stream>：

- **二進位**

- **VarBinary**

下列 <xref:Microsoft.Data.SqlClient.SqlParameter.SqlDbType%2A> 類型將接受 <xref:Microsoft.Data.SqlClient.SqlParameter.Value%2A> 的 <xref:System.IO.TextReader>：

- **Char**

- **NChar**

- **NVarChar**

- **XML**

**Xml**<xref:Microsoft.Data.SqlClient.SqlParameter.SqlDbType%2A> 型別將接受 <xref:System.Xml.XmlReader> 的 <xref:Microsoft.Data.SqlClient.SqlParameter.Value%2A>。

<xref:Microsoft.Data.SqlClient.SqlParameter.SqlValue%2A> 可接受型別 <xref:System.Xml.XmlReader>、<xref:System.IO.TextReader> 和 <xref:System.IO.Stream> 的值。

<xref:System.Xml.XmlReader>、<xref:System.IO.TextReader> 和 <xref:System.IO.Stream> 物件會被轉移到 <xref:Microsoft.Data.SqlClient.SqlParameter.Size%2A> 所定義的值。

## <a name="sample----streaming-from-sql-server"></a>範例：從 SQL Server 串流

使用下列 Transact-SQL 來建立範例資料庫：

```sql
CREATE DATABASE [Demo]
GO
USE [Demo]
GO
CREATE TABLE [Streams] (
[id] INT PRIMARY KEY IDENTITY(1, 1),
[textdata] NVARCHAR(MAX),
[bindata] VARBINARY(MAX),
[xmldata] XML)
GO
INSERT INTO [Streams] (textdata, bindata, xmldata) VALUES (N'This is a test', 0x48656C6C6F, N'<test>value</test>')
INSERT INTO [Streams] (textdata, bindata, xmldata) VALUES (N'Hello, World!', 0x54657374696E67, N'<test>value2</test>')
INSERT INTO [Streams] (textdata, bindata, xmldata) VALUES (N'Another row', 0x666F6F626172, N'<fff>bbb</fff><fff>bbc</fff>')
GO
```

範例顯示如何執行下列動作：

- 提供擷取大型檔案的非同步方法，避免封鎖使用者介面執行緒。

- 在 .NET 中從 SQL Server 傳輸大型文字檔。

- 在 .NET 中從 SQL Server 傳輸大型 XML 檔案。

- 從 SQL Server 擷取資料。

- 從某個 SQL Server 資料庫將大型檔案 (BLOB) 傳輸到另一個資料庫，而不會耗盡記憶體。

[!code-csharp[SqlClient_Streaming_FromServer#1](~/../sqlclient/doc/samples/SqlClient_Streaming_FromServer.cs#1)]

## <a name="sample----streaming-to-sql-server"></a>範例：串流到 SQL Server

使用下列 Transact-SQL 來建立範例資料庫：

```sql
CREATE DATABASE [Demo2]
GO
USE [Demo2]
GO
CREATE TABLE [BinaryStreams] (
[id] INT PRIMARY KEY IDENTITY(1, 1),
[bindata] VARBINARY(MAX))
GO
CREATE TABLE [TextStreams] (
[id] INT PRIMARY KEY IDENTITY(1, 1),
[textdata] NVARCHAR(MAX))
GO
CREATE TABLE [BinaryStreamsCopy] (
[id] INT PRIMARY KEY IDENTITY(1, 1),
[bindata] VARBINARY(MAX))
GO
```

範例顯示如何執行下列動作：

- 在 .NET 中將大型 BLOB 傳輸到 SQL Server。

- 在 .NET 中將大型文字檔傳輸到 SQL Server。

- 使用新的非同步功能傳輸大型 BLOB。

- 使用新的非同步功能及 await 關鍵字傳輸大型 BLOB。

- 取消傳輸大型 BLOB。

- 使用新的非同步功能，從某部 SQL Server 串流到另一部。

[!code-csharp[SqlClient_Streaming_ToServer#1](~/../sqlclient/doc/samples/SqlClient_Streaming_ToServer.cs#1)]

## <a name="sample----streaming-from-one-sql-server-to-another-sql-server"></a>範例：從某部 SQL Server 串流到另一部 SQL Server

此範例示範如何以非同步方式，將大型 BLOB 從某部 SQL Server 串流到另一部，並支援取消。

> [!NOTE]
> 執行下列範例之前，請確定已建立 Demo 與 Demo2 資料庫。

[!code-csharp[SqlClient_Streaming_ServerToServer#1](~/../sqlclient/doc/samples/SqlClient_Streaming_ServerToServer.cs#1)]

## <a name="see-also"></a>請參閱

- [在 ADO.NET 中擷取及修改資料](retrieving-modifying-data.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
