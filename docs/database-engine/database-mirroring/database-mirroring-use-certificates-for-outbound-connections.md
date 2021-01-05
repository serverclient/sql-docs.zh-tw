---
title: 資料庫鏡像 - 使用傳出連接的憑證 | Microsoft Docs
description: 了解如何在設定傳出連線之後設定伺服器執行個體，以使用憑證來驗證資料庫鏡像的傳入連線。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- certificates [SQL Server], database mirroring
- outbound connections [SQL Server]
- database mirroring [SQL Server], security
ms.assetid: 464c9096-10d6-4c5e-8bb1-19acba27ad9e
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 163b7af8795b663645133307317e39646deee6e5
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97637431"
---
# <a name="database-mirroring---use-certificates-for-outbound-connections"></a>資料庫鏡像 - 使用傳出連接的憑證
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  此主題描述設定伺服器執行個體的相關步驟，以便使用憑證來驗證資料庫鏡像的傳出連接。 您必須先完成傳出連接組態，才能設定傳入連接。  
  
> [!NOTE]  
>  伺服器執行個體上的所有鏡像連接都使用單一資料庫鏡像端點，而您必須在建立端點時指定伺服器執行個體的驗證方法。  
  
 設定傳出連接的程序包含下列一般步驟：  
  
1.  在 **master** 資料庫中，建立資料庫「主要金鑰」。  
  
2.  在 **master** 資料庫中，建立伺服器執行個體的加密憑證。  
  
3.  使用伺服器執行個體的憑證來建立其端點。  
  
4.  將憑證備份至檔案中，並以安全的方式將其複製到其他一或多個系統。  
  
 您必須為每一個夥伴或見證 (如果有的話) 完成上述步驟。  
  
 下列程序將詳細說明這些步驟。 對於每個步驟，此程序會提供在名為 HOST_A 的系統上設定伺服器執行個體的範例。 隨附的「範例」部分則針對名為 HOST_B 系統上的另一個伺服器執行個體示範相同的步驟。  
  
## <a name="procedure"></a>程序  
  
#### <a name="to-configure-server-instances-for-outbound-mirroring-connections-on-host_a"></a>設定 (HOST_A 上) 傳出鏡像連接的伺服器執行個體  
  
1.  在 **master** 資料庫中，若無資料庫「主要金鑰」存在，請加以建立。 若要檢視資料庫的現有金鑰，請使用 [sys.symmetric_keys](../../relational-databases/system-catalog-views/sys-symmetric-keys-transact-sql.md) 目錄檢視。  
  
     若要建立資料庫「主要金鑰」，請使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 命令：  
  
    ```  
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<1_Strong_Password!>';  
    GO  
    ```  
  
     使用唯一的增強式密碼，並將其記錄在安全的地方。  
  
     如需詳細資訊，請參閱 [CREATE MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-master-key-transact-sql.md) 和[建立資料庫主要金鑰](../../relational-databases/security/encryption/create-a-database-master-key.md)。  
  
2.  在 **master** 資料庫中，建立伺服器執行個體的加密憑證，以供資料庫鏡像的傳出連接使用。  
  
     例如，建立 HOST_A 系統的憑證。  
  
    > [!IMPORTANT]  
    >  如果您想要使用此憑證一年以上，請在 CREATE CERTIFICATE 陳述式中使用 EXPIRY_DATE 選項來指定 UTC 時間格式的到期日。 此外，我們建議您使用 SQL Server Management Studio 建立原則式管理規則，在您的憑證即將過期時通知您。 使用 [原則管理] 的 [建立新條件] 對話方塊，在 [憑證] Facet 的 **\@ExpirationDate** 欄位上建立這個規則。 如需詳細資訊，請參閱 [使用原則式管理來管理伺服器](../../relational-databases/policy-based-management/administer-servers-by-using-policy-based-management.md) 和 [保護 SQL Server 的安全](../../relational-databases/security/securing-sql-server.md)。  
  
    ```  
    USE master;  
    CREATE CERTIFICATE HOST_A_cert   
       WITH SUBJECT = 'HOST_A certificate for database mirroring',   
       EXPIRY_DATE = '11/30/2013';  
    GO  
    ```  
  
     如需詳細資訊，請參閱 [CREATE CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/create-certificate-transact-sql.md)。  
  
     若要檢視 **master** 資料庫中的憑證，您可以使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式：  
  
    ```  
    USE master;  
    SELECT * FROM sys.certificates;  
    ```  
  
     如需詳細資訊，請參閱 [sys.certificates &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-certificates-transact-sql.md)。  
  
3.  確定每個伺服器執行個體上都有資料庫鏡像端點。  
  
     若伺服器執行個體上已有資料庫鏡像端點，您應在伺服器執行個體上所建立的任何其他工作階段，重複使用該端點。 若要判斷伺服器執行個體上是否有資料庫鏡像端點，以及檢視該端點的組態，請使用下列陳述式：  
  
    ```  
    SELECT name, role_desc, state_desc, connection_auth_desc, encryption_algorithm_desc   
       FROM sys.database_mirroring_endpoints;  
    ```  
  
     若沒有端點存在，請建立一個端點，並使其在傳出連接上使用此憑證，而對其他系統的驗證則使用憑證的認證。 這屬於伺服器範圍的端點，可供伺服器執行個體參與的所有鏡像工作階段使用。  
  
     例如，為 HOST_A 上的範例伺服器執行個體建立鏡像端點。  
  
    ```  
    CREATE ENDPOINT Endpoint_Mirroring  
       STATE = STARTED  
       AS TCP (  
          LISTENER_PORT=7024  
          , LISTENER_IP = ALL  
       )   
       FOR DATABASE_MIRRORING (   
          AUTHENTICATION = CERTIFICATE HOST_A_cert  
          , ENCRYPTION = REQUIRED ALGORITHM AES  
          , ROLE = ALL  
       );  
    GO  
    ```  
  
     如需詳細資訊，請參閱 [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/create-endpoint-transact-sql.md)的相關資訊。  
  
4.  備份憑證，並將其複製到其他一或多個系統。 若要在其他系統上設定傳入連接，就必須執行此動作。  
  
    ```  
    BACKUP CERTIFICATE HOST_A_cert TO FILE = 'C:\HOST_A_cert.cer';  
    GO  
    ```  
  
     如需詳細資訊，請參閱 [BACKUP CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/backup-certificate-transact-sql.md)。  
  
     使用您所選的任何安全方式來複製此憑證。 務必將您所有的憑證小心保管。  
  
 前述幾個步驟中的範例程式碼會設定 HOST_A 上的傳出連接。  
  
 接著，您必須為 HOST_B 執行對等的傳出步驟。 這些步驟將在下一節＜範例＞中說明。  
  
## <a name="example"></a>範例  
 下列範例示範如何針對傳出連接設定 HOST_B。  
  
```  
USE master;  
--Create the database Master Key, if needed.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Strong_Password_#2>';  
GO  
-- Make a certifcate on HOST_B server instance.  
CREATE CERTIFICATE HOST_B_cert   
   WITH SUBJECT = 'HOST_B certificate for database mirroring',   
   EXPIRY_DATE = '11/30/2013';  
GO  
--Create a mirroring endpoint for the server instance on HOST_B.  
CREATE ENDPOINT Endpoint_Mirroring  
   STATE = STARTED  
   AS TCP (  
      LISTENER_PORT=7024  
      , LISTENER_IP = ALL  
   )   
   FOR DATABASE_MIRRORING (   
      AUTHENTICATION = CERTIFICATE HOST_B_cert  
      , ENCRYPTION = REQUIRED ALGORITHM AES  
      , ROLE = ALL  
   );  
GO  
--Backup HOST_B certificate.  
BACKUP CERTIFICATE HOST_B_cert TO FILE = 'C:\HOST_B_cert.cer';  
GO   
--Using any secure copy method, copy C:\HOST_B_cert.cer to HOST_A.  
```  
  
 使用您所選的任何安全方式，將憑證複製到其他系統。 務必將您所有的憑證小心保管。  
  
> [!IMPORTANT]  
>  在設定傳出連接後，您必須在每個伺服器執行個體上設定其他一或多個伺服器執行個體的傳入連接。 如需詳細資訊，請參閱 [允許資料庫鏡像端點使用輸入連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-inbound-connections.md)。  
  
 如需建立鏡像資料庫的相關資訊，包括 Transact-SQL 範例在內，請參閱[準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)。  
  
 如需建立高效能模式工作階段的 Transact-SQL 範例，請參閱[範例：使用憑證設定資料庫鏡像 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/example-setting-up-database-mirroring-using-certificates-transact-sql.md)。  
  
## <a name="net-framework-security"></a>.NET Framework 安全性  
 除非您可保證網路的安全無虞，否則建議您對資料庫鏡像連接使用加密。  
  
 將憑證複製到另一個系統時，請使用安全複製方法。  
  
## <a name="see-also"></a>另請參閱  
 [選擇加密演算法](../../relational-databases/security/encryption/choose-an-encryption-algorithm.md)   
 [準備鏡像資料庫以進行鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/prepare-a-mirror-database-for-mirroring-sql-server.md)   
 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)   
 [範例：使用憑證設定資料庫鏡像 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/example-setting-up-database-mirroring-using-certificates-transact-sql.md)   
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)   
 [為資料庫鏡像組態進行疑難排解 &#40;SQL Server&#41; &#40;SQL Server&#41;](../../database-engine/database-mirroring/troubleshoot-database-mirroring-configuration-sql-server.md)   
 [設定加密鏡像資料庫](../../database-engine/database-mirroring/set-up-an-encrypted-mirror-database.md)  
  
  
