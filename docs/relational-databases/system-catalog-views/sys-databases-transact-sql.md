---
description: sys.databases (Transact-SQL)
title: sys. 資料庫 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/08/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- databases
- databases_TSQL
- sys.databases
- sys.databases_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.databases catalog view
ms.assetid: 46c288c1-3410-4d68-a027-3bbf33239289
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5794b2720340fac295720ac2a4ab05a7906a9226
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093158"
---
# <a name="sysdatabases-transact-sql"></a>sys.databases (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體中的每個資料庫，各包含一個資料列。  
  
如果資料庫不是 `ONLINE` ，或 `AUTO_CLOSE` 設定為， `ON` 而且資料庫已關閉，則某些資料行的值可能為 `NULL` 。 如果資料庫為 `OFFLINE` ，低許可權的使用者就看不到對應的資料列。 若要在資料庫為時查看對應的資料列 `OFFLINE` ，使用者至少必須擁有 `ALTER ANY DATABASE` 伺服器層級許可權，或 `CREATE DATABASE` 資料庫中的許可權 `master` 。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|資料庫的名稱，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體或 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]伺服器內是唯一的。|  
|**database_id**|**int**|資料庫的識別碼，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體或 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]伺服器內是唯一的。|  
|**source_database_id**|**int**|Non-NULL = 這個資料庫快照集的來源資料庫識別碼。<br /> NULL = 不是資料庫快照集。|  
|**owner_sid**|**varbinary(85)**|資料庫外部擁有者的 SID (安全性識別碼)，亦即在伺服器註冊所用的識別碼。 如需誰可以擁有資料庫的詳細資訊，請參閱 [ALTER authorization](../../t-sql/statements/alter-authorization-transact-sql.md)的 **alter authorization for** database 一節。|  
|**create_date**|**datetime**|資料庫建立或重新命名的日期。 針對 **tempdb**，此值會在每次伺服器重新開機時變更。|  
|**compatibility_level**|**tinyint**|對應於與行為相容之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本的整數：<br /><br /><table border="0"><tr><td>**值**</td><td>**適用於**</td></tr><tr><td>70</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 7.0 到 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]</td></tr><tr><td>80</td><td>[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)] 通過 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)]</td></tr><tr><td>90</td><td>[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 通過 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]</td></tr><tr><td>100</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr><tr><td>110</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr><tr><td>120</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr><tr><td>130</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr><tr><td>140</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr><tr><td>150</td><td>[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]</td></tr></table>|  
|**collation_name**|**sysname**|資料庫的定序。 它是資料庫中的預設定序。<br /> NULL = 資料庫不在線上，或者 AUTO_CLOSE 設為 ON 且資料庫已關閉。|  
|**user_access**|**tinyint**|使用者存取設定：<br /> 0 = 指定了 MULTI_USER<br /> 1 = 指定了 SINGLE_USER<br /> 2 = 指定了 RESTRICTED_USER|  
|**user_access_desc**|**nvarchar(60)**|使用者存取設定的描述。|  
|**is_read_only**|**bit**|1 = 資料庫是 READ_ONLY<br /> 0 = 資料庫是 READ_WRITE|  
|**is_auto_close_on**|**bit**|1 = AUTO_CLOSE 是 ON<br /> 0 = AUTO_CLOSE 是 OFF|  
|**is_auto_shrink_on**|**bit**|1 = AUTO_SHRINK 是 ON<br /> 0 = AUTO_SHRINK 是 OFF|  
|**state**|**tinyint**|**值**<br /> 0 = ONLINE  <br /> 1 = RESTORING <br /> 2 = 復原 <sup>1</sup><br /> 3 = RECOVERY_PENDING <sup>1</sup><br /> 4 = SUSPECT <br /> 5 = 緊急 <sup>1</sup><br /> 6 = 離線 <sup>1</sup><br /> 7 = 複製 <sup>2</sup> <br /> 10 = OFFLINE_SECONDARY <sup>2</sup> <br /><br /> **注意：** 針對 Always On 資料庫，查詢 `database_state` sys.dm_hadr_database_replica_states 的或資料 `database_state_desc` 行。 [](../../relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql.md)<br /><br /><sup>1</sup> **適用** 于： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從) 開始 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)][!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]<br /><sup>2</sup> **適用** 于： [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)][!INCLUDE[ssGeoDR](../../includes/ssgeodr-md.md)]|  
|**state_desc**|**nvarchar(60)**|資料庫狀態的描述。 請參閱狀態。|  
|**is_in_standby**|**bit**|還原記錄的資料庫是唯讀資料庫。|  
|**is_cleanly_shutdown**|**bit**|1 = 資料庫完全關閉；不必在啟動時復原<br /> 0 = 資料庫並未完全關閉；必須在啟動時復原|  
|**is_supplemental_logging_enabled**|**bit**|1 = SUPPLEMENTAL_LOGGING 是 ON<br /> 0 = SUPPLEMENTAL_LOGGING 是 OFF|  
|**snapshot_isolation_state**|**tinyint**|允許進行的快照集隔離交易狀態，由 ALLOW_SNAPSHOT_ISOLATION 選項設定：<br /> 0 = 快照集隔離狀態是 OFF (預設值)。 不接受快照集隔離。<br /> 1 = 快照集隔離狀態是 ON。 接受快照集隔離。<br /> 2 = 快照集隔離狀態正轉移為 OFF 狀態。 所有的交易都把自己的修改版本化了。 新交易無法利用快照集隔離加以啟動。 資料庫仍然保持在轉移為 OFF 狀態的過渡時期，必須等到在執行 ALTER DATABASE 時，所有使用中的交易可以完成為止。<br /> 3 = 快照集隔離狀態正轉移為 ON 狀態。 新交易都把自己的修改版本化了。 除非快照集隔離狀態變成 1 (ON)，否則交易無法使用快照集隔離。 資料庫仍然保持在轉移為 ON 狀態的過渡時期，必須等到在執行 ALTER DATABASE 時，所有使用中的更新交易可以完成為止。|  
|**snapshot_isolation_state_desc**|**nvarchar(60)**|允許進行的快照集隔離交易狀態的描述，由 ALLOW_SNAPSHOT_ISOLATION 選項設定。|  
|**is_read_committed_snapshot_on**|**bit**|1 = READ_COMMITTED_SNAPSHOT 選項為 ON。 讀取認可隔離等級下的讀取作業是以快照集掃描為基礎，沒有取得鎖定。<br /> 0 = READ_COMMITTED_SNAPSHOT 選項為 OFF (預設)。 讀取認可隔離等級下的讀取作業是使用共用鎖定。|  
|**recovery_model**|**tinyint**|所選的復原模式：<br /> 1 = FULL<br /> 2 = BULK_LOGGED<br /> 3 = SIMPLE|  
|**recovery_model_desc**|**nvarchar(60)**|所選之復原模式的描述。|  
|**page_verify_option**|**tinyint**|PAGE_VERIFY 選項的設定：<br /> 0 = NONE<br /> 1 = TORN_PAGE_DETECTION<br /> 2 = CHECKSUM|  
|**page_verify_option_desc**|**nvarchar(60)**|PAGE_VERIFY 選項設定的描述。|  
|**is_auto_create_stats_on**|**bit**|1 = AUTO_CREATE_STATISTICS 是 ON<br /> 0 = AUTO_CREATE_STATISTICS 是 OFF|  
|**is_auto_create_stats_incremental_on**|**bit**|表示 Auto Stats 之累加選項的預設設定。<br /> 0 = 自動建立非累加的統計資料<br /> 1 = 盡可能自動建立累加的統計資料<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 起)。|  
|**is_auto_update_stats_on**|**bit**|1 = AUTO_UPDATE_STATISTICS 是 ON<br /> 0 = AUTO_UPDATE_STATISTICS 是 OFF|  
|**is_auto_update_stats_async_on**|**bit**|1 = AUTO_UPDATE_STATISTICS_ASYNC 是 ON<br /> 0 = AUTO_UPDATE_STATISTICS_ASYNC 是 OFF|  
|**is_ansi_null_default_on**|**bit**|1 = ANSI_NULL_DEFAULT 是 ON<br /> 0 = ANSI_NULL_DEFAULT 是 OFF|  
|**is_ansi_nulls_on**|**bit**|1 = ANSI_NULLS 是 ON<br /> 0 = ANSI_NULLS 是 OFF|  
|**is_ansi_padding_on**|**bit**|1 = ANSI_PADDING 是 ON<br /> 0 = ANSI_PADDING 是 OFF|  
|**is_ansi_warnings_on**|**bit**|1 = ANSI_WARNINGS 是 ON<br /> 0 = ANSI_WARNINGS 是 OFF|  
|**is_arithabort_on**|**bit**|1 = ARITHABORT 是 ON<br /> 0 = ARITHABORT 是 OFF|  
|**is_concat_null_yields_null_on**|**bit**|1 = CONCAT_NULL_YIELDS_NULL 是 ON<br /> 0 = CONCAT_NULL_YIELDS_NULL 是 OFF|  
|**is_numeric_roundabort_on**|**bit**|1 = NUMERIC_ROUNDABORT 是 ON<br /> 0 = NUMERIC_ROUNDABORT 是 OFF|  
|**is_quoted_identifier_on**|**bit**|1 = QUOTED_IDENTIFIER 是 ON<br /> 0 = QUOTED_IDENTIFIER 是 OFF|  
|**is_recursive_triggers_on**|**bit**|1 = RECURSIVE_TRIGGERS 是 ON<br /> 0 = RECURSIVE_TRIGGERS 是 OFF|  
|**is_cursor_close_on_commit_on**|**bit**|1 = CURSOR_CLOSE_ON_COMMIT 是 ON<br /> 0 = CURSOR_CLOSE_ON_COMMIT 是 OFF|  
|**is_local_cursor_default**|**bit**|1 = CURSOR_DEFAULT 是區域<br /> 0 = CURSOR_DEFAULT 是全域|  
|**is_fulltext_enabled**|**bit**|1 = 資料庫啟用全文檢索<br /> 0 = 資料庫停用全文檢索|  
|**is_trustworthy_on**|**bit**|1 = 資料庫已被標示為可信任<br /> 0 = 資料庫尚未標示為可信任<br /> 依預設，還原或附加的資料庫會有 [信任的未啟用]。|  
|**is_db_chaining_on**|**bit**|1 = 跨資料庫擁有權鏈結是 ON<br /> 0 = 跨資料庫擁有權鏈結是 OFF|  
|**is_parameterization_forced**|**bit**|1 = 參數化是 FORCED<br /> 0 = 參數化是 SIMPLE|  
|**is_master_key_encrypted_by_server**|**bit**|1 = 資料庫具有已加密的主要金鑰<br /> 0 = 資料庫沒有已加密的主要金鑰|  
|**is_query_store_on**|**bit**|1 = 此資料庫的查詢存放區已啟用。 檢查 [sys.database_query_store_options](../../relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql.md) 以查看查詢存放區的狀態。<br /> 0 = 未啟用查詢存放區<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 起)。|  
|**is_published**|**bit**|1 = 資料庫是交易式或快照式複寫拓撲的發行集資料庫<br /> 0 = 不是發行集資料庫|  
|**is_subscribed**|**bit**|不使用這個資料行。 不論資料庫的訂閱者狀態為何，它一定會傳回 0。|  
|**is_merge_published**|**bit**|1 = 資料庫是合併式複寫拓撲的發行集資料庫<br /> 0 = 不是合併式複寫拓撲的發行集資料庫|  
|**is_distributor**|**bit**|1 = 資料庫是複寫拓撲的散發資料庫<br /> 0 = 不是複寫拓撲的散發資料庫|  
|**is_sync_with_backup**|**bit**|1 = 資料庫是標示為利用備份進行複寫同步處理<br /> 0 = 不是標示為利用備份進行複寫同步處理|  
|**service_broker_guid**|**uniqueidentifier**|這個資料庫的 Service Broker 識別碼。 它是作為路由表中目標的 **broker_instance**。|  
|**is_broker_enabled**|**bit**|1 = 這個資料庫中的 Broker，目前正在收送訊息。<br /> 0 = 所有傳送的訊息，都會停留在傳輸佇列中，而收到的訊息並不會置於這個資料庫的佇列中。<br /> 依預設，還原或附加的資料庫都會停用 Broker。 但資料庫鏡像例外，它會在容錯移轉之後啟用 Broker。|  
|**log_reuse_wait**|**tinyint**|重複使用交易記錄空間目前正在等候下列其中一個檢查點。 如需這些值的詳細說明，請參閱 [交易記錄](../../relational-databases/logs/the-transaction-log-sql-server.md)。<br /> **值**<br /> 0 = 無<br /> 1 = 當資料庫使用復原模式而且擁有記憶體優化資料檔案群組時，檢查點 (，您應該會看到資料 `log_reuse_wait` 行指出 `checkpoint` 或 `xtp_checkpoint`) <sup>1</sup><br /> 2 = 記錄備份 <sup>1</sup><br /> 3 = 主動備份或還原 <sup>1</sup><br /> 4 = 主動交易 <sup>1</sup><br /> 5 = 資料庫鏡像 <sup>1</sup><br /> 6 = 複寫 <sup>1</sup><br /> 7 = 資料庫快照集建立 <sup>1</sup><br /> 8 = 記錄掃描 <br /> 9 = Always On 可用性群組次要複本正在將這個資料庫的交易記錄檔記錄套用到對應的次要資料庫。 <sup>2</sup><br /> 9 = 其他 (暫時性) <sup>3</sup><br /> 10 = 僅供內部使用 <sup>2</sup><br /> 11 = 僅供內部使用 <sup>2</sup><br /> 12 = 僅供內部使用 <sup>2</sup><br /> 13 = 最舊的頁面 <sup>2</sup><br /> 14 = 其他 <sup>2</sup><br />  16 = XTP_CHECKPOINT (當資料庫使用復原模式而且擁有記憶體優化資料檔案群組時，您應該會看到資料 `log_reuse_wait` 行指出 `checkpoint` 或 `xtp_checkpoint`) <sup>4</sup><br /><br /><sup>1</sup> **適用** 于： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]) 開始<br /><sup>2</sup> **適用** 于： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]) 開始<br /><sup>3</sup> **適用** 于： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (最多，並包括 [!INCLUDE[ssKilimanjaro](../../includes/ssKilimanjaro-md.md)]) <br /><sup>4</sup> **適用** 于： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) 開始|  
|**log_reuse_wait_desc**|**nvarchar(60)**|描述交易記錄空間的重複利用正等待最後一個檢查點。|  
|**is_date_correlation_on**|**bit**|1 = DATE_CORRELATION_OPTIMIZATION 是 ON<br /> 0 = DATE_CORRELATION_OPTIMIZATION 是 OFF|  
|**is_cdc_enabled**|**bit**|1 = 資料庫已啟用異動資料擷取。 如需詳細資訊，請參閱 [sys.sp_cdc_enable_db &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sys-sp-cdc-enable-db-transact-sql.md)。|  
|**is_encrypted**|**bit**|指出資料庫是否已加密 (反映上次使用子句) 所設定的狀態 `ALTER DATABASE SET ENCRYPTION` 。 可以是下列值之一：<br /> 1 = 已加密<br /> 0 = 未加密<br /> 如需資料庫加密的詳細資訊，請參閱[透明資料加密 &#40;TDE&#41;](../../relational-databases/security/encryption/transparent-data-encryption.md)。<br /> 如果資料庫正在進行解密，則會 `is_encrypted` 顯示值0。 您可以使用 [sys.dm_database_encryption_keys](../../relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql.md) 動態管理檢視來查看加密處理常式的狀態。|  
|**is_honor_broker_priority_on**|**bit**|指出資料庫是否接受交談優先權 (反映上次使用子句) 所設定的狀態 `ALTER DATABASE SET HONOR_BROKER_PRIORITY` 。 可以是下列值之一：<br /> 1 = HONOR_BROKER_PRIORITY 為 ON<br /> 0 = HONOR_BROKER_PRIORITY 為 OFF<br /> 依預設，還原或附加的資料庫會有 broker 優先權。|  
|**replica_id**|**uniqueidentifier**|資料庫正在參與之可用性群組 (如果有) 的本機 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]可用性複本的唯一識別碼。<br /> NULL = 資料庫不是可用性群組中可用性複本的一部分。<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**group_database_id**|**uniqueidentifier**|資料庫所參與的 Always On 可用性群組（如果有的話）內資料庫的唯一識別碼（如果有的話）。 在主要複本上和資料庫已加入可用性群組的每個次要複本上，此資料庫的 **group_database_id** 都相同。<br /> NULL = 資料庫不是任何可用性群組中可用性複本的一部分。<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**resource_pool_id**|**int**|對應到這個資料庫之資源集區的識別碼。 這個資源集區會控制可供這個資料庫中記憶體最佳化資料表使用的記憶體總量。<br /> **適用于**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]) 開始|  
|**default_language_lcid**|**smallint**|表示自主資料庫預設語言的地區設定識別碼 (LCID)。<br /> **注意：** 作為 [設定預設語言伺服器設定選項](../../database-engine/configure-windows/configure-the-default-language-server-configuration-option.md) 的功能 `sp_configure` 。 如果是非自主資料庫，這個值是 **null** 。<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**default_language_name**|**nvarchar(128)**|表示自主資料庫的預設語言。<br /> 如果是非自主資料庫，這個值是 **null** 。<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**default_fulltext_language_lcid**|**int**|表示自主資料庫的預設全文檢索語言 (lcid) 的地區設定識別碼。<br /> **注意：** 做為預設 [的預設全文檢索語言伺服器設定選項](../../database-engine/configure-windows/configure-the-default-full-text-language-server-configuration-option.md) 的功能 `sp_configure` 。 如果是非自主資料庫，這個值是 **null** 。<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**default_fulltext_language_name**|**nvarchar(128)**|表示自主資料庫的預設全文檢索語言。<br /> 如果是非自主資料庫，這個值是 **null** 。<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**is_nested_triggers_on**|**bit**|指出自主資料庫是否允許巢狀觸發程序。<br /> 0 = 不允許巢狀觸發程序。<br /> 1 = 允許巢狀觸發程序。<br /> **注意：** 作為 [ [設定的嵌套觸發程式伺服器設定] 選項](../../database-engine/configure-windows/configure-the-nested-triggers-server-configuration-option.md) 的功能 `sp_configure` 。 如果是非自主資料庫，這個值是 **null** 。 如需詳細資訊，請參閱 [sys.configurations &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md) 。<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**is_transform_noise_words_on**|**bit**|指出自主資料庫中是否應該轉換非搜尋字。<br /> 0 = 不應該轉換非搜尋字。<br /> 1 = 應該轉換非搜尋字。<br /> **注意：** 作為轉換非搜尋 [字伺服器設定選項](../../database-engine/configure-windows/transform-noise-words-server-configuration-option.md) 的功能 `sp_configure` 。 如果是非自主資料庫，這個值是 **null** 。 如需詳細資訊，請參閱 [sys.configurations &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md) 。<br /> **適用于**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]) 開始|  
|**two_digit_year_cutoff**|**smallint**|表示 1753 與 9999 之間的數值，代表將二位數年份解譯為四位數年份時的截斷年份 (Cutoff Year)。<br /> **注意：** 作為 [設定兩位數年份的截止伺服器設定選項](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md) 的功能 `sp_configure` 。 如果是非自主資料庫，這個值是 **null** 。 如需詳細資訊，請參閱 [sys.configurations &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md) 。<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**containment**|**Tinyint 非 null**|指示資料庫的內含項目狀態。<br />  0 = 資料庫內含項目已關閉。 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]<br /> 1 = 部分內含專案中的資料庫 **適用** 于： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 從) 開始 ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]|  
|**containment_desc**|**Nvarchar (60) not null**|指示資料庫的內含項目狀態。<br /> NONE = 舊版資料庫 (零個內含項目)<br /> PARTIAL = 部分自主資料庫<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**target_recovery_time_in_seconds**|**int**|復原資料庫的預估時間 (以秒為單位)。 可為 Null。<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**delayed_durability**|**int**|延遲的持久性設定：<br /> 0 = 已停用<br /> 1 = 允許<br /> 2 = 強制<br /> 如需詳細資訊，請參閱[控制交易持久性](../../relational-databases/logs/control-transaction-durability.md)。<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|  
|**delayed_durability_desc**|**nvarchar(60)**|延遲的持久性設定：<br /> DISABLED<br /> ALLOWED<br /> FORCED<br /> **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 起) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|  
|**is_memory_optimized_elevate_to_snapshot_on**|**bit**|當工作階段設定 TRANSACTION ISOLATION LEVEL 設定為較低的隔離等級 READ COMMITTED 或 READ UNCOMMITTED 時，會使用 SNAPSHOT 隔離存取記憶體最佳化的資料表。<br /> 1 = 最低隔離等級為 SNAPSHOT。<br /> 0 = 不提高隔離等級。|  
|**is_federation_member**|**bit**|表示資料庫是否為同盟的成員。<br /> **適用於**：[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|  
|**is_remote_data_archive_enabled**|**bit**|指出資料庫是否已伸展。<br /> 0 = 資料庫未啟用 Stretch。<br /> 1 = 資料庫已啟用 Stretch。<br /> **適用于**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]) 開始<br /> 如需詳細資訊，請參閱 [Stretch Database](../../sql-server/stretch-database/stretch-database.md)。|  
|**is_mixed_page_allocation_on**|**bit**|指出資料庫中的資料表和索引是否可以從混合範圍配置初始頁面。<br /> 0 = 資料庫中的資料表和索引一律會從統一範圍配置初始頁面。<br /> 1 = 資料庫中的資料表和索引可以從混合範圍配置初始頁面。<br /> 如需詳細資訊，請參閱 `SET MIXED_PAGE_ALLOCATION` [ALTER DATABASE SET Options &#40;transact-sql&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)的選項。<br /> **適用于**： [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]) 開始|  
|**is_temporal_history_retention_enabled**|**bit**|指出是否已啟用時態性保留原則清除工作。<br /><br />1 = 已啟用時態保留<br />0 = 已停用時態保留<br />**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|**catalog_collation_type**|**int**|目錄定序設定：<br />0 = DATABASE_DEFAULT<br />2 = SQL_Latin_1_General_CP1_CI_AS<br /> **適用於**：[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|**catalog_collation_type_desc**|**nvarchar(60)**|目錄定序設定：<br />COLLATE<br />SQL_Latin_1_General_CP1_CI_AS<br /> **適用於**：[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|**physical_database_name**|**nvarchar(128)**|若為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，則為資料庫的機構名稱。 若為 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] ，則為伺服器上資料庫的一般識別碼。 <br />**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|**is_result_set_caching_on**|**bit**|指出是否已啟用結果集快取。<br />1 = 結果集快取已啟用<br />0 = 結果集快取已停用<br />**適用** 于： [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] Gen2。 雖然這項功能會推出到所有區域，但請檢查部署至您的實例的版本和最新的 [Azure Synapse 版本](/azure/synapse-analytics/sql-data-warehouse/release-notes-10-0-10106-0) 資訊，並 Gen2 功能可用性的 [升級排程](/azure/synapse-analytics/sql-data-warehouse/gen2-migration-schedule) 。|
|**is_accelerated_database_recovery_on**|**bit**|指出是否已啟用加速資料庫復原 (ADR) 。<br />1 = ADR 已啟用<br />0 = ADR 已停用<br />**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
|**is_tempdb_spill_to_remote_store**|**bit**|指出是否已啟用 tempdb 溢出至遠端存放區。<br />1 = 已啟用<br />0 = 已停用<br />**適用** 于： [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] Gen2。 雖然這項功能會推出到所有區域，但請檢查部署至您的實例的版本和最新的 [Azure Synapse 版本](/azure/synapse-analytics/sql-data-warehouse/release-notes-10-0-10106-0) 資訊，並 Gen2 功能可用性的 [升級排程](/azure/synapse-analytics/sql-data-warehouse/gen2-migration-schedule) 。|
|**is_stale_page_detection_on**|**bit**|指出是否已啟用過時的頁面偵測。<br />1 = 已啟用過時頁面偵測<br />0 = 已停用過時的頁面偵測<br />**適用** 于： [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] Gen2。 雖然這項功能會推出到所有區域，但請檢查部署至您的實例的版本和最新的 [Azure Synapse 版本](/azure/synapse-analytics/sql-data-warehouse/release-notes-10-0-10106-0) 資訊，並 Gen2 功能可用性的 [升級排程](/azure/synapse-analytics/sql-data-warehouse/gen2-migration-schedule) 。|
|**is_memory_optimized_enabled**|**bit**|指出是否已針對資料庫啟用某些 In-Memory 功能，例如 [混合式緩衝集區](../../database-engine/configure-windows/hybrid-buffer-pool.md)。 不會反映 [記憶體內部 OLTP](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)的可用性或設定狀態。 <br />1 = 已啟用記憶體優化功能<br />0 = 已停用記憶體優化功能<br />**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]|
  
## <a name="permissions"></a>權限

 如果的呼叫端 `sys.databases` 不是資料庫的擁有者，而且資料庫不是 `master` 或 `tempdb` ，則查看對應資料列所需的最小許可權是 `ALTER ANY DATABASE` 或 `VIEW ANY DATABASE` 伺服器層級許可權，或 `CREATE DATABASE` 資料庫中的許可權 `master` 。 呼叫端連接的資料庫一律可在中查看 `sys.databases` 。  
  
> [!IMPORTANT]  
> 根據預設，public 角色具有 `VIEW ANY DATABASE` 許可權，允許所有登入查看資料庫資訊。 封鎖登入，使其無法偵測資料庫、 `REVOKE` 的 `VIEW ANY DATABASE` 許可權 `public` ，或個別登入 `DENY` 的 `VIEW ANY DATABASE` 許可權。  
  
## <a name="azure-sql-database-remarks"></a>Azure SQL Database 備註

在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 此視圖中，可在 `master` 資料庫和使用者資料庫中使用。 在 `master` 資料庫中，這個視圖會傳回 `master` 資料庫和伺服器上所有使用者資料庫的資訊。 在使用者資料庫中，這個檢視只會傳回有關目前資料庫和 master 資料庫的資訊。  
  
 使用建立新資料庫所在的 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 伺服器上 `master` 資料庫中的 `sys.databases` 檢視。 資料庫複製開始之後，您可以 `sys.databases` `sys.dm_database_copies` 從目的地伺服器的資料庫查詢和視圖， `master` 以取得有關複製進度的詳細資訊。  
  
## <a name="examples"></a>範例  
  
### <a name="a-query-the-sysdatabases-view"></a>A. 查詢 sys.databases 檢視

下列範例會傳回 view 中可用的一些資料行 `sys.databases` 。  
  
```sql  
SELECT name, user_access_desc, is_read_only, state_desc, recovery_model_desc  
FROM sys.databases;  
```  
  
### <a name="b-check-the-copying-status-in-sssds"></a>B. 檢查 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]中的複製狀態

下列範例會查詢 `sys.databases` 和 `sys.dm_database_copies` views，以傳回資料庫複製作業的相關資訊。  
  
**適用於**：[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql
-- Execute from the master database.  
SELECT a.name, a.state_desc, b.start_date, b.modify_date, b.percent_complete  
FROM sys.databases AS a  
INNER JOIN sys.dm_database_copies AS b ON a.database_id = b.database_id  
WHERE a.state = 7;  
```

### <a name="c-check-the-temporal-retention-policy-status-in-sssds"></a>C. 檢查中的時態性保留原則狀態 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]

下列範例會查詢， `sys.databases` 以傳回是否啟用時態保留清除工作的資訊。 請注意，還原作業的暫時保留預設為停用。 使用 `ALTER DATABASE` 來明確加以啟用。
  
**適用於**：[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]  
  
```sql  
-- Execute from the master database.  
SELECT a.name, a.is_temporal_history_retention_enabled 
FROM sys.databases AS a;
```  
  
## <a name="next-steps"></a>後續步驟

- [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)
- [sys.database_mirroring_witnesses &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/database-mirroring-witness-catalog-views-sys-database-mirroring-witnesses.md)
- [sys.database_recovery_status &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-database-recovery-status-transact-sql.md)
- [資料庫和檔案目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/databases-and-files-catalog-views-transact-sql.md)
- [sys.dm_database_copies &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database.md)  
