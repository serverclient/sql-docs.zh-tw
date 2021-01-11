---
title: SqlClient 中的資料追蹤
description: 說明 Microsoft SqlClient Data Provider for SQL Server 如何提供內建的資料追蹤功能。
ms.date: 12/04/2020
ms.assetid: a6a752a5-d2a9-4335-a382-b58690ccb79f
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: fc8b5a7ca06af3c3e3ea83fcb747f79517e5ef7a
ms.sourcegitcommit: 4419e99d77ee2c73f9da1559c7944f7702f2de30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744384"
---
# <a name="data-tracing-in-sqlclient"></a>SqlClient 中的資料追蹤

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

.NET 具備 Microsoft SqlClient Data Provider for SQL Server 與 SQL Server 網路通訊協定支援的內建資料追蹤功能。

追蹤資料存取 API 呼叫可協助診斷下列問題：

- 用戶端程式與資料庫之間的結構描述不符。

- 無法使用資料庫或網路程式庫發生問題。

- SQL 錯誤，該 SQL 是硬式編碼或應用程式所產生的。

- 錯誤的程式設計邏輯。

- Microsoft SqlClient Data Provider for SQL Server 與您自己的元件之間的互動所產生的問題。

為了支援不同的追蹤技術，我們製作了可擴充的追蹤，讓開發人員追蹤任何應用程式堆疊層級的問題。 Microsoft SqlClient Data Provider for SQL Server 會利用一般化的追蹤和檢測 API。

如需在 .NET 中設定受控追蹤的詳細資訊，請參閱[追蹤資料存取](/previous-versions/sql/sql-server-2012/hh880086(v=msdn.10)) \(英文\)。

## <a name="access-diagnostic-information-in-the-extended-events-log"></a>存取擴充事件記錄檔中的診斷資訊

在 Microsoft SqlClient Data Provider for SQL Server 中，[資料存取追蹤](/previous-versions/sql/sql-server-2012/hh880086(v=msdn.10))讓您更容易相互關聯用戶端事件與擴充事件記錄中伺服器連接通道緩衝區與應用程式效能資訊的診斷資訊，例如連接失敗。 如需讀取擴充事件記錄檔的資訊，請參閱[檢視事件工作階段資料](/previous-versions/sql/sql-server-2012/hh710068(v=sql.110))。

針對連線作業，Microsoft SqlClient Data Provider for SQL Server 將會傳送用戶端連線識別碼。 如果連線失敗，您可以存取連線通道緩衝區 ([SQL Server 2008 連線通道緩衝區中的連線疑難排解](/archive/blogs/sql_protocols/connectivity-troubleshooting-in-sql-server-2008-with-the-connectivity-ring-buffer))、尋找 `ClientConnectionID` 欄位，並且取得有關連接失敗的診斷資訊。 僅在發生錯誤時，才會在信號緩衝區中記錄用戶端連接識別碼。 如果在傳送登入前封包之前發生連線失敗，則不會產生用戶端連線識別碼。 用戶端連接識別碼是 16 位元組的 GUID。 如果在擴充事件工作階段中將 `client_connection_id` 動作加入到事件中，您還可以在擴充事件目標輸出中尋找用戶端連接 ID。 如需進一步的用戶端驅動程式診斷協助，您可以啟用資料存取追蹤並傳回連接命令，同時觀察資料存取追蹤當中的 `ClientConnectionID` 欄位。

您可以使用 `SqlConnection.ClientConnectionID` 屬性，以程式設計方式取得用戶端連接 ID。

> [!NOTE]
> Microsoft SqlClient Data Provider for SQL Server 支援自版本 2.1.0 起的伺服器處理序識別碼。 您可以透過使用 `SqlConnection.ServerProcessId` 屬性，來以程式設計方式加以取得。

`ClientConnectionID` 與 `ServerProcessId` 可供成功建立連線的 <xref:Microsoft.Data.SqlClient.SqlConnection> 物件使用。 如果連接嘗試失敗，可能可以透過 `ClientConnectionID` 取得 `SqlException.ToString`。

Microsoft SqlClient Data Provider for SQL Server 也會傳送執行緒特定活動識別碼。 如果已啟動工作階段並啟用 TRACK_CAUSALITY 選項，即可在擴充的事件工作階段中擷取活動識別碼。 如需與使用中連接相關的效能問題，您可以從用戶端的資料存取追蹤 (`ActivityID` 欄位) 取得活動識別碼，然後在擴充的事件輸出中尋找此活動識別碼。 擴充事件中的活動識別碼是附加 4 位元組序號的 16 位元組 GUID (與用戶端連線識別碼的 GUID 不同)。 此序號表示執行緒內要求的順序，並指出執行緒的批次和 RPC 陳述式的相對排序。 啟用資料存取追蹤功能，而且開啟資料存取追蹤組態字詞中的第 18 個位元時，目前會選擇性地傳送 SQL 批次陳述式和 RPC 要求的 `ActivityID`。

下列 SQL 陳述式是使用 Transact-SQL 啟動擴充事件工作階段的範例，該工作階段會儲存在通道緩衝區中，而且會記錄從 RPC 與批次作業上之用戶端傳送的活動識別碼。

```sql
create event session MySession on server
add event connectivity_ring_buffer_recorded,
add event sql_statement_starting (action (client_connection_id)),
add event sql_statement_completed (action (client_connection_id)),
add event rpc_starting (action (client_connection_id)),
add event rpc_completed (action (client_connection_id))
add target ring_buffer with (track_causality=on)
```

## <a name="see-also"></a>請參閱
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
