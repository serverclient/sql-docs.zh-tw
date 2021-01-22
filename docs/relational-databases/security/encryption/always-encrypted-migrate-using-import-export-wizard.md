---
description: 使用 Always Encrypted 與 [SQL Server 匯入和匯出精靈] 將資料移轉到資料行或從中移轉
title: 使用 Always Encrypted 與 [SQL Server 匯入和匯出精靈] 將資料移轉到資料行或從中移轉 | Microsoft Docs
ms.custom: ''
ms.date: 10/31/2019
ms.prod: sql
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
f1_keywords:
- SQL13.SWB.COLUMNMASTERKEY.ROTATION.F1
helpviewer_keywords:
- Always Encrypted, configure with SSMS
ms.assetid: 29816a41-f105-4414-8be1-070675d62e84
author: jaszymas
ms.author: jaszymas
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 680c1101ebd3ff9744c8e8dbe44d927d843acc0b
ms.sourcegitcommit: fc24f7ecc155d97e789676fffe55e45840fcb088
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98669132"
---
# <a name="migrate-data-to-or-from-columns-using-always-encrypted-with-sql-server-import-and-export-wizard"></a>使用 Always Encrypted 與 [SQL Server 匯入和匯出精靈] 將資料移轉到資料行或從中移轉 
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]

[SQL Server 匯入和匯出精靈](../../../integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard.md)是一種工具，可讓您將資料從來源複製到目的地。 本文件描述在來源和/或目的地為 SQL Server 資料庫，且該資料庫包含以 [Always Encrypted](../../../relational-databases/security/encryption/always-encrypted-database-engine.md) 保護的資料行時，如何使用 [SQL Server 匯入和匯出精靈]。

## <a name="migration-scenarios"></a>移轉案例
透過 [SQL Server 匯入和匯出精靈]，您可以實作下列案例，將資料移轉到加密資料行或從中移轉出。

### <a name="encrypt-plaintext-data-on-migration"></a>在移轉時加密純文字資料
如果您的資料來源包含純文字資料，而您的目的地是包含加密資料行的 SQL Server 資料庫，您可以使用 [SQL Server 匯入和匯出精靈] 來擷取來源的純文字資料，將其加密並將加密資料 (加密文字) 複製到目的地資料庫中的加密資料行。 在此移轉案例中，資料來源可以是 [SQL Server 匯入和匯出精靈] 支援的任何資料存放區。 例如，檔案、SQL Server 資料庫或另一個資料庫系統中的資料庫。

若要確保 [SQL Server 匯入和匯出精靈] 可以加密資料，您必須為目的地資料庫連接啟用 Always Encrypted，且必須能夠存取可保護目的地資料庫資料行資料的金鑰。 如需詳細資訊，請參閱[針對資料庫連接啟用和停用 Always Encrypted](#enable-and-disable-always-encrypted-for-a-database-connection) 和[在移轉期間加密或解密資料的權限](#permissions-for-encrypting-or-decrypting-data-during-migration)。

### <a name="decrypt-encrypted-data-on-migration"></a>在移轉時解密加密資料
如果您要移轉 SQL Server 資料庫的已加密資料庫資料行中所儲存資料，您可以設定 [SQL Server 匯入和匯出精靈] 來解密資料，並將解密的 (純文字) 資料複製到目的地，這可以是 [SQL Server匯入和匯出精靈] 支援的任何資料存放區，例如檔案、SQL Server 資料庫或另一個資料庫系統中的資料庫。

若要確保 [SQL Server 匯入和匯出精靈] 可以解密資料，您必須為來源資料庫連接啟用 Always Encrypted，且必須能夠存取可保護來源資料庫資料行資料的金鑰。 如需詳細資訊，請參閱[針對資料庫連接啟用和停用 Always Encrypted](#enable-and-disable-always-encrypted-for-a-database-connection) 和[在移轉期間加密或解密資料的權限](#permissions-for-encrypting-or-decrypting-data-during-migration)。

### <a name="re-encrypt-data-on-migration"></a>在移轉時重新加密資料
如果您要從來源 SQL Server 資料庫中的加密資料行，將資料複製到相同或另一個 SQL Server 資料庫中的加密資料行，您可以設定 [SQL Server 匯入和匯出精靈]，以便在從來源擷取資料之後將其解密，並在插入目的地資料庫中的加密資料行之前將其重新加密。 如果目標資料行的結構描述 (例如，資料行資料類型、加密類型和資料行加密金鑰) 與來源資料行的結構描述不同，請使用這個方法。

若要確保 [SQL Server 匯入和匯出精靈] 可以加密及解密資料，您必須為來源資料庫連接與目的地資料庫連接啟用 Always Encrypted，且必須能夠存取可保護來源和目標資料庫資料行資料的金鑰。 如需詳細資訊，請參閱[針對資料庫連接啟用和停用 Always Encrypted](#enable-and-disable-always-encrypted-for-a-database-connection) 和[在移轉期間加密或解密資料的權限](#permissions-for-encrypting-or-decrypting-data-during-migration)。

### <a name="keep-data-encrypted-during-migration"></a>在移轉期間讓資料保持加密
如果您要從來源 SQL Server 資料庫中的加密資料行，將資料複製到相同或另一個 SQL Server 資料庫中的加密資料行，且目標資料行使用與來源資料行 **完全相符的** 結構描述 (包括相同的資料類型、加密類型和資料行加密金鑰) 時，您可以設定 [SQL Server 匯入和匯出精靈] 來擷取來源資料行中的加密文字，並將加密資料 (加密文字) 插入目標 SQL Server 資料庫中的加密資料行。 

在此案例中，您可以使用支援 SQL Server 的任何資料提供者，以連線到來源或目的地 SQL Server 資料庫。 如果您使用支援 Always Encrypted 來連線到目的地資料庫的提供者，則必須確定已針對資料庫連接停用 Always Encrypted。 如需詳細資訊，請參閱[針對資料庫連接啟用和停用 Always Encrypted](#enable-and-disable-always-encrypted-for-a-database-connection)。

您還必須確定已設定 [SQL Server 匯入和匯出精靈] 用來連線到目的地資料庫的資料庫主體 (使用者)，並將 `ALLOW_ENCRYPTED_VALUE_MODIFICATIONS` 選項設為 `ON`。 此選項會在大量複製作業中抑制伺服器上的密碼編譯中繼資料檢查，讓精靈不需解密資料就能在目的地資料庫中大量插入加密資料。 如需詳細資訊，請參閱[將加密資料大量載入 Always Encrypted 所保護的資料行](migrate-sensitive-data-protected-by-always-encrypted.md)。

## <a name="enable-and-disable-always-encrypted-for-a-database-connection"></a>針對資料庫連接啟用和停用 Always Encrypted
如果您的移轉案例需要 [SQL Server 匯入和匯出精靈] 能夠加密和/或解密資料，您必須使用支援 Always Encrypted 的資料提供者來設定來源 SQL Server 資料庫連接和/或目的地 SQL Server 資料庫連接。 您還必須針對來源和/或目的地資料庫連接啟用 Always Encrypted。

如果您不需要此精靈來加密或解密該連線上的資料，則可以使用任何資料提供者進行連線。

[SQL Server 匯入和匯出精靈] 中的下列資料提供者支援 Always Encrypted。

- .NET Framework Data Provider for SQL Server
  - 請確定執行精靈的電腦使用 .NET Framework 4.6.1 或更新版本。
  - 若要針對連線啟用 Always Encrypted，請將連線屬性中的 `Column Encryption Setting` 設定為 `Enabled`。 若要停用 Always Encrypted，請將 `Column Encryption Setting` 設定為 `Disabled`。 如需詳細資訊，請參閱[使用 .NET Framework Data Provider for SQL Server 連接到 SQL Server](../../../integration-services/import-export-data/connect-to-a-sql-server-data-source-sql-server-import-and-export-wizard.md#connect-to-sql-server-with-the-net-framework-data-provider-for-sql-server) 和[針對應用程式查詢啟用 Always Encrypted](develop-using-always-encrypted-with-net-framework-data-provider.md#enabling-always-encrypted-for-application-queries)。
- .NET Framework Data Provider for ODBC。
  - 安裝 Microsoft ODBC Driver 13.1 或更新版本。
    - 若要針對連線啟用 Always Encrypted，請將連線屬性中的 `Column Encryption` 設定為 `Enabled`。 若要停用 Always Encrypted，請將 `Column Encryption` 設定為 `Disabled`。 如需詳細資訊，請參閱[使用 ODBC Driver for SQL Server 連線到 SQL Server](../../../integration-services/import-export-data/connect-to-a-sql-server-data-source-sql-server-import-and-export-wizard.md#connect-to-sql-server-with-the-odbc-driver-for-sql-server) 和[在 ODBC 應用程式中啟用 Always Encrypted](../../../connect/odbc/using-always-encrypted-with-the-odbc-driver.md#enabling-always-encrypted-in-an-odbc-application)。

## <a name="permissions-for-encrypting-or-decrypting-data-during-migration"></a>在移轉期間加密或解密資料的權限

若要加密或解密 SQL Server 來源或目的地資料庫中所儲存的資料，您需要具有來源資料庫的 *VIEW ANY COLUMN MASTER KEY DEFINITION* 和 *VIEW ANY COLUMN ENCRYPTION KEY DEFINITION* 權限。

您也需要存取針對資料行 (儲存您要加密或解密的資料) 所設定的資料行主要金鑰：

- **憑證存放區 - 本機電腦** - 您必須具有當成資料行主要金鑰使用之憑證的讀取權，或為電腦上的系統管理員。
- **Azure Key Vault** - 您需要有包含資料行主要金鑰的保存庫 _get_、_unwrapKey_ 和 _verify_ 權限。
- **金鑰存放區提供者 (CNG)** - 必要權限和認證 (您在使用金鑰存放區或金鑰時可能會收到提示) 取決於存放區和 KSP 設定。
- **密碼編譯服務提供者 (CAPI)** - 必要權限和認證 (您在使用金鑰存放區或金鑰時可能會收到提示) 取決於存放區和 CSP 設定。
如需詳細資訊，請參閱 [建立及儲存資料行主要金鑰 (永遠加密)](../../../relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted.md)。

## <a name="next-steps"></a>後續步驟
- [使用 Always Encrypted 與 SQL Server Management Studio 查詢資料行](always-encrypted-query-columns-ssms.md)
- [使用 Always Encrypted 開發應用程式](always-encrypted-client-development.md)

## <a name="see-also"></a>另請參閱
- [一律加密](always-encrypted-database-engine.md)
- [使用 Always Encrypted 匯出和匯入資料庫](always-encrypted-migrate-using-bacpac.md)
- [使用 Always Encrypted 備份和還原資料庫](always-encrypted-migrate-using-backup-restore.md)
- [使用 Always Encrypted 將加密資料大量載入至資料行](migrate-sensitive-data-protected-by-always-encrypted.md)
