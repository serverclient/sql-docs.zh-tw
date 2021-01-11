---
title: 取得 SqlClientFactory
description: 了解如何從 DbProviderFactories 類別取得 SqlClientFactory，以便在 .NET 中搭配特定資料來源使用。
ms.date: 12/22/2020
ms.assetid: a16e4a4d-6a5b-45db-8635-19570e4572ae
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 891cabf04bc8e63537983a91d8526a5cbdf238bf
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771754"
---
# <a name="obtain-a-sqlclientfactory"></a>取得 SqlClientFactory

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

取得 <xref:System.Data.Common.DbProviderFactory> 的程序包含傳遞資料提供者的相關資訊給 <xref:System.Data.Common.DbProviderFactories> 類別。 根據這項資訊，<xref:System.Data.Common.DbProviderFactories.GetFactory%2A> 方法會建立強型別 (Strongly Typed) 提供者 Factory。 例如，若要建立 <xref:Microsoft.Data.SqlClient.SqlClientFactory>，您可以將具有指定為 "**Microsoft.Data.SqlClient**" 之提供者名稱的字串傳遞給 `GetFactory`。

`GetFactory` 的其他多載會接受 <xref:System.Data.DataRow>。 一旦您建立了提供者 Factory，之後就可以使用其方法來建立其他物件。 `SqlClientFactory` 的某些方法包括 <xref:Microsoft.Data.SqlClient.SqlClientFactory.CreateConnection%2A>、<xref:Microsoft.Data.SqlClient.SqlClientFactory.CreateCommand%2A> 和 <xref:Microsoft.Data.SqlClient.SqlClientFactory.CreateDataAdapter%2A>。

## <a name="register-sqlclientfactory"></a>註冊 SqlClientFactory

若要透過 .NET Framework 中的 <xref:System.Data.Common.DbProviderFactories> 類別來擷取 <xref:Microsoft.Data.SqlClient.SqlClientFactory> 物件，必須在 **App.config** 或 **web.config** 檔案中註冊該物件。 下列組態檔片段會顯示 <xref:Microsoft.Data.SqlClient> 的語法和格式。  

```xml  
<system.data>
  <DbProviderFactories>
    <add name="Microsoft SqlClient Data Provider"
      invariant="Microsoft.Data.SqlClient"
      description="Microsoft SqlClient Data Provider for SQL Server"
      type="Microsoft.Data.SqlClient.SqlClientFactory, Microsoft.Data.SqlClient, Version=2.0.20168.4, Culture=neutral, PublicKeyToken=23ec7fc2d6eaa4a5"/>
  </DbProviderFactories>
</system.data>  
```  

**invariant** 屬性可識別底層資料提供者。 這個三部分命名語法也會用於建立新的 Factory 以及在應用程式組態檔中識別提供者，如此您就可以在執行階段擷取提供者名稱及其相關聯的連接字串。  

> [!NOTE]  
> 在 .NET Core 中，由於沒有 GAC 或全域設定支援，因此應該透過在專案中呼叫 <xref:System.Data.Common.DbProviderFactories.RegisterFactory%2A> 方法來註冊 <xref:Microsoft.Data.SqlClient.SqlClientFactory> 物件。

下列範例示範如何在 .NET Core 應用程式中使用 <xref:Microsoft.Data.SqlClient.SqlClientFactory>。

[!code-csharp[SqlClientFactory_Netcoreapp#1](~/../sqlclient/doc/samples/SqlClientFactory_Netcoreapp.cs#1)]

## <a name="see-also"></a>請參閱

- [DbProviderFactory](dbproviderfactories.md)
- [連接字串](connection-strings.md)
- [使用設定類別](/previous-versions/aspnet/ms228063(v=vs.100))
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
