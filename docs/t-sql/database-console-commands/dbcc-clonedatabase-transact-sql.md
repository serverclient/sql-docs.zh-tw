---
description: DBCC CLONEDATABASE (Transact-SQL)
title: DBCC CLONEDATABASE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/23/2019
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CLONEDATABASE
- CLONE DATABASE
- DBCC_CLONEDATABASE_TSQL
- DBCC CLONEDATABASE
- DBCC CLONE DATABASE
- CLONEDATABASE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- database cloning [SQL Server]
- cloning databases
- clone databases
- cloning database
- clone database
- copying databases
- copy databases
- copying database
- copy database
- NO_STATISTICS option
- NO_QUERYSTORE option
- VERIFY_CLONEDB option
- BACKUP_CLONEDB option
- database copying [SQL Server]
- database cloning [SQL Server]
- DBCC CLONEDATABASE statement
ms.assetid: ''
author: bluefooted
ms.author: pamela
manager: amitban
ms.openlocfilehash: 2de4f0e84b39d1384e342eab3b7b3d0bfd101611
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172580"
---
# <a name="dbcc-clonedatabase-transact-sql"></a>DBCC CLONEDATABASE (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

使用 DBCC CLONEDATABASE 產生僅限結構描述的資料庫複本，以便調查與查詢最佳化工具相關的效能問題。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DBCC CLONEDATABASE   
(  
    source_database_name
    ,  target_database_name
)
    [ WITH { [ NO_STATISTICS ] [ , NO_QUERYSTORE ] [ , VERIFY_CLONEDB | SERVICEBROKER ] [ , BACKUP_CLONEDB ] } ]     
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*source_database_name*  
要複製的資料庫名稱。 
  
*target_database_name*  
要複製來源資料庫的目標資料庫名稱。 DBCC CLONEDATABASE 將會建立此資料庫，因此不應該已經存在。 
  
NO_STATISTICS  
指定是否需要從複本排除資料表/索引統計資料。 如果未指定此選項，則會自動包含資料表/索引統計資料。 此選項從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 CU3 和 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 開始提供。

NO_QUERYSTORE<br>
指定是否需要從複本排除查詢存放區資料。 如果未指定此選項，且已啟用來源資料庫中的查詢存放區，則會將查詢存放區資料複製到複本。 此選項從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 開始提供。

VERIFY_CLONEDB  
驗證新資料庫的一致性。  如果複製資料庫要用於生產環境，則需要此選項。  啟用 VERIFY_CLONEDB 也會停用統計資料和查詢存放區集合，因此相當於執行 WITH VERIFY_CLONEDB、NO_STATISTICS、NO_QUERYSTORE。  此選項從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP3、[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU8 開始提供。

> [!NOTE]  
> 下列命令可用來確認複製資料庫已準備好用於生產環境： <br/>`SELECT DATABASEPROPERTYEX('clone_database_name', 'IsVerifiedClone')`


SERVICEBROKER<br>
指定 Service Broker 相關系統目錄是否應該包含在複本中。  SERVICEBROKER 選項無法與 VERIFY_CLONEDB 搭配使用。  此選項從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP3、[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU8 開始提供。

BACKUP_CLONEDB  
建立並驗證複製資料庫的備份。  如果搭配 VERIFY_CLONEDB 使用，則會驗證複製資料庫，再進行備份。  此選項從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP3、[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 和 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] CU8 開始提供。
  
## <a name="remarks"></a>備註
DBCC CLONEDATABASE 會執行下列驗證。 如果任一項驗證失敗，則命令會失敗。
- 來源資料庫必須是使用者資料庫。 不允許複製系統資料庫 (master、模型、msdb、tempdb、散發資料庫等)。
- 來源資料庫必須在線上或可讀取。
- 不得存在使用與複製資料庫相同名稱的資料庫。
- 命令不在使用者交易中。

如果所有驗證都成功，下列作業會執行來源資料庫的複製：
- 建立新的目的地資料庫，該資料庫使用與來源相同的檔案配置，但具有模型資料庫的預設檔案大小。
- 建立來源資料庫的內部快照集。
- 將系統中繼資料從來源複製到目的地資料庫。
- 將所有物件的所有結構描述從來源複製到目的地資料庫。
- 將所有索引的統計資料從來源複製到目的地資料庫。

> [!NOTE]  
> 從 DBCC CLONEDATABASE 產生的新資料庫主要是為了疑難排解和診斷之用。  若要支援複製資料庫用作生產資料庫，則必須使用 VERIFY_CLONEDB 選項。

目標資料庫中的所有檔案都會從模型資料庫繼承大小和成長設定。 目的地資料庫的檔案名稱會遵循 source_file_name _underscore_random number 慣例。 如果產生的檔案名稱已經存在於目的地資料夾中，則 DBCC CLONEDATABASE 將會失敗。

如果模型資料庫中已建立任何使用者物件 (資料表、索引、結構描述、角色等)，則 DBCC CLONEDATABASE 不支援建立複本。 如果模型資料庫中有使用者物件，資料庫複製會失敗，並出現下列錯誤訊息：

```
Msg 2601, Level 14, State 1, Line 1
Cannot insert duplicate key row in object <system table> with unique index 'index name'. The duplicate key value is <key value>   
```

> [!IMPORTANT]
> 如果您有資料行存放區索引，請參閱 [使用克隆數據庫上的Columnstore索引調優查詢時的注意事項](https://techcommunity.microsoft.com/t5/SQL-Server/Considerations-when-tuning-your-queries-with-columnstore-indexes/ba-p/385294) (在複製資料庫上使用資料行存放區索引調整查詢時的考量) 更新資料行存放區索引統計資料，再執行 **DBCC CLONEDATABASE** 命令。  從 SQL Server 2019 開始，將不再需要上述文章中所述的手動步驟，因為 **DBCC CLONEDATABASE** 命令會自動收集此資訊。

<a name="ctp23"></a>

## <a name="stats-blob-for-columnstore-indexes"></a>資料行存放區索引的統計資料 Blob

[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 的 `DBCC CLONEDATABASE` 會自動擷取資料行存放區索引的統計資料 Blob，因此不需要任何手動步驟。`DBCC CLONEDATABASE` 建立資料庫的僅限結構描述複本，其中包含為查詢效能問題進行疑難排解所需的所有元素，而不需複製資料。 在舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，此命令不會複製準確疑難排解資料行存放區索引查詢所需的統計資料，而且需要手動步驟才能擷取這項資訊。

如需複製資料庫上資料安全性的相關資訊，請參閱 [Understanding data security in cloned databases](https://techcommunity.microsoft.com/t5/SQL-Server/Understanding-data-security-in-cloned-databases-created-using/ba-p/385287) (了解複製資料庫中的資料安全性)。

## <a name="internal-database-snapshot"></a>內部資料庫快照集
DBCC CLONEDATABASE 使用來源資料庫的內部資料庫快照集，以取得執行複製所需的交易一致性。 使用此快照集可以防止在執行這些命令時，發生封鎖和並行問題。 如果無法建立快照集，DBCC CLONEDATABASE 將會失敗。 

在複製程序的下列步驟期間會保留資料庫層級鎖定：
- 驗證來源資料庫
- 取得來源資料庫的 S 鎖定
- 建立來源資料庫的快照集
- 建立複製資料庫 (繼承自模型資料庫的空白資料庫)
- 取得複製資料庫的 X 鎖定
- 將中繼資料複製到複製資料庫
- 釋放所有 DB 鎖定

一旦命令完成執行，就會卸除內部快照集。 並關閉複製資料庫上的 TRUSTWORTHY 和 DB_CHAINING 選項。 

## <a name="supported-objects"></a>支援的物件
您只能將下列物件複製到目的地資料庫。 加密物件會被複製，但無法在複製資料庫中使用。 下一節中未列出的任何物件都不支援複製： 
- APPLICATION ROLE
- AVAILABILITY GROUP
- COLUMNSTORE INDEX
- CDB
- CDC
- CLR (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 CU3、[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 和更新版本開始)
- DATABASE PROPERTIES
- DEFAULT
- FILES AND FILEGROUPS
- 全文檢索 (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 CU2 開始)
- FUNCTION
- INDEX
- LOGIN
- PARTITION FUNCTION
- PARTITION SCHEME
- PROCEDURE   
> [!NOTE]   
> 從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 開始，所有版本都支援 [!INCLUDE[tsql](../../includes/tsql-md.md)] 程序。 從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 CU3 開始，支援 CLR 程序。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 開始，支援原生編譯程序。  

- QUERY STORE (從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 開始)   
> [!NOTE]   
> 只有在來源資料庫上啟用時，才能複製查詢存放區資料。 若要將最新的執行階段統計資料當作查詢存放區的一部分來複製，請執行 sp_query_store_flush_db 將執行階段統計資料排清到查詢存放區，再執行 DBCC CLONEDATABASE。  

- ROLE
- RULE
- SCHEMA
- SEQUENCE
- SPATIAL INDEX
- STATISTICS
- SYNONYM
- TABLE
- MEMORY OPTIMIZED TABLES (僅限 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 和更新版本)。
- FILESTREAM AND FILETABLE OBJECTS (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 CU3、[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 和更新版本開始)。 
- TRIGGER
- TYPE
- UPGRADED DB
- USER
- VIEW
- XML INDEX
- XML SCHEMA COLLECTION  

## <a name="permissions"></a>權限  
需要 **系統管理員 (sysadmin)** 固定伺服器角色中的成員資格。

## <a name="error-log-messages"></a>錯誤記錄檔訊息
下列訊息是複製程序期間記錄到錯誤記錄檔中的訊息範例：

```
2018-03-26 15:33:56.05 spid53 Database cloning for 'sourcedb' has started with target as 'sourcedb_clone'.

2018-03-26 15:33:56.46 spid53 Starting up database 'sourcedb_clone'.

2018-03-26 15:33:57.80 spid53 Setting database option TRUSTWORTHY to OFF for database 'sourcedb_clone'.

2018-03-26 15:33:57.80 spid53 Setting database option DB_CHAINING to OFF for database 'sourcedb_clone'.

2018-03-26 15:33:57.88 spid53 Starting up database 'sourcedb_clone'.

2018-03-26 15:33:57.91 spid53 Database 'sourcedb_clone' is a cloned database. A cloned database should be used for diagnostic purposes only and is not supported for use in a production environment.

2018-03-26 15:33:57.92 spid53 Database cloning for 'sourcedb' has finished. Cloned database is 'sourcedb_clone'.
```

## <a name="database-properties"></a>資料庫屬性
如果已使用 DBCC CLONEDATABASE 產生資料庫，`DATABASEPROPERTYEX('dbname', 'IsClone')` 會傳回 1。

如果已使用 WITH VERIFY_CLONEDB 成功驗證資料庫，`DATABASEPROPERTYEX('dbname', 'IsVerifiedClone')` 會傳回 1。

## <a name="examples"></a>範例  
  
### <a name="a-creating-a-clone-of-a-database-that-includes-schema-statistics-and-query-store"></a>A. 建立資料庫複本，其中包含結構描述、統計資料和查詢存放區 
下列範例會建立 AdventureWorks 資料庫複本，其中包含結構描述、統計資料和查詢存放區資料 ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 和更新版本)

```sql  
DBCC CLONEDATABASE (AdventureWorks, AdventureWorks_Clone);    
GO 
```  
  
### <a name="b-creating-a-schema-only-clone-of-a-database-without-statistics"></a>B. 建立僅限結構描述的資料庫複本，其中不含統計資料 
下列範例會建立 AdventureWorks 資料庫複本，其中不含統計資料 ([!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 CU3 和更新版本)

```sql  
DBCC CLONEDATABASE (AdventureWorks, AdventureWorks_Clone) WITH NO_STATISTICS;    
GO 
```  

### <a name="c-creating-a-schema-only-clone-of-a-database-without-statistics-and-query-store"></a>C. 建立僅限結構描述的資料庫複本，其中不含統計資料和查詢存放區 
下列範例會建立 AdventureWorks 資料庫複本，其中不含統計資料和查詢存放區資料 ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 和更新版本)

```sql  
DBCC CLONEDATABASE (AdventureWorks, AdventureWorks_Clone) WITH NO_STATISTICS, NO_QUERYSTORE;    
GO 
```  

### <a name="d-creating-a-clone-of-a-database-that-is-verified-for-production-use"></a>D. 建立資料庫複本，並已經過驗證可用於生產環境
下列範例會建立僅限結構描述的 AdventureWorks 資料庫複本，其中不含統計資料和查詢存放區，並已經過驗證可用作生產資料庫 ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 和更新版本)。

```sql  
DBCC CLONEDATABASE (AdventureWorks, AdventureWorks_Clone) WITH VERIFY_CLONEDB;    
GO 
```  
  
### <a name="e-creating-a-clone-of-a-database-that-is-verified-for-production-use-that-includes-a-backup-of-the-cloned-database"></a>E. 建立資料庫複本，並已經過驗證可用於生產環境，其中包含複製資料庫的備份
下列範例會建立僅限結構描述的 AdventureWorks 資料庫複本，其中不含統計資料和查詢存放區，並已經過驗證可用作生產資料庫。  同時會建立已驗證複製資料庫的備份 ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 和更新版本)。

```sql  
DBCC CLONEDATABASE (AdventureWorks, AdventureWorks_Clone) WITH VERIFY_CLONEDB, BACKUP_CLONEDB;    
GO 
```

## <a name="see-also"></a>另請參閱
[DBCC &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-transact-sql.md)    
[How to generate a script of the necessary database metadata to create a statistics-only database in SQL Server](https://support.microsoft.com/help/914288) (如何產生必要資料庫中繼資料的指令碼，以在 SQL Server 中建立僅限統計資料的資料庫)   

