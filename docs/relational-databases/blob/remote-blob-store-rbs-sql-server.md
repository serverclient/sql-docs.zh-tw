---
title: 遠端 Blob 存放區 (RBS) (SQL Server) | Microsoft Docs
description: SQL Server 遠端 BLOB 存放區 (RBS) 會將二進位大型物件儲存在商品儲存體中，而不是儲存在主資料庫伺服器上。 了解此附加元件。
ms.custom: ''
ms.date: 11/03/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: filestream
ms.topic: conceptual
helpviewer_keywords:
- Remote Blob Store (RBS) [SQL Server]
- RBS (Remote Blob Store) [SQL Server]
ms.assetid: 31c947cf-53e9-4ff4-939b-4c1d034ea5b1
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: bfd234025664e7508d7d9cf942ff81a216b447a2
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171750"
---
# <a name="remote-blob-store-rbs-sql-server"></a>遠端 Blob 存放區 (RBS) (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 遠端 BLOB 存放區 (RBS) 是選用的附加元件，可讓資料庫管理員在商品儲存方案中儲存二進位大型物件，而不是直接儲存在主要資料庫伺服器上。  
  
 RBS 包含在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝媒體中，但不會由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式安裝。 在安裝媒體上搜尋 RBS.msi，找出安裝檔。

 若沒有 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝媒體，您可以在下列其中一個位置下載 RBS：

| [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本 | RBS 下載位置 |
|:---|:---|
| [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] | [[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 功能套件](https://www.microsoft.com/download/details.aspx?id=56833) |
| [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] | [[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] Feature Pack](https://www.microsoft.com/download/details.aspx?id=55992) |
| [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] | [[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] RBS 下載頁面](https://go.microsoft.com/fwlink/?linkid=2109005) |
| &nbsp; | &nbsp; |
  
 
  
## <a name="why-rbs"></a>為何使用 RBS？  
  
### <a name="optimized-database-storage-and-performance"></a>最佳化的資料庫儲存和效能  
 將 BLOB 儲存在資料庫中可能會耗用大量的檔案空間以及昂貴的伺服器資源。 RBS 會將 BLOB 傳輸到您所選擇的專用儲存方案，並將參考儲存在資料庫中的 BLOB。 這樣會為結構化的資料釋放伺服器儲存空間，並為資料庫作業釋放伺服器資源。  
  
### <a name="efficient-blob-management"></a>有效率的 BLOB 管理  
 數個 RBS 功能都支援管理已儲存的 BLOB：  
  
-   BLOB 使用 ACID (不可部分完成、一致、可隔離、持久) 交易進行管理。  
  
-   BLOB 會組織成集合。  
  
-   記憶體回收、一致性檢查，以及其他維護功能都包含在內。  
  
### <a name="standardized-api"></a>標準化的 API  
 RBS 會定義一組 API 來為應用程式提供標準化程式撰寫模型，以存取及修改任何 BLOB 存放區。 每一個 BLOB 存放區都可以指定它自己的提供者程式庫，該程式庫會外掛到 RBS 用戶端程式庫，並指定要如何儲存及存取 BLOB。  
  
 有好幾個協力廠商儲存方案廠商已經開發了符合這些標準 API，並在多種儲存平台上支援 BLOB 儲存的 RBS 提供者。  
  
## <a name="rbs-requirements"></a>RBS 需求  
 - RBS 在儲存 BLOB 中繼資料所在的主要資料庫伺服器中，需要使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Enterprise。  不過，如果您使用提供的 FILESTREAM 提供者，可以將 BLOB 本身儲存在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Standard 上。 若要連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，RBS 至少需要 [!INCLUDE[ssSQL14_md](../../includes/sssql14-md.md)] 的 ODBC 驅動程式第 11 版和 [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)]的 ODBC 驅動程式第 13 版。 這些驅動程式可透過 [Download ODBC Driver for SQL Server](../../connect/odbc/download-odbc-driver-for-sql-server.md)(下載 ODBC Driver for SQL Server) 取得。    
  
 RBS 包含一個 FILESTREAM 提供者，可讓您使用 RBS，將 BLOB 儲存在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]的執行個體上。 如果您要使用 RBS 將 BLOB 儲存在不同的儲存方案中，您必須使用針對該儲存方案開發的 RBS 提供者，或使用 RBS API 開發一個自訂的 RBS 提供者。 將 BLOB 儲存在 NTFS 檔案系統中的範例提供者，在 [Codeplex](https://go.microsoft.com/fwlink/?LinkId=210190)上作為學習資源提供。  
  
## <a name="rbs-security"></a>RBS 安全性  
 SQL 遠端 Blob 存放區團隊部落格是了解這項功能很好的資訊來源。 [RBS Security Model](/archive/blogs/sqlrbs/rbs-security-model)(RBS 安全性模型) 文章中有描述 RBS 安全性模型。  
  
### <a name="custom-providers"></a>自訂提供者  
 當您使用自訂提供者將 BLOB 儲存在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]外部時，請確認您使用適合自訂提供者之儲存媒體的權限和加密選項，保護已儲存的 BLOB。  
  
### <a name="credential-store-symmetric-key"></a>認證存放區對稱金鑰  
 如果提供者需要設定及使用儲存在認證存放區中的祕密，RBS 會針對用戶端可能用來取得提供者 Blob 存放區授權的提供者祕密，使用對稱金鑰進行加密。  
  
-   RBS 2016 使用 **AES_128** 對稱金鑰。 除了回溯相容性原因之外，[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 不允許建立新的 **TRIPLE_DES** 金鑰。 如需詳細資訊，請參閱 [CREATE SYMMETRIC KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-symmetric-key-transact-sql.md)。  
  
-   RBS 2014 和先前版本使用的認證存放區，包含了使用過時的對稱金鑰演算法 **TRIPLE_DES** 所加密的密碼。 如果您目前使用 **TRIPLE_DES**[!INCLUDE[msCoName](../../includes/msconame-md.md)] ，建議您遵循本主題中的步驟，將您的金鑰更換為更強的加密方法，以增強安全性。  
  
 您可以在 RBS 資料庫中執行下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式來判斷 RBS 認證存放區對稱金鑰屬性︰   
`SELECT * FROM sys.symmetric_keys WHERE name = 'mssqlrbs_encryption_skey';` 如果該陳述式的輸出顯示仍然使用 **TRIPLE_DES** ，則您應該更換此金鑰。  
  
### <a name="rotating-the-symmetric-key"></a>更換對稱金鑰  
 使用 RBS 時，您應該定期更換認證存放區對稱金鑰。 這是符合組織安全性原則的一般安全性最佳作法。  更換 RBS 認證存放區對稱金鑰的其中一個方法是，在 RBS 資料庫中使用 [下列指令碼](#Key_rotation) 。  您也可以使用此指令碼移轉到更強的加密強度屬性，例如演算法或金鑰長度。 更換金鑰之前，請備份您的資料庫。  在指令碼結束時，會有一些驗證步驟。  
如果您的安全性原則需要的金鑰屬性 (例如演算法或金鑰長度) 與所提供的不同，則可以使用指令碼作為範本。 您可以在兩種情況下修改金鑰屬性：1) 建立暫存金鑰時 2) 建立永久金鑰時。  
  
##  <a name="rbs-resources"></a><a name="rbsresources"></a> RBS 資源  
  
 **RBS 範例**  
 [Codeplex](https://go.microsoft.com/fwlink/?LinkId=210190) 上提供的 RBS 範例會示範如何開發 RBS 應用程式，以及如何開發與安裝自訂的 RBS 提供者。  
  
 **RBS 部落格**  
 [RBS 部落格](/archive/blogs/sqlrbs/) 會提供其他資訊來協助您了解、部署，以及維護 RBS。  
  
##  <a name="key-rotation-script"></a><a name="Key_rotation"></a> 金鑰輪替指令碼  
 此範例會建立名為 `sp_rotate_rbs_symmetric_credential_key` 的預存程序，以您選擇的金鑰來取代目前使用的 RBS  
認證存放區對稱金鑰。  如果安全性原則需要定期更換金鑰，   
或者如果有特定演算法需求，您可能會想要執行這項作業。  
 在此預存程序中，使用 **AES_256** 的對稱金鑰會取代目前的金鑰。  由於  
取代了對稱金鑰，您必須以新的金鑰來重新加密密碼。  此預存   
程序也會重新加密密碼。  更換金鑰之前應該備份資料庫。  
  
```  
CREATE PROC sp_rotate_rbs_symmetric_credential_key  
AS  
BEGIN  
BEGIN TRANSACTION;  
BEGIN TRY  
CLOSE ALL SYMMETRIC KEYS;  
  
/* Prove that all secrets can be re-encrypted, by creating a   
temporary key (#mssqlrbs_encryption_skey) and create a   
temp table (#myTable) to hold the re-encrypted secrets.    
Check to see if all re-encryption worked before moving on.*/  
  
CREATE TABLE #myTable(sql_user_sid VARBINARY(85) NOT NULL,  
    blob_store_id SMALLINT NOT NULL,  
    credential_name NVARCHAR(256) COLLATE Latin1_General_BIN2 NOT NULL,  
    old_secret VARBINARY(MAX), -- holds secrets while existing symmetric key is deleted  
    credential_secret VARBINARY(MAX)); -- holds secrets with the new permanent symmetric key  
  
/* Create a new temporary symmetric key with which the credential store secrets   
can be re-encrypted. These will be used once the existing symmetric key is deleted.*/  
CREATE SYMMETRIC KEY #mssqlrbs_encryption_skey    
    WITH ALGORITHM = AES_256 ENCRYPTION BY   
    CERTIFICATE [cert_mssqlrbs_encryption];  
  
OPEN SYMMETRIC KEY #mssqlrbs_encryption_skey    
    DECRYPTION BY CERTIFICATE [cert_mssqlrbs_encryption];  
  
INSERT INTO #myTable   
    SELECT cred_store.sql_user_sid, cred_store.blob_store_id, cred_store.credential_name,   
    encryptbykey(  
        key_guid('#mssqlrbs_encryption_skey'),   
        decryptbykeyautocert(cert_id('cert_mssqlrbs_encryption'),   
            NULL, cred_store.credential_secret)  
        ),   
    NULL  
    FROM [mssqlrbs_resources].[rbs_internal_blob_store_credentials] AS cred_store;  
  
IF( EXISTS(SELECT * FROM #myTable WHERE old_secret IS NULL))  
BEGIN  
    PRINT 'Abort. Failed to read some values';  
    SELECT * FROM #myTable;  
    ROLLBACK;  
END;  
ELSE  
BEGIN  
/* Re-encryption worked, so go ahead and drop the existing RBS credential store   
 symmetric key and replace it with a new symmetric key.*/  
DROP SYMMETRIC KEY [mssqlrbs_encryption_skey];  
  
CREATE SYMMETRIC KEY [mssqlrbs_encryption_skey]   
WITH ALGORITHM = AES_256   
ENCRYPTION BY CERTIFICATE [cert_mssqlrbs_encryption];  
  
OPEN SYMMETRIC KEY [mssqlrbs_encryption_skey]   
DECRYPTION BY CERTIFICATE [cert_mssqlrbs_encryption];  
  
/*Re-encrypt using the new permanent symmetric key.    
Verify if encryption provided a result*/  
UPDATE #myTable   
SET [credential_secret] =   
    encryptbykey(key_guid('mssqlrbs_encryption_skey'), decryptbykey(old_secret))  
  
IF( EXISTS(SELECT * FROM #myTable WHERE credential_secret IS NULL))  
BEGIN  
    PRINT 'Aborted. Failed to re-encrypt some values'  
    SELECT * FROM #myTable  
    ROLLBACK  
END  
ELSE  
BEGIN  
  
/* Replace the actual RBS credential store secrets with the newly   
encrypted secrets stored in the temp table #myTable.*/                
SET NOCOUNT ON;  
DECLARE @sql_user_sid varbinary(85);  
DECLARE @blob_store_id smallint;  
DECLARE @credential_name varchar(256);  
DECLARE @credential_secret varbinary(256);  
DECLARE curSecretValue CURSOR   
    FOR SELECT sql_user_sid, blob_store_id, credential_name, credential_secret   
FROM #myTable ORDER BY sql_user_sid, blob_store_id, credential_name;  
  
OPEN curSecretValue;  
FETCH NEXT FROM curSecretValue   
    INTO @sql_user_sid, @blob_store_id, @credential_name, @credential_secret  
WHILE @@FETCH_STATUS = 0  
BEGIN  
    UPDATE [mssqlrbs_resources].[rbs_internal_blob_store_credentials]   
        SET [credential_secret] = @credential_secret   
        FROM [mssqlrbs_resources].[rbs_internal_blob_store_credentials]   
        WHERE sql_user_sid = @sql_user_sid AND blob_store_id = @blob_store_id AND   
            credential_name = @credential_name  
FETCH NEXT FROM curSecretValue   
    INTO @sql_user_sid, @blob_store_id, @credential_name, @credential_secret  
END  
CLOSE curSecretValue  
DEALLOCATE curSecretValue  
  
DROP TABLE #myTable;  
CLOSE ALL SYMMETRIC KEYS;  
DROP SYMMETRIC KEY #mssqlrbs_encryption_skey;  
  
/* Verify that you can decrypt all encrypted credential store entries using the certificate.*/  
IF( EXISTS(SELECT * FROM [mssqlrbs_resources].[rbs_internal_blob_store_credentials]   
WHERE decryptbykeyautocert(cert_id('cert_mssqlrbs_encryption'),   
    NULL, credential_secret) IS NULL))  
BEGIN  
    print 'Aborted. Failed to verify key rotation'  
    ROLLBACK;  
END;  
ELSE  
    COMMIT;  
END;  
END;  
END TRY  
BEGIN CATCH  
     PRINT 'Exception caught: ' + cast(ERROR_NUMBER() as nvarchar) + ' ' + ERROR_MESSAGE();  
     ROLLBACK  
END CATCH  
END;  
GO  
```  
  
 現在您可以使用 `sp_rotate_rbs_symmetric_credential_key` 預存程序來更換 RBS 認證存放區對稱金鑰，而密碼在更換金鑰前後會保持不變。  
  
```  
SELECT *, decryptbykeyautocert(cert_id('cert_mssqlrbs_encryption'), NULL, credential_secret)   
FROM [mssqlrbs_resources].[rbs_internal_blob_store_credentials];  
  
EXEC sp_rotate_rbs_symmetric_credential_key;  
  
SELECT *, decryptbykeyautocert(cert_id('cert_mssqlrbs_encryption'), NULL, credential_secret)   
FROM [mssqlrbs_resources].[rbs_internal_blob_store_credentials];  
  
/* See that the RBS credential store symmetric key properties reflect the new changes*/  
SELECT * FROM sys.symmetric_keys WHERE name = 'mssqlrbs_encryption_skey';  
```  
  
## <a name="see-also"></a>另請參閱  
[遠端 BLOB 存放區及 AlwaysOn 可用性群組 (SQL Server)](../../database-engine/availability-groups/windows/remote-blob-store-rbs-and-always-on-availability-groups-sql-server.md)   
 [CREATE SYMMETRIC KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-symmetric-key-transact-sql.md)  
  
