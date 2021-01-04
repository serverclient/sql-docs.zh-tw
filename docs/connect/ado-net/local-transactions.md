---
title: 本機交易
description: 示範如何使用 Microsoft SqlClient Data Provider for SQL Server，針對資料庫執行交易。
ms.date: 11/24/2020
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: a310ab409c2300eb31d1e3c0e58b7ebe32096bfd
ms.sourcegitcommit: 2add15a99df7b85d271adb261523689984dfd134
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051271"
---
# <a name="local-transactions"></a>本機交易

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

當您想要將多個工作繫結在一起，使其以單一工作單位的形式執行時，將會使用 ADO.NET 中的交易。 例如，想像應用程式正在執行兩項工作。 首先，它會更新包含訂單資訊的資料表。 然後會更新包含存貨資訊的資料表，將訂購項目記入借方。 如果其中任何一項作業失敗，則兩個更新作業都會復原。  

## <a name="determining-the-transaction-type"></a>判斷交易類型

當交易為單一階段交易，且直接由資料庫處理時，系統便會將該交易視為本機交易。 當交易由交易監視器進行協調，並使用保全機制 (如兩階段交易認可) 進行交易解析時，系統便會將該交易視為分散式交易。

Microsoft SqlClient Data Provider for SQL Server 有自己的 <xref:Microsoft.Data.SqlClient.SqlTransaction> 物件，可在 SQL Server 資料庫中執行本機交易。 其他的 .NET 資料提供者也會提供自己的 `Transaction` 物件。 此外，還有一個 <xref:System.Data.Common.DbTransaction> 類別可用於撰寫獨立於提供者以外且需要交易的程式碼。

> [!NOTE]
> 交易在伺服器上執行時效率最高。 如果您使用的 SQL Server 資料庫大量使用明確的異動，請考慮使用 Transact-SQL BEGIN TRANSACTION 陳述式，將它們寫入為預存程序。

## <a name="performing-a-transaction-using-a-single-connection"></a>使用單一連線執行交易 

在 ADO.NET 中，您可以使用 `Connection` 物件控制交易。 您可使用 `BeginTransaction` 方法來起始本機異動。 開始異動之後，您可使用 `Transaction` 物件的 `Command` 屬性，在該異動中登記命令。 然後，您可根據異動元件的成敗，來認可或復原對資料來源所做的修改。

> [!NOTE]
> `EnlistDistributedTransaction` 方法不可用於本機異動。

交易的範圍僅限於連接。 下列範例執行由 `try` 區塊中的兩個單獨命令所組成的明確異動。 這些命令會針對 AdventureWorks SQL Server 範例資料庫中的 `Production.ScrapReason` 資料表執行 INSERT 陳述式，如果未擲回例外狀況，該陳述式便會得到認可。 如果擲回例外狀況，`catch` 區塊中的程式碼便會復原交易。 如果異動在完成之前遭到中止或連接關閉，則會自動復原該異動。

## <a name="example"></a>範例  

 請遵循下列步驟來執行異動。

1. 呼叫 <xref:Microsoft.Data.SqlClient.SqlConnection.BeginTransaction%2A> 物件的 <xref:Microsoft.Data.SqlClient.SqlConnection> 方法，來標記交易的開始。 <xref:Microsoft.Data.SqlClient.SqlConnection.BeginTransaction%2A> 方法會將參考傳回給交易。 此參考會指派給登記在交易中的 <xref:Microsoft.Data.SqlClient.SqlCommand> 物件。

2. 將 `Transaction` 物件指派給要執行之 <xref:Microsoft.Data.SqlClient.SqlCommand.Transaction%2A> 的 <xref:Microsoft.Data.SqlClient.SqlCommand> 屬性。 如果在有異動正在作用中的連接上執行命令，且 `Transaction` 物件尚未指派給 `Transaction` 物件的 `Command` 屬性，便會擲回例外狀況。

3. 執行必要的命令。

4. 呼叫 <xref:Microsoft.Data.SqlClient.SqlTransaction.Commit%2A> 物件的 <xref:Microsoft.Data.SqlClient.SqlTransaction> 方法來完成交易，或呼叫 <xref:Microsoft.Data.SqlClient.SqlTransaction.Rollback%2A> 方法來中止交易。 如果在執行 <xref:Microsoft.Data.SqlClient.SqlTransaction.Commit%2A> 或 <xref:Microsoft.Data.SqlClient.SqlTransaction.Rollback%2A> 方法之前關閉或處置連接，則會復原異動。

下列程式碼範例將示範使用 Microsoft SqlClient Data Provider for SQL Server 的交易邏輯。  

[!code-csharp[SqlTransactionLocal#1](~/../sqlclient/doc/samples/SqlTransactionLocal.cs#1)]

## <a name="see-also"></a>請參閱

- [異動和並行存取](transactions-and-concurrency.md)
- [分散式交易](distributed-transactions.md)
- [System.Transactions 與 SQL Server 的整合](system-transactions-integration-with-sql-server.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
