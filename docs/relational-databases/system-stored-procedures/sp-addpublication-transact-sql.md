---
description: sp_addpublication (Transact-SQL)
title: sp_addpublication (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sp_addpublication_TSQL
- sp_addpublication
helpviewer_keywords:
- sp_addpublication
ms.assetid: c7167ed1-2b7e-4824-b82b-65f4667c4407
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 9aa65ff9b5b9c14441ac555d40788e92082f01c5
ms.sourcegitcommit: 7791bd2ba339edc5cd2078a6537c8f6bfe72a19b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98564450"
---
# <a name="sp_addpublication-transact-sql"></a>sp_addpublication (Transact-SQL)
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

  建立一個快照式或交易式發行集。 這個預存程序執行於發行集資料庫的發行者端。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sp_addpublication [ @publication = ] 'publication'  
    [ , [ @taskid = ] tasked ]  
    [ , [ @restricted = ] 'restricted' ]  
    [ , [ @sync_method = ] 'sync_method' ]  
    [ , [ @repl_freq = ] 'repl_freq' ]  
    [ , [ @description = ] 'description' ]  
    [ , [ @status = ] 'status' ]  
    [ , [ @independent_agent = ] 'independent_agent' ]  
    [ , [ @immediate_sync = ] 'immediate_sync' ]  
    [ , [ @enabled_for_internet = ] 'enabled_for_internet' ]  
    [ , [ @allow_push = ] 'allow_push'  
    [ , [ @allow_pull = ] 'allow_pull' ]  
    [ , [ @allow_anonymous = ] 'allow_anonymous' ]  
    [ , [ @allow_sync_tran = ] 'allow_sync_tran' ]  
    [ , [ @autogen_sync_procs = ] 'autogen_sync_procs' ]  
    [ , [ @retention = ] retention ]  
    [ , [ @allow_queued_tran= ] 'allow_queued_updating' ]  
    [ , [ @snapshot_in_defaultfolder= ] 'snapshot_in_default_folder' ]  
    [ , [ @alt_snapshot_folder= ] 'alternate_snapshot_folder' ]  
    [ , [ @pre_snapshot_script= ] 'pre_snapshot_script' ]  
    [ , [ @post_snapshot_script= ] 'post_snapshot_script' ]  
    [ , [ @compress_snapshot= ] 'compress_snapshot' ]  
    [ , [ @ftp_address = ] 'ftp_address' ]  
    [ , [ @ftp_port= ] ftp_port ]  
    [ , [ @ftp_subdirectory = ] 'ftp_subdirectory' ]  
    [ , [ @ftp_login = ] 'ftp_login' ]  
    [ , [ @ftp_password = ] 'ftp_password' ]  
    [ , [ @allow_dts = ] 'allow_dts' ]  
    [ , [ @allow_subscription_copy = ] 'allow_subscription_copy' ]  
    [ , [ @conflict_policy = ] 'conflict_policy' ]  
    [ , [ @centralized_conflicts = ] 'centralized_conflicts' ]   
    [ , [ @conflict_retention = ] conflict_retention ]  
    [ , [ @queue_type = ] 'queue_type' ]  
    [ , [ @add_to_active_directory = ] 'add_to_active_directory' ]  
    [ , [ @logreader_job_name = ] 'logreader_agent_name' ]  
    [ , [ @qreader_job_name = ] 'queue_reader_agent_name' ]  
    [ , [ @publisher = ] 'publisher' ]   
    [ , [ @allow_initialize_from_backup = ] 'allow_initialize_from_backup' ]  
    [ , [ @replicate_ddl = ] replicate_ddl ]  
    [ , [ @enabled_for_p2p = ] 'enabled_for_p2p' ]  
    [ , [ @publish_local_changes_only = ] 'publish_local_changes_only' ]  
    [ , [ @enabled_for_het_sub = ] 'enabled_for_het_sub' ]  
    [ , [ @p2p_conflictdetection = ] 'p2p_conflictdetection' ]  
    [ , [ @p2p_originator_id = ] p2p_originator_id  
    [ , [ @p2p_continue_onconflict = ] 'p2p_continue_onconflict'  
    [ , [ @allow_partition_switch = ] 'allow_partition_switch'  
    [ , [ @replicate_partition_switch = ]'replicate_partition_switch'  
```  
  
## <a name="arguments"></a>引數  
`[ \@publication = ] 'publication'` 這是要建立的發行集名稱。 *發行* 集是 **sysname**，沒有預設值。 這個名稱在資料庫內必須是唯一的。  
  
`[ \@taskid = ] taskid` 僅支援回溯相容性;使用 [sp_addpublication_snapshot &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addpublication-snapshot-transact-sql.md)。  
  
`[ \@restricted = ] 'restricted'` 僅支援回溯相容性;使用 *default_access*。  
  
`[ \@sync_method = ] _'sync_method'` 這是同步處理模式。 *sync_method* 是 **Nvarchar (13)**，它可以是下列值之一。  
  
|值|描述|  
|-----------|-----------------|  
|**native**|產生所有資料表的原生模式大量複製程式輸出。 *不支援 Oracle 發行者*。|  
|**character**|產生所有資料表的字元模式大量複製程式輸出。 _針對 Oracle 發行者，_ **字元**_只適用于快照_ 式複寫。|  
|**併發**|產生所有資料表的原生模式大量複製程式輸出，但在快照集期間，不鎖定資料表。 只支援交易式發行集使用這個項目。 *不支援 Oracle 發行者*。|  
|**concurrent_c**|產生所有資料表的字元模式大量複製程式輸出，但在快照集期間，不鎖定資料表。 只支援交易式發行集使用這個項目。|  
|**資料庫快照集**|從資料庫快照集產生所有資料表的原生模式大量複製程式輸出。 並非所有版本都可使用資料庫快照集 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本支援的功能清單，請參閱 [SQL Server 2016 版本支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)。|  
|**database snapshot character**|從資料庫快照集產生所有資料表的字元模式大量複製程式輸出。 並非所有版本都可使用資料庫快照集 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]版本支援的功能清單，請參閱 [SQL Server 2016 版本支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)。|  
|NULL (預設值)|發行者的預設值為 **native** [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 若為非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行者，當 *repl_freq* 的值為 **Snapshot** 時，預設為 **字元**，若為所有其他案例，則預設為 **concurrent_c** 。|  
  
`[ \@repl_freq = ] 'repl_freq'` 這是複寫頻率的類型， *repl_freq* 是 **Nvarchar (10)**，而且可以是下列其中一個值。  
  
|值|描述|  
|-----------|-----------------|  
|**連續** (預設) |發行者會提供所有記錄式交易的輸出。 若為非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行者，則必須將 *sync_method* 設定為 **concurrent_c**。|  
|**快照**|發行者只會產生已排程的同步處理事件。 若為非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行者，則必須將 *sync_method* 設定為 **字元**。|  
  
`[ \@description = ] 'description'` 這是發行集的選擇性描述。 *描述* 是 **Nvarchar (255)**，預設值是 Null。  
  
`[ \@status = ] 'status'` 指定發行集資料是否可用。 *狀態* 是 **Nvarchar (8)**，它可以是下列值之一。  
  
|值|描述|  
|-----------|-----------------|  
|**active**|訂閱者可以立即使用發行集資料。|  
|**非** 使用中 (預設) |在最初建立發行集時，訂閱者無法使用發行集資料 (它們可以訂閱，但不會處理這些訂閱)。|  
  
 *不支援 Oracle 發行者*。  
  
`[ \@independent_agent = ] 'independent_agent'` 指定這個發行集是否有獨立的散發代理程式。 *independent_agent* 是 **Nvarchar (5)**，預設值是 FALSE。 若為 **true**，則表示這個發行集有獨立的散發代理程式。 如果 **為 false**，則表示發行集使用共用散發代理程式，且每個發行者資料庫/訂閱者資料庫配對都有一個共用的代理程式。  
  
`[ \@immediate_sync = ] 'immediate_synchronization'` 指定每次執行快照集代理程式時，是否要建立發行集的同步處理檔案。 *immediate_synchronization* 是 **Nvarchar (5)**，預設值是 FALSE。 若 **為 true**，則每次執行快照集代理程式時都會建立或重新建立同步處理檔案。 如果在建立訂閱之前，快照集代理程式已經完成，訂閱者便能夠立即取得同步處理檔案。 新的訂閱會取得最近執行快照集代理程式所產生的最新同步處理檔案。 *independent_agent* 必須是 **true** ，才能讓 *immediate_synchronization* 成為 **true**。 如果 **為 false**，則只有在有新的訂閱時，才會建立同步處理檔案。 當您以累加方式將新的發行項加入至現有的發行集時，必須為每個訂用帳戶呼叫 [sp_addsubscription](../../relational-databases/system-stored-procedures/sp-addsubscription-transact-sql.md) 。 在訂閱之後，快照集代理程式啟動和完成之前，訂閱者無法接收同步處理檔案。  
  
`[ \@enabled_for_internet = ] 'enabled_for_internet'` 指定是否啟用發行集的網際網路，並判斷是否可以使用檔案傳輸協定 (FTP) ，將快照集檔案傳送給訂閱者。 *enabled_for_internet* 是 **Nvarchar (5)**，預設值是 FALSE。 若為 **true**，則發行集的同步處理檔案會放在 C:\PROGRAM Files\Microsoft SQL Server\MSSQL\MSSQL.x\Repldata\Ftp 目錄中。 使用者必須建立這個 Ftp 目錄。  
  
`[ \@allow_push = ] 'allow_push'` 指定是否可以為給定的發行集建立發送訂閱。 *allow_push* 是 **Nvarchar (5)**，預設值為 TRUE，允許發行集的發送訂閱。  
  
`[ \@allow_pull = ] 'allow_pull'` 指定是否可以為給定的發行集建立提取訂閱。 *allow_pull* 是 **Nvarchar (5)**，預設值是 FALSE。 如果 **為 false**，則表示發行集不允許提取訂閱。  
  
`[ \@allow_anonymous = ] 'allow_anonymous'` 指定是否可以為給定的發行集建立匿名訂閱。 *allow_anonymous* 是 **Nvarchar (5)**，預設值是 FALSE。 若 **為 true**， *immediate_synchronization* 也必須設定為 **true**。 如果 **為 false**，則表示發行集不允許匿名訂閱。  
  
`[ \@allow_sync_tran = ] 'allow_sync_tran'` 指定發行集是否允許立即更新訂閱。 *allow_sync_tran* 是 **Nvarchar (5)**，預設值是 FALSE。 *Oracle 發行者不支援* **true** 。  
  
`[ \@autogen_sync_procs = ] 'autogen_sync_procs'` 指定是否在發行者端產生更新訂閱的同步處理預存程式。 *autogen_sync_procs* 是 **Nvarchar (5)**，它可以是下列值之一。  
  
|值|描述|  
|-----------|-----------------|  
|**true**|在啟用更新訂閱時自動設定。|  
|**false**|在不啟用更新訂閱時自動設定，或針對 Oracle 發行者來自動設定。|  
|NULL (預設值)|當更新訂閱已啟用時，預設為 **true** ，當更新訂閱未啟用時，則為 **false** 。|  
  
> [!NOTE]  
>  系統會根據針對 *allow_queued_tran* 和 *allow_sync_tran* 所指定的值，覆寫 *autogen_sync_procs* 使用者提供的值。  
  
`[ \@retention = ] retention` 這是訂閱活動的保留期限（以小時為單位）。 *保留期* 為 **int**，預設值為336小時。 如果在保留期限內，訂閱不在使用中，它便會到期，且會被移除。 這個值可以大於發行者所用的散發資料庫的最大保留期限。 如果是 **0**，發行集的已知訂閱永遠不會過期，而且會由過期的訂閱清除代理程式移除。  
  
`[ \@allow_queued_tran = ] 'allow_queued_updating'` 啟用或停用訂閱者端的變更佇列，直到可以在發行者端套用變更為止。 *allow_queued_updating* 是 **Nvarchar (5)** ，預設值是 FALSE。 如果 **為 false**，則表示訂閱者端的變更未排入佇列。 *Oracle 發行者不支援* **true** 。  
  
`[ \@snapshot_in_defaultfolder = ] 'snapshot_in_default_folder'` 指定是否將快照集檔案儲存在預設資料夾中。 *snapshot_in_default_folder* 是 **Nvarchar (5)** 預設值為 TRUE。 若 **為 true**，則可以在預設資料夾中找到快照集檔案。 如果 **為 false**，則表示快照集檔案儲存在 *alternate_snapshot_folder* 所指定的替代位置。 替代位置可以在另一部伺服器、網路磁碟機或抽取式媒體 (如 CD-ROM 或抽取式磁碟) 中。 另外，您也可以將快照集檔案儲存在 FTP 站台中，供訂閱者以後擷取它們。 請注意，這個參數可以是 true，而且在 **\@ alt_snapshot_folder** 參數中仍有位置。 這個組合會指定將快照集檔案同時儲存在預設位置和替代位置中。  
  
`[ \@alt_snapshot_folder = ] 'alternate_snapshot_folder'` 指定快照集替代資料夾的位置。 *alternate_snapshot_folder* 是 **Nvarchar (255)** ，預設值是 Null。  
  
`[ \@pre_snapshot_script = ] 'pre_snapshot_script'` 指定 **.sql** 檔案位置的指標。 *pre_snapshot_script* 是 **Nvarchar (255)**，預設值是 Null。 在訂閱者端套用快照集時，散發代理程式會在執行任何複寫的物件指令碼之前，先執行前快照集 (pre-snapshot) 指令碼。 這個指令碼是在連接到訂閱資料庫時，在散發代理程式所用的安全性內容中執行。  
  
`[ \@post_snapshot_script = ] 'post_snapshot_script'` 指定 **.sql** 檔案位置的指標。 *post_snapshot_script* 是 **Nvarchar (255)**，預設值是 Null。 在初始同步處理期間，散發代理程式會先套用所有其他複寫的物件指令碼和資料，然後才執行後快照集 (post-snapshot) 指令碼。 這個指令碼是在連接到訂閱資料庫時，在散發代理程式所用的安全性內容中執行。  
  
`[ \@compress_snapshot = ] 'compress_snapshot'`指定將寫入 **\@ alt_snapshot_folder** 位置的快照集壓縮成 [!INCLUDE[msCoName](../../includes/msconame-md.md)] CAB 格式。 *compress_snapshot* 是 **Nvarchar (5)**，預設值是 FALSE。 **false** 指定不壓縮快照集。 **true** 指定將壓縮快照集。 超出 2 GB 的快照集檔案無法壓縮。 壓縮的快照集檔案是在執行散發代理程式的位置進行解壓縮；提取訂閱通常會搭配使用壓縮的快照集，因此，會在訂閱者端將檔案解壓縮。 預設資料夾中的快照集無法壓縮。  
  
`[ \@ftp_address = ] 'ftp_address'` 這是散發者之 FTP 服務的網路位址。 *ftp_address* 是 **sysname**，預設值是 Null。 指定發行集快照集檔案所在的位置，以便訂閱者的散發代理程式或合併代理程式能夠加以收取。 由於每個發行集都會儲存這個屬性，因此每個發行集都可以有不同的 *ftp_address*。 發行集必須支援利用 FTP 來傳播快照集。  
  
`[ \@ftp_port = ] ftp_port` 這是散發者之 FTP 服務的埠號碼。 *ftp_port* 是 **int**，預設值是21。 指定發行集快照集檔案所在的位置，以便訂閱者的散發代理程式或合併代理程式能夠加以收取。 由於每個發行集都會儲存這個屬性，因此每個發行集都可以有它自己的 *ftp_port*。  
  
`[ \@ftp_subdirectory = ] 'ftp_subdirectory'` 指定如果發行集支援利用 FTP 來傳播快照集，散發代理程式或合併代理程式訂閱者可收取快照集檔案的位置。 *ftp_subdirectory* 是 **Nvarchar (255)**，預設值是 Null。 由於每個發行集都會儲存這個屬性，因此每個發行集都可以有自己的 *ftp_subdirctory* 或選擇沒有任何子目錄，以 Null 值表示。  
  
`[ \@ftp_login = ] 'ftp_login'` 這是用來連接到 FTP 服務的使用者名稱。 *ftp_login* 是 **sysname**，預設值是 ANONYMOUS。  
  
`[ \@ftp_password = ] 'ftp_password'` 這是用來連接到 FTP 服務的使用者密碼。 *ftp_password* 是 **sysname**，預設值是 Null。  
  
`[ \@allow_dts = ] 'allow_dts'` 指定發行集允許資料轉換。 您可以在建立訂閱時，指定一個 DTS 封裝。 *allow_transformable_subscriptions* 是 **Nvarchar (5)** ，預設值是 FALSE，不允許 DTS 轉換。 當 *allow_dts* 為 true 時， *sync_method* 必須設定為 **字元** 或 **concurrent_c**。  
  
 *Oracle 發行者不支援* **true** 。  
  
`[ \@allow_subscription_copy = ] 'allow_subscription_copy'` 啟用或停用複製訂閱這個發行集之訂閱資料庫的能力。 *allow_subscription_copy* 是 **Nvarchar (5)**，預設值是 FALSE。  
  
`[ \@conflict_policy = ] 'conflict_policy'` 指定使用佇列更新訂閱者選項時，所遵循的衝突解決原則。 *conflict_policy* 是 **Nvarchar (100)** 預設值為 Null，它可以是下列值之一。  
  
|值|描述|  
|-----------|-----------------|  
|**pub wins**|發行者在衝突中獲勝。|  
|**sub reinit**|重新初始化這項訂閱。|  
|**sub wins**|訂閱者在衝突中獲勝。|  
|NULL (預設值)|如果是 Null，且發行集為快照式發行集，則預設原則會變成 **sub 重新初始化**。 如果是 Null，且發行集不是快照式發行集，則預設值會變成 **pub wins**。|  
  
 *不支援 Oracle 發行者*。  
  
`[ \@centralized_conflicts = ] 'centralized_conflicts'` 指定是否將衝突記錄儲存在發行者端。 *centralized_conflicts* 是 **Nvarchar (5)**，預設值為 TRUE。 若 **為 true**，則會將衝突記錄儲存在發行者端。 如果 **為 false**，則表示衝突記錄同時儲存在發行者端和造成衝突的訂閱者端。 *不支援 Oracle 發行者*。  
  
`[ \@conflict_retention = ] conflict_retention` 指定衝突的保留期限（以天為單位）。 這是針對點對點異動複寫和佇列更新訂閱儲存衝突中繼資料的時間週期。 *conflict_retention* 是 **int**，預設值是14。 *不支援 Oracle 發行者*。  
  
`[ \@queue_type = ] 'queue_type'` 指定使用哪一種類型的佇列。 *queue_type* 是 **Nvarchar (10)**，預設值是 Null，而且可以是下列值之一。  
  
|值|描述|  
|-----------|-----------------|  
|**sql**|利用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 來儲存交易。|  
|NULL (預設值)|預設為 **sql**，指定用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 來儲存交易。|  
  
> [!NOTE]  
>  已不再支援使用 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Message Queuing。 指定 **msmq** 的值將會產生警告，而且複寫會自動將值設定為 **sql**。  
  
 *不支援 Oracle 發行者*。  
  
`[ \@add_to_active_directory = ] 'add\to_active_directory'` 此參數已被取代，而且僅支援腳本的回溯相容性。 您不能再將發行集資訊加入 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Active Directory 中。  
  
`[ \@logreader_job_name = ] 'logreader_agent_name'` 這是現有代理程式作業的名稱。 *logreader_agent_name* 是 **sysname**，預設值是 Null。 只有在記錄讀取器代理程式將使用現有的作業，而不建立新的作業時，才指定這個參數。  
  
`[ \@qreader_job_name = ] 'queue_reader_agent_name'` 這是現有代理程式作業的名稱。 *queue_reader_agent_name* 是 **sysname**，預設值是 Null。 只有在佇列讀取器代理程式將使用現有的作業，而不建立新的作業時，才指定這個參數。  
  
`[ \@publisher = ] 'publisher'` 指定非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行者。 *publisher* 是 **sysname**，預設值是 Null。  
  
> [!NOTE]  
>  將發行集加入至發行者時，不應使用「*發行者*」 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
`[ \@allow_initialize_from_backup = ] 'allow_initialize_from_backup'` 指出訂閱者是否可以從備份（而不是初始快照集）初始化這個發行集的訂閱。 *allow_initialize_from_backup* 是 **Nvarchar (5)**，它可以是下列其中一個值：  
  
|值|描述|  
|-----------|-----------------|  
|**true**|啟用從備份進行的初始化。|  
|**false**|停用從備份進行的初始化。|  
|NULL (預設值)|針對點對點複寫拓撲中的發行集，預設為 **true** ，所有其他發行集的預設值為 **false** 。|  
  
 如需詳細資訊，請參閱 [不使用快照集初始化交易式訂閱](../../relational-databases/replication/initialize-a-transactional-subscription-without-a-snapshot.md)中手動初始化訂閱。  
  
> [!WARNING]  
>  為了避免遺漏訂閱者資料，當您使用 **sp_addpublication** 搭配 `@allow_initialize_from_backup = N'true'`時，請一律使用 `@immediate_sync = N'true'`。  
  
`[ \@replicate_ddl = ] replicate_ddl` 指出是否支援發行集的架構複寫。 *replicate_ddl* 是 **int**，發行者的預設值是 **1** ，而非發行者的預設值是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **0** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 **1** 表示複寫在發行者端執行的資料定義語言 (DDL) 語句， **0** 表示不復寫 ddl 語句。 *不支援 Oracle 發行者使用結構描述複寫。* 如需詳細資訊，請參閱[對發行集資料庫進行結構描述變更](../../relational-databases/replication/publish/make-schema-changes-on-publication-databases.md)。  
  
 當 DDL 語句加入資料行時，就會接受 *\@ replicate_ddl* 參數。 當 DDL 語句因為下列原因而改變或卸載資料行時，會忽略 *\@ replicate_ddl* 參數。  
  
-   當卸除資料行時必須更新 sysarticlecolumns，以免新的 DML 陳述式包含造成散發代理程式失敗的已卸除資料行。 因為複寫必須一律複寫架構變更，所以會忽略 *\@ replicate_ddl* 參數。  
  
-   當更改資料行時，來源資料類型或 Null 屬性可能已變更，造成 DML 陳述式包含的值可能與散發者端的資料表不相容。 這類 DML 陳述式可能會造成散發代理程式失敗。 因為複寫必須一律複寫架構變更，所以會忽略 *\@ replicate_ddl* 參數。  
  
-   當 DDL 陳述式加入新的資料行時，sysarticlecolumns 不包括新的資料行。 DML 陳述式不會嘗試複寫新資料行的資料。 接受此參數是因為可接受複寫或不複寫 DDL。  
  
`[ \@enabled_for_p2p = ] 'enabled_for_p2p'` 讓發行集用於點對點複寫拓撲中。 *enabled_for_p2p* 是 **Nvarchar (5)**，預設值是 FALSE。 **true** 表示發行集支援點對點複寫。 將 *enabled_for_p2p* 設定為 [ **true**] 時，會套用下列限制：  
  
-   *allow_anonymous* 必須為 **false**。  
  
-   *allow_dts* 必須為 **false**。  
  
-   *allow_initialize_from_backup* 必須是 **true**。  
  
-   *allow_queued_tran* 必須為 **false**。  
  
-   *allow_sync_tran* 必須為 **false**。  
  
-   *conflict_policy* 必須為 **false**。  
  
-   *independent_agent* 必須是 **true**。  
  
-   *repl_freq* 必須是 **連續** 的。  
  
-   *replicate_ddl* 必須是 **1**。  
  
 如需相關資訊，請參閱 [Peer-to-Peer Transactional Replication](../../relational-databases/replication/transactional/peer-to-peer-transactional-replication.md)。  
  
`[ \@publish_local_changes_only = ] 'publish_local_changes_only'` [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]  
  
`[ \@enabled_for_het_sub = ] 'enabled_for_het_sub'` 可讓發行集支援非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 訂閱者。 *enabled_for_het_sub* 是 **Nvarchar (5)** ，預設值為 FALSE。 **True** 值表示發行集支援非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 訂閱者。 當 *enabled_for_het_sub* 為 **true** 時，適用下列限制：  
  
-   *allow_initialize_from_backup* 必須為 **false**。  
  
-   *allow_push* 必須是 **true**。  
  
-   *allow_queued_tran* 必須為 **false**。  
  
-   *allow_subscription_copy* 必須為 **false**。  
  
-   *allow_sync_tran* 必須為 **false**。  
  
-   *autogen_sync_procs* 必須為 **false**。  
  
-   *conflict_policy* 必須是 Null。  
  
-   *enabled_for_internet* 必須為 **false**。  
  
-   *enabled_for_p2p* 必須為 **false**。  
  
-   *ftp_address* 必須是 Null。  
  
-   *ftp_subdirectory* 必須是 Null。  
  
-   *ftp_password* 必須是 Null。  
  
-   *pre_snapshot_script* 必須是 Null。  
  
-   *post_snapshot_script* 必須是 Null。  
  
-   *replicate_ddl* 必須是0。  
  
-   *qreader_job_name* 必須是 Null。  
  
-   *queue_type* 必須是 Null。  
  
-   *sync_method* 不能是 **原生** 或 **並行**。  
  
 如需詳細資訊，請參閱 [Non-SQL Server Subscribers](../../relational-databases/replication/non-sql/non-sql-server-subscribers.md)。  
  
`[ \@p2p_conflictdetection = ] 'p2p_conflictdetection'` 當發行集啟用點對點複寫時，可讓散發代理程式偵測衝突。 *p2p_conflictdetection* 是 **Nvarchar (5)** ，預設值為 TRUE。 如需相關資訊，請參閱 [Conflict Detection in Peer-to-Peer Replication](../../relational-databases/replication/transactional/peer-to-peer-conflict-detection-in-peer-to-peer-replication.md)。  
  
`[ \@p2p_originator_id = ] p2p_originator_id` 指定點對點拓撲中節點的識別碼。 *p2p_originator_id* 是 **int**，預設值是 Null。 如果 *p2p_conflictdetection* 設定為 TRUE，此識別碼會用於衝突偵測。 請指定拓撲中從未使用過的非零正數識別碼。 如需已經使用的識別碼清單，請執行 [sp_help_peerconflictdetection](../../relational-databases/system-stored-procedures/sp-help-peerconflictdetection-transact-sql.md)。  
  
`[ \@p2p_continue_onconflict = ] 'p2p_continue_onconflict'` 決定在偵測到衝突之後，散發代理程式是否繼續處理變更。 *p2p_continue_onconflict* 是 **Nvarchar (5)** ，預設值為 FALSE。  
  
> [!CAUTION]  
>  我們建議您使用預設值 FALSE。 當這個選項設定為 TRUE 時，散發代理程式會套用具有最高訂閱者識別碼之節點的衝突資料列，藉以嘗試聚合拓撲中的資料。 但是，這個方法無法保證聚合。 您應該確定在偵測到衝突之後，拓撲是一致的。 如需詳細資訊，請參閱＜ [Conflict Detection in Peer-to-Peer Replication](../../relational-databases/replication/transactional/peer-to-peer-conflict-detection-in-peer-to-peer-replication.md)＞中的「處理衝突」。  
  
  
`[ \@allow_partition_switch = ] 'allow_partition_switch'` 指定 ALTER TABLE .。。SWITCH 語句可以針對已發行的資料庫執行。 *allow_partition_switch* 是 **Nvarchar (5)** ，預設值為 FALSE。 如需詳細資訊，請參閱[複寫資料分割資料表及索引](../../relational-databases/replication/publish/replicate-partitioned-tables-and-indexes.md)。  
  
`[ \@replicate_partition_switch = ] 'replicate_partition_switch'` 指定 ALTER TABLE .。。針對已發行資料庫執行的 SWITCH 語句應複寫至「訂閱者」。 *replicate_partition_switch* 是 **Nvarchar (5)** ，預設值為 FALSE。 只有當 *allow_partition_switch* 設定為 TRUE 時，這個選項才有效。  

## <a name="return-code-values"></a>傳回碼值  
 **0** (成功) 或 **1** (失敗)   
  
## <a name="remarks"></a>備註  
 **sp_addpublication** 用於快照式複寫和異動複寫中。  
  
 如果有多個發行集發行相同的資料庫物件，則只有 *replicate_ddl* 值為 **1** 的發行集，才會複寫 ALTER TABLE、ALTER VIEW、ALTER PROCEDURE、ALTER FUNCTION 和 alter TRIGGER ddl 語句。 不過，所有發行已卸除之資料行的發行集都會複寫 ALTER TABLE DROP COLUMN DDL 陳述式。  
  
 在啟用 DDL 複寫的情況下 (*replicate_ddl*  =  **1**) 發行集，為了進行發行集的非複寫 DDL 變更，必須先執行 [sp_changepublication](../../relational-databases/system-stored-procedures/sp-changepublication-transact-sql.md) ，以將 *replicate_ddl* 設定為 **0**。 發出非複寫 DDL 語句之後， [sp_changepublication](../../relational-databases/system-stored-procedures/sp-changepublication-transact-sql.md) 可以再次執行，以重新開啟 ddl 複寫。  
  
## <a name="example"></a>範例  
 [!code-sql[HowTo#sp_AddTranPub](../../relational-databases/replication/codesnippet/tsql/sp-addpublication-transa_1.sql)]  
  
## <a name="permissions"></a>權限  
 只有 **系統管理員（sysadmin** ）固定伺服器角色或 **db_owner** 固定資料庫角色的成員，才能夠執行 **sp_addpublication**。 Windows 驗證登入必須擁有代表其 Windows 使用者帳戶的資料庫使用者帳戶。 代表 Windows 群組的使用者帳戶權限不足。  
  
## <a name="see-also"></a>另請參閱  
 [sp_addlogreader_agent &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addlogreader-agent-transact-sql.md)   
 [sp_addpublication_snapshot &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addpublication-snapshot-transact-sql.md)   
 [sp_changepublication &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-changepublication-transact-sql.md)   
 [sp_droppublication &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-droppublication-transact-sql.md)   
 [sp_helppublication &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helppublication-transact-sql.md)   
 [sp_replicationdboption &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-replicationdboption-transact-sql.md)   
 [發行資料和資料庫物件](../../relational-databases/replication/publish/publish-data-and-database-objects.md)   
 [複寫預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/replication-stored-procedures-transact-sql.md)  
  
  
