---
title: 備份壓縮 (SQL Server) | Microsoft Docs
description: 了解 SQL Server 備份的壓縮，包括限制、效能取捨、備份壓縮設定以及壓縮比率。
ms.custom: ''
ms.date: 07/08/2020
ms.prod: sql
ms.prod_service: backup-restore
ms.reviewer: ''
ms.technology: backup-restore
ms.topic: conceptual
helpviewer_keywords:
- log shipping [SQL Server], backup compression
- backup compression [SQL Server], about backup compression
- compression [SQL Server], backup compression
- backups [SQL Server], compression
- backing up [SQL Server], backup compression
- backup compression [SQL Server]
ms.assetid: 05bc9c4f-3947-4dd4-b823-db77519bd4d2
author: cawrites
ms.author: chadam
ms.openlocfilehash: a98aaaea7d243853f31d19c0e7c3aa824ee2cc4e
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171470"
---
# <a name="backup-compression-sql-server"></a>備份壓縮 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  本主題會說明 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 備份的壓縮，包括限制、壓縮備份的效能取捨、備份壓縮的組態及壓縮比率。  下列 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本支援備份壓縮：Enterprise、Standard 和 Developer。  每個 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 版本與更新版本都可以還原壓縮的備份。 
 
  
##  <a name="benefits"></a><a name="Benefits"></a> 優點  
  
-   由於壓縮的備份小於相同資料的未壓縮備份，所以壓縮備份通常需要更少的裝置 I/O 而且通常會大幅提升備份速度。  
  
     如需詳細資訊，請參閱本主題稍後介紹的＜ [壓縮備份的效能影響](#PerfImpact)＞。  
  
  
##  <a name="restrictions"></a><a name="Restrictions"></a> 限制  
 下列限制適用於壓縮的備份：  
  
-   壓縮和未壓縮的備份無法在媒體集中並存。  
  
-   舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無法讀取壓縮的備份。  
  
-   NTbackup 無法與壓縮的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 備份共用磁帶。  
  
  
##  <a name="performance-impact-of-compressing-backups"></a><a name="PerfImpact"></a> 壓縮備份的效能影響  
 根據預設，壓縮會大幅增加 CPU 使用量，而且壓縮程序所耗用的額外 CPU 可能會對並行作業造成不良的影響。 因此，您可能會想要在其 CPU 使用量由[資源管理員](../../relational-databases/resource-governor/resource-governor.md)限制的工作階段中，建立低優先權的壓縮備份。 如需詳細資訊，請參閱本主題稍後介紹的＜ [使用資源管理員進行備份壓縮，以限制 CPU 使用率 &#40;Transact-SQL&#41;](../../relational-databases/backup-restore/use-resource-governor-to-limit-cpu-usage-by-backup-compression-transact-sql.md)限制的工作階段中，建立低優先權的壓縮備份。  
  
 若要全盤了解備份 I/O 效能，您可以透過評估下列效能計數器種類，隔離往返裝置之間的備份 I/O：  
  
-   Windows I/O 效能計數器，例如實體磁碟計數器  
  
-   **SQLServer:Backup Device** 物件的 [Device Throughput Bytes/sec](../../relational-databases/performance-monitor/sql-server-backup-device-object.md) 計數器  
  
-   **SQLServer:Databases** 物件的 [Backup/Restore Throughput/sec](../../relational-databases/performance-monitor/sql-server-databases-object.md) 計數器  
  
 如需有關 Windows 計數器的詳細資訊，請參閱 Windows 說明。 如需如何使用 SQL Server 計數器的相關資訊，請參閱 [使用 SQL Server 物件](../../relational-databases/performance-monitor/use-sql-server-objects.md)。  
  
   
##  <a name="calculate-the-compression-ratio-of-a-compressed-backup"></a><a name="CompressionRatio"></a> 計算壓縮備份的壓縮率  
 若要計算備份的壓縮率，請使用 **backupset** 記錄資料表的 **backup_size** 和 [compressed_backup_size](../../relational-databases/system-tables/backupset-transact-sql.md) 資料行中的備份值，如下所示：  
  
 **backup_size**：**compressed_backup_size**  
  
 例如，壓縮比 3:1 表示您節省了大約 66% 的磁碟空間。 若要針對這些資料行進行查詢，您可以使用下列 Transact-SQL 陳述式：  
  
```sql  
SELECT backup_size/compressed_backup_size FROM msdb..backupset;  
```  
  
 壓縮備份的壓縮比取決於已經壓縮的資料。 有許多因素可能會影響取得的壓縮比。 主要的因素包括：  
  
-   資料的類型。  
  
     字元資料的壓縮比較其他資料類型要高。  
  
-   頁面上資料列之間的資料一致性。  
  
     一般而言，如果某個頁面包含許多資料列，而且其中某個欄位包含相同的值，則系統可能會針對該值進行大幅壓縮。 反之，如果某個資料庫包含隨機資料或者每個頁面僅包含單一大型資料列，則壓縮的備份幾乎會與未壓縮的備份一樣大。  
  
-   資料是否經過加密  
  
     加密資料的壓縮比大幅低於對等的未加密資料。 例如，如果在資料行層級以 Always Encrypted 加密資料，或使用其他應用層級加密來加密資料，則壓縮備份可能不會明顯縮減大小。

     如需壓縮以透明資料加密 (TDE) 加密的資料庫詳細資訊，請參閱[使用 TDE 備份壓縮](#backup-compression-with-tde)。

-   資料庫是否經過壓縮。  
  
     如果資料庫已壓縮，壓縮備份可能不會大幅縮減其大小 (如果有的話)。  

## <a name="backup-compression-with-tde"></a>使用 TDE 備份壓縮

從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，設定 **大於 65536 (64 KB)** 的 `MAXTRANSFERSIZE` 會針對 [透明資料加密 (TDE)](../../relational-databases/security/encryption/transparent-data-encryption.md) 加密的資料庫啟用最佳化壓縮演算法，此演算法會先將頁面解密、將其壓縮，然後再次加密。 如果未指定 `MAXTRANSFERSIZE` 或使用了 `MAXTRANSFERSIZE = 65536` (64 KB)，則搭配 TDE 加密資料庫的備份壓縮就會直接壓縮已加密頁面，且可能不會產生良好的壓縮率。 如需詳細資訊，請參閱[適用於已啟用 TDE 之資料庫的備份壓縮](/archive/blogs/sqlcat/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases) \(英文\)。

從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] CU5 開始，不再需要設定 `MAXTRANSFERSIZE` 來啟用搭配 TDE 的這個最佳化壓縮演算法。 如果備份命令指定了 `WITH COMPRESSION`，或 *backup compression default* 伺服器組態設為 1，則 `MAXTRANSFERSIZE` 會自動增加至 128K，以啟用最佳化演算法。 如果在備份命令上指定了值大於 64K 的 `MAXTRANSFERSIZE`，則會接受提供的值。 換句話說，SQL Server 永遠不會自動降低值，其只會增加。 如果需要使用 `MAXTRANSFERSIZE = 65536` 備份 TDE 加密的資料庫，則必須指定 `WITH NO_COMPRESSION`，或確定 *backup compression default* 伺服器組態設為 0。

如需詳細資訊，請參閱 [BACKUP (Transact-SQL)](../../t-sql/statements/backup-transact-sql.md)。

##  <a name="allocation-of-space-for-the-backup-file"></a><a name="Allocation"></a> 配置備份檔案的空間  
 對於壓縮備份，最後備份檔案的大小取決於資料可壓縮的程度，但是在備份作業完成之前，無法得知這項資訊。  因此，根據預設，使用壓縮備份資料庫時，Database Engine 會針對備份檔案使用預先配置的演算法。 此演算法預先為備份檔案配置了預先定義的資料庫大小百分比。 如果在備份作業期間需要更多空間，Database Engine 會增加檔案大小。 如果最後的大小小於配置的空間，在備份作業結束時，Database Engine 會將檔案縮小為最後的實際備份大小。  
  
 若要讓備份檔案只依照需要增加至最終大小，請使用追蹤旗標 3042。 追蹤旗標 3042 會導致備份作業略過預設備份壓縮預先配置演算法。 如果您只要配置壓縮備份所需的實際大小，藉以節省空間，這個追蹤旗標就很有用。 不過，使用此追蹤旗標可能會導致效能稍微降低 (可能會增加備份作業的持續時間)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [設定備份壓縮 &#40;SQL Server&#41;](../../relational-databases/backup-restore/configure-backup-compression-sql-server.md)  
  
-   [檢視或設定 backup compression default 伺服器組態選項](../../database-engine/configure-windows/view-or-configure-the-backup-compression-default-server-configuration-option.md)  
  
-   [使用資源管理員進行備份壓縮，以限制 CPU 使用率 &#40;Transact-SQL&#41;](../../relational-databases/backup-restore/use-resource-governor-to-limit-cpu-usage-by-backup-compression-transact-sql.md)  
  
-   [DBCC TRACEON &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-traceon-transact-sql.md)  
  
-   [DBCC TRACEOFF &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-traceoff-transact-sql.md)  
  
## <a name="see-also"></a>另請參閱  
 [備份概觀 &#40;SQL Server&#41;](../../relational-databases/backup-restore/backup-overview-sql-server.md)   
 [追蹤旗標 &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)  
  
