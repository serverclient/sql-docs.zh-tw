---
title: 管理交易記錄檔的大小 | Microsoft Docs
description: 了解如何監視 SQL Server 交易記錄大小、壓縮記錄、加大記錄、最佳化 tempdb 記錄成長率，以及控制交易記錄的成長。
ms.custom: ''
ms.date: 01/05/2018
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
helpviewer_keywords:
- transaction logs [SQL Server], size management
- manage log size
- log size, manage
ms.assetid: 3a70e606-303f-47a8-96d4-2456a18d4297
author: MashaMSFT
ms.author: mathoma
ms.openlocfilehash: e0d5c070d961e71711189b9b32ad12508812b277
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171540"
---
# <a name="manage-the-size-of-the-transaction-log-file"></a>管理交易記錄檔的大小
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
本主題涵蓋如何監視 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 交易記錄大小、壓縮交易記錄、加入或加大交易記錄檔、最佳化 **tempdb** 交易記錄成長率，以及控制交易記錄檔的成長。  

##  <a name="monitor-log-space-use"></a><a name="MonitorSpaceUse"></a>監視記錄空間的使用  
使用 [sys.dm_db_log_space_usage](../../relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql.md) 來監視記錄空間的使用。 這個 DMV 會傳回目前使用之記錄空間量的相關資訊，並指出交易記錄需要截斷的時機。 

如需目前的記錄檔大小、大小上限及檔案的自動成長選項等詳細資訊，您也可以在 [sys.database_files](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md) 中使用該記錄檔的 **size**、**max_size** 和 **growth** 資料行。  
  
> [!IMPORTANT]
> 請避免讓記錄磁碟多載。 請確定記錄檔儲存體可以承受交易式負載的 [IOPS](https://wikipedia.org/wiki/IOPS) 和低延遲需求。 
  
##  <a name="shrink-log-file-size"></a><a name="ShrinkSize"></a> 壓縮記錄檔大小  
 若要減少實體記錄檔的實體大小，則必須壓縮記錄檔。 如果您知道交易記錄檔包含未使用的空間，則這十分有用。 只有當資料庫已上線，而且至少有一個[虛擬記錄檔 (VLF)](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#physical_arch) 可用時，您才能壓縮記錄檔。 在某些情況下，壓縮記錄可能要等到下一個記錄截斷之後才能進行。  
  
> [!NOTE]
> 如長時間執行的交易之類的因素，使 [VLF](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#physical_arch) 保持作用中一段很長的時間，可能限制記錄檔壓縮，甚至完全阻止記錄檔壓縮。 如需資訊，請參閱[可能會延遲記錄截斷的因素](../../relational-databases/logs/the-transaction-log-sql-server.md#FactorsThatDelayTruncation)。  
  
壓縮記錄檔會移除一或多個不保留任何邏輯記錄的 [VLF](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#physical_arch) (即「非使用中 VLF」)。 當交易記錄檔壓縮之後，就會從記錄檔的結尾移除非使用中的 VLF，將記錄縮減至大約目標大小。 

> [!IMPORTANT]
> 壓縮交易記錄檔之前，請記住[可能會延遲記錄截斷的因素](../../relational-databases/logs/the-transaction-log-sql-server.md#FactorsThatDelayTruncation)。 如果壓縮記錄檔之後再次需要儲存空間，交易記錄檔將再次成長，並且會因此在記錄檔成長作業期間導入效能額外負荷。 如需詳細資訊，請參閱本主題中的[建議](#Recommendations)。
  
 **壓縮記錄檔 (但不壓縮資料庫檔案)**  
  
-   [DBCC SHRINKFILE &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-shrinkfile-transact-sql.md)  
  
-   [壓縮檔案](../../relational-databases/databases/shrink-a-file.md)  
  
 **監視記錄檔壓縮事件**  
  
-   [Log File Auto Shrink Event Class](../../relational-databases/event-classes/log-file-auto-shrink-event-class.md)。  
  
 **監視記錄空間**  
  
-   [sys.dm_db_log_space_usage &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql.md)  
  
-   [sys.database_files &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-files-transact-sql.md) (請參閱一或多個記錄檔的 **size**、**max_size** 和 **growth** 資料行。)  
  
##  <a name="add-or-enlarge-a-log-file"></a><a name="AddOrEnlarge"></a> 加入或加大記錄檔  
您可以加大現有的記錄檔 (如果磁碟空間允許的話)，或是將記錄檔加入資料庫 (通常是在不同的磁碟上)，來取得空間。 除非記錄檔空間不足，且保存記錄檔的磁碟區上磁碟空間也不足，否則一個交易記錄檔便已足夠。   
  
-   若要對資料庫新增一個記錄檔，請使用 `ALTER DATABASE` 陳述式的 `ADD LOG FILE` 子句。 新增記錄檔可讓記錄檔增大。  
-   若要加大記錄檔，請使用 `ALTER DATABASE` 陳述式的 `MODIFY FILE` 子句，並指定 `SIZE` 和 `MAXSIZE` 語法。 如需詳細資訊，請參閱 [ALTER DATABASE &#40;Transact-SQL&#41; 檔案及檔案群組選項](../../t-sql/statements/alter-database-transact-sql-file-and-filegroup-options.md)。  

如需詳細資訊，請參閱本主題中的[建議](#Recommendations)。
    
##  <a name="optimize-tempdb-transaction-log-size"></a><a name="tempdbOptimize"></a> 最佳化 tempdb 交易記錄的大小  
 重新啟動伺服器執行個體時，就會將 **tempdb** 資料庫的交易記錄大小重新調整為自動成長之前的原始大小。 這樣會降低 **tempdb** 交易記錄的效能。 
 
 您可以在啟動或重新啟動伺服器執行個體後，增加 **tempdb** 交易記錄的大小，藉以避免這項負擔。 如需詳細資訊，請參閱 [tempdb Database](../../relational-databases/databases/tempdb-database.md)。  
  
##  <a name="control-transaction-log-file-growth"></a><a name="ControlGrowth"></a> 控制交易記錄檔的成長  
 請使用 [ALTER DATABASE &#40;Transact-SQL&#41; 檔案及檔案群組選項](../../t-sql/statements/alter-database-transact-sql-file-and-filegroup-options.md)陳述式來管理交易記錄檔的成長。 請注意：  
  
-   若要變更目前的檔案大小 (單位為 KB、MB、GB 和 TB)，請使用 `SIZE` 選項。  
-   若要變更成長的增量，請使用 `FILEGROWTH` 選項。 0 的值表示將自動成長設為關閉，而且不允許任何其他空間。  
-   若要控制記錄檔大小的最大值 (單位為 KB、MB、GB 和 TB) 或是將成長設定為 UNLIMITED，請使用 `MAXSIZE` 選項。  

如需詳細資訊，請參閱本主題中的[建議](#Recommendations)。

## <a name="recommendations"></a><a name="Recommendations"></a> 建議
以下是關於使用交易記錄檔時的一般建議：

-   交易記錄檔的自動成長 (autogrow) 增量，如 `FILEGROWTH` 選項所設定，必須要夠大才能保持領先工作負載交易的需求。 記錄檔的檔案成長量應夠大，才不用經常進行擴充。 若要適當設定交易記錄檔的大小，有一項良好的指標就是監視下列期間所佔用的記錄檔數量：
    -  執行完整備份所需的時間，因為直到完成為止才會發生記錄檔備份。
    -  最大索引維護作業所需的時間。
    -  執行資料庫中最大批次所需的時間。

-   使用 `FILEGROWTH` 選項設定資料和記錄檔的 **autogrow** 時，最好以 [大小] 來設定它，而不是使用 [百分比]，以便更能控制成長比率，因為百分比是個不斷成長的數量。
    -  請記住，交易記錄檔無法利用[立即檔案初始化](../../relational-databases/databases/database-instant-file-initialization.md)，因此延伸的記錄成長時間特別重要。 
    -  最佳做法是不要將交易記錄的 `FILEGROWTH` 選項值設定為超過 1024 MB。 `FILEGROWTH` 選項的預設值是：  
  
      |版本|預設值|  
      |-------------|--------------------|  
      |從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始|資料 64 MB。 記錄檔 64 MB。|  
      |從 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 開始|資料 1 MB。 記錄檔 10%。|  
      |[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 之前|資料 10%。 記錄檔 10%。|  

-   小型的成長增量可能會產生太多小型 [VLF](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#physical_arch)，且可能會降低效能。 若要判斷指定執行個體中所有資料庫的目前交易記錄大小的最佳 VLF 分佈，以及達到所需大小的必要成長增量，請參閱此[指令碼](https://github.com/Microsoft/tigertoolbox/tree/master/Fixing-VLFs)。

-   大型的成長增量可能會產生太少且大型的 [VLF](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#physical_arch)，且亦可能會降低效能。 若要判斷指定執行個體中所有資料庫的目前交易記錄大小的最佳 VLF 分佈，以及達到所需大小的必要成長增量，請參閱此[指令碼](https://github.com/Microsoft/tigertoolbox/tree/master/Fixing-VLFs)。 

-   如果無法成長得夠快速以滿足查詢的需求，即使已啟用 autogrow，您還是可能收到訊息，指出交易記錄檔已滿。 如需變更成長增量的詳細資訊，請參閱 [ALTER DATABASE &#40;Transact-SQL&#41; 檔案及檔案群組選項](../../t-sql/statements/alter-database-transact-sql-file-and-filegroup-options.md)

-   在資料庫中具有多個記錄檔將無法以任何方式強化效能，因為交易記錄檔不像相同檔案群組中的資料檔案那樣使用[比例填滿](../../relational-databases/pages-and-extents-architecture-guide.md#ProportionalFill)。  

-   可以將記錄檔設定為自動壓縮。 不過並 **不建議** 如此，且 **auto_shrink** 資料庫屬性預設會設定為 FALSE。 如果 **auto_shrink** 設定為 TRUE，只有當超過 25% 的空間未使用時，自動壓縮才會減少檔案的大小。 
    -   此時，檔案會壓縮成只有 25% 的檔案是未使用空間的大小，或檔案的原始大小，以較大者為準。 
    -   如需變更 **auto_shrink** 屬性設定的資訊，請參閱 [檢視或變更資料庫的屬性](../../relational-databases/databases/view-or-change-the-properties-of-a-database.md)和 [ALTER DATABASE SET 選項 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)。 
  
## <a name="see-also"></a>另請參閱  
[BACKUP &#40;Transact-SQL&#41;](../../t-sql/statements/backup-transact-sql.md)   
[針對完整交易記錄 &#40;SQL Server 錯誤 9002&#41; 進行疑難排解](../../relational-databases/logs/troubleshoot-a-full-transaction-log-sql-server-error-9002.md)    
[SQL Server 中的交易記錄備份交易記錄架構與管理指南](../../relational-databases/sql-server-transaction-log-architecture-and-management-guide.md#Backups)    
[交易記錄備份 &#40;SQL Server&#41;](../../relational-databases/backup-restore/transaction-log-backups-sql-server.md)    
[ALTER DATABASE &#40;Transact-SQL&#41; 檔案及檔案群組選項](../../t-sql/statements/alter-database-transact-sql-file-and-filegroup-options.md)
