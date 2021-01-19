---
description: RESTORE 陳述式 - 引數 (Transact-SQL)
title: RESTORE 引數 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/08/2018
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- RESTORE statement, arguments
- RESTORE statement
ms.assetid: 4bfe5734-3003-4165-afd4-b1131ea26e2b
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 35ed1cf50f1d39af97ac636c8aba3ca36f90e7d7
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171670"
---
# <a name="restore-statements---arguments-transact-sql"></a>RESTORE 陳述式 - 引數 (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

本主題說明 RESTORE {DATABASE|LOG} 陳述式和下列輔助陳述式關聯集之「語法」一節中所述的引數：RESTORE FILELISTONLY、RESTORE HEADERONLY、RESTORE LABELONLY、RESTORE REWINDONLY 和 RESTORE VERIFYONLY。 大部份引數都只得到這六個引數其中一部份的支援。 在每個引數的描述中，都會指出引數所得到的支援。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
 如需語法的詳細資訊，請參閱下列主題：  
  
-   [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)  
  
-   [RESTORE FILELISTONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)  
  
-   [RESTORE HEADERONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)  
  
-   [RESTORE LABELONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-labelonly-transact-sql.md)  
  
-   [RESTORE REWINDONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-rewindonly-transact-sql.md)  
  
-   [RESTORE VERIFYONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 DATABASE  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 指定目標資料庫。 如果指定了檔案和檔案群組清單，就只會還原這些檔案和檔案群組。  
  
 如果是使用完整或大量記錄復原模式的資料庫，在大部分情況下，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 都會要求您先備份記錄結尾，再還原資料庫。 除非 RESTORE DATABASE 陳述式包含 WITH REPLACE 或 WITH STOPAT 子句 (必須指定在資料備份結束之後發生的時間或交易)，否則如果沒有先備份記錄結尾便還原資料庫，就會產生錯誤。 如需結尾記錄備份的詳細資訊，請參閱[結尾記錄備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/tail-log-backups-sql-server.md)。  
  
 記錄  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 指定要將交易記錄備份套用在這個資料庫上。 您必須依照順序來套用交易記錄。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會檢查備份的交易記錄，以確保交易是依照正確順序載入正確的資料庫。 若要套用多個交易記錄，請在所有還原作業上使用 NORECOVERY 選項，但最後一項還原作業除外。  
  
> [!NOTE]  
>  最後還原的記錄通常是結尾記錄備份。 「結尾記錄備份」是在剛要還原資料庫之前 (通常是在資料庫作業失敗之後) 所建立的記錄備份。 從可能已損毀的資料庫中取得結尾記錄備份，可以擷取尚未備份的記錄 (記錄結尾) 來防止遺失工作。 如需詳細資訊，請參閱[結尾記錄備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/tail-log-backups-sql-server.md)。  
  
 如需詳細資訊，請參閱[套用交易記錄備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/apply-transaction-log-backups-sql-server.md)。  
  
 { _database\_name_ |  **@** _database\_name\_var_}  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 這是記錄或完整資料庫要還原到其中的資料庫。 如果這個名稱是以變數 ( **@** _database\_name\_var_) 的形式提供，您還可以將這個名稱指定為字串常數 ( **@** _database\_name\_var_ = *database*\_*name*)，或指定為字元字串資料類型的變數，但 **ntext** 或 **text** 資料類型除外。  
  
 \<file_or_filegroup_or_page> [ **,** ...*n* ]  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 指定要包括在 RESTORE DATABASE 或 RESTORE LOG 陳述式中的邏輯檔案或檔案群組或頁面的名稱。 您可以指定一份檔案或檔案群組的清單。  
  
 針對使用簡單復原模式的資料庫，只有當目標檔案或檔案群組是唯讀，或者這是 PARTIAL 還原 (會導致[無用的檔案群組](../../relational-databases/backup-restore/remove-defunct-filegroups-sql-server.md)) 時，才能指定 FILE 和 FILEGROUP 選項。  
  
 如果是使用完整或大量記錄復原模式的資料庫，在利用 RESTORE DATABASE 來還原一或多個檔案、檔案群組及 (或) 頁面之後，您必須將交易記錄套用在還原的資料所在的檔案上；套用記錄會使這些檔案與資料庫的其餘部分一致。 例外狀況如下：  
  
-   如果要還原的檔案在上次備份之前是唯讀的，便不需要套用交易記錄，RESTORE 陳述式會通知您這個情況。  
  
-   備份包含主要檔案群組，且正在執行部分還原。 在這個情況下，不需要還原記錄，因為此時會從備份組中自動還原記錄。  
  
FILE **=** { *logical_file_name_in_backup*|  **@** _logical\_file\_name\_in\_backup\_var_}  
 命名要包括在資料庫還原中的檔案。  
  
FILEGROUP **=** { *logical_filegroup_name* |  **@** _logical\_filegroup\_name\_var_ }  
 命名要包括在資料庫還原中的檔案群組。  
  
 **注意：** 只有在指定的檔案群組是唯讀的，且這是部分還原 (也就是使用 WITH PARTIAL) 時，才能在簡單復原模式中使用 FILEGROUP。 任何未還原的讀寫檔案群組都會標示為已解除功能，且以後無法還原到結果資料庫中。  
  
READ_WRITE_FILEGROUPS  
 選取所有讀寫檔案群組。 如果您有唯讀檔案群組要在讀寫檔案群組之後，在唯讀檔案群組之前還原，這個選項特別有用。  
  
PAGE = **'** _file_ **:** _page* [ **,** ...*n* ] **'**  
 指定要進行分頁還原 (只有使用完整或大量記錄復原模式的資料庫，才能支援分頁還原) 的一或多個頁面清單。 其值如下：  
  
PAGE  
 指出一或多個檔案和頁面的清單。  
  
 *file*  
 這是包含所要還原特定頁面之檔案的檔案識別碼。  
  
 *page*  
 這是要在檔案中還原之頁面的頁面識別碼。  
  
 *n*  
 這是一個預留位置，表示可以指定多個頁面。  
  
 在還原序列中，能夠還原到任何單一檔案的最大頁面數目是 1000。 不過，如果檔案中有不少損毀的頁面，請考慮還原整個檔案，而不是還原頁面。  
  
> [!NOTE]  
>  分頁還原絕對不會復原。  
  
 如需分頁還原的詳細資訊，請參閱[還原分頁 &#40;SQL Server&#41;](../../relational-databases/backup-restore/restore-pages-sql-server.md)。  
  
 [ **,** ...*n* ]  
 這是一個預留位置，表示可以在逗號分隔清單中指定多個檔案、檔案群組和頁面。 數目沒有限制。  
  
FROM { \<backup_device> [ **,** ...*n* ]| \<database_snapshot> } 通常用來指定備份裝置，將會從中還原備份。 另外，您也可以在 RESTORE DATABASE 陳述式中，利用 FROM 子句來指定資料庫所要還原的資料庫快照集名稱，此時不能使用 WITH 子句。  
  
 如果省略 FROM 子句，就不會還原備份。 相反地，此時會復原資料庫。 這可讓您復原已利用 NORECOVERY 選項來還原的資料庫，或切換到待命伺服器。 如果省略 FROM 子句，就必須在 WITH 子句中指定 NORECOVERY、RECOVERY 或 STANDBY。  
  
 \<backup_device> [ **,** ...*n* ] 指定還原作業要使用的邏輯或實體備份裝置。  
  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)、[RESTORE FILELISTONLY](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)、[RESTORE HEADERONLY](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)、[RESTORE LABELONLY](../../t-sql/statements/restore-statements-labelonly-transact-sql.md)、[RESTORE REWINDONLY](../../t-sql/statements/restore-statements-rewindonly-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)。  
  
 \<backup_device>::= 指定備份作業要使用的邏輯或實體備份裝置，如下列：  
  
 { _logical\_backup\_device\_name_ |  **@** _logical\_backup\_device\_name\_var_ }  
 這是用來還原資料庫 **sp_addumpdevice** 所建立備份裝置的邏輯名稱，它必須遵循識別碼的規則。 如果備份裝置名稱是以變數 ( **@** _logical\_backup\_device\_name\_var_) 的方式來提供，您可以將此名稱指定為字串常數 ( **@** _logical\_backup\_device\_name\_var_ = _logical\_backup\_device\_name_)，或指定為字元字串資料類型的變數，但 **ntext** 或 **text** 資料類型除外。  
  
 {DISK | TAPE } **=** { **'** _physical\_backup\_device\_name_ **'**  |  **@** _physical\_backup\_device\_name\_var_ }  
 可讓您從指定的磁碟或磁帶裝置中還原備份。 您應該用裝置的實際名稱 (例如，完整路徑和檔案名稱) 來指定磁碟和磁帶的裝置類型：`DISK ='Z:\SQLServerBackups\AdventureWorks.bak'` 或 `TAPE ='\\\\.\TAPE0'`。 如果裝置名稱是以變數 ( **@** _physical\_backup\_device\_name\_var_) 的方式來提供，您可以將此裝置名稱指定為字串常數 ( **@** _physical\_backup\_device\_name\_var_ = '*physical_backup_device_name*')，或指定為字元字串資料類型的變數，但 **ntext** 或 **text** 資料類型除外。  
  
 如果所用的網路伺服器是用 UNC 名稱 (必須包含機器名稱)，請指定磁碟裝置類型。 如需如何使用通用命名慣例 (UNC) 名稱的詳細資訊，請參閱[備份裝置 &#40;SQL Server&#41;](../../relational-databases/backup-restore/backup-devices-sql-server.md)。  
  
 您用來執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的帳戶，必須有遠端電腦或網路伺服器的 READ 存取權，才能執行 RESTORE 作業。  
  
 *n*  
 這是一個預留位置，表示可以在逗號分隔清單中指定最多達 64 個備份裝置。  
  
 還原序列所需要的備份裝置數目，是否與建立備份所屬的媒體集時所用的備份裝置數目相同，取決於還原作業是離線或在線上進行，如下所示：  
  
-   如果是離線還原，用來還原備份的裝置可以比建立備份時所用的裝置少。  
  
-   線上還原需要備份的所有備份裝置。 試圖用較少的裝置來還原會失敗。  
  
 例如，設想您已將資料庫備份在連接到伺服器的四部磁帶機中。 線上還原會要求您將四個磁碟機連接到伺服器；離線還原則可讓您在機器只有不到四個磁碟機時還原備份。  
  
> [!NOTE]  
>  當從鏡像媒體集中還原備份時，每個媒體家族只能指定單一鏡像。 不過，如果有其他鏡像，當出現錯誤時，解決部分還原問題的速度會比較快。 您可以利用另一個鏡像的對應磁碟區來替代損毀的媒體磁碟區。 請注意，如果是離線還原，您可以從比媒體家族少的裝置進行還原，但每個家族只會處理一次。  
  
\<database_snapshot>::=  
**支援者：** [RESTORE DATABASE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
DATABASE_SNAPSHOT **=** _database\_snapshot\_name_  
 將資料庫還原到 *database_snapshot_name* 所指定的資料庫快照集。 DATABASE_SNAPSHOT 選項只適用於完整的資料庫還原。 在還原作業中，資料庫快照集會取代完整資料庫備份。  
  
 還原作業需要指定的資料庫快照集是資料庫中的唯一資料庫快照集。 在還原作業期間，資料庫快照集和目的地資料庫都會標示為 `In restore`。 如需詳細資訊，請參閱 [RESTORE DATABASE](../../t-sql/statements/restore-statements-transact-sql.md) 中的＜備註＞一節。  
  
### <a name="with-options"></a>WITH 選項  
 指定還原作業要用的選項。 如需哪些陳述式使用各個選項的摘要，請參閱這個主題稍後的＜WITH 選項的支援摘要＞一節。  
  
> [!NOTE]  
>  這裡組織 WITH 選項的順序，與 [RESTORE {DATABASE|LOG}](../../t-sql/statements/restore-statements-transact-sql.md) 中＜語法＞一節的順序相同。  
  
 PARTIAL  
 **支援者：** [RESTORE DATABASE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 指定部分還原作業，以還原主要檔案群組和任何指定的次要檔案群組。 PARTIAL 選項已隱含地選取主要檔案群組；不需要指定 FILEGROUP = 'PRIMARY'。 若要還原次要檔案群組，您必須使用 FILE 選項或 FILEGROUP 選項明確指定檔案群組。  
  
 在 RESTORE LOG 陳述式中，不得使用 PARTIAL 選項。  
  
 PARTIAL 選項會啟動分次還原的初始階段，可讓您稍後再還原其餘的檔案群組。 如需詳細資訊，請參閱[分次還原 &#40;SQL Server&#41;](../../relational-databases/backup-restore/piecemeal-restores-sql-server.md)。  
  
 [ **RECOVERY** | NORECOVERY | STANDBY ]  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 **RECOVERY**  
 指示還原作業回復任何未認可的交易。 在復原程序之後，資料庫便已備妥，可供使用。 如果 NORECOVERY、RECOVERY 和 STANDBY 三者都沒有指定，預設值就是 RECOVERY。  
  
 如果規劃了後續的 RESTORE 作業 (RESTORE LOG，或差異備份的 RESTORE DATABASE)，就應該指定 NORECOVERY 或 STANDBY。  
  
 當從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 還原備份組時，可能需要升級資料庫。 當指定 WITH RECOVERY 時，會自動執行這項升級。 如需詳細資訊，請參閱[套用交易記錄備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/apply-transaction-log-backups-sql-server.md)。  
  
> [!NOTE]  
>  如果省略 FROM 子句，就必須在 WITH 子句中指定 NORECOVERY、RECOVERY 或 STANDBY。  
  
 NORECOVERY  
 指示還原作業不回復任何未認可的交易。 如果稍後必須套用另一個交易記錄，請指定 NORECOVERY 或 STANDBY 選項。 如果 NORECOVERY、RECOVERY 和 STANDBY 三者都沒有指定，預設值就是 RECOVERY。 在使用 NORECOVERY 選項的離線還原作業期間，無法使用資料庫。  
  
 若要還原資料庫備份和一或多個交易記錄，或每當需要多個 RESTORE 陳述式 (例如，還原完整資料庫備份，後面再接著差異資料庫備份) 時，除了最後一個 RESTORE 陳述式，RESTORE 需要所有 RESTORE 陳述式都使用 WITH NORECOVERY 選項。 最佳作法是在多步驟的還原序列中，在 ALL 陳述式上使用 WITH NORECOVERY，直到抵達所需要的復原點為止，之後，再使用專供復原的個別 RESTORE WITH RECOVERY 陳述式。  
  
 當搭配檔案或檔案群組還原作業來使用時，NORECOVERY 會強制資料庫在還原作業之後，維持還原狀態。 在下列情況下，這非常有用：  
  
-   正在執行還原指令碼，且始終在套用記錄。  
  
-   使用一系列檔案還原，且資料庫的用途並不是要能夠在兩項還原作業之間使用。  
  
 在某些情況下，RESTORE WITH NORECOVERY 會將向前復原集向前捲動到足以與資料庫一致。 在這種情況下，並不會進行回復，資料會依照這個選項的預期，維持離線狀態。 不過，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會發出參考用訊息，說明此時可以利用 RECOVERY 選項來復原向前復原集。  
  
STANDBY **=** _standby\_file\_name_  
 指定可供恢復復原效果的待命資料庫檔案。 離線還原可以使用 STANDBY 選項 (包括部分還原)。 線上還原不允許使用這個選項。 試圖指定線上還原的 STANDBY 選項，會使還原作業失敗。 當資料庫需要升級時，不允許使用 STANDBY。  
  
 待命資料庫檔案是用來保存 RESTORE WITH STANDBY 的恢復階段期間，所修改之分頁的「寫入時複製」前置影像。 待命資料庫檔案可讓您在各次交易記錄還原作業之間，呼叫資料庫來進行唯讀存取，且可以搭配暖待命伺服器狀況或特殊復原狀況來使用，在這個狀況下，在各次記錄還原之間檢查資料庫會很有用。 在 RESTORE WITH STANDBY 作業之後，下一項 RESTORE 作業會自動刪除恢復檔案。 如果在下一項 RESTORE 作業之前，自動刪除這個待命資料庫檔案，就必須重新還原整個資料庫。 當資料庫在 STANDBY 狀態中，您應該如同任何其他資料庫檔案一樣，小心處理這個待命資料庫檔案。 這個檔案不像其他資料庫檔案，只有在使用中的還原作業期間，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 才會將這個檔案保持為開啟狀態。  
  
 *standby_file_name* 會指定一個待命資料庫檔案，這個檔案的位置儲存在資料庫記錄中。 如果現有的檔案使用指定的名稱，就會覆寫檔案；否則，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會建立這個檔案。  
  
 給定待命資料庫檔案的大小需求，會隨著還原作業期間，未認可的交易所產生的恢復動作量而不同。  
  
> [!IMPORTANT]  
>  如果指定的待命資料庫檔案名稱所在的磁碟機之可用磁碟空間已用完，還原作業就會停止。  
  
 如需 RECOVERY 和 NORECOVERY 的比較，請參閱 [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md) 中的＜備註＞一節。  
  
LOADHISTORY  
 **支援者：** [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)  
  
 指定還原作業將資訊載入 **msdb** 記錄資料表中。 LOADHISTORY 選項會針對所驗證的單一備份組，將媒體集所儲存之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 備份的相關資訊載入 **msdb** 資料庫的備份和還原記錄資料表中。 如需記錄資料表的詳細資訊，請參閱[系統資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)。  
  
#### <a name="general_with_options--n-"></a>\<general_WITH_options> [ ,...n ]  
 RESTORE DATABASE 和 RESTORE LOG 陳述式中支援一般的所有 WITH 選項。 其中的某些選項也受到一或多個輔助陳述式所支援，如下所示。  
  
##### <a name="restore-operation-options"></a>還原作業選項  
 這些選項會影響還原作業的行為。  
  
MOVE **'** _logical\_file\_name\_in\_backup_ **'** TO **'** _operating\_system\_file\_name_ **'** [ ...*n* ]  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)  
  
 指定由 *logical_file_name_in_backup* 指定其邏輯名稱的資料或記錄檔，應該透過還原到 *operating_system_file_name* 所指定的位置來移動。 備份組中資料或記錄檔的邏輯檔案名稱，會與當初建立備份組時資料庫中的邏輯名稱相符。  
  
*n* 是預留位置，表示您可以指定其他 MOVE 陳述式。 針對您想要從備份組還原到新位置的每一個邏輯檔案指定 MOVE 陳述式。 根據預設，*logical_file_name_in_backup* 檔案會還原到它的原始位置。  
  
> [!NOTE]  
>  若要取得備份組中的邏輯檔清單，請使用 RESTORE FILELISTONLY。  
  
 如果利用 RESTORE 陳述式，將資料庫重新放置在相同的伺服器中，或將它複製到不同的伺服器中，您可能需要 MOVE 選項，才能重新放置資料庫檔案，以及避免與現有檔案發生衝突。  
  
 當搭配 RESTORE LOG 來使用時，您只能利用 MOVE 選項來重新放置在還原的記錄所涵蓋的間隔期間加入的檔案。 例如，如果記錄備份包含 `file23` 檔的新增檔案作業，您可以在 RESTORE LOG 上，利用 MOVE 選項來重新放置這個檔案。  
  
 和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 快照集備份一起使用時，MOVE 選項只能用來在和原始 Blob 相同的儲存體帳戶內，將檔案重新配置到 Azure Blob。 MOVE 選項無法用來將快照集備份還原至本機檔案或不同的儲存體帳戶。  
  
 如果您計畫將資料庫重新放置在相同的伺服器中，或將它複製到不同的伺服器時，使用 RESTORE VERIFYONLY 陳述式，您可能需要 MOVE 選項，才能確認目標有足夠的空間，以及識別與現有檔案可能發生的衝突。  
  
 如需詳細資訊，請參閱 [使用備份與還原複製資料庫](../../relational-databases/databases/copy-databases-with-backup-and-restore.md)。  
  
CREDENTIAL  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)、[RESTORE FILELISTONLY](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)、[RESTORE HEADERONLY](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)、[RESTORE LABELONLY](../../t-sql/statements/restore-statements-labelonly-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)。  
  
**適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 及更新版本
  
 只有在從 Microsoft Azure Blob 儲存體服務還原備份時使用。  
  
> [!NOTE]  
>  使用 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 直到 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]，從 URL 還原時，都只能從單一裝置還原。 為了在從 URL 還原時能從多部裝置還原，您必須使用 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前的版本](https://go.microsoft.com/fwlink/p/?LinkId=299658)，而且必須使用共用存取簽章 (SAS) 權杖。 如需詳細資訊，請參閱[啟用 SQL Server Managed Backup to Microsoft Azure](../../relational-databases/backup-restore/enable-sql-server-managed-backup-to-microsoft-azure.md) 和[在 Azure 儲存體上使用 Powershell 搭配共用存取簽章 (SAS) 權杖來簡化 SQL 認證的建立](https://docs.microsoft.com/archive/blogs/sqlcat/simplifying-creation-of-sql-credentials-with-shared-access-signature-sas-tokens-on-azure-storage-with-powershell) \(英文\)。  
  
 REPLACE  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 指定即使已有另一個同名資料庫存在，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 仍應該建立指定的資料庫及其相關檔案。 在這種情況下，會刪除現有的資料庫。 如果沒有指定 REPLACE 選項，就會進行安全檢查。 這樣可避免意外覆寫不同的資料庫。 這項安全檢查可確保在同時發生下列兩種狀況時，RESTORE DATABASE 陳述式不會將資料庫還原到目前的伺服器中：  
  
-   RESTORE 陳述式所指定的資料庫已在目前的伺服器中，且  
  
-   資料庫名稱不是備份組所記錄的資料庫名稱。  
  
 另外，REPLACE 也可讓 RESTORE 覆寫無法確認為屬於還原中之資料庫的現有檔案。 RESTORE 通常會拒絕覆寫先前已存在的檔案。 您也可以依照 RESTORE LOG 選項的相同方式來使用 WITH REPLACE。  
  
 REPLACE 也覆寫了在還原資料庫之前，您必須先備份記錄結尾的需求。  
  
 如需使用 REPLACE 選項所造成之影響的詳細資訊，請參閱 [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)。  
  
RESTART  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 應該重新啟動已中斷的還原作業。 RESTART 會在中斷點上，重新啟動還原作業。  
  
RESTRICTED_USER  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)。  
  
 限制 **db_owner**、**dbcreator** 或 **sysadmin** 等角色的成員才能存取新還原的資料庫。  RESTRICTED_USER 會取代 DBO_ONLY 選項。 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 已停止 DBO_ONLY。  
  
 請搭配 RECOVERY 選項來使用這個項目。  
  
##### <a name="backup-set-options"></a>備份組選項  
 這些選項會在包含要還原之備份的備份組上運作。  
  
FILE **=** { *backup_set_file_number* |  **@** _backup\_set\_file\_number_ }  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)、[RESTORE FILELISTONLY](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)、[RESTORE HEADERONLY](../../t-sql/statements/restore-statements-headeronly-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)。  
  
 識別要還原的備份組。 例如， *backup_set_file_number* 為 **1** ，表示備份媒體的第一個備份組； *backup_set_file_number* 為 **2** ，表示第二個備份組。 您可以使用 *RESTORE HEADERONLY* 陳述式來取得備份組的 [backup_set_file_number](../../t-sql/statements/restore-statements-headeronly-transact-sql.md) 。  
  
 當未指定時，預設值是 **1**，但 RESTORE HEADERONLY 除外，在此案例中會處理媒體集中的所有備份組。 如需詳細資訊，請參閱本主題稍後的「指定備份組」。  
  
> [!IMPORTANT]  
>  此 FILE 選項和用來指定資料庫檔案的 FILE 選項無關，FILE **=** { *logical_file_name_in_backup* |  **@** _logical\_file\_name\_in\_backup\_var_ }。  
  
 PASSWORD  **=** { *password* |  **@** _password\_variable_ }  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)、[RESTORE FILELISTONLY](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)、[RESTORE HEADERONLY](../../t-sql/statements/restore-statements-headeronly-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)。  
  
 提供備份組的密碼。 備份組密碼是一個字元字串。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
 如果建立備份組時指定了密碼，您就必須提供密碼，才能從備份組中執行任何還原作業。 指定錯誤密碼，或備份組沒有密碼，卻指定了密碼，都是錯誤。  
  
> [!IMPORTANT]  
>  這個密碼只為媒體集提供弱的保護。 如需詳細資訊，請參閱相關陳述式的＜權限＞一節。  
  
##### <a name="media-set-options"></a>媒體集選項  
 這些選項會處理整個媒體集。  
  
 MEDIANAME **=** { *media_name* |  **@** _media\_name\_variable_}  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)、[RESTORE FILELISTONLY](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)、[RESTORE HEADERONLY](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)、[RESTORE LABELONLY](../../t-sql/statements/restore-statements-labelonly-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)。  
  
 指定媒體的名稱。 如果提供的話，媒體名稱必須符合備份磁碟區中的媒體名稱；否則，還原作業會終止。 如果 RESTORE 陳述式中沒有媒體名稱，就不會檢查備份磁碟區中的相符媒體名稱。  
  
> [!IMPORTANT]  
>  在備份和還原作業中使用一致的媒體名稱，可以為還原作業所選的媒體提供額外的安全檢查。  
  
 MEDIAPASSWORD **=** { *mediapassword* |  **@** _mediapassword\_variable_ }  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)、[RESTORE FILELISTONLY](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)、[RESTORE HEADERONLY](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)、[RESTORE LABELONLY](../../t-sql/statements/restore-statements-labelonly-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)。  
  
 提供媒體集的密碼。 媒體集密碼是一個字元字串。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
 如果在格式化媒體集時提供了密碼，就必須使用這個密碼，才能存取媒體集中的任何備份組。 指定錯誤密碼，或媒體集沒有密碼，卻指定了密碼，都是錯誤。  
  
> [!IMPORTANT]  
>  這個密碼只為媒體集提供弱的保護。 如需詳細資訊，請參閱相關陳述式的＜權限＞一節。  
  
 BLOCKSIZE **=** { *blocksize* |  **@** _blocksize\_variable_ }  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 指定實體區塊大小 (以位元組為單位)。 支援的大小為 512、1024、2048、4096、8192、16384、32768 和 65536 (64 KB) 位元組。 磁帶裝置的預設值為 65536，其他裝置則為 512。 一般而言這個選項是不必要的，因為 RESTORE 會自動選取裝置適用的區塊大小。 明確指出區塊大小會覆寫自動選取的區塊大小。  
  
 如果您要從 CD-ROM 還原備份，請指定 BLOCKSIZE=2048。  
  
> [!NOTE]  
>  一般而言，只有從磁帶裝置讀取時，這個選項才會對效能造成影響。  
  
##### <a name="data-transfer-options"></a>資料傳送選項  
 這些選項可讓您從備份裝置最佳化資料傳送。  
  
 BUFFERCOUNT **=** { *buffercount* |  **@** _buffercount\_variable_ }  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 指定要用於還原作業的 I/O 緩衝區總數。 您可以指定任何正整數，不過，緩衝區的數目很大時，可能會因為 Sqlservr.exe 處理序中的虛擬位址空間不足而造成「記憶體不足」錯誤。  
  
 緩衝區使用的總空間可由下列公式判斷：_buffercount_* *\** _maxtransfersize_。  
  
 MAXTRANSFERSIZE **=** { _maxtransfersize_ |  **@** _maxtransfersize\_variable_ }  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 以位元組為單位，指定要用於備份媒體與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之間的最大傳送單位。 可能的值是 65536 位元組 (64 KB) 的倍數，最大可達 4194304 位元組 (4 MB)。  
> [!NOTE]  
>  當資料庫已設定 FILESTREAM，或包括記憶體內 OLTP 檔案群組時，`MAXTRANSFERSIZE` 在還原時應該大於或等於備份建立時使用的大小。  
  
##### <a name="error-management-options"></a>錯誤管理選項  
 這些選項可讓您決定還原作業是否要啟用備份總和檢查碼，以及作業在發生錯誤時是否要停止。    
  
 { CHECKSUM | NO_CHECKSUM }  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)、[RESTORE FILELISTONLY](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)、[RESTORE HEADERONLY](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)、[RESTORE LABELONLY](../../t-sql/statements/restore-statements-labelonly-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)。  
  
 預設行為是，如果有總和檢查碼，便驗證總和檢查碼，如果沒有，就不檢查，繼續作業。  
  
 CHECKSUM  
 指定必須驗證備份總和檢查碼，如果備份沒有備份總和檢查碼，還原作業便會失敗，且會出現一則訊息，指出總和檢查碼不存在。  
  
> [!NOTE]  
>  只有在使用備份總和檢查碼時，頁面總和檢查碼才會與備份作業相關。  
  
 依預設，在發現無效的總和檢查碼時，RESTORE 會報告一則總和檢查碼錯誤，且會停止作業。 不過，如果您指定 CONTINUE_AFTER_ERROR，在損毀狀況可接受的情況下，RESTORE 會在傳回總和檢查碼錯誤和無效總和檢查碼所在頁碼之後，繼續作業。  
  
 如需使用備份總和檢查碼的詳細資訊，請參閱[在備份和還原期間的可能媒體錯誤 &#40;SQL Server&#41;](../../relational-databases/backup-restore/possible-media-errors-during-backup-and-restore-sql-server.md)。  
  
 NO_CHECKSUM  
 明確停用還原作業的總和檢查碼驗證。  
  
 { **STOP_ON_ERROR** | CONTINUE_AFTER_ERROR }  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)、[RESTORE FILELISTONLY](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)、[RESTORE HEADERONLY](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)、[RESTORE LABELONLY](../../t-sql/statements/restore-statements-labelonly-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)。  
  
 STOP_ON_ERROR  
 指定在發生第一個錯誤之後，便停止還原作業。 這是 RESTORE 的預設行為，但預設值是 CONTINUE_AFTER_ERROR 的 VERIFYONLY 除外。  
  
 CONTINUE_AFTER_ERROR  
 指定在發生錯誤之後，還原作業繼續運作。  
  
 如果備份包含損毀的分頁，您最好利用不含錯誤的替代備份 (像是在頁面損毀之前所取得的備份) 來重複還原作業。 不過，您也可以利用還原陳述式的 CONTINUE_AFTER_ERROR 選項來還原損毀的備份，嘗試搶救這些資料，做為最後的手段。  
  
##### <a name="filestream-options"></a>FILESTREAM 選項  
 FILESTREAM ( DIRECTORY_NAME =*directory_name* )  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)  
  
**適用於**：[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本
  
 Windows 相容的目錄名稱。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的所有資料庫層級 FILESTREAM 目錄名稱之間，此名稱必須是唯一的。 不論 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 定序設定為何，唯一性比較不區分大小寫。  
  
##### <a name="monitoring-options"></a>監視選項  
 這些選項可讓您從備份裝置監視資料傳送。  
  
 STATS [ **=** _percentage_ ]  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)  
  
 每次另一個百分比完成時，便顯示一則訊息，用來量測進度。 如果省略 *percentage*，每完成約 10%，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 都會顯示一則訊息。  
  
 STATS 選項報告到達下一個間隔之報告臨界值的完成百分比。 這項作業大略是以指定的百分比來進行；例如，當 STATS=10 時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會大約依照這個間隔來進行報告；例如，當實際的精確百分比為 40% 時，這個選項可能會顯示 43%。 對大型備份組而言，這不成問題，因為在已完成的 I/O 呼叫之間，百分比完成的移動非常緩慢。  
  
##### <a name="tape-options"></a>磁帶選項  
 這些選項僅適用於「磁帶」裝置。 如果所使用的不是磁帶裝置，將忽略這些選項。  
  
 { **REWIND** | NOREWIND }  
 這些選項僅適用於「磁帶」裝置。 如果所使用的不是磁帶裝置，將忽略這些選項。  
  
 REWIND  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)、[RESTORE FILELISTONLY](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)、[RESTORE HEADERONLY](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)、[RESTORE LABELONLY](../../t-sql/statements/restore-statements-labelonly-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)。  
  
 指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將釋放和倒轉磁帶。 REWIND 是預設值。  
  
 NOREWIND  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)  
  
 在任何其他 RESTORE 陳述式中指定 NOREWIND，都會產生錯誤。  
  
 指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將會在備份作業之後，讓磁帶維持在開啟狀態。 對磁帶執行多次備份作業時，可以使用這個選項來改進效能。  
  
 NOREWIND 隱含 NOUNLOAD，而這些選項無法相容於單一的 RESTORE 陳述式。  
  
> [!NOTE]  
>  如果您使用 NOREWIND，則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體會保有磁帶機的擁有權，直到在相同處理序中執行的 BACKUP 或 RESTORE 陳述式使用 REWIND 或 UNLOAD 選項，或是伺服器執行個體關閉為止。 保留磁帶的開啟狀態可以防止其他處理序存取這個磁帶。 如需如何顯示開啟的磁帶清單及關閉開啟的磁帶之詳細資訊，請參閱[備份裝置 &#40;SQL Server&#41;](../../relational-databases/backup-restore/backup-devices-sql-server.md)。  
  
 { **UNLOAD** | NOUNLOAD }  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)、[RESTORE FILELISTONLY](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)、[RESTORE HEADERONLY](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)、[RESTORE LABELONLY](../../t-sql/statements/restore-statements-labelonly-transact-sql.md)、[RESTORE REWINDONLY](../../t-sql/statements/restore-statements-rewindonly-transact-sql.md) 和 [RESTORE VERIFYONLY](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)。  
  
 這些選項僅適用於「磁帶」裝置。 如果所使用的不是磁帶裝置，將忽略這些選項。  
  
> [!NOTE]  
>  UNLOAD/NOUNLOAD 是工作階段設定，在工作階段的存留期間會一直保持不變，直到指定其他設定來進行重設為止。  
  
 UNLOAD  
 指定在備份完成之後，便自動倒轉和卸載磁帶。 UNLOAD 是在工作階段開始時的預設值。  
  
 NOUNLOAD  
 指定在 RESTORE 作業之後，磁帶仍會在磁帶機上保持載入。  
  
#### <a name="replication_with_option"></a><replication_WITH_option>  
 只有在建立備份時複寫了資料庫，這個選項才會相關。  
  
 KEEP_REPLICATION  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
當設定複寫來處理記錄傳送時，請使用 KEEP_REPLICATION。 它可防止在暖待命伺服器中還原資料庫備份或記錄備份時，或在復原資料庫時，移除複寫設定。 不允許使用 NORECOVERY 選項還原備份時，請指定這個選項。 若要確定在還原之後，複寫能夠正常運作，請執行下列動作：  
  
-   暖待命伺服器的 **msdb** 和 **master** 資料庫必須與主要伺服器的 **msdb** 和 **master** 資料庫同步。  
  
-   必須重新命名暖待命伺服器，使用與主要伺服器相同的名稱。  
  
#### <a name="change_data_capture_with_option"></a><change_data_capture_WITH_option>  
 只有在建立備份時，資料庫啟用了異動資料擷取，這個選項才會相關。  
  
 KEEP_CDC  
 **支援者：** [RESTORE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 KEEP_CDC 應該用來防止在另一部伺服器中還原資料庫備份或記錄備份時，或在復原資料庫時，移除異動資料擷取設定。 不允許使用 NORECOVERY 選項還原備份時，請指定這個選項。  
  
 使用 KEEP_CDC 來還原資料庫不會建立異動資料擷取作業。 若要在還原資料庫之後擷取記錄檔中的變更，請針對已還原的資料庫重新建立擷取程序作業和清除作業。 如需詳細資訊，請參閱 [sys.sp_cdc_add_job &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sys-sp-cdc-add-job-transact-sql.md)。  
  
 如需如何搭配資料庫鏡像使用異動資料擷取的詳細資訊，請參閱[異動資料擷取和其他 SQL Server 功能](../../relational-databases/track-changes/change-data-capture-and-other-sql-server-features.md)。  
  
#### \<service_broker_WITH_options>  
 開啟或關閉 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 訊息傳遞，或設定新的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼。 只有在建立備份時，資料庫啟用 (啟動) 了 [!INCLUDE[ssSB](../../includes/sssb-md.md)]，這個選項才會相關。  
  
 { ENABLE_BROKER  | ERROR_BROKER_CONVERSATIONS  | NEW_BROKER }  
 **支援者：** [RESTORE DATABASE](../../t-sql/statements/restore-statements-transact-sql.md)  
  
 ENABLE_BROKER  
 指定在還原結束時啟用 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 訊息傳遞，以便立即傳送訊息。 根據預設，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 訊息傳遞會在還原期間停用。 資料庫會保留現有的 Service Broker 識別碼。  
  
 ERROR_BROKER_CONVERSATIONS  
 結束所有交談，並顯示一則指出已附加或還原資料庫的錯誤。 這可讓您的應用程式執行現有交談作業的正規清除工作。 Service Broker 訊息傳遞將保持停用，直到這項作業完成之後才會啟用。 資料庫會保留現有的 Service Broker 識別碼。  
  
 NEW_BROKER  
 指定資料庫應該被指派新的 Service Broker 識別碼。 由於資料庫會被視為新的 Service Broker，因此系統會立即移除資料庫中所有現有的交談，而不會產生結束對話訊息。 您必須使用新的識別碼來重新建立參考舊 Service Broker 識別碼的任何路由。  
  
#### \<point_in_time_WITH_options>  
 **支援者：** [RESTORE {DATABASE|LOG}](../../t-sql/statements/restore-statements-transact-sql.md)，且只適用於完整或大量記錄復原模式。  
  
 您可以在 STOPAT、STOPATMARK 或 STOPBEFOREMARK 子句中指定目標復原點，藉以將資料庫還原至特定時間點或交易。 指定的時間或交易一律是從記錄備份中還原。 在還原順序的每個 RESTORE LOG 陳述式中，您必須在相同的 STOPAT、STOPATMARK 或 STOPBEFOREMARK 子句中指定目標時間或交易。  
  
 您必須先還原其端點早於目標復原點的完整資料庫備份，當做時間點還原的必要條件。 若要協助您識別要還原哪個資料庫備份，可以選擇性地在 RESTORE DATABASE 陳述式中指定 WITH STOPAT、STOPATMARK 或 STOPBEFOREMARK 子句，以便在資料庫備份太接近指定的目標時間時引發錯誤。 不過，系統一定會還原完整的資料備份，即使它包含目標時間也一樣。  
  
> [!NOTE]  
>  RESTORE_DATABASE 與 RESTORE_LOG 時間點 WITH 選項類似，但是只有 RESTORE LOG 可支援 *mark_name* 引數。  
  
 { STOPAT | STOPATMARK | STOPBEFOREMARK }   
 
 STOPAT **=** { **'** _datetime_ **'**  |  **@** _datetime\_var* }  
 指定要將資料庫還原到 *datetime* 或 **@** _datetime\_var_ 參數指定的日期和時間當時所處狀態。 如需指定日期和時間的詳細資訊，請參閱[日期和時間資料類型與函數 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)。  
  
 如果變數用於 STOPAT，這個變數必須是 **varchar**、**char**、**smalldatetime** 或 **datetime** 資料類型。 只有在指定日期和時間之前寫入的交易記錄會套用至資料庫上。  
  
> [!NOTE]  
>  如果指定的 STOPAT 時間是在上一次 LOG 備份之後，資料庫便會停留在未復原的狀態，如同使用 NORECOVERY 執行了 RESTORE LOG 一樣。  
  
 如需詳細資訊，請參閱[將 SQL Server 資料庫還原至某個時間點 &#40;完整復原模式&#41;](../../relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model.md)。  
  
 STOPATMARK **=** { **'** _mark\_name_ **'**  |  **'** lsn:_lsn\_number_ **'** } [ AFTER **'** _datetime_ **'** ]  
 指定復原至指定的復原點。 雖然指定的交易包含在復原中，但除非最初實際產生這項交易時已認可這項交易，否則便不會認可它。  
  
 RESTORE DATABASE 和 RESTORE LOG 都支援 *lsn_number* 參數。 這個參數會指定記錄序號。  
  
 只有 RESTORE LOG 陳述式才支援 *mark_name* 參數。 這個參數會識別記錄備份中的交易標示。  
  
 在 RESTORE LOG 陳述式中，如果省略 AFTER *datetime*，復原會停在具有指定名稱的第一個標記。 如果指定 AFTER *datetime*，復原會剛好在 *datetime* 或之後停在具有指定名稱的第一個標記。  
  
> [!NOTE]  
>  如果指定的標記 LSN 或時間是在上一次 LOG 備份之後，資料庫便會停留在未復原的狀態，如同使用 NORECOVERY 執行了 RESTORE LOG 一樣。  
  
 如需詳細資訊，請參閱[使用標示的異動以一致的方式復原相關資料庫 &#40;完整復原模式&#41;](../../relational-databases/backup-restore/use-marked-transactions-to-recover-related-databases-consistently.md) 和[復原到記錄序號 &#40;SQL Server&#41;](../../relational-databases/backup-restore/recover-to-a-log-sequence-number-sql-server.md)。  
  
 STOPBEFOREMARK **=** { **'** _mark\_name_ **'**  |  **'** lsn:_lsn\_number_ **'** } [ AFTER **'** _datetime_ **'** ]  
 指定復原至指定的復原點。 指定的交易不會包含在復原中，而且會在使用 WITH RECOVERY 時回復。  
  
 RESTORE DATABASE 和 RESTORE LOG 都支援 *lsn_number* 參數。 這個參數會指定記錄序號。  
  
 只有 RESTORE LOG 陳述式才支援 *mark_name* 參數。 這個參數會識別記錄備份中的交易標示。  
  
 在 RESTORE LOG 陳述式中，如果省略 AFTER *datetime*，復原會正好停在具有指定名稱的第一個標記之前。 如果指定 AFTER *datetime*，復原會正好在 *datetime* 之前或之後停在具有指定名稱的第一個標記。  
  
> [!IMPORTANT]  
>  如果部分還原順序排除任何 FILESTREAM 檔案群組，則不支援時間點還原。 您可以強制還原順序，以繼續進行。 但是，絕對無法還原 RESTORE 陳述式中省略的 FILESTREAM 檔案群組。 若要強制時間點還原，請指定 CONTINUE_AFTER_ERROR 選項，連同 STOPAT、STOPATMARK 或 STOPBEFOREMARK 選項。 如果您指定 CONTINUE_AFTER_ERROR，則部分還原順序會成功，而 FILESTREAM 檔案群組則會變成無法復原。  
  
## <a name="result-sets"></a>結果集  
 如需結果集的詳細資訊，請參閱下列主題：  
  
-   [RESTORE FILELISTONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)  
  
-   [RESTORE HEADERONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)  
  
-   [RESTORE LABELONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-labelonly-transact-sql.md)  
  
## <a name="remarks"></a>備註  
 如需其他備註的詳細資訊，請參閱下列主題：  
  
-   [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)  
  
-   [RESTORE HEADERONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)  
  
-   [RESTORE LABELONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-labelonly-transact-sql.md)  
  
-   [RESTORE REWINDONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-rewindonly-transact-sql.md)  
  
-   [RESTORE VERIFYONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)  
  
## <a name="specifying-a-backup-set"></a>指定備份組  
 「備份組」包含單次成功備份作業的備份。 RESTORE、RESTORE FILELISTONLY、RESTORE HEADERONLY 和 RESTORE VERIFYONLY 陳述式會在位於指定之單一或多重備份裝置上媒體集內單一備份組上操作。 您應該在媒體集內指定所需的備份。 您可以使用 *RESTORE HEADERONLY* 陳述式來取得備份組的 [backup_set_file_number](../../t-sql/statements/restore-statements-headeronly-transact-sql.md) 。  
  
 用於指定要還原之備份組的選項為：  
  
 FILE **=** { *backup_set_file_number* |  **@** _backup\_set\_file\_number_ }  
  
 其中，*backup_set_file_number* 表示媒體集中的備份位置。 *backup_set_file_number* 為 1 (FILE = 1)，表示備份媒體的第一個備份組；*backup_set_file_number* 為 2 (FILE = 2)，表示第二個備份組，依此類推。  
  
 這個選項的行為會隨陳述式而不同，如下表中所述：  
  
|引數|備份組 FILE 選項的行為|  
|---------------|-----------------------------------------|  
|RESTORE|預設的備份組檔案編號為 1。 RESTORE 陳述式中只允許使用一個備份組 FILE 選項。 依序指定備份組很重要。|  
|RESTORE FILELISTONLY|預設的備份組檔案編號為 1。|  
|RESTORE HEADERONLY|依預設會處理媒體集中的所有備份組。 RESTORE HEADERONLY 結果集會傳回每個備份組的相關資訊，包括它在媒體集中的 [位置]。 若要傳回指定備份組的相關資訊，請使用它的位置編號作為 FILE 選項中的 *backup_set_file_number* 值。<br /><br /> 注意:針對磁帶媒體，RESTORE HEADER 只會處理已載入磁帶上的備份組。|  
|RESTORE VERIFYONLY|*backup_set_file_number* 預設值為 1。|  
  
> [!NOTE]  
>  用來指定備份組的這個 FILE 選項和用來指定資料庫檔案的 FILE 選項無關，FILE **=** { *logical_file_name_in_backup* |  **@** _logical\_file\_name\_in\_backup\_var_ }。  
  
## <a name="summary-of-support-for-with-options"></a>WITH 選項的支援摘要  
 只有 RESTORE 陳述式支援下列 WITH 選項：BLOCKSIZE、BUFFERCOUNT、MAXTRANSFERSIZE、PARTIAL、KEEP_REPLICATION、{ RECOVERY | NORECOVERY | STANDBY }、REPLACE、RESTART、RESTRICTED_USER 和 { STOPAT | STOPATMARK | STOPBEFOREMARK }  
  
> [!NOTE]  
>  只有 RESTORE DATABASE 支援 PARTIAL 選項。  
  
 下表列出一或多個陳述式所用的 WITH 選項，指出哪些陳述式支援各個選項。 核取記號 (√) 表示支援該選項；破折號 (-) 表示不支援該選項。  
  
|WITH 選項|RESTORE|RESTORE FILELISTONLY|RESTORE HEADERONLY|RESTORE LABELONLY|RESTORE REWINDONLY|RESTORE VERIFYONLY|  
|-----------------|-------------|--------------------------|------------------------|-----------------------|------------------------|------------------------|  
|{ CHECKSUM<br /><br /> &#124; NO_CHECKSUM }|√|√|√|√|-|√|  
|{ CONTINUE_AFTER_ERROR<br /><br /> &#124; STOP_ON_ERROR }|√|√|√|√|-|√|  
|FILE<sup>1</sup>|√|√|√|-|-|√|  
|LOADHISTORY|-|-|-|-|-|√|  
|MEDIANAME|√|√|√|√|-|√|  
|MEDIAPASSWORD|√|√|√|√|-|√|  
|MOVE|√|-|-|-|-|√|  
|PASSWORD|√|√|√|-|-|√|  
|{ REWIND &#124; NOREWIND }|√|只有 REWIND|只有 REWIND|只有 REWIND|-|√|  
|STATS|√|-|-|-|-|√|  
|{ UNLOAD &#124; NOUNLOAD }|√|√|√|√|√|√|  
  
 <sup>1</sup> FILE **=** _backup\_set\_file\_number_，這有別於 {FILE | FILEGROUP}。  
  
## <a name="permissions"></a>權限  
 如需權限的詳細資訊，請參閱下列主題：  
  
-   [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)  
  
-   [RESTORE FILELISTONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)  
  
-   [RESTORE HEADERONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)  
  
-   [RESTORE LABELONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-labelonly-transact-sql.md)  
  
-   [RESTORE REWINDONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-rewindonly-transact-sql.md)  
  
-   [RESTORE VERIFYONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)  
  
## <a name="examples"></a>範例  
 如需範例，請參閱下列主題：  
  
-   [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)  
  
-   [RESTORE FILELISTONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)  
  
-   [RESTORE HEADERONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)  
  
## <a name="see-also"></a>另請參閱  
 [BACKUP &#40;Transact-SQL&#41;](../../t-sql/statements/backup-transact-sql.md)   
 [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)   
 [RESTORE FILELISTONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-filelistonly-transact-sql.md)   
 [RESTORE HEADERONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-headeronly-transact-sql.md)   
 [RESTORE LABELONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-labelonly-transact-sql.md)   
 [RESTORE REWINDONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-rewindonly-transact-sql.md)   
 [RESTORE VERIFYONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-verifyonly-transact-sql.md)   
 [SQL Server 資料庫的備份與還原](../../relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases.md)   
 [FILESTREAM &#40;SQL Server&#41;](../../relational-databases/blob/filestream-sql-server.md)  
  
  

