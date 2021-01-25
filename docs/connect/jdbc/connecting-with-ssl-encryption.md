---
description: 使用加密連線
title: 使用加密連線 |Microsoft Docs
ms.custom: ''
ms.date: 09/12/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: vanto
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: ec91fa8a-ab7e-4c1e-a05a-d7951ddf33b1
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 1f60a186626514ed5532cc2ffc0fa4ffd1af0674
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88438460"
---
# <a name="connecting-with-encryption"></a>使用加密連線
[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

  本文的範例描述如何在 Java 應用程式中使用連接字串屬性，讓應用程式使用傳輸層安全性 (TLS) 加密。 如需這些新連接字串屬性 (例如 **encrypt**、**trustServerCertificate**、**trustStore**、**trustStorePassword** 和 **hostNameInCertificate**) 的詳細資訊，請參閱[設定連線屬性](../../connect/jdbc/setting-the-connection-properties.md)。  
  
 當 **encrypt** 屬性設定為 **true**，而且 **trustServerCertificate** 屬性設定為 **true** 時，[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] 不會驗證 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] TLS 憑證。 若要在測試環境 (例如，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體僅有一個自簽憑證的情況下) 中允許這些連線，通常需要執行此動作。  
  
 下列程式碼範例示範如何在連接字串中設定 **trustServerCertificate** 屬性：  
  
```java
String connectionUrl =   
    "jdbc:sqlserver://localhost:1433;" +  
     "databaseName=AdventureWorks;integratedSecurity=true;" +  
     "encrypt=true;trustServerCertificate=true";  
```  
  
 當 **encrypt** 屬性設定為 **true**，而且 **trustServerCertificate** 屬性設定為 **false** 時，[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] 會驗證 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] TLS 憑證。 驗證伺服器憑證是 TLS 交握的一部分，而且這麼做可以確保伺服器是所要連線的正確伺服器。 若要驗證伺服器憑證，必須在連線時間，明確地使用 **trustStore** 和 **trustStorePassword** 連線屬性，或隱含地使用基礎 Java Virtual Machine (JVM) 的預設信任存放區來提供信任的資料。  
  
 **trustStore** 屬性會指定 trustStore 憑證檔案的路徑 (包括檔案名稱)，該檔案包含用戶端所信任之憑證的清單。 **trustStorePassword** 屬性會指定用於檢查 trustStore 資料完整性的密碼。 如需使用 JVM 預設信任存放區的詳細資訊，請參閱[為用戶端設定加密](../../connect/jdbc/configuring-the-client-for-ssl-encryption.md)。  
  
 下列程式碼範例示範如何在連接字串中設定 **trustStore** 和 **trustStorePassword** 屬性：  
  
```java
String connectionUrl =   
    "jdbc:sqlserver://localhost:1433;" +  
     "databaseName=AdventureWorks;integratedSecurity=true;" +  
     "encrypt=true; trustServerCertificate=false;" +  
     "trustStore=storeName;trustStorePassword=storePassword";  
```  
  
 JDBC Driver 會提供額外的屬性 **hostNameInCertificate**，該屬性會指定伺服器的主機名稱。 此屬性的值必須符合憑證的 Subject 屬性。  
  
 下列程式碼範例示範如何在連接字串中使用 **hostNameInCertificate** 屬性：  
  
```java
String connectionUrl =   
    "jdbc:sqlserver://localhost:1433;" +  
     "databaseName=AdventureWorks;integratedSecurity=true;" +  
     "encrypt=true; trustServerCertificate=false;" +  
     "trustStore=storeName;trustStorePassword=storePassword" +  
     "hostNameInCertificate=hostName";  
```  
  
> [!NOTE]  
>  或者，您可以使用 [SQLServerDataSource](../../connect/jdbc/reference/sqlserverdatasource-class.md) 類別所提供的適當 **setter** 方法來設定連線屬性的值。  
  
 如果 **encrypt** 屬性設定為 **true** 且 **trustServerCertificate** 屬性設定為 **false**，而且連接字串中的伺服器名稱不符合 TLS 憑證中的伺服器名稱，則會發出下列錯誤：`The driver couldn't establish a secure connection to SQL Server by using Secure Sockets Layer (SSL) encryption. Error: "java.security.cert.CertificateException: Failed to validate the server name in a certificate during Secure Sockets Layer (SSL) initialization."`。 從 7.2 版開始，驅動程式支援在 TLS 憑證中伺服器名稱最左側標籤中的萬用字元模式比對。

## <a name="see-also"></a>另請參閱

 [使用加密](../../connect/jdbc/using-ssl-encryption.md) [保護 JDBC 驅動程式應用程式](../../connect/jdbc/securing-jdbc-driver-applications.md)
