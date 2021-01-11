---
title: 使用預存程序修改資料
description: 說明如何使用預存程序 (Stored Procedure) 輸入參數和輸出參數，將資料列插入資料庫中，並傳回新的識別值。
ms.date: 12/04/2020
dev_langs:
- csharp
ms.assetid: 7d8e9a46-1af6-4a02-bf61-969d77ae07e0
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: f04884302bb1f13852097182d6ebc8e06570c66b
ms.sourcegitcommit: 4419e99d77ee2c73f9da1559c7944f7702f2de30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744370"
---
# <a name="modify-data-with-stored-procedures"></a>使用預存程序修改資料

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

預存程序 (Stored Procedure) 可以接受資料做為輸入參數，也可以將資料以輸出參數、結果集 (Result Set) 或傳回值的形式傳回。 下列的範例說明 Microsoft SqlClient Data Provider for SQL Server 如何傳送及接收輸入參數、輸出參數和傳回值。 此範例會將新記錄插入至主索引鍵資料行為識別欄位的資料表。

> [!NOTE]
> 如果您透過 <xref:Microsoft.Data.SqlClient.SqlDataAdapter> 使用預存程序來編輯或刪除資料，請確定您未在預存程序定義中使用 **SET NOCOUNT ON**。 因為這樣會讓傳回的受影響資料列計數成為零，而 `DataAdapter` 會將它解譯為並行衝突。 在此事件中，系統會擲回 <xref:System.Data.DBConcurrencyException>。

## <a name="example"></a>範例

此範例使用下列預存程序來將新類別插入 **Northwind** 的 **Categories** 資料表。 此預存程序使用 **CategoryName** 資料行中的值作為輸入參數，並使用 **SCOPE_IDENTITY()** 函式來擷取識別欄位 **CategoryID** 的新值，然後在輸出參數中加以傳回。 RETURN 陳述式使用 **\@\@ROWCOUNT** 函式來傳回插入的資料列數。

```sql
CREATE PROCEDURE dbo.InsertCategory  
  @CategoryName nvarchar(15),  
  @Identity int OUT  
AS  
INSERT INTO Categories (CategoryName) VALUES(@CategoryName)  
SET @Identity = SCOPE_IDENTITY()  
RETURN @@ROWCOUNT  
```  

下列程式碼範例使用以上所示的 `InsertCategory` 預存程序做為 <xref:Microsoft.Data.SqlClient.SqlDataAdapter.InsertCommand%2A> 的 <xref:Microsoft.Data.SqlClient.SqlDataAdapter> 的來源。 在呼叫 `@Identity` 的 <xref:System.Data.DataSet> 方法而將記錄插入至資料庫之後，`Update` 輸出參數將反映在 <xref:Microsoft.Data.SqlClient.SqlDataAdapter> 中。 程式碼也會擷取傳回值。

[!code-csharp[DataWorks SqlClient.SprocIdentityReturn#1](~/../sqlclient/doc/samples/SqlDataAdapter_SPIdentityReturn.cs#1)]

## <a name="see-also"></a>請參閱

- [在 ADO.NET 中擷取及修改資料](retrieving-modifying-data.md)
- [DataAdapter 和 DataReader](dataadapters-datareaders.md)
- [執行命令](execute-command.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
