---
description: DATABASEPROPERTYEX (Transact-SQL)
title: DATABASEPROPERTYEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/23/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DATABASEPROPERTYEX
- DATABASEPROPERTYEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- DATABASEPROPERTYEX function
- displaying database properties
- database properties [SQL Server]
ms.assetid: 8a9e0ffb-28b5-4640-95b2-a54e3e5ad941
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 096d215aa0962b3a580cdd43a337075aab638964
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171140"
---
# <a name="databasepropertyex-transact-sql"></a>DATABASEPROPERTYEX (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的指定資料庫，此函式會傳回指定資料庫選項或屬性的目前設定。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DATABASEPROPERTYEX ( database , property )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*database*  
指定資料庫名稱的運算式，其 `DATABASEPROPERTYEX` 會傳回具名屬性資訊。 *database* 具有 **nvarchar(128)** 資料類型。  

若是 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]，`DATABASEPROPERTYEX` 需要目前資料庫的名稱。 如果指定了不同的資料庫名稱，它會對所有屬性傳回 NULL。
  
*property*  
指定要傳回之資料庫屬性名稱的運算式。 *property* 具有 **varchar(128)** 資料類型，並支援此資料表中的其中一個值：
  
> [!NOTE]  
>  如果資料庫尚未啟動，且 `DATABASEPROPERTYEX` 透過直接存取資料庫來擷取這些值，而不是從中繼資料擷取，則呼叫 `DATABASEPROPERTYEX` 會傳回 NULL。 AUTO_CLOSE 設定為 ON 或已離線的資料庫會定義為「未啟動」。  
  
|屬性|描述|傳回的值|  
|---|---|---|
|定序|資料庫的預設定序名稱。|定序名稱<br /><br /> NULL：資料庫未啟動。<br /><br /> 基底資料型別：**nvarchar(128)**|  
|ComparisonStyle|Windows 的定序比較樣式。 使用下列樣式值為完成的 ComparisonStyle 值建立點陣圖：<br /><br /> 忽略大小寫：1<br /><br /> 忽略腔調字：2<br /><br /> 忽略假名：65536<br /><br /> 忽略寬度：131072<br /><br /> <br /><br /> 例如，預設值 196609 是結合「忽略大小寫」、「忽略假名」和「忽略寬度」等選項的結果。|傳回比較樣式。<br /><br /> 所有的二進位定序皆傳回 0。<br /><br /> 基底資料類型：**int**|  
|版本|資料庫版本或服務層。|**適用於**：[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]、[!INCLUDE[ssSDW](../../includes/sssdw-md.md)]。<br /><br /> <br /><br /> 一般用途<br /><br /> 業務關鍵<br /><br /> 基本<br /><br /> 標準<br /><br /> Premium<br /><br /> 系統 (適用於 master 資料庫)<br /><br /> NULL：資料庫未啟動。<br /><br /> 基底資料型別：**nvarchar**(64)|  
|IsAnsiNullDefault|資料庫遵照允許 Null 值的 ISO 規則。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsAnsiNullsEnabled|所有對於 Null 的比較，都會得出「未知」。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsAnsiPaddingEnabled|字串在進行比較或插入處理之前，先填補至相同的長度。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsAnsiWarningsEnabled|當發生標準錯誤狀況時，SQL Server 會發出錯誤或警告訊息。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsArithmeticAbortEnabled|在查詢執行期間，當發生溢位或除以零的錯誤時，查詢會停止。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsAutoClose|在最後一個使用者結束之後，資料庫完整關機並釋出資源。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsAutoCreateStatistics|查詢最佳化工具會視需要建立單一資料行統計資料來改善查詢效能。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsAutoCreateStatisticsIncremental|自動建立的單一資料行統計資料會累加 (如果可能)。|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsAutoShrink|資料庫檔案是自動定期壓縮的候選項。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsAutoUpdateStatistics|當查詢使用可能已過期的現有統計資料時，查詢最佳化工具就會更新這些統計資料。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|
|IsClone|資料庫僅為使用 DBCC CLONEDATABASE 所建立之使用者資料庫的結構描述和統計資料複本。 如需詳細資訊，請參閱 [Microsoft 支援服務文章](https://support.microsoft.com/help/3177838)。|**適用於**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 及更新版本。<br /><br /> 1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**| 
|IsCloseCursorsOnCommitEnabled|當交易認可時，所有開啟的資料指標都會關閉。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsFulltextEnabled|資料庫已啟用全文檢索和語意索引。|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> <br /><br /> 1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**<br /><br /> **注意：** 此屬性的值現在沒有任何作用。 使用者資料庫一定會啟用全文檢索搜尋。 未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本將會移除這個屬性。 請勿在新的開發工作中使用此屬性，並且盡快修改使用此屬性的應用程式。|  
|IsInStandBy|資料庫在線上唯讀，允許還原記錄。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsLocalCursorsDefault|資料指標宣告預設為 LOCAL。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsMemoryOptimizedElevateToSnapshotEnabled|當工作階段設定 TRANSACTION ISOLATION LEVEL 設為 READ COMMITTED、READ UNCOMMITTED 或較低的隔離等級時，會使用 SNAPSHOT 隔離存取經記憶體最佳化的資料表。|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> <br /><br /> 1：TRUE<br /><br /> 0：FALSE<br /><br /> 基底資料類型：**int**|  
|IsMergePublished|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援合併式複寫的資料庫資料表發行集 (若已安裝複寫功能)。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsNullConcat|Null 串連運算元產生 NULL。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsNumericRoundAbortEnabled|當運算式發生遺失有效位數的情形時，則產生錯誤。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsParameterizationForced|PARAMETERIZATION 資料庫 SET 選項是 FORCED。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效|  
|IsQuotedIdentifiersEnabled|識別碼允許有雙引號。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsPublished|若已安裝複寫功能，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援快照集或異動複寫的資料庫資料表發行集。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsRecursiveTriggersEnabled|啟用觸發程序的遞迴引發。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsSubscribed|將資料庫訂閱到發行集中。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsSyncWithBackup|該資料庫是已發行的資料庫或散發資料庫，並支援在不干擾異動複寫的情況下進行還原。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**|  
|IsTornPageDetectionEnabled|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 偵測到因為斷電或其他系統失效所造成的不完全 I/O 作業。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**| 
|IsVerifiedClone|資料庫僅為使用 DBCC CLONEDATABASE 之 WITH VERIFY_CLONEDB 選項所建立之使用者資料庫的結構描述和統計資料複本。 如需詳細資訊，請參閱此 [Microsoft 支援服務文章](https://support.microsoft.com/help/3177838)。|**適用於**：從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP2 開始。<br /><br /> <br /><br /> 1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效<br /><br /> 基底資料類型：**int**| 
|IsXTPSupported|指出資料庫是否支援記憶體內部 OLTP，即建立及使用經記憶體最佳化的資料表和原生編譯模組。<br /><br /> 特定於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]：<br /><br /> IsXTPSupported 是獨立於任何建立記憶體內部 OLTP 物件所必須之 MEMORY_OPTIMIZED_DATA 檔案群組的存在之外。|**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。<br /><br /> 1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：輸入無效、發生錯誤或不適用<br /><br /> 基底資料類型：**int**|  
|LastGoodCheckDbTime|最後一個成功 DBCC CHECKDB 在指定的資料庫上執行的日期和時間。<sup>1</sup> 如果 DBCC CHECKDB 尚未在資料庫上執行，則回傳回 1900-01-01 00:00:00.000。|**適用於**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] (從 SP2 開始)。</br>[!INCLUDE[sssqlv14](../../includes/sssqlv14-md.md)] 從 CU9 開始。</br>[!INCLUDE[sssqlv15](../../includes/sssqlv15-md.md)] 或更新版本。</br>Azure SQL Database。<br/><br/>日期時間值<br /><br /> NULL：輸入無效<br /><br /> 基底資料型別：**datetime**| 
|LCID|Windows 的定序地區設定識別碼 (LCID)。|LCID 值 (十進位格式)。<br /><br /> 基底資料類型：**int**|  
|MaxSizeInBytes|資料庫的大小上限 (以位元組為單位)。|**適用於**：[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]、[!INCLUDE[ssSDW](../../includes/sssdw-md.md)]。<br /><br />[Azure SQL Database 與 Azure Synapse Analytics](/azure/sql-database/sql-database-single-database-scale#dtu-based-purchasing-model) - 值會以 SLO 為基礎，除非已購買額外的儲存體。<br /><br />[vCore](/azure/sql-database/sql-database-single-database-scale#vcore-based-purchasing-model) - 值會以 1GB 為單位遞增，直到大小上限為止。<br /><br />NULL：資料庫未啟動<br /><br /> 基底資料型別：**bigint**|  
|復原|資料庫復原模式|FULL：完整復原模式<br /><br /> BULK_LOGGED：大量記錄模式<br /><br /> SIMPLE：簡單復原模式<br /><br /> 基底資料型別：**nvarchar(128)**|  
|ServiceObjective|描述 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 或 [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 中資料庫的效能層級。|下列其中之一：<br /><br /> Null：資料庫尚未啟動<br /><br /> 共用 (適用於 Web/Business 版本)<br /><br /> 基本<br /><br /> S0<br /><br /> S1<br /><br /> S2<br /><br /> S3<br /><br /> P1<br /><br /> P2<br /><br /> P3<br /><br /> ElasticPool<br /><br /> 系統 (適用於 master 資料庫)<br /><br /> 基底資料型別：**nvarchar(32)**|  
|ServiceObjectiveId|[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 中服務目標的識別碼。|識別服務目標的 **uniqueidentifier**。|  
|SQLSortOrder|舊版 SQL Server 中所支援的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 排序順序識別碼。|0：資料庫使用 Windows 定序<br /><br /> >0：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 排序順序識別碼<br /><br /> NULL：輸入無效或資料庫尚未啟動<br /><br /> 基底資料型別：**tinyint**|  
|狀態|資料庫狀態。|ONLINE：資料庫可用於查詢。<br /><br /> **注意：** 當資料庫開啟且尚未復原時，函式可能會傳回 ONLINE 狀態。 若要識別 ONLINE 資料庫是否可接受連線，請查詢 **DATABASEPROPERTYEX** 的 Collation 屬性。 當 ONLINE 資料庫定序傳回非 Null 值時，表示該資料庫可接受連線。 對於 AlwaysOn 資料庫，可查詢 `sys.dm_hadr_database_replica_states` 的 database_state 或 database_state_desc 資料行。<br /><br /> OFFLINE：資料庫明確離線。<br /><br /> RESTORING：資料庫還原已啟動。<br /><br /> RECOVERING：資料庫復原已啟動，但資料庫尚未準備好提供查詢。<br /><br /> SUSPECT：資料庫未復原。<br /><br /> EMERGENCY：資料庫處於緊急、唯讀的狀態。 存取限於系統管理員 (sysadmin) 成員<br /><br /> 基底資料型別：**nvarchar(128)**|  
|Updateability|指出資料是否可以修改。|READ_ONLY：資料庫支援資料讀取，但不支援資料修改。<br /><br /> READ_WRITE：資料庫支援資料讀取和修改。<br /><br /> 基底資料型別：**nvarchar(128)**|  
|UserAccess|指定那些使用者可以存取資料庫。|SINGLE_USER：一次只有一個 db_owner、資料庫建立者 (dbcreator) 或系統管理員使用者<br /><br /> RESTRICTED_USER：只有 db_owner、資料庫建立者 (dbcreator) 或系統管理員角色的成員<br /><br /> MULTI_USER：所有使用者<br /><br /> 基底資料型別：**nvarchar(128)**|  
|版本|建立資料庫所用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 程式碼的內部版本號碼。 [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|版本號碼：資料庫是開啟的。<br /><br /> NULL：資料庫尚未啟動。<br /><br /> 基底資料類型：**int**| 

<br/>   

> [!NOTE]  
> <sup>1</sup> 對於屬於可用性群組一部分的資料庫，`LastGoodCheckDbTime` 將會傳回最後一個成功 DBCC CHECKDB 在主要複本上執行的日期和時間，而不論您是從哪一個複本執行命令。 

## <a name="return-types"></a>傳回類型
**sql_variant**
  
## <a name="exceptions"></a>例外狀況  
發生錯誤或呼叫端沒有檢視物件的權限時，會傳回 NULL。
  
在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，使用者只能檢視使用者擁有或被授與某些權限之安全性實體的中繼資料。 這表示，如果使用者沒有物件的權限，則發出中繼資料的內建函式 (例如 `OBJECT_ID`) 可能會傳回 NULL。 如需詳細資訊，請參閱[中繼資料可見性設定](../../relational-databases/security/metadata-visibility-configuration.md)。
  
## <a name="remarks"></a>備註  
`DATABASEPROPERTYEX` 一次只傳回一個屬性設定。 若要顯示多個屬性設定，請使用 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視。
  
## <a name="examples"></a>範例  
  
### <a name="a-retrieving-the-status-of-the-auto_shrink-database-option"></a>A. 擷取 AUTO_SHRINK 資料庫選項的狀態  
此範例會傳回 `AdventureWorks` 資料庫 AUTO_SHRINK 資料庫選項的狀態。
  
```sql
SELECT DATABASEPROPERTYEX('AdventureWorks2014', 'IsAutoShrink');  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)] 這表示 AUTO_SHRINK 是關閉狀態。
  
```
------------------  
0  
```  
  
### <a name="b-retrieving-the-default-collation-for-a-database"></a>B. 擷取資料庫的預設定序  
此範例會傳回 `AdventureWorks` 資料庫的多個屬性。
  
```sql
SELECT   
    DATABASEPROPERTYEX('AdventureWorks2014', 'Collation') AS Collation,  
    DATABASEPROPERTYEX('AdventureWorks2014', 'Edition') AS Edition,  
    DATABASEPROPERTYEX('AdventureWorks2014', 'ServiceObjective') AS ServiceObjective,  
    DATABASEPROPERTYEX('AdventureWorks2014', 'MaxSizeInBytes') AS MaxSizeInBytes  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Collation                     Edition        ServiceObjective  MaxSizeInBytes  
----------------------------  -------------  ----------------  --------------  
SQL_Latin1_General_CP1_CI_AS  DataWarehouse  DW1000            5368709120  
```  
  
## <a name="see-also"></a>另請參閱
[ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)  
[資料庫狀態](../../relational-databases/databases/database-states.md)  
[sys.databases &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)  
[sys.database_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md)  
[SERVERPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/serverproperty-transact-sql.md)
  
  
