---
title: 連接字串建立器
description: 了解 ADO.NET 中不同提供者所使用的連接字串建立器類別，這些全都繼承自 DbConnectionStringBuilder。
ms.date: 11/13/2020
dev_langs:
- csharp
ms.assetid: 8434b608-c4d3-43d3-8ae3-6d8c6b726759
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 38c1ad34fb4819460c08f00da4c1dbd31749b7cb
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771430"
---
# <a name="connection-string-builders"></a>連接字串建立器

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

在舊版 ADO.NET 中，系統不會在編譯時間檢查含有串連字串值的連接字串，因此在執行階段，不正確的關鍵字就會產生 <xref:System.ArgumentException>。 Microsoft SqlClient Data Provider for SQL Server 包含繼承自 <xref:System.Data.Common.DbConnectionStringBuilder> 的強型別連接字串建立器類別 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder?displayProperty=nameWithType>。

## <a name="connection-string-injection-attacks"></a>連接字串插入式攻擊

當使用動態字串串連產生根據使用者輸入而來的連接字串時，就可能發生連接字串隱碼攻擊。 如果字串未經驗證且未逸出惡意的文字或字元，攻擊者就可能得以存取伺服器上的機密資料或其他資源。 例如，攻擊者可以藉由提供分號並附加額外的值而掛上 (Mount) 攻擊。 連接字串會使用「**最後一個獲勝**」演算法進行剖析，並將惡意的輸入替換為合法的值。

連接字串產生器類別的目的是排除不確定性，並可防止語法錯誤和安全性漏洞。 其提供對應至資料提供者所允許之已知機碼/值組的方法與屬性。 每個類別都會維持固定的同義資料表 (Synonym) 集合，且可從同義資料表轉譯為相對應的已知索引鍵名稱。 系統將針對有效的索引鍵/值組執行檢查，無效的索引鍵/值組將擲回例外狀況 (Exception)。 此外，插入的值也會以安全的方式處理。

下列範例示範 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder> 如何針對 `Initial Catalog` 設定而處理插入的額外值。

[!code-csharp[SqlConnectionStringBuilder_InjectionAttack#1](~/../sqlclient/doc/samples/SqlConnectionStringBuilder_InjectionAttack.cs#1)]

輸出顯示 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder> 進行正確處理的方式是，使用雙引號來逸出額外的值，而不是將其附加至連接字串以作為新的機碼/值組。

```output
data source=(local);Integrated Security=True;
initial catalog="AdventureWorks;NewValue=Bad"
```

## <a name="build-connection-strings-from-configuration-files"></a>從組態檔建置連接字串

如果事先知道連接字串的特定項目，就可以先將其儲存在組態檔中，執行階段時再進行擷取，用以建構完整的連接字串。 例如，您可能會預先知道資料庫的名稱，但不知道伺服器的名稱； 或者，也可以讓使用者在執行階段提供名稱和密碼，但不能將其他值插入連接字串。

連接字串產生器其中一個多載建構函式會採用 <xref:System.String> 做為引數，這可讓您先提供部分連接字串，然後藉由使用者輸入完成。 部分連接字串可以儲存在組態檔中，並在執行階段進行擷取。

> [!NOTE]
> <xref:System.Configuration> 命名空間可讓您以程式設計方式存取使用 <xref:System.Web.Configuration.WebConfigurationManager> (Web 應用程式) 和 <xref:System.Configuration.ConfigurationManager> (Windows 應用程式) 的組態檔。 如需使用連接字串與組態檔的詳細資訊，請參閱[連接字串與組態檔](connection-strings-and-configuration-files.md)。

### <a name="example"></a>範例

這個範例示範如何從組態檔擷取部分的連接字串，然後藉由設定 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder.DataSource%2A> 的 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder.UserID%2A>、<xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 和 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder> 屬性加以完成。 組態檔的定義如下。

```xml
<connectionStrings>
  <clear/>
  <add name="partialConnectString"
    connectionString="Initial Catalog=Northwind;"
    providerName="Microsoft.Data.SqlClient" />
</connectionStrings>
```

> [!NOTE]
> 您必須在專案中將參考設定至 `System.Configuration.dll`，程式碼才能執行。

[!code-csharp[DataWorks SqlConnectionStringBuilder.UserNamePwd#1](~/../sqlclient/doc/samples/SqlConnectionStringBuilder_UserNamePwd.cs#1)]
  
## <a name="see-also"></a>另請參閱

- [連接字串](connection-strings.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
