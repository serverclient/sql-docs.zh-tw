---
title: 連接字串語法
description: 了解 Microsoft SqlClient Data Provider for SQL Server 中的連接字串語法。 適用於每個提供者的語法均記載於其 ConnectionString 屬性中。
ms.date: 11/13/2020
ms.assetid: 0977aeee-04d1-4cce-bbed-750c77fce06e
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 42b1f8704be721c0b52b3c42946c9abbf7efd7db
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771621"
---
# <a name="connection-string-syntax"></a>連接字串語法

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

<xref:Microsoft.Data.SqlClient> 具有繼承自 <xref:System.Data.Common.DbConnection> 的 `Connection` 物件，以及提供者特定的 <xref:System.Data.Common.DbConnection.ConnectionString%2A> 屬性。 適用於 SqlClient 提供者的特定連接字串語法記載於其 `ConnectionString` 屬性中。 如需連接字串語法的詳細資訊，請參閱 <xref:Microsoft.Data.SqlClient.SqlConnection.ConnectionString%2A>。

## <a name="connection-string-builders"></a>連接字串建立器

 Microsoft SqlClient Data Provider for SQL Server 引進了下列連接字串建立器。

- <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder>

連接字串產生器可讓您在執行階段建構語法有效的連接字串，所以您不需要在程式碼中手動串連連接字串值。 如需詳細資訊，請參閱[連接字串建置器](connection-string-builders.md)。

## <a name="windows-authentication"></a>Windows 驗證

建議您使用 Windows 驗證 (有時也稱為「整合式安全性」) 來連線到支援此驗證方法的資料來源。 下表顯示與 Microsoft SqlClient Data Provider for SQL Server 搭配使用的 Windows 驗證語法。

|提供者|Syntax|  
|--------------|------------|  
|`SqlClient`|`Integrated Security=true;`<br /><br /> `-- or --`<br /><br /> `Integrated Security=SSPI;`|  

## <a name="sqlclient-connection-strings"></a>SqlClient 連接字串

<xref:Microsoft.Data.SqlClient.SqlConnection> 連接字串的語法列於 <xref:Microsoft.Data.SqlClient.SqlConnection.ConnectionString%2A?displayProperty=nameWithType> 屬性中。 您可以使用 <xref:Microsoft.Data.SqlClient.SqlConnection.ConnectionString%2A> 屬性來取得或設定 SQL Server 資料庫的連接字串。 連接字串關鍵字也會對應至 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder> 中的屬性。

> [!IMPORTANT]
> `Persist Security Info` 關鍵字的預設設定為 `false`。 如果將其設為 `true` 或 `yes`，則在開啟連接後，可透過連接獲得機密資訊，包含使用者 ID 和密碼。 將 `Persist Security Info` 保持設定為 `false`，可確保未受信任的來源不能存取敏感性連接字串資訊。

### <a name="windows-authentication-with-sqlclient"></a>使用 SqlClient 進行 Windows 驗證

下列每種形式的語法都會使用 Windows 驗證，來連線到本機伺服器上的 **AdventureWorks** 資料庫。

```csharp  
"Persist Security Info=False;Integrated Security=true;  
    Initial Catalog=AdventureWorks;Server=MSSQL1"  
"Persist Security Info=False;Integrated Security=SSPI;  
    database=AdventureWorks;server=(local)"  
"Persist Security Info=False;Trusted_Connection=True;  
    database=AdventureWorks;server=(local)"  
```  

### <a name="sql-server-authentication-with-sqlclient"></a>使用 SqlClient 進行 SQL Server 驗證

連接至 SQL Server 時慣用「Windows 驗證」。 不過，如果需要「SQL Server 驗證」，請使用下列語法來指定使用者名稱和密碼。 在這個範例中使用了星號來表示有效的使用者名稱和密碼。

```csharp  
"Persist Security Info=False;User ID=*****;Password=*****;Initial Catalog=AdventureWorks;Server=MySqlServer"  
```  

當您連線到 Azure SQL Database 或 Azure Synapse Analytics，並以 `user@servername` 格式提供登入時，請確定登入中的 `servername` 值符合為 `Server=` 提供的值。

> [!NOTE]
> Windows 驗證的優先順序高於 SQL Server 登入。 如果您同時指定 Integrated Security=true 以及使用者名稱和密碼，系統就會忽略使用者名稱和密碼，而使用 Windows 驗證。

### <a name="connect-to-a-named-instance-of-sql-server"></a>連線到 SQL Server 的具名執行個體

若要連線到 SQL Server 的具名執行個體，請使用「伺服器名稱\執行個體名稱」語法。

```csharp  
"Data Source=MySqlServer\MSSQL1;"  
```  

您也可以在建立連接字串時，將 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder.DataSource%2A> 的 `SqlConnectionStringBuilder` 屬性設定為執行個體名稱。 <xref:Microsoft.Data.SqlClient.SqlConnection.DataSource%2A> 物件的 <xref:Microsoft.Data.SqlClient.SqlConnection> 屬性是唯讀的。

### <a name="type-system-version-changes"></a>型別系統版本變更

<xref:Microsoft.Data.SqlClient.SqlConnection.ConnectionString%2A?displayProperty=nameWithType> 中的 `Type System Version` 關鍵字會指定 SQL Server 類型的用戶端表示法。 如需 <xref:Microsoft.Data.SqlClient.SqlConnection.ConnectionString%2A?displayProperty=nameWithType> 關鍵字的詳細資訊，請參閱 `Type System Version`。

## <a name="connect-and-attach-to-sql-server-express-user-instances"></a>連線並附加至 SQL Server Express 使用者執行個體

使用者執行個體是 SQL Server Express 中的功能。 透過使用者執行個體，在最低權限的本機 Windows 帳戶上執行的使用者不需要系統管理員權限，即可附加及執行 SQL Server 資料庫。 使用者執行個體會使用使用者的 Windows 認證執行，而不是以服務方式執行。

如需利用使用者執行個體的詳細資訊，請參閱 [SQL Server Express 使用者執行個體](./sql/sql-server-express-user-instances.md)。

## <a name="use-trustservercertificate"></a>使用 TrustServerCertificate

`TrustServerCertificate` 關鍵字只有在使用有效憑證連線到 SQL Server 執行個體時才有效。 將 `TrustServerCertificate` 設為 `true` 時，傳輸層將使用 TLS/SSL 來加密通道，並略過憑證鏈結來驗證信任。

```csharp  
"TrustServerCertificate=true;"
```  

> [!NOTE]
> 如果 `TrustServerCertificate` 是設定為 `true` 且加密功能已開啟，則即使 `Encrypt` 在連接字串中是設定為 `false`，仍將使用伺服器上指定的加密等級， 否則連接將會失敗。

### <a name="enable-encryption"></a>啟用加密

若要在伺服器上尚未佈建憑證時啟用加密，則必須在 SQL Server 組態管理員中設定 [強制通訊協定加密] 與 [信任伺服器憑證] 選項。 在此情況下，如果伺服器上未提供任何可驗證的憑證，加密將會使用自行簽署的伺服器憑證，而不需驗證。

應用程式設定無法降低 SQL Server 中設定的安全性層級，但可以選擇性地進行加強。 應用程式可要求加密的方式是，將 `TrustServerCertificate` 與 `Encrypt` 關鍵字設為 `true`，並保證即使在尚未佈建伺服器憑證，而且也未針對用戶端設定 [強制通訊協定加密] 的情況下，仍會進行加密。 不過，如果用戶端組態中未啟用 `TrustServerCertificate`，則仍需要提供伺服器憑證。

下表說明所有案例。

|強制通訊協定加密用戶端設定|信任伺服器憑證用戶端設定|資料連接字串/屬性的加密/使用加密|信任伺服器憑證連接字串/屬性|結果|  
|----------------------------------------------|---------------------------------------------|-------------------------------------------------------------------|-----------------------------------------------------------|------------|  
|否|N/A|無 (預設值)|忽略|不發生任何加密。|  
|否|N/A|是|無 (預設值)|只有當有可驗證的伺服器憑證時才會發生加密，否則連接嘗試會失敗。|  
|否|N/A|是|是|加密一定會發生，但是可能會使用自行簽署的伺服器憑證。|  
|是|否|忽略|忽略|只有當有可驗證的伺服器憑證時才會發生加密，否則連線嘗試會失敗。|  
|是|是|無 (預設值)|忽略|加密一定會發生，但是可能會使用自行簽署的伺服器憑證。|  
|是|是|是|無 (預設值)|只有當有可驗證的伺服器憑證時才會發生加密，否則連線嘗試會失敗。|  
|是|是|是|是|加密一定會發生，但是可能會使用自行簽署的伺服器憑證。|  

如需詳細資訊，請參閱[使用加密而不需驗證](/sql/relational-databases/native-client/features/using-encryption-without-validation)。

## <a name="see-also"></a>另請參閱

- [連接字串](connection-strings.md)
- [連線到資料來源](connecting-to-data-source.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
