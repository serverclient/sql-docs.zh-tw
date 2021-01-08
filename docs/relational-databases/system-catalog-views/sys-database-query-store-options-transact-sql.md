---
title: 'sys.database_query_store_options (Transact-sql) '
description: 'sys.database_query_store_options (Transact-sql) '
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- DATABASE_QUERY_STORE_OPTIONS_TSQL
- DATABASE_QUERY_STORE_OPTIONS
- SYS.DATABASE_QUERY_STORE_OPTIONS_TSQL
- SYS.DATABASE_QUERY_STORE_OPTIONS
dev_langs:
- TSQL
helpviewer_keywords:
- database_query_store_options catalog view
- sys.database_query_store_options catalog view
author: markingmyname
ms.author: maghan
ms.custom: ''
ms.date: 05/27/2020
monikerRange: =azuresqldb-current||>=sql-server-2016||= azure-sqldw-latest||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: f8fce0932d546470206bbc7752429090c0212158
ms.sourcegitcommit: d681796e8c012eca2d9629d3b816749e9f50f868
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98005409"
---
# <a name="sysdatabase_query_store_options-transact-sql"></a>sys.database_query_store_options (Transact-sql) 

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

  傳回這個資料庫的查詢存放區選項。  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本)、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**desired_state**|**smallint**|指出查詢存放區所需的作業模式（由使用者明確設定）。<br /> 0 = OFF <br /> 1 = READ_ONLY<br /> 2 = READ_WRITE|  
|**desired_state_desc**|**nvarchar(60)**|查詢存放區所需作業模式的文字描述：<br />OFF<br />READ_ONLY<br />READ_WRITE|  
|**actual_state**|**smallint**|指出查詢存放區的操作模式。 除了使用者需要的預期狀態清單之外，實際狀態可能會是錯誤狀態。<br /> 0 = OFF <br /> 1 = READ_ONLY<br /> 2 = READ_WRITE<br /> 3 = 錯誤|  
|**actual_state_desc**|**nvarchar(60)**|查詢存放區實際操作模式的文字描述。<br />OFF<br />READ_ONLY<br />READ_WRITE<br />ERROR<br /><br /> 在某些情況下，實際狀態與預期狀態不同：<br />-如果資料庫設定為唯讀模式，或如果查詢存放區大小超過其設定的配額，查詢存放區可能會在唯讀模式中運作，即使使用者已指定讀寫也是一樣。<br />-在極端案例中查詢存放區可能因為內部錯誤而進入錯誤狀態。 從開始 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] ，如果發生這種情況，查詢存放區可以 `sp_query_store_consistency_check` 在受影響的資料庫中執行預存程式來復原。 如果執行 `sp_query_store_consistency_check` 無法運作，或者您使用的是 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] ，您將需要藉由執行來清除資料 `ALTER DATABASE [YourDatabaseName] SET QUERY_STORE CLEAR ALL;`|  
|**readonly_reason**|**int**|當 **desired_state_desc** READ_WRITE，而且 **actual_state_desc** 是 READ_ONLY 時， **readonly_reason** 會傳回一個位對應，指出查詢存放區在唯讀模式中的原因。<br /><br /> **1** -資料庫處於唯讀模式<br /><br /> **2** -資料庫處於單一使用者模式<br /><br /> **4** -資料庫處於緊急模式<br /><br /> **8** -資料庫是次要複本 (適用于 Always On 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 異地複寫) 。 此值只能在 **可讀取** 的次要複本上有效觀察到<br /><br /> **65536** -查詢存放區已達到選項設定的大小限制 `MAX_STORAGE_SIZE_MB` 。 如需此選項的詳細資訊，請參閱 [ALTER DATABASE SET options (transact-sql) ](../../t-sql/statements/alter-database-transact-sql-set-options.md)。<br /><br /> **131072** -查詢存放區中的不同語句數目已達內部記憶體限制。 請考慮移除您不需要的查詢，或升級至較高的服務層級，以啟用將查詢存放區傳送至讀寫模式。<br /><br /><br /> **262144** -等候保存在磁片上的記憶體中專案大小，已達內部記憶體限制。 在記憶體中的專案保存在磁片上之前，查詢存放區將會暫時處於唯讀模式。 <br /><br /><br /> **524288** -資料庫已達到磁片大小限制。 查詢存放區是使用者資料庫的一部分，因此，如果資料庫沒有其他可用的空間，這表示查詢存放區無法再成長。<br /><br /> <br /> 若要將查詢存放區的作業模式切換回讀寫，請參閱使用查詢存放區， **確認查詢存放區會持續收集查詢資料** 的 [最佳作法](../../relational-databases/performance/best-practice-with-the-query-store.md#Verify)區段。|  
|**current_storage_size_mb**|**bigint**|磁片上的查詢存放區大小（以 mb 為單位）。|  
|**flush_interval_seconds**|**bigint**|定期將查詢存放區資料排清到磁片的期間（以秒為單位）。 預設值為 **900** (15 分鐘) 。<br /><br /> 使用語句進行變更 `ALTER DATABASE <database> SET QUERY_STORE (DATA_FLUSH_INTERVAL_SECONDS  = <interval>)` 。|  
|**interval_length_minutes**|**bigint**|統計資料匯總間隔（以分鐘為單位）。 不允許任意值。 使用下列其中一項：1、5、10、15、30、60和1440分鐘。 預設值為 **60** 分鐘。|  
|**max_storage_size_mb**|**bigint**|查詢存放區的磁片大小上限（ (mb）) 。 預設值為 **100** MB （最高至 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] ），從開始則為 **1 GB** [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 。<br />針對 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] Premium edition，預設值為 1 GB，而針對 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] Basic edition，預設值為 10 MB。<br /><br /> 使用語句進行變更 `ALTER DATABASE <database> SET QUERY_STORE (MAX_STORAGE_SIZE_MB = <size>)` 。|  
|**stale_query_threshold_days**|**bigint**|在查詢存放區中保留查詢資訊的天數。 預設值為 **30**。 設定為0可停用保留原則。<br />[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] Basic 版的預設值為 7 天。<br /><br /> 使用語句進行變更 `ALTER DATABASE <database> SET QUERY_STORE ( CLEANUP_POLICY = ( STALE_QUERY_THRESHOLD_DAYS = <value> ) )` 。|  
|**max_plans_per_query**|**bigint**|限制預存計畫的最大數目。 預設值為 **200**。 如果達到最大值，查詢存放區會停止捕獲該查詢的新方案。 設定為0會移除已捕捉的方案數目的限制。<br /><br /> 使用語句進行變更 `ALTER DATABASE<database> SET QUERY_STORE (MAX_PLANS_PER_QUERY = <n>)` 。|  
|**query_capture_mode**|**smallint**|目前作用中的查詢捕獲模式：<br /><br /> **1** = 全部-已捕捉所有查詢。 這是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和更新版本) 的預設設定值。<br /><br /> 2 = 根據執行計數和資源耗用量自動捕捉相關的查詢。 這是 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 的預設組態值。<br /><br /> 3 = 無-停止捕捉新的查詢。 查詢存放區將會繼續收集已擷取查詢的編譯和執行階段統計資料。 請小心使用此設定，因為您可能會錯過重要的查詢。|  
|**query_capture_mode_desc**|**nvarchar(60)**|查詢存放區實際捕捉模式的文字描述：<br /><br /> ) 的所有 (預設值 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]<br /><br /> ) 的 **自動** (預設值 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]<br /><br /> NONE|  
|**size_based_cleanup_mode**|**smallint**|控制當總資料量接近大小上限時，是否將自動啟用清除：<br /><br /> 0 = 不會自動啟用以大小為依據的清除。<br /><br /> **1** = 當磁片上的大小達到 *max_storage_size_mb* 的 **90%** 時，會自動啟用以大小為基礎的自動清除。 這是預設組態值。<br /><br />以大小為依據之清除會先移除成本最高和最舊的查詢。 達到大約 **80%** 的 *max_storage_size_mb* 時，就會停止。|  
|**size_based_cleanup_mode_desc**|**nvarchar(60)**|查詢存放區的實際以大小為基礎的清除模式的文字描述：<br /><br /> OFF <br /> **自動** (預設) |  
|**wait_stats_capture_mode**|**smallint**|控制查詢存放區是否執行等候統計資料的捕獲： <br /><br /> 0 = OFF <br /> **1** = 開啟<br /> **適用對象**：[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 及更新版本。|
|**wait_stats_capture_mode_desc**|**nvarchar(60)**|實際等候統計資料捕捉模式的文字描述： <br /><br /> OFF <br /> **ON** (預設) <br /> **適用對象**：[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 及更新版本。| 
  
## <a name="permissions"></a>權限  
 需要 `VIEW DATABASE STATE` 權限。  
  
## <a name="see-also"></a>另請參閱  
 [sys.query_coNtext_settings &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-context-settings-transact-sql.md)   
 [sys.query_store_plan &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-plan-transact-sql.md)   
 [sys.query_store_query &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-query-transact-sql.md)   
 [sys.query_store_query_text &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql.md)   
 [sys.query_store_runtime_stats &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql.md)   
 [sys.query_store_wait_stats &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql.md)  
 [sys.query_store_runtime_stats_interval &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-query-store-runtime-stats-interval-transact-sql.md)   
 [相關檢視、函數與程序](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [sys.fn_stmt_sql_handle_from_sql_stmt &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-stmt-sql-handle-from-sql-stmt-transact-sql.md)   
 [查詢存放區預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/query-store-stored-procedures-transact-sql.md)  
  
  
