---
title: 將備份卸載到次要可用性群組複本
description: 了解將備份卸載至 Always On 可用性群組次要複本時的各種支援備份類型。
ms.custom: seo-lt-2019
ms.date: 09/01/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
helpviewer_keywords:
- backup priority
- backup on secondary replicas
- Availability Groups [SQL Server], availability replicas
- Availability Groups [SQL Server], backup on secondary replicas
- active secondary replicas [SQL Server], backup on
- automated backup preference
- Availability Groups [SQL Server], active secondary replicas
ms.assetid: 82afe51b-71d1-4d5b-b20a-b57afc002405
author: cawrites
ms.author: chadam
ms.openlocfilehash: 79972c5670370c28404b5cc93d7c3b833551f988
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644304"
---
# <a name="offload-supported-backups-to-secondary-replicas-of-an-availability-group"></a>將支援的備份卸載至可用性群組次要複本
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

  [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 的作用中次要功能包含對次要複本進行備份的支援。 備份作業可能會對 I/O 和 CPU 造成相當大的壓力 (備份壓縮已啟用時)。 將備份卸載至已同步處理或正在同步處理的次要複本，可讓裝載主要複本的伺服器執行個體上的資源用於第 1 層工作負載。  

> [!NOTE]  
>  不可對可用性群組的主要或次要資料庫執行 RESTORE 陳述式。  
  
 
##  <a name="backup-types-supported-on-secondary-replicas"></a><a name="SupportedBuTypes"></a> 次要複本支援的備份類型  
  
-   **BACKUP DATABASE** 只有在次要複本上執行時，才支援資料庫、檔案或檔案群組的只複製完整備份。 只複製備份不會影響記錄鏈結或清除差異式點陣圖。  
  
-   次要複本不支援差異備份。

-   目前不支援並行備份，例如在次要複本上執行完整資料庫備份時，在主要複本上執行交易記錄備份。 
  
-   **BACKUP LOG** 只支援一般記錄備份 (次要複本的記錄備份不支援 COPY_ONLY 選項)。  
  
     跨任何複本 (主要或次要) 上所做的記錄檔備份可確保記錄檔鏈結一致，無論其可用性模式為何 (同步認可或非同步認可)。  
  
-   若要備份次要資料庫，次要複本必須能夠與主要複本通訊，而且必須處於 **SYNCHRONIZED** 或 **SYNCHRONIZING** 狀態。  

在分散式可用性群組中，可在與使用中的主要複本所在相同之可用性群組中的次要複本上，或在任何次要可用性群組的主要複本上執行備份。 因為次要複本只會與其所屬可用性群組中的主要複本通訊，所以無法在次要可用性群組中的次要複本上執行備份。 只有直接與全域主要複本通訊的複本才能執行備份作業。

##  <a name="configuring-where-backup-jobs-run"></a><a name="WhereBuJobsRun"></a> 設定執行備份作業的位置  
 在次要複本執行備份，以便從主要實際執行伺服器卸載備份工作負載，是一極大的好處。 不過，在次要複本上執行備份會讓決定是否應該執行備份作業的程序複雜許多。 若要解決這個問題，請依照以下方式設定執行備份作業的位置：  
  
1.  設定可用性群組來指定您想要在哪些可用性複本執行備份。 如需詳細資訊，請參閱 *CREATE AVAILABILITY GROUP &amp;#40;Transact-SQL&amp;#41;* 或 *ALTER AVAILABILITY GROUP &amp;#40;Transact-SQL&amp;#41;* 的 [CREATE AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/create-availability-group-transact-sql.md) 或 [ALTER AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-availability-group-transact-sql.md)狀態。  
  
2.  在裝載可用性複本的每個伺服器執行個體，而此可用性複本是執行備份的候選複本，為每個可用性資料庫建立已編寫指令碼的備份作業。 如需詳細資訊，請參閱 [設定可用性複本的備份 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server.md)狀態。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要設定次要複本的備份**  
  
-   [設定可用性複本的備份 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server.md)  
  
 **若要判斷目前的複本是否為慣用的備份複本**  
  
-   [sys.fn_hadr_backup_is_preferred_replica](../../../relational-databases/system-functions/sys-fn-hadr-backup-is-preferred-replica-transact-sql.md)  
  
 **若要建立備份作業**  
  
-   [使用維護計畫精靈](../../../relational-databases/maintenance-plans/use-the-maintenance-plan-wizard.md)  
  
-   [實作作業](../../../ssms/agent/implement-jobs.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [只複製備份 &#40;SQL Server&#41;](../../../relational-databases/backup-restore/copy-only-backups-sql-server.md)   
 [CREATE AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/create-availability-group-transact-sql.md)   
 [ALTER AVAILABILITY GROUP &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-availability-group-transact-sql.md)  
  
  
