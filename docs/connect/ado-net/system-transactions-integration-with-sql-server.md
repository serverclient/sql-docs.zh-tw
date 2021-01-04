---
title: System.Transactions 與 SQL Server 的整合
description: 說明 System.Transactions 如何與 SQL Server 整合以使用分散式交易。
ms.date: 11/25/2020
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: af0ba2865719a5388314a4ca695e09191cb56173
ms.sourcegitcommit: 2add15a99df7b85d271adb261523689984dfd134
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051274"
---
# <a name="systemtransactions-integration-with-sql-server"></a>System.Transactions 與 SQL Server 的整合

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

.NET 包括可透過 <xref:System.Transactions> 命名空間進行存取的交易架構。 此架構會以在 .NET 中 (包括 ADO.NET) 完全整合的方式公開交易。  
  
除了可程式性增強功能之外，<xref:System.Transactions> 和 ADO.NET 還可以一起運作，以在您使用交易時協調最佳化。 可提升的交易是可視需要自動提升為完全分散式交易的輕量型 (本機) 交易。

當您使用 SQL Server 時，Microsoft SqlClient Data Provider for SQL Server 支援可提升交易。 除非需要已加入的負荷，否則可提升交易不會叫用分散式交易的已加入負荷。 可提升交易是自動的，不需要開發人員介入。

## <a name="creating-promotable-transactions"></a>建立可提升交易

Microsoft SqlClient Data Provider for SQL Server 支援可提升交易，可透過 <xref:System.Transactions> 命名空間中的類別處理這些交易。 可提升交易藉由直到需要時才建立分散式交易的方式，來最佳化分散式交易。 如果只需要一個資源管理員，則不會發生分散式交易。

> [!NOTE]
> 在部分信任案例中，當某筆交易提升至分散式交易時， <xref:System.Transactions.DistributedTransactionPermission> 就是必要項目。

## <a name="promotable-transaction-scenarios"></a>可提升交易案例

分散式交易通常要耗用大量的系統資源，並由「Microsoft 分散式交易協調器 (MS DTC)」進行管理，其整合了交易中會存取的所有資源管理者。 可提升交易是 <xref:System.Transactions> 交易的特殊形式，可有效地委派工作給簡單的 SQL Server 交易。 <xref:System.Transactions>、<xref:Microsoft.Data.SqlClient> 及 SQL Server 會協調處理交易時所涉及的工作，並視需要將其提升為完全分散式交易。

使用可提升交易的好處是：當以作用中的 <xref:System.Transactions.TransactionScope> 交易開啟連接，且未開啟其他連接時，會將交易認可為輕量型交易，不會產生完全分散式交易的額外負擔。

### <a name="connection-string-keywords"></a>連接字串關鍵字

<xref:Microsoft.Data.SqlClient.SqlConnection.ConnectionString%2A> 屬性支援關鍵字 `Enlist`，它表示 <xref:Microsoft.Data.SqlClient> 是否會偵測到交易內容，並自動在分散式交易中登記連接。 如果 `Enlist=true`，則會在開啟之執行緒的目前交易內容中自動登記連接。 如果 `Enlist=false`，則 `SqlClient` 連接將不會與分散式交易進行互動。 `Enlist` 的預設值為 True。 如果未在連接字串中指定 `Enlist` ，則若在開啟連接時偵測到連接，便會自動在分散式交易中登記該連接。

在 `Transaction Binding` 連接字串中的 <xref:Microsoft.Data.SqlClient.SqlConnection> 關鍵字會控制連接與已登記 `System.Transactions` 交易之間的關聯。 這也可以透過 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder.TransactionBinding%2A> 的 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder>屬性取得。

下表說明可能出現的值。
  
|關鍵字|描述|  
|-------------|-----------------|  
|Implicit Unbind|預設值。 結束時，連接會與交易中斷連結，並切換回自動認可模式。|
|Explicit Unbind|連接會維持附加至交易的狀態，直到交易關閉為止。 如果相關聯的交易並非使用中或與 <xref:System.Transactions.Transaction.Current%2A>不符，連接將會失敗。|

## <a name="using-transactionscope"></a>使用 TransactionScope

<xref:System.Transactions.TransactionScope> 類別可藉由在分散式交易中隱含地編列連接，讓程式碼區塊可進行交易。 離開區塊之前，您必須呼叫 <xref:System.Transactions.TransactionScope.Complete%2A> 區塊結尾的 <xref:System.Transactions.TransactionScope> 方法。 離開區塊會叫用 <xref:System.Transactions.TransactionScope.Dispose%2A> 方法。 如果已擲回造成程式碼離開範圍的例外狀況，則會將交易視為中止。

建議您使用 `using` 區塊，以確保結束 using 區塊時，會在 <xref:System.Transactions.TransactionScope.Dispose%2A> 物件上呼叫 <xref:System.Transactions.TransactionScope> 。 無法認可或復原暫止的交易會嚴重損害效能，因為 default time-out for the <xref:System.Transactions.TransactionScope> 的預設逾時是 1 分鐘。 如果您不使用 `using` 陳述式，則必須在 `Try` 區塊中執行所有工作，並明確呼叫 <xref:System.Transactions.TransactionScope.Dispose%2A> 區塊中的 `Finally` 方法。

如果 <xref:System.Transactions.TransactionScope>中發生例外狀況，則會將交易標記為不一致並放棄。 處置 <xref:System.Transactions.TransactionScope> 時，則會復原該交易。 如果沒有發生任何例外狀況，則會認可參與的交易。

> [!NOTE]
> `TransactionScope` 類別預設會以 <xref:System.Transactions.Transaction.IsolationLevel%2A> 的 `Serializable` 建立交易。 根據應用程式，您可能會考慮降低隔離等級，以避免應用程式中發生劇烈的競爭現象。

> [!NOTE]
> 建議您在分散式交易內僅執行更新、插入及刪除作業，因為它們會耗用大量的資料庫資源。 SELECT 陳述式可能會不必要地鎖定資料庫資源，而在某些案例中，您可能需要使用交易而不是選取。 除非涉及其他已交易的資源管理者，否則所有非資料庫工作都應在交易範圍外執行。
雖然交易範圍內的例外狀況會防止認可交易，但 <xref:System.Transactions.TransactionScope> 類別不支援針對在交易本身範圍外所做的任何程式碼變更進行復原。 如果在復原交易時需要採取某些動作，則必須撰寫您自己的 <xref:System.Transactions.IEnlistmentNotification> 介面實作，並在交易中明確登記。

## <a name="example"></a>範例

您需要參考 System.Transactions.dll，才能使用 <xref:System.Transactions> 。

下列函式示範如何針對由兩個不同的 <xref:Microsoft.Data.SqlClient.SqlConnection> 物件表示，並包裝於 <xref:System.Transactions.TransactionScope> 區塊中的兩個不同 SQL Server 執行個體，建立可提升交易。

下列程式碼會使用 `using` 陳述式建立 <xref:System.Transactions.TransactionScope> 區塊，並開啟第一個連線，這樣會自動在 <xref:System.Transactions.TransactionScope> 中加以登錄。

一開始交易登記為輕量型交易，而不是完全分散式交易。 只有當第一個連接中的命令沒有擲回例外狀況時，第二個連接才會登記於 <xref:System.Transactions.TransactionScope> 中。 開啟第二個連接時，交易會自動提升為完全分散式交易。

稍後會叫用 <xref:System.Transactions.TransactionScope.Complete%2A> 方法，該方法只有在未擲回任何例外狀況時，才會認可交易。 如果在 <xref:System.Transactions.TransactionScope> 區塊中的任何一點擲回例外狀況，便不會呼叫 `Complete` ，且分散式交易會在 <xref:System.Transactions.TransactionScope> 區塊結束並處置 `using` 時復原。

[!code-csharp[SqlTransactionScope#1](~/../sqlclient/doc/samples/SqlTransactionScope.cs#1)]

## <a name="see-also"></a>請參閱

- [異動和並行存取](transactions-and-concurrency.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
