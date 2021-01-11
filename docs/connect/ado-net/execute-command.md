---
title: 執行命令
description: 描述 Microsoft SqlClient Data Provider for SQL Server `Command` 物件，以及如何加以使用來針對資料來源執行查詢與命令。
ms.date: 11/25/2020
dev_langs:
- csharp
ms.assetid: 40494916-c25a-4cb8-8f7c-fcb8d378464e
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 772ecef470b69a34e4bd4b0eba2ca60255657eaf
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771420"
---
# <a name="executing-a-command"></a>執行命令

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

Microsoft SqlClient Data Provider for SQL Server 具有繼承自 <xref:System.Data.Common.DbCommand> 的 <xref:Microsoft.Data.SqlClient.SqlCommand> 物件。 此物件會公開根據命令類型與所需傳回值來執行命令的方法，如下表所述。

|命令|傳回值|  
|-------------|------------------|  
|`ExecuteReader`|傳回 `DataReader` 物件。|  
|`ExecuteScalar`|傳回單一純量值。|  
|`ExecuteNonQuery`|執行不會傳回任何資料列的命令。|  
|`ExecuteXMLReader`|傳回 <xref:System.Xml.XmlReader>。 僅適用於 `SqlCommand` 物件。|

每個強型別 (Strongly Typed) 的命令物件也會支援 <xref:System.Data.CommandType> 列舉型別 (Enumeration)，此型別可指定解譯命令字串的方式。

|CommandType|說明|
|-----------------|-----------------|  
|`Text`|SQL 命令，可定義在資料來源執行的陳述式。|  
|`StoredProcedure`|預存程序的名稱。 您可以使用命令的 `Parameters` 屬性來存取輸入和輸出參數及傳回值，不論呼叫的是哪一個 `Execute` 方法。|  
|`TableDirect`|資料表的名稱。|

> [!IMPORTANT]
> 在使用 `ExecuteReader` 時，無法在 `DataReader` 關閉之前存取傳回值和輸出參數。

## <a name="example"></a>範例

下列程式碼範例示範如何建立 <xref:Microsoft.Data.SqlClient.SqlCommand> 物件，以藉由設定其屬性來執行預存程序。 用來指定預存程序輸出參數的 <xref:Microsoft.Data.SqlClient.SqlParameter> 物件。 此命令是藉由使用 <xref:Microsoft.Data.SqlClient.SqlCommand.ExecuteReader%2A> 方法來執行，而 <xref:Microsoft.Data.SqlClient.SqlDataReader> 的輸出則會顯示在主控台視窗中。

[!code-csharp[DataWorks SqlClient.StoredProcedure#1](~/../sqlclient/doc/samples/SqlCommand_StoredProcedure.cs#1)]

### <a name="troubleshooting-commands"></a>針對命令進行疑難排解

[!INCLUDE[appliesto-netfx-xxxx-xxxx-md](../../includes/appliesto-netfx-xxxx-xxxx-md.md)]

Microsoft SqlClient Data Provider for SQL Server 會新增 **效能計數器** 來讓您偵測與命令執行失敗相關的間歇性問題。 如需詳細資訊，請參閱 [SqlClient 中的效能計數器](performance-counters.md)。

## <a name="see-also"></a>請參閱

- [命令與參數](commands-parameters.md)
- [DataAdapter 和 DataReader](dataadapters-datareaders.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
