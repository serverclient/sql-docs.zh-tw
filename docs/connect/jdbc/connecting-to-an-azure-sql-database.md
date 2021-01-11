---
title: 連接到 Azure SQL Database
description: 此文章討論使用 Microsoft JDBC Driver for SQL Server 連線到 Azure SQL Database 時發生的問題。
ms.custom: ''
ms.date: 12/18/2020
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 49645b1f-39b1-4757-bda1-c51ebc375c34
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 03768a309ac10fc16fd1a743660df6fe74b088e7
ms.sourcegitcommit: bc8474fa200ef0de7498dbb103bc76e3e3a4def4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2020
ms.locfileid: "97709667"
---
# <a name="connecting-to-an-azure-sql-database"></a>連接到 Azure SQL Database

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

此文章討論使用 [!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] 連線到 [!INCLUDE[ssAzure](../../includes/ssazure_md.md)] 時發生的問題。 如需連線到 [!INCLUDE[ssAzure](../../includes/ssazure_md.md)] 的詳細資訊，請參閱：  
  
- [Azure SQL Database](/azure/sql-database/sql-database-technical-overview)  
  
- [操作說明：使用 JDBC 連線到 Azure SQL](/azure/sql-database/sql-database-connect-query-java)  

- [使用 Azure Active Directory 驗證連接](connecting-using-azure-active-directory-authentication.md)  
  
## <a name="details"></a>詳細資料

連線到 [!INCLUDE[ssAzure](../../includes/ssazure_md.md)] 時，您應該連線到 master 資料庫來呼叫 **SQLServerDatabaseMetaData.getCatalogs**。  
[!INCLUDE[ssAzure](../../includes/ssazure_md.md)] 不支援從使用者資料庫傳回整組目錄。 **SQLServerDatabaseMetaData.getCatalogs** 會使用 sys.databases 檢視來取得目錄。 請參閱 [sys.databases (Transact-SQL)](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 中對於權限的討論，以了解 [!INCLUDE[ssAzure](../../includes/ssazure_md.md)] 上的 **SQLServerDatabaseMetaData.getCatalogs** 行為。  
  
## <a name="connections-dropped"></a>連線中斷

連線到 [!INCLUDE[ssAzure](../../includes/ssazure_md.md)] 時，網路元件 (例如防火牆) 可能會在一段時間沒有活動之後終止閒置連線。 在此內容中，有兩種閒置連接類型：  

- TCP 層閒置，其中任何數目的網路裝置都可能會卸除連接。  

- 於 Azure SQL 閘道閒置，其中可能會發生 TCP **keepalive** 訊息 (使連線從 TCP 觀點而言未閒置)，但在 30 分鐘內沒有使用中查詢。 在此狀況下，閘道會在 30 分鐘時判定 TDS 連接閒置並結束連接。  
  
若要解決第二點並避免閘道終止閒置連線，您可以：

* 在設定 Azure SQL 資料來源時，使用 [重新導向] [連線原則](/azure/azure-sql/database/connectivity-architecture#connection-policy)。

* 透過輕量活動讓連線保持作用中狀態。 不建議使用這個方法，如果要使用，應該只在沒有其他可能的選擇時才使用。

若要解決第一點並避免網路元件中斷閒置連線，您應該在載入該驅動程式的作業系統上設定下列登錄設定 (或其非 Windows 對等項目)：  
  
|登錄設定|建議值|  
|----------------------|-----------------------|  
|HKEY_LOCAL_MACHINE \ SYSTEM \ CurrentControlSet \ Services \ Tcpip \ Parameters \ KeepAliveTime|30000|  
|HKEY_LOCAL_MACHINE \ SYSTEM \ CurrentControlSet \ Services \ Tcpip \ Parameters \ KeepAliveInterval|1000|  
|HKEY_LOCAL_MACHINE \ SYSTEM \ CurrentControlSet \ Services \ Tcpip \ Parameters \ TcpMaxDataRetransmissions|10|  
  
重新啟動電腦，讓這些登錄設定生效。  

KeepAliveTime 與 KeepAliveInterval 值是以毫秒為單位。 這些設定將會在 10 到 40 秒內中斷無回應的連線。 傳送「保持運作」封包之後，如果沒有收到任何回應，則會每秒重試一次，最多 10 次。 如果在這段時間內沒有收到任何回應，就會中斷用戶端通訊端的連線。 視環境而定，建議您增加 KeepAliveInterval 以配合可能會導致伺服器超過 10 無法回應的已知中斷作業 (例如虛擬機器移轉)。

> [!NOTE]
> 在 Windows Vista 或 Windows 2008 與更新版本上，無法控制 TcpMaxDataRetransmissions。

若要在於 Azure 中執行時執行此設定，請建立啟動工作以加入登錄機碼。  例如，您可以將下列啟動工作加入至服務定義檔：  


```xml
<Startup>  
    <Task commandLine="AddKeepAlive.cmd" executionContext="elevated" taskType="simple">  
    </Task>  
</Startup>  
```

然後，將 AddKeepAlive.cmd 檔案加入至您的專案。 將 [複製到輸出目錄] 設定設為 [永遠複製]。 下列指令碼是範例 AddKeepAlive.cmd 檔案：  

```bat
if exist keepalive.txt goto done  
time /t > keepalive.txt  
REM Workaround for JDBC keep alive on Azure SQL  
REG ADD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /v KeepAliveTime /t REG_DWORD /d 30000 >> keepalive.txt  
REG ADD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /v KeepAliveInterval /t REG_DWORD /d 1000 >> keepalive.txt  
REG ADD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /v TcpMaxDataRetransmissions /t REG_DWORD /d 10 >> keepalive.txt  
shutdown /r /t 1  
:done  
```

## <a name="appending-the-server-name-to-the-userid-in-the-connection-string"></a>將伺服器名稱附加至連接字串中的 userId  

在 [!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] 4.0 版之前，連線到 [!INCLUDE[ssAzure](../../includes/ssazure_md.md)] 時，您必須將伺服器名稱附加至連接字串中的 UserId。 例如： user@servername 。 從 [!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] 4.0 版開始，您不再需要將 @servername 附加至連接字串中的 UserId。  

## <a name="using-encryption-requires-setting-hostnameincertificate"></a>使用加密需要設定 hostNameInCertificate

在 [!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] 7.2 版之前，連線到 [!INCLUDE[ssAzure](../../includes/ssazure_md.md)] 時，如果您指定了 **encrypt=true** (如果連接字串中的伺服器名稱為 *shortName*.*domainName*，請將 **hostNameInCertificate** 屬性設定為 \*.*domainName*)，則應該指定 **hostNameInCertificate**。 從驅動程式 7.2 版開始，這個屬性是選擇性的。

例如：

```java
jdbc:sqlserver://abcd.int.mscds.com;databaseName=myDatabase;user=myName;password=myPassword;encrypt=true;hostNameInCertificate=*.int.mscds.com;
```

## <a name="see-also"></a>另請參閱

[使用 JDBC 驅動程式連線到 SQL Server](connecting-to-sql-server-with-the-jdbc-driver.md)
