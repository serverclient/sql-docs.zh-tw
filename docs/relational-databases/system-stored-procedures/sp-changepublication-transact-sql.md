---
description: sp_changepublication (Transact-SQL)
title: sp_changepublication (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/29/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sp_changepublication
- sp_changepublication_TSQL
helpviewer_keywords:
- sp_changepublication
ms.assetid: c36e5865-25d5-42b7-b045-dc5036225081
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 6e29fade73adba6cb82b6d4ac22cae122d4c60cd
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98169441"
---
# <a name="sp_changepublication-transact-sql"></a>sp_changepublication (Transact-SQL)
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

  變更發行集的屬性。 這個預存程序執行於發行集資料庫的發行者端。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
sp_changepublication [ [ @publication = ] 'publication' ]  
    [ , [ @property = ] 'property' ]  
    [ , [ @value = ] 'value' ]  
    [ , [ @force_invalidate_snapshot = ] force_invalidate_snapshot ]  
    [ , [ @force_reinit_subscription = ] force_reinit_subscription ]  
    [ , [ @publisher = ] 'publisher' ]  
```  
  
## <a name="arguments"></a>引數  
`[ @publication = ] 'publication'` 這是發行集的名稱。 *發行* 集是 **sysname**，預設值是 Null。  
  
`[ @property = ] 'property'` 這是要變更的發行集屬性。 *屬性* 是 **Nvarchar (255)**。  
  
`[ @value = ] 'value'` 這是新的屬性值。 *值* 是 **Nvarchar (255)**，預設值是 Null。  
  
 下表描述可變更的發行集屬性及這些屬性值的限制。  
  
|屬性|值|描述|  
|--------------|-----------|-----------------|  
|**allow_anonymous**|**true**|您可以為給定的發行集建立匿名訂閱， *immediate_sync* 也必須是 **true**。 點對點發行集的這個項目不能變更。|  
||**false**|不能建立給定發行集的匿名訂閱。 點對點發行集的這個項目不能變更。|  
|**allow_initialize_from_backup**|**true**|訂閱者能夠從備份中，而不是從初始快照集中，對這個發行集的訂閱進行初始化。 非 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
||**false**|訂閱者必須使用初始快照集。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**allow_partition_switch**|**true**|ALTER TABLE .。。SWITCH 語句可以針對已發行的資料庫執行。 如需詳細資訊，請參閱[複寫資料分割資料表及索引](../../relational-databases/replication/publish/replicate-partitioned-tables-and-indexes.md)。|  
||**false**|ALTER TABLE .。。SWITCH 語句無法針對已發行的資料庫執行。|  
|**allow_pull**|**true**|允許給定發行集的提取訂閱。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
||**false**|不允許給定發行集的提取訂閱。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**allow_push**|**true**|允許給定發行集的發送訂閱。|  
||**false**|不允許給定發行集的發送訂閱。|  
|**allow_subscription_copy**|**true**|啟用複製訂閱這個發行集之資料庫的能力。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
||**false**|停用複製訂閱這個發行集之資料庫的能力。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**alt_snapshot_folder**||快照集替代資料夾的位置。|  
|**centralized_conflicts**|**true**|衝突記錄會儲存在發行者端。 只有在沒有使用中的訂閱時，這個項目才能改變。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
||**false**|衝突記錄會儲存在發行者端和造成衝突的訂閱者端。 只有在沒有使用中的訂閱時，這個項目才能改變。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**compress_snapshot**|**true**|將替代快照集資料夾中的快照集壓縮成 .cab 檔案格式。 預設快照集資料夾中的快照集無法壓縮。|  
||**false**|不壓縮快照集，這是複寫的預設行為。|  
|**conflict_policy**|**pub wins**|更新訂閱者的衝突解決原則，發行者在衝突中獲勝。 只有在沒有使用中的訂閱時，才能改變這個屬性。 不支援 Oracle 發行者使用這個值。|  
||**sub reinit**|對於更新訂閱者，如果發生衝突，訂閱就必須重新初始化。 只有在沒有使用中的訂閱時，才能改變這個屬性。 不支援 Oracle 發行者使用這個值。|  
||**sub wins**|更新訂閱者的衝突解決原則，訂閱者在衝突中獲勝。 只有在沒有使用中的訂閱時，才能改變這個屬性。 不支援 Oracle 發行者使用這個值。|  
|**conflict_retention**||指定衝突保留期限的 **整數**（以天為單位）。 預設保留 14 天。 **0** 表示不需要清除衝突。 不支援 Oracle 發行者使用這個值。|  
|**description**||描述發行集的選擇性項目。|  
|**enabled_for_het_sub**|**true**|啟用發行集以支援非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 訂閱者。 當發行集有訂閱時， **enabled_for_het_sub** 無法變更。 將 **enabled_for_het_sub** 設定為 true 之前，您可能需要在 [transact-sql)  (執行複寫預存程式](../../relational-databases/system-stored-procedures/sp-changepublication-transact-sql.md)，以符合下列需求：<br /> - **allow_queued_tran** 必須為 **false**。<br /> - **allow_sync_tran** 必須為 **false**。<br /> 將 **enabled_for_het_sub** 變更為 [ **true** ] 可能會變更現有的發行集設定。 如需詳細資訊，請參閱 [Non-SQL Server Subscribers](../../relational-databases/replication/non-sql/non-sql-server-subscribers.md)。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
||**false**|發行集不支援非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 訂閱者。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**enabled_for_internet**|**true**|啟用發行集的網際網路功能，以及可以利用檔案傳輸通訊協定 (FTP)，將快照集檔案傳送給訂閱者。 發行集的同步處理檔案會放在下列目錄中：C:\Program Files\Microsoft SQL Server\MSSQL\Repldata\ftp。 *ftp_address* 不可以是 Null。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
||**false**|不啟用發行集的網際網路功能。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**enabled_for_p2p**|**true**|發行集支援點對點複寫。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。<br /> 若要將 **enabled_for_p2p** 設定為 **true**，則適用下列限制：<br /> - **allow_anonymous** 必須為 **false**<br /> - **allow_dts** 必須為 **false**。<br /> - **allow_initialize_from_backup** 必須是 **true**<br /> - **allow_queued_tran** 必須為 **false**。<br /> - **allow_sync_tran** 必須為 **false**。<br /> - **enabled_for_het_sub** 必須為 **false**。<br /> - **independent_agent** 必須是 **true**。<br /> - **repl_freq** 必須是 **連續** 的。<br /> - **replicate_ddl** 必須是 **1**。|  
||**false**|發行集不支援點對點複寫。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**ftp_address**||發行集快照集檔案的 FTP 存取位置。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**ftp_login**||用來連接到 FTP 服務的使用者名稱，允許使用 ANONYMOUS 值。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**ftp_password**||用來連接到 FTP 服務之使用者名稱的密碼。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**ftp_port**||散發者的 FTP 服務通訊埠編號。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**ftp_subdirectory**||指定在發行集支援利用 FTP 來傳播快照集時，要在哪裡建立快照集檔案。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
|**immediate_sync**|**true**|每次執行快照集代理程式時，都要建立或重新建立發行集的同步處理檔案。 如果在訂閱之前，快照集代理程式已完成一次，訂閱者便能在訂閱之後，立即收到同步處理檔案。 新的訂閱會取得最近執行快照集代理程式所產生的最新同步處理檔案。 *independent_agent* 也必須是 **true**。 如需 **immediate_sync** 的詳細資訊，請參閱下面的備註。|  
||**false**|只有在新訂閱存在時，才會建立同步處理檔案。 在訂閱之後，快照集代理程式啟動和完成之前，訂閱者無法接收同步處理檔案。|  
|**independent_agent**|**true**|發行集有它自己專用的散發代理程式。|  
||**false**|發行集使用共用的散發代理程式，每一組發行者/訂閱資料庫都有共用的代理程式。|  
|**p2p_continue_onconflict**|**true**|散發代理程式會在偵測到衝突時繼續處理變更。<br /> **注意：** 建議您使用的預設值 `FALSE` 。 當這個選項設定為時 `TRUE` ，散發代理程式會嘗試從具有最高擁有者識別碼的節點套用衝突資料列，藉以在拓撲中將資料聚合。 但是，這個方法無法保證聚合。 您應該確定在偵測到衝突之後，拓撲是一致的。 如需詳細資訊，請參閱＜ [Conflict Detection in Peer-to-Peer Replication](../../relational-databases/replication/transactional/peer-to-peer-conflict-detection-in-peer-to-peer-replication.md)＞中的「處理衝突」。|  
||**false**|散發代理程式會在偵測到衝突時停止處理變更。|  
|**post_snapshot_script**||指定在初始同步處理期間，套用所有其他複寫的物件指令碼和資料之後，散發代理程式所執行之 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼檔案的位置。|  
|**pre_snapshot_script**||指定在初始同步處理期間，套用所有其他複寫的物件指令碼和資料之前，散發代理程式所執行之 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼檔案的位置。|  
|**publish_to_ActiveDirectory**|**true**|這個參數已被取代，支援它的目的，只是為了與舊版的指令碼相容。 您不能再將發行集資訊加入 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Active Directory 中。|  
||**false**|從 Active Directory 中移除發行集資訊。|  
|**queue_type**|**sql**|利用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 來儲存交易。 只有在沒有使用中的訂閱時，才能改變這個屬性。<br /><br /> 注意： [!INCLUDE[msCoName](../../includes/msconame-md.md)] 已不再支援使用訊息佇列。 指定 **msmq** 值 *的值會導致錯誤* 。|  
|**repl_freq**|**連續**|發行所有記錄式交易的輸出。|  
||**snapshot (快照集)** |只發行已排程的同步處理事件。|  
|**replicate_ddl**|**1**|複寫在發行者端執行的資料定義語言 (DDL) 陳述式。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。|  
||**0**|不複寫 DDL 陳述式。 非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行集的這個屬性不能變更。 使用點對點複寫時，不能停用結構描述變更的複寫。|  
|**replicate_partition_switch**|**true**|ALTER TABLE .。。針對已發行資料庫執行的 SWITCH 語句應複寫至「訂閱者」。 只有當 *allow_partition_switch* 設定為 TRUE 時，這個選項才有效。 如需詳細資訊，請參閱[複寫資料分割資料表及索引](../../relational-databases/replication/publish/replicate-partitioned-tables-and-indexes.md)。|  
||**false**|ALTER TABLE .。。SWITCH 語句不應該複寫至「訂閱者」。|  
|**保留**||**整數** ，代表訂閱活動的保留期限（以小時為單位）。 如果在保留期限內，訂閱不在使用中，就會移除它。|  
|**snapshot_in_defaultfolder**|**true**|快照集檔案儲存在預設快照集資料夾中。 如果也指定了 *alt_snapshot_folder*，快照集檔案會儲存在預設位置和替代位置中。|  
||**false**|快照集檔案會儲存在 *alt_snapshot_folder* 所指定的替代位置。|  
|**status**|**active**|當建立發行集時，訂閱者可以立即使用發行集資料。 不支援 Oracle 發行者使用這個值。|  
||**inactive**|當建立發行集時，訂閱者無法使用發行集資料。 不支援 Oracle 發行者使用這個值。|  
|**sync_method**|**native**|當同步處理訂閱時，使用所有資料表的原生模式大量複製輸出。|  
||**character**|當同步處理訂閱時，使用所有資料表的字元模式大量複製輸出。|  
||**併發**|使用所有資料表的原生模式大量複製程式輸出，但在快照集的產生程序中，不鎖定資料表。 這個項目對快照式複寫無效。|  
||**concurrent_c**|使用所有資料表的字元模式大量複製程式輸出，但在快照集的產生程序中，不鎖定資料表。 這個項目對快照式複寫無效。|  
|**taskid**||這個屬性已被取代，不再受到支援。|  
|**allow_drop**|**true**|`DROP TABLE`針對屬於異動複寫一部分的發行項啟用 DLL 支援。 支援的最低版本： [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] Service pack 2 或更新版本，以及 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] service pack 1 或更新版本。 其他參考： [KB 3170123](https://support.microsoft.com/help/3170123/supports-drop-table-ddl-for-articles-that-are-included-in-transactional-replication-in-sql-server-2014-or-in-sql-server-2016-sp1)|
||**false**|`DROP TABLE`針對屬於異動複寫一部分的發行項停用 DLL 支援。 這是這個屬性的 **預設** 值。|
|**Null** (預設) ||傳回 *屬性* 支援值的清單。|  
  
`[ @force_invalidate_snapshot = ] force_invalidate_snapshot` 認可這個預存程式所採取的動作可能使現有的快照集失效。 *force_invalidate_snapshot* 是 **bit**，預設值是 **0**。  
  - **0** 指定發行項的變更不會使快照集失效。 如果預存程序偵測到變更需要新的快照集，就會發生錯誤，且不會進行任何變更。  
  - **1** 指定發行項的變更可能會導致快照集無效。 如果有現有的訂閱需要新的快照集，這個值會提供要標示為已棄用之現有快照集的權限，此時會產生新的快照集。   
請參閱＜備註＞一節，以了解在變更時需要產生新快照集的屬性。  
  
[**@force_reinit_subscription =** ] *force_reinit_subscription*  
 認可這個預存程序所採取的動作可能需要重新初始化現有的訂閱。 *force_reinit_subscription* 是 **bit** ，預設值是 **0**。  
  - **0** 指定發行項的變更不會使訂閱重新初始化。 如果預存程序偵測到變更需要重新初始化現有的訂閱，就會發生錯誤，且不會進行任何變更。  
  - **1** 指定發行項的變更會使現有的訂閱重新初始化，且會提供將發生之訂閱重新初始化的許可權。  
  
`[ @publisher = ] 'publisher'` 指定非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 發行者。 *publisher* 是 **sysname**，預設值是 Null。  
  
  > [!NOTE]  
  >  在發行者上變更發行項屬性時，不應使用「*發行者*」 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
## <a name="return-code-values"></a>傳回碼值  
 **0** (成功) 或 **1** (失敗)   
  
## <a name="remarks"></a>備註  
 **sp_changepublication** 用於快照式複寫和異動複寫中。  
  
 變更下列任何屬性之後，您必須產生新的快照集，而且您必須為 *force_invalidate_snapshot* 參數指定 **1** 的值。  
-   **alt_snapshot_folder**  
-   **compress_snapshot**  
-   **enabled_for_het_sub**  
-   **ftp_address**  
-   **ftp_login**  
-   **ftp_password**  
-   **ftp_port**  
-   **ftp_subdirectory**  
-   **post_snapshot_script**  
-   **pre_snapshot_script**  
-   **snapshot_in_defaultfolder**  
-   **sync_mode**  
  
若要使用 **publish_to_active_directory** 參數列出 Active Directory 中的發行集物件， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 必須已經在 Active Directory 中建立物件。  
  
## <a name="impact-of-immediate-sync"></a>立即同步的影響  
 當立即同步開啟時，即使沒有任何訂閱，系統也會在產生初始快照集之後，立即追蹤記錄中的所有變更。 當客戶使用備份來加入新的對等節點時，就會使用記錄的變更。 還原備份之後，對等會與執行備份之後發生的任何其他變更進行同步處理。 因為這些命令會在散發資料庫中追蹤，所以同步處理邏輯可以查看最後一個備份的 LSN，並使用這個做為起點，知道如果備份是在最大保留期限內進行，則可使用此命令。  (最小保留期間的預設值為0小時，而最大保留期限為24小時。 )   
  
 當立即同步關閉時，系統至少會在最小保留期限內保存變更，並且針對已經複寫的所有交易立即清除變更。 如果立即同步已關閉，而且設定了預設保留期限，系統可能會清除建立備份之後發生的必要變更，而且新的對等節點不會正確初始化。 剩下的唯一選項就是停止拓撲。 將立即同步設定為開啟可提供較大的彈性，建議您針對 P2P 複寫設定此選項。  
  
## <a name="example"></a>範例  
 [!code-sql[HowTo#sp_changepublication](../../relational-databases/replication/codesnippet/tsql/sp-changepublication-tra_1.sql)]  
  
## <a name="permissions"></a>權限  
 只有 **系統管理員（sysadmin** ）固定伺服器角色或 **db_owner** 固定資料庫角色的成員，才能夠執行 **sp_changepublication**。  
  
## <a name="see-also"></a>另請參閱  
 [檢視及修改發行集屬性](../../relational-databases/replication/publish/view-and-modify-publication-properties.md)   
 [變更發行集與發行項屬性](../../relational-databases/replication/publish/change-publication-and-article-properties.md)   
 [sp_addpublication &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addpublication-transact-sql.md)   
 [sp_droppublication &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-droppublication-transact-sql.md)   
 [sp_helppublication &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helppublication-transact-sql.md)   
 [複寫預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/replication-stored-procedures-transact-sql.md)  
  
  
