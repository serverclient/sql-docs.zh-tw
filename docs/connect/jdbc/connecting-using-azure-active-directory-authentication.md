---
title: 使用 Azure Active Directory 驗證連線
description: 了解如何開發 Java 應用程式，以搭配 Microsoft JDBC Driver for SQL Server 使用 Azure Active Directory 驗證功能。
ms.custom: ''
ms.date: 01/04/2020
ms.reviewer: ''
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 9c9d97be-de1d-412f-901d-5d9860c3df8c
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 68d8b2a131fa6ab1c9e287f70cb584db3aeedacc
ms.sourcegitcommit: 6154ee7f20bccce9d458ac7f3b0a21b9613d1131
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97902636"
---
# <a name="connecting-using-azure-active-directory-authentication"></a>使用 Azure Active Directory 驗證連線

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

此文章提供如何開發 Java 應用程式，以搭配 Microsoft JDBC Driver for SQL Server 使用 Azure Active Directory 驗證功能的相關資訊。

您可以使用 Azure Active Directory (Azure AD) 驗證，這是使用 Azure Active Directory 中身分識別連線至 Azure SQL Database v12 的機制。 使用 Azure Active Directory 驗證集中管理資料庫使用者的身分識別，並作為 SQL Server 的替代驗證。 JDBC 驅動程式可讓您在連線到 Azure SQL Database 的 JDBC 連接字串中指定 Azure Active Directory 認證。 如需如何設定 Azure Active Directory 驗證的資訊，請瀏覽[使用 Azure Active Directory 驗證連線到 SQL Database](/azure/azure-sql/database/authentication-aad-overview) \(部分機器翻譯\)。 

在 Microsoft JDBC Driver for SQL Server 中，對 Azure Active Directory 驗證提供支援的連接屬性為：
*   **authentication**：使用此屬性來指出要用於連線的 SQL 驗證方法。 可能的值包括： 
    * **ActiveDirectoryMSI**
        * 從驅動程式 **v7.2** 版開始支援，可以從已啟用「身分識別」支援的 Azure 資源內，使用 `authentication=ActiveDirectoryMSI` 連線到 Azure SQL Database/資料倉儲。 此外，使用此驗證模式時，您也可以在 Connection/DataSource 屬性中指定 **msiClientId**，其必須包含用來取得建立連線之 **accessToken** 的受控識別用戶端識別碼。
    * **ActiveDirectoryIntegrated**
        * 從驅動程式 **v6.0** 版開始支援，可使用 `authentication=ActiveDirectoryIntegrated` 來連線到使用整合式驗證的 Azure SQL Database/資料倉儲。 若要使用此驗證模式，您必須將內部部署 Active Directory 同盟服務 (ADFS) 與雲端中的 Azure Active Directory 建立同盟。 設定後，您可以將原生程式庫 'mssql-jdbc_auth-\<version>-\<arch>.dll' 新增至 Windows 作業系統上的應用程式類別路徑，或為跨平台驗證支援設定 Kerberos 票證，藉以連線。 登入已加入網域的電腦之後，您將能夠在系統不提示輸入認證的情況下存取 Azure SQL Database/Azure Synapse Analytics。
    * **ActiveDirectoryPassword**
        * 從驅動程式 **v6.0** 版開始支援，可使用 `authentication=ActiveDirectoryPassword` 來連線到使用 Azure AD 使用者名稱和密碼的 Azure SQL Database/資料倉儲。
    * **SqlPassword**
        * 使用 `authentication=SqlPassword` 來連線至使用 userName/user 和 password 屬性的 SQL Server。
    * **NotSpecified**
        * 當不需要這些驗證方法時，請使用 `authentication=NotSpecified` 或將其保留為預設值。

*   **accessToken**：使用此連接屬性來使用存取權杖連線到 SQL Database。 accessToken 只能使用 DriverManager 類別中 getConnection() 方法的 Properties 參數來設定。 不能用於連線 URL 中。  

如需詳細資訊，請參閱[設定連接屬性](setting-the-connection-properties.md)頁面的驗證屬性。  


## <a name="client-setup-requirements"></a>用戶端安裝需求
針對 **ActiveDirectoryMSI** 驗證，必須在用戶端機器上安裝下列元件：
* Java 8 或更新版本
* Microsoft JDBC Driver 7.2 (或更新版本) for SQL Server
* 用戶端環境必須是 Azure 資源，而且必須啟用「身分識別」功能的支援。
* 代表您 Azure 資源的「系統指派的受控識別」或「使用者指派的受控識別」，或代表您受控識別所屬其中一個群組的自主資料庫使用者，必須存在於目標資料庫中，且必須有 CONNECT 權限。

針對其他驗證模式，必須在用戶端機器上安裝下列元件：
* Java 7 或更新版本
* Microsoft JDBC Driver 6.0 (或更新版本) for SQL Server
* 如果您使用存取權杖型驗證模式，則需要 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) \(英文\) 及其相依性，才能執行此文章中的範例。 如需詳細資訊，請參閱 **使用存取權杖連線** 一節。
* 如果您使用 **ActiveDirectoryPassword** 驗證模式，則需要 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) \(英文\) 及其相依性。 如需詳細資訊，請參閱 **使用 ActiveDirectoryPassword 驗證模式來連線** 一節。
* 如果您使用 **ActiveDirectoryIntegrated** 模式，則需要 azure-activedirectory-library-for-java 及其相依性。 如需詳細資訊，請參閱 **使用 ActiveDirectoryIntegrated 驗證模式來連線** 一節。

## <a name="connecting-using-activedirectorymsi-authentication-mode"></a>使用 ActiveDirectoryMSI 驗證模式來連線
下列範例示範如何使用 `authentication=ActiveDirectoryMSI` 模式。 從 Azure 資源 (例如 Azure 虛擬機器、App Service 或與 Azure Active Directory 建立同盟的函數應用程式) 內部執行此範例。

在執行範例之前，請將下列行中的伺服器/資料庫名稱，取代為您的伺服器/資料庫名稱：

```java
ds.setServerName("aad-managed-demo.database.windows.net"); // replace 'aad-managed-demo' with your server name
ds.setDatabaseName("demo"); // replace with your database name
//Optional
ds.setMSIClientId("94de34e9-8e8c-470a-96df-08110924b814"); // Replace with Client ID of User-Assigned Managed Identity to be used
```

使用 ActiveDirectoryMSI 驗證模式的範例：

```java
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;

import com.microsoft.sqlserver.jdbc.SQLServerDataSource;

public class AAD_MSI {
    public static void main(String[] args) throws Exception {

        SQLServerDataSource ds = new SQLServerDataSource();
        ds.setServerName("aad-managed-demo.database.windows.net"); // Replace with your server name
        ds.setDatabaseName("demo"); // Replace with your database name
        ds.setAuthentication("ActiveDirectoryMSI");
        // Optional
        ds.setMSIClientId("94de34e9-8e8c-470a-96df-08110924b814"); // Replace with Client ID of User-Assigned Managed Identity to be used

        try (Connection connection = ds.getConnection(); 
                Statement stmt = connection.createStatement();
                ResultSet rs = stmt.executeQuery("SELECT SUSER_SNAME()")) {
            if (rs.next()) {
                System.out.println("You have successfully logged on as: " + rs.getString(1));
            }
        }
    }
}
```

在 Azure 虛擬機器上執行此範例時，會從「系統指派的受控識別」  或「使用者指派的受控識別」  (如果已指定 **msiClientId**) 擷取存取權杖，並使用所擷取的存取權杖建立連線。 如果已建立連線，您應該會看到下列訊息：

```bash
You have successfully logged on as: <your Managed Identity username>
```

## <a name="connecting-using-activedirectoryintegrated-authentication-mode"></a>使用 ActiveDirectoryIntegrated 驗證模式來連線
若使用 6.4 版，Microsoft JDBC Driver 新增了在多個平台 (Windows、Linux 和 macOS) 上使用 Kerberos 票證的 ActiveDirectoryIntegrated 驗證支援。
如需詳細資訊，請參閱[在 Windows、Linux 和 macOS 上設定 Kerberos 票證](#set-kerberos-ticket-on-windows-linux-and-macos)，以取得更多詳細資料。 或者，mssql-jdbc_auth-\<version>-\<arch>.dll 也可以在 Windows 上搭配 JDBC Driver 用於 ActiveDirectoryIntegrated 驗證。

> [!NOTE]
>  如果您使用較舊版本的驅動程式，請查看此[連結](feature-dependencies-of-microsoft-jdbc-driver-for-sql-server.md)，以取得使用此驗證模式所需的個別相依性。 

下列範例示範如何使用 `authentication=ActiveDirectoryIntegrated` 模式。 在已加入網域的機器 (與 Azure Active Directory 建立同盟) 上，執行此範例。 代表您 Azure AD 使用者或您所屬其中一個群組的自主資料庫使用者，必須存在於資料庫中，且必須有 CONNECT 權限。 

在建立並執行範例之前，請在您想要執行範例的用戶端機器上，下載 [azure-activedirectory-library-for-java 程式庫](https://github.com/AzureAD/azure-activedirectory-library-for-java) \(英文\) 及其相依性，並將其包含在 Java 建置路徑中

在執行範例之前，請將下列行中的伺服器/資料庫名稱，取代為您的伺服器/資料庫名稱：

```java
ds.setServerName("aad-managed-demo.database.windows.net"); // replace 'aad-managed-demo' with your server name
ds.setDatabaseName("demo"); // replace with your database name
```

使用 ActiveDirectoryIntegrated 驗證模式的範例：
```java
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;

import com.microsoft.sqlserver.jdbc.SQLServerDataSource;

public class AADIntegrated {
    public static void main(String[] args) throws Exception {

        SQLServerDataSource ds = new SQLServerDataSource();
        ds.setServerName("aad-managed-demo.database.windows.net"); // Replace with your server name
        ds.setDatabaseName("demo"); // Replace with your database name
        ds.setAuthentication("ActiveDirectoryIntegrated");

        try (Connection connection = ds.getConnection(); 
                Statement stmt = connection.createStatement();
                ResultSet rs = stmt.executeQuery("SELECT SUSER_SNAME()")) {
            if (rs.next()) {
                System.out.println("You have successfully logged on as: " + rs.getString(1));
            }
        }
    }
}
```

在用戶端機器上執行此範例時，會自動使用您的 Kerberos 票證，不需要密碼。 如果已建立連線，您應該會看到下列訊息：

```
You have successfully logged on as: <your domain user name>
```

### <a name="set-kerberos-ticket-on-windows-linux-and-macos"></a>在 Windows、Linux 和 macOS 上設定 Kerberos 票證

您必須設定 Kerberos 票證，將目前使用者連結至 Windows 網域帳戶。 主要步驟的摘要包含在下面。

#### <a name="windows"></a>Windows
JDK 隨附 `kinit`，可讓您在與 Azure Active Directory 建立同盟已加入網域的機器上，從金鑰發佈中心 (KDC) 取得 TGT。

##### <a name="step-1-ticket-granting-ticket-retrieval"></a>步驟 1:票證授權票證的擷取
- **執行於**：Windows
- **動作**：
  - 使用命令 `kinit username@DOMAIN.COMPANY.COM` 從 KDC 取得 TGT，然後其會提示您輸入您的網域密碼。
  - 使用 `klist` 查看可用的票證。 如果 kinit 成功，您應該會看到來自 krbtgt/DOMAIN.COMPANY.COM@ DOMAIN.COMPANY.COM 的票證。

> [!NOTE]
>  您可能必須使用 `-Djava.security.krb5.conf` 指定 `.ini` 檔案，應用程式才能找到 KDC。

#### <a name="linux-and-macos"></a>Linux 與 macOS

##### <a name="requirements"></a>需求
存取已加入網域的 Windows 電腦，以查詢您的 Kerberos 網域控制站。

##### <a name="step-1-find-kerberos-kdc"></a>步驟 1:尋找 Kerberos KDC
- **執行於**：Windows 命令列
- **動作**：`nltest /dsgetdc:DOMAIN.COMPANY.COM` (其中 "DOMAIN.COMPANY.COM" 對應至您網域的名稱)
- **範例輸出**
  ```
  DC: \\co1-red-dc-33.domain.company.com
  Address: \\2111:4444:2111:33:1111:ecff:ffff:3333
  ...
  The command completed successfully
  ```
- **要擷取的資訊** DC 名稱，在此案例中為 `co1-red-dc-33.domain.company.com`

##### <a name="step-2-configuring-kdc-in-krb5conf"></a>步驟 2:在 krb5.conf 中設定 KDC
- **執行於**：Linux/macOS
- **動作**：在您所選的編輯器中，編輯 /etc/krb5.conf。 設定下列金鑰
  ```
  [libdefaults]
    default_realm = DOMAIN.COMPANY.COM
   
  [realms]
  DOMAIN.COMPANY.COM = {
     kdc = co1-red-dc-28.domain.company.com
  }
  ```
  然後，儲存 krb5.conf 檔案並結束

> [!NOTE]
>  網域必須全部為大寫。

##### <a name="step-3-testing-the-ticket-granting-ticket-retrieval"></a>步驟 3：測試票證授權票證的擷取
- **執行於**：Linux/macOS
- **動作**：
  - 使用命令 `kinit username@DOMAIN.COMPANY.COM` 從 KDC 取得 TGT，然後其會提示您輸入您的網域密碼。
  - 使用 `klist` 查看可用的票證。 如果 kinit 成功，您應該會看到來自 krbtgt/DOMAIN.COMPANY.COM@ DOMAIN.COMPANY.COM 的票證。

## <a name="connecting-using-activedirectorypassword-authentication-mode"></a>使用 ActiveDirectoryPassword 驗證模式來連線
下列範例示範如何使用 `authentication=ActiveDirectoryPassword` 模式。

建置並執行範例之前：
1.  在您想要執行範例的用戶端機器上，下載 [azure-activedirectory-library-for-java 程式庫](https://github.com/AzureAD/azure-activedirectory-library-for-java) \(英文\) 及其相依性，並將其包含在 Java 建置路徑中
2.  找出下列幾行程式碼，並將伺服器/資料庫名稱取代為您的伺服器/資料庫名稱。
    ```java
    ds.setServerName("aad-managed-demo.database.windows.net"); // replace 'aad-managed-demo' with your server name
    ds.setDatabaseName("demo"); // replace with your database name
    ```
3.  找出下列幾行程式碼，並將使用者名稱取代為您想要以該身分連線的 Azure AD 使用者名稱。
    ```java
    ds.setUser("bob@cqclinic.onmicrosoft.com"); // replace with your user name
    ds.setPassword("password");     // replace with your password
    ```

使用 ActiveDirectoryPassword 驗證模式的範例：
```java
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;

import com.microsoft.sqlserver.jdbc.SQLServerDataSource;

public class AADUserPassword {
    
    public static void main(String[] args) throws Exception{
        
        SQLServerDataSource ds = new SQLServerDataSource();
        ds.setServerName("aad-managed-demo.database.windows.net"); // Replace with your server name
        ds.setDatabaseName("demo"); // Replace with your database
        ds.setUser("bob@cqclinic.onmicrosoft.com"); // Replace with your user name
        ds.setPassword("password"); // Replace with your password
        ds.setAuthentication("ActiveDirectoryPassword");
        
        try (Connection connection = ds.getConnection(); 
                Statement stmt = connection.createStatement();
                ResultSet rs = stmt.executeQuery("SELECT SUSER_SNAME()")) {
            if (rs.next()) {
                System.out.println("You have successfully logged on as: " + rs.getString(1));
            }
        }
    }
}
```
如果已建立連線，您應該會看到下列訊息作為輸出：
```
You have successfully logged on as: <your user name>
```

> [!NOTE]  
> 自主使用者資料庫必須存在，且代表指定的 Azure AD 使用者，或代表其中一個群組 (指定的 Azure AD 使用者所屬) 的自主資料庫使用者必須存在於資料庫中，且必須具有 CONNECT 權限 (但不包括 Azure Active Directory 伺服器管理員或群組)

## <a name="connecting-using-access-token"></a>使用存取權杖來連線
應用程式/服務可以從 Azure Active Directory 擷取存取權杖，並用來連線到 Azure SQL Database/資料倉儲。

> [!NOTE] 
> **accessToken** 只能使用 DriverManager 類別中 getConnection() 方法的 Properties 參數來設定。 不能用於連接字串中。

下列範例包含簡單的 Java 應用程式，可使用存取權杖型驗證連線到 Azure SQL Database/資料倉儲。 在建置並執行範例之前，請執行下列步驟：
1.  為您的服務在 Azure Active Directory 中建立應用程式帳戶。
    1. 登入 Azure 入口網站。
    2. 在左側導覽中，按一下 [Azure Active Directory]。
    3. 按一下 [應用程式註冊] 索引標籤。
    4. 在抽屜中，按一下 [新增應用程式註冊]。
    5. 輸入 mytokentest 作為應用程式的易記名稱，然後選取 [Web 應用程式/API]。
    6. 我們不需要登入 URL。 只是提供的任何項目: “https://mytokentest" 。
    7. 按一下底部的 [建立]。
    9. 仍然在 Azure 入口網站中，按一下應用程式的 [設定] 索引標籤，然後開啟 [內容] 索引標籤。
    10. 尋找「應用程式識別碼」(也稱為用戶端識別碼) 值並加以複製，稍後在設定應用程式時會用到 (例如，1846943b-ad04-4808-aa13-4702d908b5c1)。 請參閱下列快照集。
    11. 在 [金鑰] 區段底下，填入名稱欄位，選取金鑰的持續時間，然後儲存設定 (值欄位保留空白) 以建立金鑰。 儲存之後，值欄位應該會自動填入，請複製所產生的值。 這是用戶端密碼。
    12. 在左側導覽面板中，按一下 [Azure Active Directory]。 在 [應用程式註冊] 下，尋找 [結束點] 索引標籤。複製 [OATH 2.0 權杖端點] 底下的 URL，這是您的 STS URL。
    
    ![Azure 入口網站應用程式註冊端點 - STS URL](media/jdbc_aad_token.png)  
2. 以 Azure Active Directory 管理員身分登入 Azure SQL Server 的使用者資料庫，並使用 T-SQL 命令為您的應用程式主體佈建自主資料庫使用者。 如需建立 Azure Active Directory 系統管理員和自主資料庫使用者的詳細資訊，請參閱[使用 Azure Active Directory 驗證連線到 SQL Database 或 Azure Synapse Analytics](/azure/azure-sql/database/authentication-aad-overview)。

    ```
    CREATE USER [mytokentest] FROM EXTERNAL PROVIDER
    ```

3.  在您想要執行範例的用戶端機器上，下載 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 程式庫 (英文) 及其相依性，並將其包含在 Java 建置路徑中。 請注意，只有在執行此特定範例時，才需要 azure-activedirectory-library-for-java。 此範例使用此程式庫中的 API，從 Azure AD 擷取存取權杖。 如果您已經擁有存取權杖，則可以跳過此步驟。 請注意，您也必須移除範例中擷取存取權杖的區段。

在下列範例中，將 STS URL、用戶端識別碼、用戶端密碼、伺服器和資料庫名稱取代為您的值。

```java
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import com.microsoft.sqlserver.jdbc.SQLServerDataSource;

// The azure-activedirectory-library-for-java is needed to retrieve the access token from the AD.
import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;
import com.microsoft.aad.adal4j.ClientCredential;

public class AADTokenBased {

    public static void main(String[] args) throws Exception {

        // Retrieve the access token from the AD.
        String spn = "https://database.windows.net/";
        String stsurl = "https://login.microsoftonline.com/..."; // Replace with your STS URL.
        String clientId = "1846943b-ad04-4808-aa13-4702d908b5c1"; // Replace with your client ID.
        String clientSecret = "..."; // Replace with your client secret.

        AuthenticationContext context = new AuthenticationContext(stsurl, false, Executors.newFixedThreadPool(1));
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        Future<AuthenticationResult> future = context.acquireToken(spn, cred, null);
        String accessToken = future.get().getAccessToken();

        System.out.println("Access Token: " + accessToken);

        // Connect with the access token.
        SQLServerDataSource ds = new SQLServerDataSource();

        ds.setServerName("aad-managed-demo.database.windows.net"); // Replace with your server name.
        ds.setDatabaseName("demo"); // Replace with your database name.
        ds.setAccessToken(accessToken);

        try (Connection connection = ds.getConnection(); 
                Statement stmt = connection.createStatement();
                ResultSet rs = stmt.executeQuery("SELECT SUSER_SNAME()")) {
            if (rs.next()) {
                System.out.println("You have successfully logged on as: " + rs.getString(1));
            }
        }
    }
}
``` 

如果連線已成功建立，您應該會看到下列訊息作為輸出：

```bash
Access Token: <your access token>
You have successfully logged on as: <your client ID>    
```
