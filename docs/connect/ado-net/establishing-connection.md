---
title: 建立連線
description: SqlClient 提供者連線到 SQL Server 的指導方針。
ms.date: 11/13/2020
dev_langs:
- csharp
ms.assetid: 3af512f3-87d9-4005-9e2f-abb1060ff43f
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: b3d92f49fb4eae6184a673ffc25ff6bbdf521626
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771590"
---
# <a name="establishing-connection"></a>建立連線

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

若要連線到 Microsoft SQL Server，請使用 Microsoft SqlClient Data Provider for SQL Server 的 <xref:Microsoft.Data.SqlClient.SqlConnection> 物件。 若要安全地儲存及擷取連接字串，請參閱[保護連線資訊](protecting-connection-information.md)。

## <a name="closing-connections"></a>關閉連線

建議您在使用完連接後一律關閉該連接，以便將連接傳回集區。 即使有未處理的例外狀況，Visual Basic 或 C# 中的 `Using` 區塊也會在程式碼結束該區塊時自動處理連接。 如需詳細資訊，請參閱 [using 陳述式](/dotnet/csharp/language-reference/keywords/using-statement)與 [Using 陳述式](/dotnet/visual-basic/language-reference/statements/using-statement)。

您也可以使用 Connection 物件的 `Close` 或 `Dispose` 方法。 可能不會將未明確關閉的連接加入或傳回集區。 例如，已離開範圍但尚未明確關閉的連接僅會在已達到最大集區大小，且連接仍然有效時，才會回到連接集區。

> [!NOTE]
> 請不要在類別之 `Finalize` 方法中的 **Connection**、**DataReader** 或任何其他受控物件上呼叫 `Close` 或 `Dispose`。 在完成項中，只需釋放類別直接擁有的 Unmanaged 資源。 如果類別未擁有任何 Unmanaged 資源，請不要在類別定義中包含 `Finalize` 方法。 如需詳細資訊，請參閱[記憶體回收](/dotnet/standard/garbage-collection/index)。

> [!NOTE]
> 從連接集區中擷取連接或將連接傳回連接集區時，系統不會在伺服器上引發登入和登出事件，因為當連接傳回連接集區時，連接實際上並未關閉。 如需詳細資訊，請參閱 [SQL Server 連線共用 (ADO.NET)](sql-server-connection-pooling.md) \(機器翻譯\)。

## <a name="connecting-to-sql-server"></a>連線到 SQL Server

如需有效的字串格式名稱及值，請參閱 <xref:Microsoft.Data.SqlClient.SqlConnection.ConnectionString%2A> 物件的 <xref:Microsoft.Data.SqlClient.SqlConnection> 屬性。 您也可以使用 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder> 類別在執行階段建立語法有效的連接字串。 如需詳細資訊，請參閱[連接字串建置器](connection-string-builders.md)。

下列程式碼範例示範如何建立及開啟與 SQL Server 資料庫的連接。

[!code-csharp[SqlConnection.Open#1](~/../sqlclient/doc/samples/SqlConnection_Open.cs#1)]

### <a name="integrated-security-and-aspnet"></a>整合式安全性與 ASP.NET

連線到 SQL Server 時，SQL Server 整合式安全性 (也稱為信任連接) 有助於提供保護，因為其不會在連接字串中公開使用者識別碼與密碼，而且是建議用於驗證連線的方法。 整合安全性會使用執行中處理序的目前安全性識別或語彙基元。 針對傳統型應用程式，這個身分識別通常是目前登入之使用者的身分識別。

ASP.NET 應用程式的安全性識別可設為數個不同的選項之一。 若要更加了解 ASP.NET 應用程式在連線到 SQL Server 時所使用的安全性身分識別，請參閱 [ASP.NET 模擬](/previous-versions/aspnet/xh507fc5(v=vs.100))、[ASP.NET 驗證](/previous-versions/aspnet/eeyk640h(v=vs.100))與[操作說明：使用 Windows 整合式安全性存取 SQL Server](/previous-versions/aspnet/bsz5788z(v=vs.100))。

## <a name="see-also"></a>另請參閱

- [連線到資料來源](connecting-to-data-source.md)
- [連接字串](connection-strings.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
