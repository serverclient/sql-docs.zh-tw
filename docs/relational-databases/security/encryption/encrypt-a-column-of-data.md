---
title: 加密資料行 | Microsoft Docs
description: 了解如何在使用 Transact-SQL 的 SQL Server 中，使用對稱式加密 (有時稱為資料行層級或資料格層級加密) 將資料行加密。
ms.custom: ''
titleSuffix: SQL Server & Azure Synapse Analytics & Azure SQL Database & SQL Managed Instance
ms.date: 12/15/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- encryption [SQL Server], columns
- cryptography [SQL Server], columns
- column level encryption
- cell level encryption
ms.assetid: 38e9bf58-10c6-46ed-83cb-e2d76cda0adc
author: jaszymas
ms.author: jaszymas
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current||=azure-sqldw-latest
ms.openlocfilehash: c09f7f91edf2cada0464b6cfbc1922664a86866f
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97640973"
---
# <a name="encrypt-a-column-of-data"></a>加密資料行

[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]  

本文描述如何使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)]，在 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] 中透過對稱加密來加密資料行。 這有時候稱為資料行層級加密或資料格層級加密。 此功能針對 Azure Synapse Analytics 處於預覽狀態

此文章中的範例已針對 AdventureWorks2017 完成驗證。 若要取得範例資料庫，請參閱 [AdventureWorks 範例資料庫](../../../samples/adventureworks-install-configure.md)。

## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  

需要下列權限，才能執行以下步驟：  
  
- 資料庫的 `CONTROL` 權限。  
- 資料庫的 `CREATE CERTIFICATE` 權限。 只有 Windows 登入、SQL Server 登入，以及應用程式角色可以擁有憑證。 群組和角色無法擁有憑證。  
- 資料表的 `ALTER` 權限。  
- 金鑰的某種權限，而且不得被拒絕 `VIEW DEFINITION` 權限。  
  
## <a name="create-database-master-key"></a>建立資料庫主要金鑰  

若要使用下列範例，您必須有資料庫主要金鑰。 如果您的資料庫還沒有資料庫主要金鑰，請建立一個。 若要建立主要金鑰，請連線到您的資料庫，然後執行下列指令碼。 請務必使用複雜密碼。

將下列範例複製並貼入已連線到 AdventureWorks 範例資料庫的查詢視窗中。 按一下 **[執行]** 。  

```sql  
CREATE MASTER KEY ENCRYPTION BY   
PASSWORD = '<complex password>';  
```  

一律備份您的資料庫主要金鑰。 如需資料庫主要金鑰的詳細資訊，請參閱 [CREATE MASTER KEY &#40;Transact-SQL&#41;](../../../t-sql/statements/create-master-key-transact-sql.md)。

## <a name="example-encrypt-with-symmetric-encryption-and-authenticator"></a>範例：使用對稱式加密和驗證器進行加密
  
1. 在 **[物件總管]** 中，連接到 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]的執行個體。  
  
2. 在標準列上，按一下 **[新增查詢]** 。  
  
3. 將下列範例複製並貼入已連線到 AdventureWorks 範例資料庫的查詢視窗中。 按一下 **[執行]** 。

    ```sql
    CREATE CERTIFICATE Sales09  
       WITH SUBJECT = 'Customer Credit Card Numbers';  
    GO  
  
    CREATE SYMMETRIC KEY CreditCards_Key11  
        WITH ALGORITHM = AES_256  
        ENCRYPTION BY CERTIFICATE Sales09;  
    GO  
  
    -- Create a column in which to store the encrypted data.  
    ALTER TABLE Sales.CreditCard   
        ADD CardNumber_Encrypted varbinary(160);   
    GO  
  
    -- Open the symmetric key with which to encrypt the data.  
    OPEN SYMMETRIC KEY CreditCards_Key11  
       DECRYPTION BY CERTIFICATE Sales09;  
  
    -- Encrypt the value in column CardNumber using the  
    -- symmetric key CreditCards_Key11.  
    -- Save the result in column CardNumber_Encrypted.    
    UPDATE Sales.CreditCard  
    SET CardNumber_Encrypted = EncryptByKey(Key_GUID('CreditCards_Key11')  
        , CardNumber, 1, HASHBYTES('SHA2_256', CONVERT( varbinary  
        , CreditCardID)));  
    GO  
  
    -- Verify the encryption.  
    -- First, open the symmetric key with which to decrypt the data.  
  
    OPEN SYMMETRIC KEY CreditCards_Key11  
       DECRYPTION BY CERTIFICATE Sales09;  
    GO  
  
    -- Now list the original card number, the encrypted card number,  
    -- and the decrypted ciphertext. If the decryption worked,  
    -- the original number will match the decrypted number.  
  
    SELECT CardNumber, CardNumber_Encrypted   
        AS 'Encrypted card number', CONVERT(nvarchar,  
        DecryptByKey(CardNumber_Encrypted, 1 ,   
        HASHBYTES('SHA2_256', CONVERT(varbinary, CreditCardID))))  
        AS 'Decrypted card number' FROM Sales.CreditCard;  
    GO  
    ```  
  
## <a name="encrypt-with-simple-symmetric-encryption"></a>使用簡單的對稱式加密進行加密  

1. 在 **[物件總管]** 中，連接到 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]的執行個體。  
  
2. 在標準列上，按一下 **[新增查詢]** 。  
  
3. 將下列範例複製並貼入已連線到 AdventureWorks 範例資料庫的查詢視窗中。 按一下 **[執行]** 。  
  
    ```sql
     CREATE CERTIFICATE HumanResources037  
       WITH SUBJECT = 'Employee Social Security Numbers';  
    GO  
  
    CREATE SYMMETRIC KEY SSN_Key_01  
        WITH ALGORITHM = AES_256  
        ENCRYPTION BY CERTIFICATE HumanResources037;  
    GO  
  
    USE [AdventureWorks2012];  
    GO  
  
    -- Create a column in which to store the encrypted data.  
    ALTER TABLE HumanResources.Employee  
        ADD EncryptedNationalIDNumber varbinary(128);   
    GO  
  
    -- Open the symmetric key with which to encrypt the data.  
    OPEN SYMMETRIC KEY SSN_Key_01  
       DECRYPTION BY CERTIFICATE HumanResources037;  
  
    -- Encrypt the value in column NationalIDNumber with symmetric   
    -- key SSN_Key_01. Save the result in column EncryptedNationalIDNumber.  
    UPDATE HumanResources.Employee  
    SET EncryptedNationalIDNumber = EncryptByKey(Key_GUID('SSN_Key_01'), NationalIDNumber);  
    GO  
  
    -- Verify the encryption.  
    -- First, open the symmetric key with which to decrypt the data.  
    OPEN SYMMETRIC KEY SSN_Key_01  
       DECRYPTION BY CERTIFICATE HumanResources037;  
    GO  
  
    -- Now list the original ID, the encrypted ID, and the   
    -- decrypted ciphertext. If the decryption worked, the original  
    -- and the decrypted ID will match.  
    SELECT NationalIDNumber, EncryptedNationalIDNumber   
        AS 'Encrypted ID Number',  
        CONVERT(nvarchar, DecryptByKey(EncryptedNationalIDNumber))   
        AS 'Decrypted ID Number'  
        FROM HumanResources.Employee;  
    GO  
    ```  
  
 如需詳細資訊，請參閱下列：  
  
-   [CREATE CERTIFICATE &#40;Transact-SQL&#41;](../../../t-sql/statements/create-certificate-transact-sql.md)  
  
-   [CREATE SYMMETRIC KEY &#40;Transact-SQL&#41;](../../../t-sql/statements/create-symmetric-key-transact-sql.md)  
  
-   [ALTER TABLE &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-table-transact-sql.md)  
  
-   [OPEN SYMMETRIC KEY &#40;Transact-SQL&#41;](../../../t-sql/statements/open-symmetric-key-transact-sql.md)  
