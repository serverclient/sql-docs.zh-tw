---
title: 設定參數
description: Command 物件會使用參數來將值傳遞到 SQL 陳述式或預存程序，以在 ADO.NET 中提供類型檢查和驗證。
ms.date: 11/25/2020
dev_langs:
- csharp
ms.assetid: 537d8a2c-d40b-4000-83eb-bc1fcc93f707
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 14442892bc59fc733c6a67153832b95c3f368b95
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771449"
---
# <a name="configuring-parameters"></a>設定參數

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

命令物件會使用參數將值傳遞至 SQL 陳述式或預存程序 (Stored Procedure)，以提供型別檢查及驗證。 與命令文字不同的是，參數輸入會被視為常值 (Literal)，而非可執行程式碼。 這有助於防衛「SQL 插入式」攻擊，在此類攻擊中，攻擊者會將危害伺服器安全的命令插入 SQL 陳述式中。

參數型命令 (Parameterized Command) 也可以改善查詢執行效能，因為它們可以協助資料庫伺服器正確地比對內送命令與正確快取的查詢計畫。 如需詳細資訊，請參閱[執行計畫快取與重複使用](/sql/relational-databases/query-processing-architecture-guide#execution-plan-caching-and-reuse)和[參數和執行計畫的重複使用](/sql/relational-databases/query-processing-architecture-guide#PlanReuse)。 除了安全性和效能的優點以外，參數型命令也提供方便的方法，可讓您安排傳遞至資料來源的值。

<xref:System.Data.Common.DbParameter> 物件可透過其建構函式建立，或者透過呼叫 <xref:System.Data.Common.DbCommand.DbParameterCollection%2A> 集合的 `Add` 方法，將其加入 <xref:System.Data.Common.DbParameterCollection> 來建立。 `Add` 方法會將建構函式引數或現有的參數物件當做輸出，依資料提供者而定。

## <a name="supply-the-parameterdirection-property"></a>提供 ParameterDirection 屬性

在加入參數時，您必須為不是輸入參數的參數提供 <xref:System.Data.ParameterDirection> 屬性。 下表所顯示的 `ParameterDirection` 值是可以與 <xref:System.Data.ParameterDirection> 列舉一起使用的。

|成員名稱|說明|
|-----------------|-----------------|
|<xref:System.Data.ParameterDirection.Input>|這是輸入參數， 此為預設值。|
|<xref:System.Data.ParameterDirection.InputOutput>|這個參數可執行輸入和輸出。|
|<xref:System.Data.ParameterDirection.Output>|這是輸出參數。|
|<xref:System.Data.ParameterDirection.ReturnValue>|此參數代表預存程序 (Stored Procedure)、內建函式或使用者定義函式等作業的傳回值。|

## <a name="work-with-parameter-placeholders"></a>使用參數預留位置

參數預留位置的語法會隨資料來源而有所不同。 Microsoft SqlClient Data Provider for SQL Server 會以不同的方式來處理命名及指定參數與參數預留位置。 SqlClient 資料提供者會使用 `@`*parametername* 格式的具名參數。

## <a name="specify-parameter-data-types"></a>指定參數資料類型

參數的資料類型是特定於 Microsoft SqlClient Data Provider for SQL Server。 指定類型會先將 `Parameter` 的值轉換成 Microsoft SqlClient Data Provider for SQL Server 類型，然後再將該值傳遞到資料來源。 您也可以使用一般方式指定 `Parameter` 的型別，方法是將 `DbType` 物件的 `Parameter` 屬性設為特定的 <xref:System.Data.DbType>。

`Parameter` 物件的 Microsoft SqlClient Data Provider for SQL Server 類型是從 `Value` 物件之 `Parameter` 的 .NET Framework 類型，或是從 `Parameter` 物件的 `DbType` 推斷而來。 下列表格說明根據以 `Parameter` 值或指定之 `Parameter` 來傳遞的物件而推斷出的 `DbType`型別。

|.NET 類型|DbType|SqlDbType|
|-------------------------|------------|---------------|
|<xref:System.Boolean>|布林值|bit|
|<xref:System.Byte>|Byte|TinyInt|
|byte[]|Binary|VarBinary。 如果位元組陣列超過 VarBinary 的大小上限 (8000 個位元組)，則這個隱含轉換將會失敗。 若要使用超過 8000 個位元組的位元組陣列，請明確設定 <xref:System.Data.SqlDbType>。|
|<xref:System.Char>| |不支援從 char 推斷 <xref:System.Data.SqlDbType> 。|
|<xref:System.DateTime>|Datetime|Datetime|
|<xref:System.DateTimeOffset>|DateTimeOffset|SQL Server 2008 中的 DateTimeOffset。 SQL Server 2008 之前的 SQL Server 版本不支援從 DateTimeOffset 推斷 <xref:System.Data.SqlDbType> 。|
|<xref:System.Decimal>|Decimal|Decimal|
|<xref:System.Double>|Double|Float|
|<xref:System.Single>|Single|Real|
|<xref:System.Guid>|Guid|UniqueIdentifier|
|<xref:System.Int16>|Int16|SmallInt|
|<xref:System.Int32>|Int32|Int|
|<xref:System.Int64>|Int64|BigInt|
|<xref:System.Object>|物件|Variant|
|<xref:System.String>|String|NVarChar。 如果字串超過 NVarChar 的最大大小 (4000 個字元)，則這項隱含轉換將會失敗。 若要使用超過 4000 個字元的字串，請明確設定 <xref:System.Data.SqlDbType>。|
|<xref:System.TimeSpan>|Time|SQL Server 2008 中的 Time。 SQL Server 2008 之前的 SQL Server 版本不支援從 TimeSpan 推斷 <xref:System.Data.SqlDbType> 。|
|<xref:System.UInt16>|UInt16|不支援從 UInt16 推斷 <xref:System.Data.SqlDbType> 。|
|<xref:System.UInt32>|UInt32|不支援從 UInt32 推斷 <xref:System.Data.SqlDbType> 。|
|<xref:System.UInt64>|UInt64|不支援從 UInt64 推斷 <xref:System.Data.SqlDbType> 。|
||AnsiString|VarChar|
||AnsiStringFixedLength|Char|
||貨幣|Money|
||Date|SQL Server 2008 中的 Date。 SQL Server 2008 之前的 SQL Server 版本不支援從 Date 推斷 <xref:System.Data.SqlDbType> 。|
||SByte|不支援從 SByte 推斷 <xref:System.Data.SqlDbType> 。|
||StringFixedLength|NChar|
||Time|SQL Server 2008 中的 Time。 SQL Server 2008 之前的 SQL Server 版本不支援從 Time 推斷 <xref:System.Data.SqlDbType> 。|
||VarNumeric|不支援從 VarNumeric 推斷 <xref:System.Data.SqlDbType> 。|
|使用者定義型別 (包含 <xref:Microsoft.SqlServer.Server.SqlUserDefinedAggregateAttribute>的物件)|SqlClient 一律會傳回物件|如果 <xref:Microsoft.SqlServer.Server.SqlUserDefinedTypeAttribute> 存在，則為 `SqlDbType.Udt`，否則為 `Variant`|

> [!NOTE]
> 將十進位值轉換為其他型別的過程稱為窄化轉換，此類轉換會將十進位值向零的方向取整數。 如果目的型別無法代表此項轉換的結果，則會擲回 <xref:System.OverflowException> 。

> [!NOTE]
> 當您將 Null 參數值傳送到伺服器時，必須指定 <xref:System.DBNull>，而不是 `null` (在 Visual Basic 中為 `Nothing`)。 系統中的 Null 值是沒有值的空物件。 <xref:System.DBNull> 用於表示 null 值。

## <a name="derive-parameter-information"></a>衍生參數資訊

您也可以使用 `DbCommandBuilder` 類別 (Class) 從預存程序衍生參數。 `SqlCommandBuilder` 類別會提供靜態方法 `DeriveParameters`，其會自動填入使用來自預存程序參數資訊之 Command 物件的參數集合。 請注意， `DeriveParameters` 將會覆寫命令所有的現有參數資訊。

> [!NOTE]
> 衍生參數資訊會造成效能降低，因為這項作業需要對資料來源進行額外的來回行程才能擷取資訊。 若在設計階段已知參數資訊，您便可以明確設定參數，改善應用程式的效能。

如需詳細資訊，請參閱[使用 CommandBuilder 產生命令](generate-commands-with-commandbuilders.md)。

## <a name="using-parameters-with-a-sqlcommand-and-a-stored-procedure"></a>搭配 SqlCommand 與預存程序使用參數

預存程序對資料驅動應用程式有許多好處。 藉由使用預存程序，資料庫作業可以封裝在單一命令中、最佳化為最佳效能，並且可進一步提升安全性。 雖然只要將後接參數引數的預存程序名稱當成 SQL 陳述式傳遞即可呼叫預存程序，透過使用 ADO.NET <xref:System.Data.Common.DbCommand> 物件的 <xref:System.Data.Common.DbCommand.Parameters%2A> 集合，可以讓您更明確地定義預存程序參數，以及存取輸出參數與傳回值。

> [!NOTE]
> 參數化陳述式能在伺服器上執行，都是透過允許查詢計畫重複使用的 `sp_executesql,` 。 呼叫 `sp_executesql` 的批次無法見到 `sp_executesql`批次中的本機資料指標或變數。 資料庫內容中的變更只會持續到 `sp_executesql` 陳述式結束。 如需詳細資訊，請參閱 [sp_executesql (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql)。

將參數與 <xref:Microsoft.Data.SqlClient.SqlCommand> 搭配使用以執行 SQL Server 預存程序時，加入 <xref:Microsoft.Data.SqlClient.SqlCommand.Parameters%2A> 集合的參數名稱必須與預存程序中的參數標記名稱相符。 Microsoft SqlClient Data Provider for SQL Server 針對將參數傳遞至 SQL 陳述式或預存程序，並不支援問號 (?) 預留位置。 它會將預存程式中的參數視為具名參數，並搜尋相符的參數標記。 例如， `CustOrderHist` 預存程序是使用名為 `@CustomerID`的參數所定義的。 則當您的程式碼執行預存程序時，也必須使用名為 `@CustomerID`的參數。

```sql
CREATE PROCEDURE dbo.CustOrderHist @CustomerID varchar(5)
```

### <a name="example"></a>範例

此範例會示範如何在 `Northwind` 範例資料庫中呼叫 SQL Server 預存程序。 預存程序的名稱為 `dbo.SalesByCategory` ，而且具有名為 `@CategoryName` 的輸入參數 (資料型別為 `nvarchar(15)`)。 此程式碼會在 Using 區塊中建立新的 <xref:Microsoft.Data.SqlClient.SqlConnection> ，這樣當程序結束時就會清除連接。 <xref:Microsoft.Data.SqlClient.SqlCommand> 和 <xref:Microsoft.Data.SqlClient.SqlParameter> 物件會建立，其屬性也會設定。 <xref:Microsoft.Data.SqlClient.SqlDataReader> 會執行 `SqlCommand` 並從預存程序傳回結果集，在主控台視窗中顯示輸出。

> [!NOTE]
> 與其建立 `SqlCommand` 和 `SqlParameter` 物件，然後再以個別的陳述式設定屬性，您可以選擇使用其中一個多載建構函式 (Constructor)，以單一的陳述式設定多個屬性。

[!code-csharp[DataWorks SqlClient.StoredProcedure#1](~/../sqlclient/doc/samples/SqlCommand_StoredProcedure.cs#1)]

## <a name="see-also"></a>請參閱

- [命令與參數](commands-parameters.md)
- [DataAdapter 和 DataReader](dataadapters-datareaders.md)
- [ADO.NET 中的資料類型對應](data-type-mappings-ado-net.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
