---
description: sys.dm_tran_locks (Transact-SQL)
title: sys.dm_tran_locks (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/30/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_tran_locks
- sys.dm_tran_locks
- sys.dm_tran_locks_TSQL
- dm_tran_locks_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_locks dynamic management view
ms.assetid: f0d3b95a-8a00-471b-9da4-14cb8f5b045f
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e3dad8a8c42e2189a29b2e75743653aca5055eae
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101460"
---
# <a name="sysdm_tran_locks-transact-sql"></a>sys.dm_tran_locks (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回有關 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中目前使用中鎖定管理員資源的資訊。 每一個資料列皆代表一個對鎖定管理員針對已經授與或在等待授與的鎖定的目前使用中要求。  
  
 結果集中的資料行，共分成資源和要求兩個主要群組。 資源群組描述鎖定要求所針對的資源，而要求群組則描述該鎖定要求。  
  
> [!NOTE]  
> 若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_tran_locks** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**resource_type**|**nvarchar(60)**|代表資源類型。 這個值可以是下列值之一：DATABASE、FILE、OBJECT、PAGE、KEY、EXTENT、RID、APPLICATION、METADATA、HOBT 或 ALLOCATION_UNIT。|  
|**resource_subtype**|**nvarchar(60)**|代表 **resource_type** 的子類型。 在技術上即使不保留父類型的非子類型鎖定，它也可以取得子類型鎖定。 不同的子類型，並不會彼此衝突，也不會與非子類型的父類型相衝突。 不過並非所有的資源類型都有子類型。|  
|**resource_database_id**|**int**|決定這個資源範圍所用的資料庫識別碼。 所有由鎖定管理員處理的資源，都由資料庫識別碼決定範圍。|  
|**resource_description**|**nvarchar(256)**|資源的描述，其中只包含無法從其他資源資料行取得的資訊。|  
|**resource_associated_entity_id**|**bigint**|資料庫中與資源相關聯的實體識別碼。 視資源類型而定，它可以是物件識別碼、Hobt 識別碼或配置單位識別碼。|  
|**resource_lock_partition**|**整數**|資料分割鎖定資源的鎖定資料分割識別碼。 非資料分割鎖定資源的值是 0。|  
|**request_mode**|**nvarchar(60)**|要求的模式。 如果是已授與的要求，則為已授與的模式；如果是等待授與的要求，則為正在要求的模式。 <br /><br /> NULL = 未授與資源的任何存取權。 這用來作為預留位置。<br /><br /> .Sch-S (架構穩定性) = 確定不會卸載架構專案，例如資料表或索引，而任何會話都會保留架構專案的架構穩定性鎖定。<br /><br /> 任何想要變更指定資源之架構的會話，都必須保留 .sch-M (架構修改) =。 請確定沒有其他工作階段在參考指示的物件。<br /><br /> S (共用) = 持有的會話會被授與資源的共用存取權。<br /><br /> U (Update) = 表示已取得資源的更新鎖定，而這些資源最終可能會更新。 它用來防止當多個工作階段為了未來可能更新資源而鎖定資源時，所常見的死結形式。<br /><br /> X (獨佔) = 持有的會話會被授與資源的獨佔存取權。<br /><br /> 是 (意圖共用的) = 表示要在鎖定階層中的某些從屬資源上放置 S 鎖定的意圖。<br /><br /> IU (意圖更新) = 指出將 U 鎖定放置在鎖定階層中的某些從屬資源上的意圖。<br /><br /> IX (意圖專屬) = 表示要將 X 鎖定放置在鎖定階層中的某些從屬資源上。<br /><br /> SIU (共用意圖更新) = 表示在鎖定階層中的從屬資源上取得更新鎖定的資源分享存取。<br /><br /> 六個 (共用意圖獨佔) = 指出資源的共用存取權，意圖是取得鎖定階層中從屬資源的獨佔鎖定。<br /><br /> UIX (更新意圖專屬) = 指出資源的更新鎖定，其目的是要取得鎖定階層中從屬資源的獨佔鎖定。<br /><br /> BU = 供大量作業使用。<br /><br /> RangeS_S (共用 Key-Range 和共用資源鎖定) = 表示可序列化範圍掃描。<br /><br /> RangeS_U (共用 Key-Range 和更新資源鎖定) = 表示可序列化的更新掃描。<br /><br /> RangeI_N (Insert Key-Range 和 Null 資源鎖定) = 用來測試範圍，然後再將新的索引鍵插入索引。<br /><br /> RangeI_S = Key-Range 轉換鎖定，由重迭的 RangeI_N 和 S 鎖定所建立。<br /><br /> RangeI_U = Key-Range 轉換鎖定，由 RangeI_N 和 U 鎖定的重迭所建立。<br /><br /> RangeI_X = Key-Range 轉換鎖定，由 RangeI_N 和 X 鎖定的重迭所建立。<br /><br /> RangeX_S = Key-Range 轉換鎖定，由 RangeI_N 和 RangeS_S 重迭所建立。 。<br /><br /> RangeX_U = Key-Range 轉換鎖定，由 RangeI_N 重迭和 RangeS_U 鎖定所建立。<br /><br /> RangeX_X (獨佔 Key-Range 和獨佔資源鎖定) = 這是更新範圍中的索引鍵時所使用的轉換鎖定。|  
|**request_type**|**nvarchar(60)**|要求類型。 值為 LOCK。|  
|**request_status**|**nvarchar(60)**|這項要求的目前狀態。 可能的值是 GRANTED、CONVERT、WAIT、LOW_PRIORITY_CONVERT、LOW_PRIORITY_WAIT 或 ABORT_BLOCKERS。 如需低優先順序等候和中止封鎖器的詳細資訊，請參閱 [ALTER INDEX &#40;transact-sql&#41;](../../t-sql/statements/alter-index-transact-sql.md)的 *low_priority_lock_wait* 一節。|  
|**request_reference_count**|**smallint**|傳回同一個要求器要求這項資源的大約次數。|  
|**request_lifetime**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**request_session_id**|**int**|目前擁有這項要求的工作階段識別碼。 主控工作階段識別碼會隨著分散式和繫結式交易而改變。 -2 值表示要求屬於被遺棄的分散式交易。 -3 值表示該要求是屬於延遲的復原交易，例如，由於回復作業無法順利完成，因而在復原時延遲的交易。|  
|**request_exec_context_id**|**int**|目前擁有這項要求之處理序的工作階段內容識別碼。|  
|**request_request_id**|**int**|目前擁有這項要求之處理序的要求識別碼 (批次識別碼)。 只要交易的使用中 Multiple Active Result Set (MARS) 連接一改變，這個值就會隨之改變。|  
|**request_owner_type**|**nvarchar(60)**|擁有要求的實體類型。 鎖定管理員要求可以由各種實體所擁有。 可能的值包括：<br /><br /> TRANSACTION = 要求是由交易所擁有。<br /><br /> CURSOR = 要求是由資料指標所擁有。<br /><br /> SESSION = 要求是由使用者工作階段所擁有。<br /><br /> SHARED_TRANSACTION_WORKSPACE = 要求是由交易工作空間的共用部分所擁有。<br /><br /> EXCLUSIVE_TRANSACTION_WORKSPACE = 要求是由交易工作空間的獨佔部分所擁有。<br /><br /> NOTIFICATION_OBJECT = 要求是由內部 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件所擁有。 這個元件已要求鎖定管理員在另一個元件等候取得鎖定時通知它。 FileTable 功能是使用此值的元件。<br /><br /> **注意：** 工作空間會在內部使用，以保存已登錄會話的鎖定。|  
|**request_owner_id**|**bigint**|這項要求的特定擁有者識別碼。<br /><br /> 當交易是要求的擁有者時，這個值包含交易識別碼。<br /><br /> 當 FileTable 是要求的擁有者時，**request_owner_id** 有下列其中一個值：<br /> <ul><li>**-4** ： FileTable 已取得資料庫鎖定。<li> **-3** ： FileTable 已採用表鎖。<li> **其他值** ：此值代表檔案控制代碼。 此值也會顯示為動態管理檢視中的 **fcb_id** ， [sys.dm_filestream_non_transacted_handles &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-filestream-non-transacted-handles-transact-sql.md)。</li></ul>|  
|**request_owner_guid**|**uniqueidentifier**|此要求之特定擁有者的 GUID。 只有值對應於該交易之 MS DTC GUID 的分散式交易才會使用這個值。|  
|**request_owner_lockspace_id**|**nvarchar(32)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)] 這個值代表要求器的鎖定空間識別碼。 鎖定空間識別碼可以判斷兩個要求器是否彼此相容，如果其模式會彼此衝突，是否可被授與鎖定。|  
|**lock_owner_address**|**varbinary(8)**|追蹤這項要求所用的內部資料結構記憶體位址。 這個資料行可以與 **sys.dm_os_waiting_tasks** 中的 **resource_address** 資料行聯結。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限
在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
 
## <a name="remarks"></a>備註  
 如果是已授與的要求狀態，表示已將資源鎖定授與要求器。 而等待中的要求，表示尚未授與該要求。 **request_status** 資料行會傳回下列等候要求類型：  
  
-   如果是轉換要求狀態，表示要求器已被授與該資源的要求，目前正在等候升級為即將授與的起始要求。  
  
-   如果是等候要求狀態，則表示要求器目前沒有已授與的資源要求。  
  
 由於 **sys.dm_tran_locks** 是從內部鎖定管理員資料結構擴展而來，因此維護這些資訊，並不會對正常的處理作業增加額外的負擔。 若要將檢視具體化，需要具備鎖定管理員內部資料結構的存取權。 這可能會對伺服器的正常處理作業產生一些影響。 這些影響應該很難查覺，而且只會影響到常用的資源。 由於這份檢視的資料對應到活性鎖定管理員狀態，因此資料隨時可以變更，而且只要一取得和釋放鎖定，就可以加入和移除資料列。 這份檢視沒有記錄資訊。  
  
 只有當所有的資源群組資料行都相等時，才能在相同的資源上進行兩項要求。  
  
 您可以利用下列工具控制讀取作業的鎖定：  
  
-   SET TRANSACTION ISOLATION LEVEL，用來指定工作階段的鎖定層級。 如需詳細資訊，請參閱 [SET TRANSACTION ISOLATION LEVEL &#40;Transact-SQL&#41;](../../t-sql/statements/set-transaction-isolation-level-transact-sql.md)。  
  
-   鎖定資料表提示，用來在 FROM 子句中指定資料表之個別參考的鎖定層級。 如需語法和限制，請參閱 [&#40;transact-sql&#41;的資料表提示 ](../../t-sql/queries/hints-transact-sql-table.md)。  
  
 在一個工作階段識別碼下執行資源，可以有一個以上的授與鎖定。 在一個工作階段下執行的不同實體，可以在相同資源各自擁有一個鎖定，而資訊會顯示在 **sys.dm_tran_locks** 傳回的 **request_owner_type** 和 **request_owner_id** 資料行中。 如果同一個 **request_owner_type** 有多個執行個體，則會用 **request_owner_id** 資料行來區別每一個執行個體。 如果是分散式交易，則 **request_owner_type** 和 **request_owner_guid** 資料行會顯示不同的實體資訊。  
  
 例如，假設工作階段 S1 在 **Table1** 擁有一個共用鎖定，而在工作階段 S1 下執行的交易 T1 也在 **Table1** 擁有共用鎖定。 在本例中，**sys.dm_tran_locks** 所傳回的 **resource_description** 資料行，會顯示相同資源的兩個執行個體。 **request_owner_type** 資料行會把其中一個執行個體顯示為工作階段，把另一個顯示為交易。 同時，**resource_owner_id** 資料行也會有不同的值。  
  
 在一個工作階段下執行的多個資料指標是無法區分的，它們會被視為一個實體。  
  
 與工作階段識別碼值無關的分散式交易是被遺棄的交易，系統會指派 -2 值做為交易的工作階段識別碼。 如需詳細資訊，請參閱 [KILL &#40;Transact-SQL&#41;](../../t-sql/language-elements/kill-transact-sql.md)。  

## <a name="locks"></a><a name="locks"></a> 鎖
鎖定發生於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資源上，例如交易期間讀取或修改的資料列，以避免不同交易同時使用資源。 例如，若某個交易將資料表內的資料列獨佔 (X) 鎖定，就沒有其他交易可修改該資料列，直到鎖定解除為止。 將鎖定減至最少可增加並行 (Concurrency)，以改善效能。 

## <a name="resource-details"></a>資源詳細資料  
 下表列出 **resource_associated_entity_id** 資料行中所呈現的資源。  
  
|資源類型|資源描述|Resource_associated_entity_id|  
|-------------------|--------------------------|--------------------------------------|  
|DATABASE|代表資料庫。|不適用|  
|FILE|代表資料庫檔案。 這個檔案可以是資料或記錄檔。|不適用|  
|OBJECT|代表資料庫物件。 這個物件可以是資料表、檢視、預存程序、擴充預存程序或任何具有物件識別碼的物件。|物件識別碼|  
|PAGE|代表資料檔中的一頁。|HoBt 識別碼。 這個值對應於 **sys.partitions.hobt_id**。 HoBt 識別碼不一定都適用於 PAGE 資源，因為 HoBt 識別碼是可由呼叫端提供的額外資訊，但不是所有的呼叫端都可以提供這些資訊。|  
|KEY|代表索引中的一個資料列。|HoBt 識別碼。 這個值對應於 **sys.partitions.hobt_id**。|  
|EXTENT|代表資料檔範圍。 一個範圍是八個連續頁的群組。|不適用|  
|RID|代表堆積中的一個實體資料列。|HoBt 識別碼。 這個值對應於 **sys.partitions.hobt_id**。 HoBt 識別碼不一定都適用於 RID 資源，因為 HoBt 識別碼是可由呼叫端提供的額外資訊，但不是所有的呼叫端都可以提供這些資訊。|  
|APPLICATION|代表應用程式指定的資源。|不適用|  
|METADATA|代表中繼資料資訊。|不適用|  
|HOBT|代表堆積或 B 型樹狀目錄。 這些是基本的存取路徑結構。|HoBt 識別碼。 這個值對應於 **sys.partitions.hobt_id**。|  
|ALLOCATION_UNIT|代表一組相關頁面，例如，索引資料分割。 每一個配置單位都涵蓋一個索引配置對應 (IAM) 鏈結。|配置單位識別碼。 這個值對應於 **sys.allocation_units.allocation_unit_id**。|  
  
 下表列出各資源類型所關聯的子類型。  
  
|ResourceSubType|同步處理|  
|---------------------|------------------|  
|ALLOCATION_UNIT.BULK_OPERATION_PAGE|用於大量作業的預先配置頁面。|  
|ALLOCATION_UNIT.PAGE_COUNT|延遲卸除作業期間的配置單位頁面計數統計資料。|  
|DATABASE.BULKOP_BACKUP_DB|包含大量作業的資料庫備份。|  
|DATABASE.BULKOP_BACKUP_LOG|包含大量作業的資料庫記錄備份。|  
|DATABASE.CHANGE_TRACKING_CLEANUP|變更追蹤清除工作。|  
|DATABASE.CT_DDL|資料庫與資料表層級的變更追蹤 DDL 作業。|  
|DATABASE.CONVERSATION_PRIORITY|Service Broker 交談優先權作業，例如 CREATE BROKER PRIORITY。|  
|DATABASE.DDL|包含檔案群組作業的資料定義語言 (DDL) 作業，例如卸除。|  
|DATABASE.ENCRYPTION_SCAN|TDE 加密同步處理。|  
|DATABASE.PLANGUIDE|計畫指南同步處理。|  
|DATABASE.RESOURCE_GOVERNOR_DDL|資源管理員作業的 DDL 作業，例如 ALTER RESOURCE POOL。|  
|DATABASE.SHRINK|資料庫壓縮作業。|  
|DATABASE.STARTUP|用於同步處理資料庫啟動。|  
|FILE.SHRINK|檔案壓縮作業。|  
|HOBT.BULK_OPERATION|在下列隔離等級下，具有並行掃描的堆積最佳化大量載入作業：快照集、讀取未認可，以及使用資料列版本設定認可的讀取。|  
|HOBT.INDEX_REORGANIZE|堆積或索引重新組織作業。|  
|OBJECT.COMPILE|預存程序編譯。|  
|OBJECT.INDEX_OPERATION|索引作業。|  
|OBJECT.UPDSTATS|資料表的統計資料更新。|  
|METADATA.ASSEMBLY|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASSEMBLY_CLR_NAME|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASSEMBLY_TOKEN|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASYMMETRIC_KEY|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT_ACTIONS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT_SPECIFICATION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AVAILABILITY_GROUP|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CERTIFICATE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CHILD_INSTANCE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.COMPRESSED_FRAGMENT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.COMPRESSED_ROWSET|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSTATION_ENDPOINT_RECV|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSTATION_ENDPOINT_SEND|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSATION_GROUP|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSATION_PRIORITY|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CREDENTIAL|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CRYPTOGRAPHIC_PROVIDER|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATA_SPACE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATABASE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATABASE_PRINCIPAL|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_MIRRORING_SESSION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_MIRRORING_WITNESS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_PRINCIPAL_SID|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ENDPOINT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ENDPOINT_WEBMETHOD|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.EXPR_COLUMN|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.EXPR_HASH|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_CATALOG|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_INDEX|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_STOPLIST|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INDEX_EXTENSION_SCHEME|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INDEXSTATS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INSTANTIATED_TYPE_HASH|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.MESSAGE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.METADATA_CACHE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PARTITION_FUNCTION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PASSWORD_POLICY|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PERMISSIONS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PLAN_GUIDE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PLAN_GUIDE_HASH|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PLAN_GUIDE_SCOPE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.QNAME|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.QNAME_HASH|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.REMOTE_SERVICE_BINDING|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ROUTE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SCHEMA|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SECURITY_CACHE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SECURITY_DESCRIPTOR|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SEQUENCE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVER_EVENT_SESSIONS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVER_PRINCIPAL|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_BROKER_GUID|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_CONTRACT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_MESSAGE_TYPE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.STATS|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SYMMETRIC_KEY|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.USER_TYPE|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_COLLECTION|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_COMPONENT|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_INDEX_QNAME|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
  
 下表提供各種資源類型之 **resource_description** 資料行的格式。  
  
|資源|格式|描述|  
|--------------|------------|-----------------|  
|DATABASE|不適用|**resource_database_id** 資料行中已經提供了資料庫識別碼。|  
|FILE|<file_id>|這項資源所代表的檔案識別碼。|  
|OBJECT|<object_id>|這項資源所代表的物件識別碼。 這個物件可以是 **sys.objects** 中列出的任何物件，而不只是資料表。|  
|PAGE|<file_id>:<page_in_file>|代表這項資源所代表之頁面的檔案和頁面識別碼。|  
|KEY|<hash_value>|代表這項資源所代表之資料列的索引鍵資料行雜湊。|  
|EXTENT|<file_id>:<page_in_files>|代表這項資源所代表之範圍的檔案和頁面識別碼。 這個範圍識別碼，與範圍中第一頁的頁面識別碼相同。|  
|RID|<file_id>:<page_in_file>:<row_on_page>|代表這項資源所代表之資料列的頁面識別碼和資料列識別碼。 請注意，如果相關聯的物件識別碼是 99，這項資源就代表 IAM 鏈結的第一個 IAM 頁面上，八個混合頁面位置之一。|  
|APPLICATION|\<DbPrincipalId>： \<upto 32 characters> ： ( # B0 hash_value>) |代表制定這個應用程式鎖定資源範圍所用之資料庫主體的識別碼。 其中包含來自對應於這個應用程式鎖定資源的資源字串，最多可以包含 32 個字元。 在某些情況下，由於完整字串已經無法使用，因此只能顯示 2 個字元。 這個行為只發生在資料庫復原時，復原程序必須重新取得應用程式鎖定。 雜湊值代表對應於這個應用程式鎖定資源的完整資源字串雜湊。|  
|HOBT|不適用|HoBt 識別碼會當做 **resource_associated_entity_id** 加入。|  
|ALLOCATION_UNIT|不適用|配置單位識別碼會被當做 **resource_associated_entity_id** 併入。|  
|METADATA.ASSEMBLY|assembly_id = A|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASSEMBLY_CLR_NAME|$qname_id = Q|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASSEMBLY_TOKEN|assembly_id = A, $token_id|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ASSYMMETRIC_KEY|asymmetric_key_id = A|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT|audit_id = A|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT_ACTIONS|device_id = D, major_id = M|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AUDIT_SPECIFICATION|audit_specification_id = A|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.AVAILABILITY_GROUP|availability_group_id = A|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CERTIFICATE|certificate_id = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CHILD_INSTANCE|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.COMPRESSED_FRAGMENT|object_id = O , compressed_fragment_id = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.COMPRESSED_ROW|object_id = O|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSTATION_ENDPOINT_RECV|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSTATION_ENDPOINT_SEND|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSATION_GROUP|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CONVERSATION_PRIORITY|conversation_priority_id = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CREDENTIAL|credential_id = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.CRYPTOGRAPHIC_PROVIDER|provider_id = P|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATA_SPACE|data_space_id = D|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATABASE|database_id = D|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DATABASE_PRINCIPAL|principal_id = P|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_MIRRORING_SESSION|database_id = D|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_MIRRORING_WITNESS|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.DB_PRINCIPAL_SID|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ENDPOINT|endpoint_id = E|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ENDPOINT_WEBMETHOD|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_CATALOG|fulltext_catalog_id = F|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_INDEX|object_id = O|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.EXPR_COLUMN|object_id = O, column_id = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.EXPR_HASH|object_id = O, $hash = H|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_CATALOG|fulltext_catalog_id = F|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_INDEX|object_id = O|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.FULLTEXT_STOPLIST|fulltext_stoplist_id = F|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INDEX_EXTENSION_SCHEME|index_extension_id = I|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INDEXSTATS|object_id = O, index_id or stats_id = I|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.INSTANTIATED_TYPE_HASH|user_type_id = U, hash = H|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.MESSAGE|message_id = M|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.METADATA_CACHE|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PARTITION_FUNCTION|function_id = F|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PASSWORD_POLICY|principal_id = P|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PERMISSIONS|class = C|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.PLAN_GUIDE|plan_guide_id = P|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA. PLAN_GUIDE_HASH|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA. PLAN_GUIDE_SCOPE|scope_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.QNAME|$qname_id = Q|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.QNAME_HASH|$qname_scope_id = Q, $qname_hash = H|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.REMOTE_SERVICE_BINDING|remote_service_binding_id = R|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.ROUTE|route_id = R|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SCHEMA|schema_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SECURITY_CACHE|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SECURITY_DESCRIPTOR|sd_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SEQUENCE|$seq_type = S, object_id = O|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVER|server_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVER_EVENT_SESSIONS|event_session_id = E|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVER_PRINCIPAL|principal_id = P|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE|service_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_BROKER_GUID|$hash = H1:H2:H3|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_CONTRACT|service_contract_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SERVICE_MESSAGE_TYPE|message_type_id = M|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.STATS|object_id = O, stats_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.SYMMETRIC_KEY|symmetric_key_id = S|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.USER_TYPE|user_type_id = U|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_COLLECTION|xml_collection_id = X|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_COMPONENT|xml_component_id = X|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|METADATA.XML_INDEX_QNAME|object_id = O, $qname_id = Q|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
  
 下列 XEvents 與資料分割 **切換** 和線上索引重建有關。 如需語法的詳細資訊，請參閱 [ALTER TABLE &#40;transact-sql&#41;](../../t-sql/statements/alter-table-transact-sql.md) 和 [Alter INDEX &#40;transact-sql&#41;](../../t-sql/statements/alter-index-transact-sql.md)。  
  
-   lock_request_priority_state  
  
-   process_killed_by_abort_blockers  
  
-   ddl_with_wait_at_low_priority  
  
 線上索引作業的現有 XEvent **progress_report_online_index_operation** 是藉由新增 **partition_number** 和 **partition_id** 來擴充。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-sysdm_tran_locks-with-other-tools"></a>A. 使用 sys.dm_tran_locks 與其他工具  
 下列範例將使用更新作業遭其他交易封鎖的案例。 利用 **sys.dm_tran_locks** 和其他工具，提供有關鎖定資源的資訊。  
  
```sql  
USE tempdb;  
GO  
  
-- Create test table and index.  
CREATE TABLE t_lock  
    (  
    c1 int, c2 int  
    );  
GO  
  
CREATE INDEX t_lock_ci on t_lock(c1);  
GO  
  
-- Insert values into test table  
INSERT INTO t_lock VALUES (1, 1);  
INSERT INTO t_lock VALUES (2,2);  
INSERT INTO t_lock VALUES (3,3);  
INSERT INTO t_lock VALUES (4,4);  
INSERT INTO t_lock VALUES (5,5);  
INSERT INTO t_lock VALUES (6,6);  
GO  
  
-- Session 1  
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;  
  
BEGIN TRAN  
    SELECT c1  
        FROM t_lock  
        WITH(holdlock, rowlock);  
  
-- Session 2  
BEGIN TRAN  
    UPDATE t_lock SET c1 = 10  
```  
  
 下列查詢將會顯示鎖定資訊。 `<dbid>` 的值應該會被取代成 **sys.databases** 中的 **database_id**。  
  
```sql  
SELECT resource_type, resource_associated_entity_id,  
    request_status, request_mode,request_session_id,  
    resource_description   
    FROM sys.dm_tran_locks  
    WHERE resource_database_id = <dbid>  
```  
  
 下列查詢會利用上一項查詢的 `resource_associated_entity_id` 來傳回物件資訊。 您必須在連接到包含物件的資料庫時執行這項查詢。  
  
```  
SELECT object_name(object_id), *  
    FROM sys.partitions  
    WHERE hobt_id=<resource_associated_entity_id>  
```  
  
 下列查詢將會顯示封鎖資訊。  
  
```sql  
SELECT   
        t1.resource_type,  
        t1.resource_database_id,  
        t1.resource_associated_entity_id,  
        t1.request_mode,  
        t1.request_session_id,  
        t2.blocking_session_id  
    FROM sys.dm_tran_locks as t1  
    INNER JOIN sys.dm_os_waiting_tasks as t2  
        ON t1.lock_owner_address = t2.resource_address;  
```  
  
 透過回復交易的方式來釋放資源。  
  
```sql  
-- Session 1  
ROLLBACK;  
GO  
  
-- Session 2  
ROLLBACK;  
GO  
```  
  
### <a name="b-linking-session-information-to-operating-system-threads"></a>B. 將工作階段資訊連結到作業系統執行緒  
 下列範例會傳回與 Windows 執行緒識別碼的工作階段識別碼相關聯的資訊。 執行緒的效能可以再 Windows 效能監視器中監視。 這項查詢不會傳回目前睡眠中的工作階段識別碼。  
  
```sql  
SELECT STasks.session_id, SThreads.os_thread_id  
    FROM sys.dm_os_tasks AS STasks  
    INNER JOIN sys.dm_os_threads AS SThreads  
        ON STasks.worker_address = SThreads.worker_address  
    WHERE STasks.session_id IS NOT NULL  
    ORDER BY STasks.session_id;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
[sys.dm_tran_database_transactions &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql.md)      
[動態管理檢視與函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)     
[交易相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)      
[SQL Server 的 Locks 物件](../../relational-databases/performance-monitor/sql-server-locks-object.md)      
