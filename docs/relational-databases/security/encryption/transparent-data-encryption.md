---
title: 透明資料加密 (TDE) | Microsoft 文件
description: 了解會將 SQL Server、Azure SQL Database 及 Azure Synapse Analytics 資料加密的透明資料加密，也稱為加密待用資料。
ms.custom: ''
ms.date: 05/09/2019
ms.prod: sql
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- Transparent Data Encryption
- database encryption key, about
- TDE
- database encryption key
- TDE, about
- Transparent Data Encryption, about
- encryption [SQL Server], transparent data encryption
ms.assetid: c75d0d4b-4008-4e71-9a9d-cee2a566bd3b
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 06288dc0bc6e16ab8928322005254f5fac1ddc61
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171480"
---
# <a name="transparent-data-encryption-tde"></a>透明資料加密 (TDE)

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

「透明資料加密」 (TDE) 會加密 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]、[!INCLUDE[ssSDSFull](../../../includes/sssdsfull-md.md)] 和 [!INCLUDE[ssSDWfull](../../../includes/sssdwfull-md.md)] 資料檔案。 此加密稱為加密待用資料。

為協助保護資料庫安全，您可採取以下的預防措施：

* 設計安全的系統。
* 加密機密資產。
* 在資料庫伺服器周圍建置防火牆。

但是竊取磁碟機或備份磁帶等實體媒體的惡意人士，可以還原或附加資料庫，並瀏覽其資料。

解決方案之一是加密資料庫中的敏感性資料，並使用憑證來保護加密資料的金鑰。 這個解決方案可防止沒有金鑰的人使用該資料。 但這種防護必須事先規劃。

TDE 會執行資料和記錄檔的即時 I/O 加密和解密。 加密使用資料庫加密金鑰 (DEK)。 資料庫開機記錄會儲存金鑰以供復原時使用。 DEK 是對稱金鑰。 受伺服器 master 資料庫儲存的憑證保護，或受 EKM 模組保護的非對稱金鑰所保護。

TDE 會保護待用資料，亦即資料檔和記錄檔。 這讓您必須遵循不同行業建立的許多法令、規章和指導方針。 此功能可讓軟體開發人員使用 AES 和 3DES 加密演算法來加密資料，而無需變更現有的應用程式。

> [!IMPORTANT]
> TDE 不提供跨通訊通道的加密。 如需如何跨通訊通道加密資料的詳細資訊，請參閱[啟用資料庫引擎的加密連接 (SQL Server 組態管理員)](../../../database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine.md)。
>
>**相關主題：**
>
> - [Azure SQL Database 的透明資料加密](/azure/azure-sql/database/transparent-data-encryption-tde-overview)
> - [開始使用 Azure Synapse Analytics 中的透明資料加密 (TDE)](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql)
> - [將 TDE 保護的資料庫移至另一個 SQL Server](../../../relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server.md)
> - [使用 EKM 在 SQL Server 上啟用 TDE](../../../relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm.md)
> - [搭配使用 SQL Server 連接器與 SQL 加密功能](../../../relational-databases/security/encryption/use-sql-server-connector-with-sql-encryption-features.md)
> - [SQL Server 安全性部落格上的 TDE 常見問題集](/archive/blogs/sqlsecurity/feature-spotlight-transparent-data-encryption-tde)

## <a name="about-tde"></a>關於 TDE

資料庫檔案的加密會在頁面層級執行。 加密資料庫中的頁面會在寫入磁碟前即已加密，並在讀入記憶體時解密。 TDE 不會增加加密資料庫的大小。

### <a name="information-applicable-to-sssds"></a>適用於 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 的資訊

搭配使用 TDE 和 [!INCLUDE[sqldbesa](../../../includes/sqldbesa-md.md)] V12 時，[!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 會自動建立儲存在 master 資料庫中的伺服器層級憑證。 若要移動 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 上的 TDE 資料庫，無需為移動作業解密資料庫。 如需搭配使用 TDE 和 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 的詳細資訊，請參閱[使用 Azure SQL Database 的透明資料加密](/azure/azure-sql/database/transparent-data-encryption-tde-overview)。

### <a name="information-applicable-to-ssnoversion"></a>適用於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的資訊

保護資料庫之後，即可使用正確的憑證來還原資料庫。 如需有關憑證的詳細資訊，請參閱＜ [SQL Server Certificates and Asymmetric Keys](../../../relational-databases/security/sql-server-certificates-and-asymmetric-keys.md)＞。

啟用 TDE 之後，請立即備份憑證及其相關聯的私密金鑰。 如果憑證無法使用，或您在另一部伺服器上還原或附加資料庫，則需要使用備份的憑證和私密金鑰。 否則，即無法開啟資料庫。

即使資料庫已停用 TDE，仍請保留加密憑證。 雖然資料庫未加密，但部分交易記錄檔可能仍受到保護。 在執行完整資料庫備份之前，執行某些作業可能也需要憑證。

已超過到期日的憑證仍可與 TDE 搭配用來加密和解密資料。

### <a name="encryption-hierarchy"></a>加密階層

下圖顯示 TDE 加密的架構。 在 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 上使用 TDE 時，使用者只能設定資料庫層級項目 (資料庫加密金鑰和 ALTER DATABASE 部分)。

![透明資料庫加密架構](../../../relational-databases/security/encryption/media/tde-architecture.png)

## <a name="enable-tde"></a>啟用 TDE

若要使用 TDE，請遵循下列步驟。

**適用於**： [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]。

1. 建立建立主要金鑰。

1. 建立或取得受主要金鑰保護的憑證。

1. 建立資料庫加密金鑰，並使用憑證保護金鑰。

1. 設定資料庫以使用加密。

下例示範使用安裝在伺服器上名為 `MyServerCert` 的憑證來加密和解密 `AdventureWorks2012` 資料庫。

```sql
USE master;
GO
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<UseStrongPasswordHere>';
go
CREATE CERTIFICATE MyServerCert WITH SUBJECT = 'My DEK Certificate';
go
USE AdventureWorks2012;
GO
CREATE DATABASE ENCRYPTION KEY
WITH ALGORITHM = AES_128
ENCRYPTION BY SERVER CERTIFICATE MyServerCert;
GO
ALTER DATABASE AdventureWorks2012
SET ENCRYPTION ON;
GO
```

[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]會將加密和解密作業排定在背景執行緒上。 請使用本文中稍後出現的資料表目錄檢視和動態管理檢視，以檢視這些作業的狀態。

> [!CAUTION]
> 啟用 TDE 的資料庫備份檔案時，也會使用資料庫加密金鑰來加密。 因此，當要還原這些備份時，必須可以使用用來保護此資料庫加密金鑰的憑證。 所以，除了備份資料庫以外，請務必也要維護伺服器憑證的備份。 如果無法再使用此憑證，即會遺失資料。
>
> 如需詳細資訊，請參閱 [SQL Server Certificates and Asymmetric Keys](../../../relational-databases/security/sql-server-certificates-and-asymmetric-keys.md)。

## <a name="commands-and-functions"></a>命令與函式

若要讓下列陳述式接受 TDE 憑證，請使用資料庫主要金鑰來加密憑證。 如果只用密碼來加密憑證，則其陳述式會拒絕將憑證作為加密程式。

> [!IMPORTANT]
> 如果在 TDE 使用憑證後以密碼保護憑證，則在重新開機之後，即無法存取資料庫。

下表提供 TDE 命令與函式的連結及說明：

|命令或函數|目的|
|-------------------------|-------------|
|[CREATE DATABASE ENCRYPTION KEY (Transact-SQL)](../../../t-sql/statements/create-database-encryption-key-transact-sql.md)|建立用於加密資料庫的金鑰| 
|[ALTER DATABASE ENCRYPTION KEY (Transact-SQL)](../../../t-sql/statements/alter-database-encryption-key-transact-sql.md)|變更用於加密資料庫的金鑰|
|[DROP DATABASE ENCRYPTION KEY (Transact-SQL)](../../../t-sql/statements/drop-database-encryption-key-transact-sql.md)|移除用於加密資料庫的金鑰|
|[ALTER DATABASE SET 選項 (Transact-SQL)](../../../t-sql/statements/alter-database-transact-sql-set-options.md)|說明啟用 TDE 所用的 **ALTER DATABASE** 選項|

## <a name="catalog-views-and-dynamic-management-views"></a>目錄檢視和動態管理檢視

 下表顯示 TDE 目錄檢視和動態管理檢視。

|目錄檢視或動態管理檢視|目的|
|---------------------------------------------|-------------|
|[sys.databases (Transact-SQL)](../../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)|顯示資料庫資訊的目錄檢視|
|[sys.certificates (Transact-SQL)](../../../relational-databases/system-catalog-views/sys-certificates-transact-sql.md)|目錄檢視，其顯示資料庫中的憑證|
|[sys.dm_database_encryption_keys (Transact-SQL)](../../../relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql.md)|動態管理檢視，其提供資料庫加密金鑰的資訊及加密狀態|

## <a name="permissions"></a>權限

如先前的資料表中所述，每項 TDE 功能和命令都有個別的權限需求。

若要檢視與 TDE 有關的中繼資料，則需要具有憑證的 VIEW DEFINITION 權限。

## <a name="considerations"></a>考量

當資料庫加密作業的重新加密掃描正在進行時，對資料庫的維護作業將會停用。 您可使用資料庫的單一使用者模式設定來執行維護作業。 如需詳細資訊，請參閱 [將資料庫設定為單一使用者模式](../../../relational-databases/databases/set-a-database-to-single-user-mode.md)。

使用 sys.dm_database_encryption_keys 動態管理檢視來尋找資料庫加密的狀態。 如需詳細資訊，請參閱本文先前的＜目錄檢視和動態管理檢視＞一節。

在 TDE 中，資料庫內的所有檔案和檔案群組都會加密。 如果資料庫中有任何檔案群組標示為 READ ONLY，則資料庫加密作業將會失敗。

如果在資料庫鏡像或記錄傳送中使用資料庫，則兩個資料庫都會加密。 在兩者之間傳送記錄交易時會予以加密。

> [!IMPORTANT]
> 設定資料庫加密時，即會加密全文檢索索引。 在 SQL Server 2008 前的 SQL Server 版本中建立的這類索引會由 SQL Server 2008 或更新版本匯入資料庫，並由 TDE 加密。

> [!TIP]
> 若要監視資料庫的 TDE 狀態變更，請使用 SQL Server Audit 或 SQL Database 稽核。 SQL Server 將 TDE 追蹤記錄放在稽核動作群組 DATABASE_CHANGE_GROUP 下，此群組位於 [動作群組和動作](../../../relational-databases/security/auditing/sql-server-audit-action-groups-and-actions.md)中。

## <a name="restrictions"></a>限制

在初始資料庫加密、金鑰變更或資料庫解密期間，不允許下列作業：

- 從資料庫的檔案群組中卸除檔案

- 卸除資料庫

- 讓資料庫離線

- 卸離資料庫

- 將資料庫或檔案群組轉換成 READ ONLY 狀態

執行 CREATE DATABASE ENCRYPTION KEY、ALTER DATABASE ENCRYPTION KEY、DROP DATABASE ENCRYPTION KEY 及 ALTER DATABASE...SET ENCRYPTION 陳述式時，不允許下列作業：

- 從資料庫的檔案群組中卸除檔案

- 卸除資料庫

- 讓資料庫離線

- 卸離資料庫

- 將資料庫或檔案群組轉換成 READ ONLY 狀態

- 使用 ALTER DATABASE 命令

- 啟動資料庫或資料庫檔案備份

- 啟動資料庫或資料庫檔案還原

- 建立快照集

下列作業或條件會讓 CREATE DATABASE ENCRYPTION KEY、ALTER DATABASE ENCRYPTION KEY、DROP DATABASE ENCRYPTION KEY 和 ALTER DATABASE...SET ENCRYPTION 陳述式無法執行：

- 資料庫是唯讀的，或具有唯讀的檔案群組。

- 正在執行 ALTER DATABASE 命令。

- 正在執行資料備份。

- 資料庫為離線或還原狀態。

- 快照集正在進行中。

- 正在執行資料庫維護工作。

建立資料庫檔案時，如果啟用 TDE 即無法使用立即檔案初始化功能。

為了使用非對稱金鑰來加密資料庫加密金鑰，此非對稱金鑰必須位在可延伸的金鑰管理提供者上。

## <a name="tde-scan"></a>TDE 掃描

若要啟用資料庫上的 TDE，[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 必須執行加密掃描。 掃描會將資料檔案的每一頁都讀入緩衝集區，然後將加密的頁面寫回磁片。

為了讓您能更充分掌握加密掃描，[!INCLUDE[sql-server-2019](../../../includes/sssqlv15-md.md)] 引進具有暫停和繼續語法的 TDE 掃描。 您可在系統工作負載過重或業務忙碌時段暫停掃描，再於稍後時間繼續掃描。

使用下列語法可暫停 TDE 加密掃描：

```sql
ALTER DATABASE <db_name> SET ENCRYPTION SUSPEND;
```

您也可以使用下列語法繼續 TDE 加密掃描：

```sql
ALTER DATABASE <db_name> SET ENCRYPTION RESUME;
```

encryption_scan_state 資料行已新增至 sys.dm_database_encryption_keys 動態管理檢視。 其顯示加密掃描目前的狀態。 另有稱為 encryption_scan_modify_date 的新資料行，其包含上次加密掃描狀態變更的日期和時間。

如果 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體在加密掃描暫停期間重新開機，則啟動時即會在錯誤記錄檔中記錄訊息。 該訊息會指出現有的掃描已暫停。

## <a name="tde-and-transaction-logs"></a>TDE 和交易記錄

讓資料庫使用 TDE 會移除目前虛擬交易記錄的其餘部分。 移除會強制建立下一筆交易記錄。 此行為保證在設定資料庫加密之後，記錄中不會留下任何純文字。

若要尋找記錄檔加密的狀態，請參閱 `sys.dm_database_encryption_keys` 檢視中的 `encryption_state` 資料行，如以下範例所示：

```sql
USE AdventureWorks2012;
GO
/* The value 3 represents an encrypted state
   on the database and transaction logs. */
SELECT *
FROM sys.dm_database_encryption_keys
WHERE encryption_state = 3;
GO
```

如需 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 記錄檔架構的詳細資訊，請參閱[交易記錄 (SQL Server)](../../../relational-databases/logs/the-transaction-log-sql-server.md)。

在資料庫加密金鑰變更前，所有寫入交易記錄的資料都是使用前一個資料庫加密金鑰來加密。

如要變更兩次資料庫加密金鑰，則必須先執行記錄備份，才能再次變更資料庫加密金鑰。

## <a name="tde-and-the-tempdb-system-database"></a>TDE 和 tempdb 系統資料庫

如果 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體上有任一其他資料庫使用 TDE 進行加密，則會加密 **tempdb** 系統資料庫。 這加密可能會影響相同 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體上未加密的資料庫效能。 如需 **tempdb** 系統資料庫的詳細資訊，請參閱 [tempdb 資料庫](../../../relational-databases/databases/tempdb-database.md)。

## <a name="tde-and-replication"></a>TDE 和複寫

複寫不會自動以加密形式複寫啟用 TDE 的資料庫資料。 如果想要保護散發資料庫和訂閱者資料庫，請分別啟用 TDE。

快照式複寫可將資料儲存在未加密的中繼檔案中，例如 BCP 檔案。 異動複寫和合併式複寫的初始資料散發亦可。 在這類複寫期間，您可啟用加密來保護通訊通道。

如需詳細資訊，請參閱[啟用資料庫引擎的加密連線 (SQL Server 組態管理員)](../../../database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine.md)。

## <a name="tde-and-always-on"></a>TDE 和 Always On    
您可[將加密的資料庫新增至 Always On 可用性群組](../../../database-engine/availability-groups/windows/encrypted-databases-with-always-on-availability-groups-sql-server.md)。  

若要加密屬於可用性群組的資料庫，請先在所有次要複本上建立主要金鑰和憑證或非對稱金鑰 (EKM)，再於主要複本上建立[資料庫加密金鑰](../../../t-sql/statements/create-database-encryption-key-transact-sql.md)。  

如果使用憑證保護資料庫加密金鑰 (DEK)，請[備份在主要複本上建立的憑證](../../../t-sql/statements/backup-certificate-transact-sql.md)，然後[先在所有次要複本上使用檔案建立憑證](../../../t-sql/statements/create-certificate-transact-sql.md)，再於主要複本上建立資料庫加密金鑰。 

## <a name="tde-and-filestream-data"></a>TDE 和 FILESTREAM 資料

即使啟用 TDE 也不會加密 **FILESTREAM** 資料。

<a name="scan-suspend-resume"></a>

## <a name="remove-tde"></a>移除 TDE

使用 `ALTER DATABASE` 陳述式移除資料庫的加密。

```sql
ALTER DATABASE <db_name> SET ENCRYPTION OFF;
```

若要檢視資料庫的狀態，請使用 [sys.dm_database_encryption_keys](../../../relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql.md) 動態管理檢視。

等候解密完成，然後使用 [DROP DATABASE ENCRYPTION KEY](../../../t-sql/statements/drop-database-encryption-key-transact-sql.md) 來移除資料庫加密金鑰。

> [!IMPORTANT]
> 將用於 TDE 的主要金鑰和憑證備份至安全位置。 需有主要金鑰和憑證才能還原以 TDE 加密資料庫時所取得的備份。 移除資料庫加密金鑰之後，請先進行記錄備份，再建立已解密資料庫的全新完整備份。 

## <a name="tde-and-buffer-pool-extension"></a>TDE 和緩衝集區延伸

使用 TDE 加密資料庫時，不會加密與緩衝集區延伸模組 (BPE) 相關的檔案。 針對這些檔案，請在檔案系統層級使用 Bitlocker 或 EFS 等加密工具。

## <a name="tde-and-in-memory-oltp"></a>TDE 和記憶體內部 OLTP

您可對具有記憶體中 OLTP 物件的資料庫啟用 TDE。 在 [!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)] 和 [!INCLUDE[ssSDSfull](../../../includes/sssdsfull-md.md)] 中，如果啟用 TDE，即會加密記憶體中 OLTP 記錄和資料。 在 [!INCLUDE[ssSQL14](../../../includes/sssql14-md.md)] 中，如果啟用 TDE，即會加密記憶體中 OLTP 記錄檔的記錄，但不會加密 MEMORY_OPTIMIZED_DATA 檔案群組中的檔案。

## <a name="related-tasks"></a>相關工作

[將 TDE 保護的資料庫移至另一個 SQL Server](../../../relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server.md)  
[使用 EKM 在 SQL Server 上啟用 TDE](../../../relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm.md)  
[使用 Azure Key Vault 的可延伸金鑰管理 (SQL Server)](../../../relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server.md)  

## <a name="related-content"></a>相關內容

[Azure SQL Database 的透明資料加密](/azure/azure-sql/database/transparent-data-encryption-tde-overview)  
[開始使用 Azure Synapse Analytics 中的透明資料加密 (TDE)](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql)  
[SQL Server 加密](../../../relational-databases/security/encryption/sql-server-encryption.md)  
[SQL Server 和資料庫加密金鑰 (資料庫引擎)](../../../relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine.md)  

## <a name="see-also"></a>另請參閱

[SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](../../../relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database.md)  
[FILESTREAM (SQL Server)](../../../relational-databases/blob/filestream-sql-server.md)