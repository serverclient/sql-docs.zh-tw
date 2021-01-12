---
description: sysmergepublications (Transact-SQL)
title: sysmergepublications (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sysmergepublications
- sysmergepublications_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysmergepublications system table
ms.assetid: 7f82c6c3-22d1-47c0-a92b-4d64b98cc455
author: cawrites
ms.author: chadam
ms.openlocfilehash: 0b2a7e7783ffb67688041a6da39ea99f945a607f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097328"
---
# <a name="sysmergepublications-transact-sql"></a>sysmergepublications (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對資料庫中每個已定義的合併式發行集，各包含一個資料列。 這份資料表儲存在發行集和訂閱資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**publisher**|**sysname**|預設伺服器的名稱。|  
|**publisher_db**|**sysname**|預設發行者資料庫的名稱。|  
|**name**|**sysname**|發行集的名稱。|  
|**description**|**nvarchar(255)**|發行集的簡要描述。|  
|**保留**|**int**|整個發行集的保留期限，其中單位是以 **retention_period_unit** 資料行的值表示。|  
|**publication_type**|**tinyint**|指出發行集的篩選：<br /><br /> **0** = 未篩選。<br /><br /> **1** = 已篩選。|  
|**pubid**|**uniqueidentifier**|這個發行集的唯一識別碼。 這是加入發行集時產生的識別碼。|  
|**designmasterid**|**uniqueidentifier**|保留供未來使用。|  
|**parentid**|**uniqueidentifier**|指出從中建立目前的對等或子集發行集 (用於階層式發行拓撲) 的父發行集。|  
|**sync_mode**|**tinyint**|這個發行集的同步處理模式：<br /><br /> **0** = 原生。<br /><br /> **1** = 字元。|  
|**allow_push**|**int**|指出發行集是否允許發送訂閱。<br /><br /> **0** = 不允許發送訂閱。<br /><br /> **1** = 允許發送訂閱。|  
|**allow_pull**|**int**|指出發行集是否允許提取訂閱。<br /><br /> **0** = 不允許提取訂閱。<br /><br /> **1** = 允許提取訂閱。|  
|**allow_anonymous**|**int**|指出發行集是否允許匿名訂閱。<br /><br /> **0** = 不允許匿名訂閱。<br /><br /> **1** = 允許匿名訂閱。|  
|**centralized_conflicts**|**int**|指出衝突記錄是否會儲存在發行者端：<br /><br /> **0** = 衝突記錄不會儲存在發行者端。<br /><br /> **1** = 將衝突記錄儲存在發行者端。|  
|**status**|**tinyint**|保留供未來使用。|  
|**snapshot_ready**|**tinyint**|指出發行集快照集的狀態：<br /><br /> **0** = 快照集尚未準備好可供使用。<br /><br /> **1** = 快照集已備妥可供使用。<br /><br /> **2** = 必須建立這個發行集的新快照集。|  
|**enabled_for_internet**|**bit**|指出是否利用 FTP 和其他服務，在網際網路中公開發行集的同步處理檔案。<br /><br /> **0** = 可以從網際網路存取同步處理檔案。<br /><br /> **1** = 無法從網際網路存取同步處理檔案。|  
|**dynamic_filters**|**bit**|指出是否利用參數化資料列篩選器來篩選發行集。<br /><br /> **0** = 發行集未篩選資料列。<br /><br /> **1** = 發行集已篩選資料列。|  
|**snapshot_in_defaultfolder**|**bit**|指定是否將快照集檔案儲存在預設資料夾中。<br /><br /> **0** = 快照集檔案位於預設資料夾中。<br /><br /> **1** = 快照集檔案儲存在 **alt_snapshot_folder** 所指定的位置。|  
|**alt_snapshot_folder**|**nvarchar(255)**|快照集替代資料夾的位置。|  
|**pre_snapshot_script**|**nvarchar(255)**|的指標。在訂閱者端套用快照集時，合併代理程式在任何複寫物件腳本之前執行的 **sql** 檔。|  
|**post_snapshot_script**|**nvarchar(255)**|的指標。在初始同步處理期間套用所有其他複寫物件腳本和資料之後，合併代理程式所執行的 **sql** 檔。|  
|**compress_snapshot**|**bit**|指定寫入 **alt_snapshot_folder** 位置的快照集是否壓縮成 [!INCLUDE[msCoName](../../includes/msconame-md.md)] CAB 格式。 **0** 指定不壓縮檔案。|  
|**ftp_address**|**sysname**|散發者的檔案傳輸通訊協定 (FTP) 服務的網路位址。 指定發行集快照集檔案所在的位置，以便在啟用 FTP 的情況下，合併代理程式能夠加以收取。|  
|**ftp_port**|**int**|散發者的 FTP 服務通訊埠編號。|  
|**ftp_subdirectory**|**nvarchar(255)**|合併代理程式能夠從中收取快照集檔案的子目錄。|  
|**ftp_login**|**sysname**|用來連接到 FTP 服務的使用者名稱。|  
|**ftp_password**|**Nvarchar (524)**|用來連接到 FTP 服務的使用者密碼。|  
|**conflict_retention**|**int**|指定衝突的保留期限 (以天為單位)。 在這段時間之後，會從衝突資料表中清除衝突資料列。|  
|**keep_before_values**|**int**|指定這個發行集是否進行最佳化的同步處理：<br /><br /> **0** = 同步處理未優化，而且當資料分割中的資料變更時，會驗證傳送給所有訂閱者的資料分割。<br /><br /> **1** = 同步處理已優化，而且只有在已變更之資料分割中具有資料列的訂閱者會受到影響。|  
|**allow_subscription_copy**|**bit**|指定是否已啟用複製訂閱資料庫的能力。 **0** 表示不允許複製。|  
|**allow_synctoalternate**|**bit**|指定是否允許替代的同步處理夥伴與這個發行者同步。 **0** 表示不允許同步處理夥伴。|  
|**validate_subscriber_info**|**Nvarchar (500)**|列出用於擷取訂閱者資訊以及驗證訂閱者參數化資料列篩選器準則的函數。|  
|**ad_guidname**|**sysname**|指定發行集是否在 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Active Directory 中發行。 有效的 GUID 會指定發行集發行于 Active Directory 中，而 GUID 是對應的 Active Directory 發行集物件 **objectGUID**。 如果是 NULL，發行集就不會發行在 Active Directory 中。|  
|**backward_comp_level**|**int**|資料庫相容性層級。 可以是下列值之一：<br /><br /> **90**  =  [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 。<br /><br /> **100**  =  [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 。|  
|**max_concurrent_merge**|**int**|允許使用的最大並行合併處理序數目。 這個屬性的值為 **0** ，表示在任何給定時間執行的並行合併進程數目沒有任何限制。 這個屬性會設定能夠針對合併式發行集來同時執行的並行合併處理序的數目限制。 如果排程同時執行的快照集處理序數目超出允許執行的值，超出的作業便會放在佇列中，等到目前在執行中的合併處理序完成為止。|  
|**max_concurrent_dynamic_snapshots**|**int**|允許針對合併式發行集來執行的最大並行已篩選資料快照集工作階段數目。 如果是 **0**，則在任何指定時間內，可同時針對發行集執行的並行已篩選資料快照集會話數目上限沒有限制。 這個屬性會設定能夠針對合併式發行集來同時執行的並行快照集處理序的數目限制。 如果排程同時執行的快照集處理序數目超出允許執行的值，超出的作業便會放在佇列中，等到目前在執行中的合併處理序完成為止。|  
|**use_partition_groups**|**smallint**|指定發行集是否使用預先計算的資料分割。|  
|**dynamic_filters_function_list**|**Nvarchar (500)**|發行集的參數化資料列篩選器所用的函數清單 (用分號分隔)。|  
|**partition_id_eval_proc**|**sysname**|指定訂閱者的合併代理程式為了判斷指派的資料分割識別碼而執行的程序名稱。|  
|**publication_number**|**smallint**|指定將2位元組對應提供給 **pubid** 的識別欄位。 **pubid** 是發行集的全域唯一識別碼，而發行集編號只在指定資料庫中是唯一的。|  
|**replicate_ddl**|**int**|指出是否支援發行集的結構描述複寫。<br /><br /> **0** = 不復寫 DDL 語句。<br /><br /> **1** = 複寫在發行者端執行的 DDL 語句。<br /><br /> 如需詳細資訊，請參閱[對發行集資料庫進行結構描述變更](../../relational-databases/replication/publish/make-schema-changes-on-publication-databases.md)。|  
|**allow_subscriber_initiated_snapshot**|**bit**|指出訂閱者能夠起始利用參數化篩選來產生發行集快照集的處理序。 **1** 表示訂閱者可以起始快照集進程。|  
|**dynamic_snapshot_queue_timeout**|**int**|指定在使用參數化篩選時，訂閱者必須在佇列中等待快照集產生程序開始的分鐘數。|  
|**dynamic_snapshot_ready_timeout**|**int**|指定在使用參數化篩選時，訂閱者等待快照集產生程序完成的分鐘數。|  
|**轉銷商**|**sysname**|發行集散發者的名稱。|  
|**snapshot_jobid**|**binary(16)**|識別在訂閱者能夠起始快照集產生程序時，產生快照集的代理程式作業。|  
|**allow_web_synchronization**|**bit**|指定是否啟用發行集的 Web 同步處理，其中 **1** 表示已啟用發行集的 Web 同步處理。|  
|**web_synchronization_url**|**Nvarchar (500)**|指定 Web 同步處理所用的網際網路 URL 預設值。|  
|**allow_partition_realignment**|**bit**|指出當修改發行者的資料列造成資料分割的變更時，是否要將刪除動作傳給訂閱者。<br /><br /> **0** = 舊分割區中的資料將保留在訂閱者上，在發行者上對此資料所做的變更將不會複寫到這個訂閱者，但在訂閱者端所做的變更會複寫到發行者。<br /><br /> **1** = 藉由移除不在訂閱者資料分割中的資料，刪除訂閱者以反映資料分割變更的結果。<br /><br /> 如需詳細資訊，請參閱 [sp_addmergepublication &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addmergepublication-transact-sql.md)。<br /><br /> 注意：當此值為 **0** 時，保留在訂閱者端的資料應視為唯讀狀態;不過，複寫系統並不會嚴格強制執行這項功能。|  
|**retention_period_unit**|**tinyint**|定義定義 *保留* 時所使用的單位，它可以是下列其中一個值：<br /><br /> **0** = 日。<br /><br /> **1** = 周。<br /><br /> **2** = 月。<br /><br /> **3** = 年。|  
|**decentralized_conflicts**|**int**|指出是否將衝突記錄儲存在造成衝突的訂閱者端：<br /><br /> **0** = 衝突記錄不會儲存在訂閱者端。<br /><br /> **1** = 將衝突記錄儲存在訂閱者端。|  
|**generation_leveling_threshold**|**int**|指定某個層代中包含的變更數目。 層代是指傳遞給發行者或訂閱者的變更集合。|  
|**automatic_reinitialization_policy**|**bit**|指出在自動重新初始化之前，是否從訂閱者上傳變更。<br /><br /> **1** = 在自動重新初始化之前，會從訂閱者上傳變更。<br /><br /> **0** = 在自動重新初始化之前，不會上傳變更。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [&#40;Transact-sql&#41;的複寫視圖 ](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [sp_addmergepublication &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addmergepublication-transact-sql.md)   
 [sp_changemergepublication &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-changemergepublication-transact-sql.md)   
 [sp_helpmergepublication &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helpmergepublication-transact-sql.md)  
  
  
