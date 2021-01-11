---
title: 連線事件
description: 連線事件會從資料來源擷取參考訊息，並判斷其狀態是否已變更。
ms.date: 11/13/2020
dev_langs:
- csharp
ms.assetid: 5a29de74-acfc-4134-8616-829dd7ce0710
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 8151915dc6c16c6225fec9ab90cb5a88e86b992f
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771437"
---
# <a name="connection-events"></a>連線事件

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

Microsoft SqlClient Data Provider for SQL Server 具有含兩個事件的 **Connection** 物件，可讓您從資料來源擷取參考訊息，或判斷 **Connection** 的狀態是否已變更。 下表描述 **Connection** 物件的事件。

|事件|描述|  
|-----------|-----------------|  
|**InfoMessage**|會在資訊訊息從資料來源傳回時發生。 資訊訊息是從資料來源傳回，且不會造成擲回例外狀況息。|  
|**StateChange**|於 **Connection** 狀態變更時發生。|  

## <a name="work-with-the-infomessage-event"></a>使用 InfoMessage 事件

您可以使用 <xref:Microsoft.Data.SqlClient.SqlConnection.InfoMessage> 物件的 <xref:Microsoft.Data.SqlClient.SqlConnection> 事件，從 SQL Server 資料來源擷取警告和資訊訊息。 如果資料來源傳回嚴重性層級 11 到 16 的錯誤，則可能會擲回例外狀況。 不過，您可以使用 <xref:Microsoft.Data.SqlClient.SqlConnection.InfoMessage> 事件，從與錯誤無關的資料來源取得訊息。 以 Microsoft SQL Server 為例，任何嚴重性為 10 或以下的錯誤都視為資訊訊息，而且可以使用 <xref:Microsoft.Data.SqlClient.SqlConnection.InfoMessage> 事件進行擷取。 如需詳細資訊，請參閱[資料庫引擎錯誤嚴重性](/sql/relational-databases/errors-events/database-engine-error-severities)一文。

<xref:Microsoft.Data.SqlClient.SqlConnection.InfoMessage> 事件會接收 <xref:Microsoft.Data.SqlClient.SqlInfoMessageEventArgs> 物件，該物件會在其 **Errors** 屬性中包含來自資料來源的訊息集合。 您可以查詢這個集合中的 **Error** 物件，以取得錯誤號碼、訊息文字與錯誤來源。 Microsoft SqlClient Data Provider for SQL Server 也會包含有關訊息來源之資料庫、預存程序與行號的詳細資料。

### <a name="example"></a>範例

下列程式碼範例會顯示如何加入 <xref:Microsoft.Data.SqlClient.SqlConnection.InfoMessage> 事件的事件處理常式。

[!code-csharp[SqlConnection_._InfoMessage#1](~/../sqlclient/doc/samples/SqlConnection_InfoMessage_StateChange.cs#1)]

## <a name="handle-errors-as-infomessages"></a>將錯誤視為 InfoMessage 來處理

只有在資訊訊息及警告訊息是從伺服器傳回時，才會正常地引發 <xref:Microsoft.Data.SqlClient.SqlConnection.InfoMessage> 事件。 不過，在發生實際錯誤時，會終止執行起始伺服器作業的 **ExecuteNonQuery** 或 **ExecuteReader** 方法，並擲回例外狀況。

若要繼續處理命令中的其餘陳述式，而不理會伺服器產生的任何錯誤，請將 <xref:Microsoft.Data.SqlClient.SqlConnection.FireInfoMessageEventOnUserErrors%2A> 的 <xref:Microsoft.Data.SqlClient.SqlConnection> 屬性設定為 `true`。 這麼做會使得連接引發錯誤的 <xref:Microsoft.Data.SqlClient.SqlConnection.InfoMessage> 事件，而非擲回例外狀況並中斷處理。 用戶端應用程式接下來處理這個事件並回應錯誤狀況。

> [!NOTE]
> 必須將嚴重性層級 17 或以上的錯誤 (造成伺服器停止處理命令) 當做例外狀況處理。 在此情況下會擲回例外狀況，而不理會<xref:Microsoft.Data.SqlClient.SqlConnection.InfoMessage> 事件中如何處理錯誤。

## <a name="work-with-the-statechange-event"></a>使用 StateChange 事件

當 **Connection** 狀態變更時，會發生 **StateChange** 事件。 **StateChange** 事件會接收 <xref:System.Data.StateChangeEventArgs>，讓您能夠使用 **OriginalState** 與 **CurrentState** 屬性，來判斷 **Connection** 狀態中的變更。 **OriginalState** 屬性是 <xref:System.Data.ConnectionState> 列舉，表示 **Connection** 變更前的狀態。 **CurrentState** 是 <xref:System.Data.ConnectionState> 列舉，表示 **Connection** 變更後的狀態。

下列程式碼範例會在 **Connection** 狀態變更時，使用 **StateChange** 事件，將訊息寫入到主控台。

[!code-csharp[SqlConnection_._StateChange#2](~/../sqlclient/doc/samples/SqlConnection_InfoMessage_StateChange.cs#2)]

## <a name="see-also"></a>另請參閱

- [連線到資料來源](connecting-to-data-source.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
