---
description: sys.database_connection_stats (Azure SQL Database)
title: sys.database_connection_stats
titleSuffix: Azure SQL Database
ms.date: 01/28/2019
ms.service: sql-database
ms.reviewer: ''
ms.topic: language-reference
f1_keywords:
- sys.database_connection_stats
- database_connection_stats
- database_connection_stats_TSQL
- sys.database_connection_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.database_connection_stats
- database_connection_stats
ms.assetid: 5c8cece0-63b0-4dee-8db7-6b43d94027ec
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.custom: seo-dt-2019
monikerRange: = azuresqldb-current
ms.openlocfilehash: b5d01ec490009c2c3b26dd888bd6050b0638e952
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102813"
---
# <a name="sysdatabase_connection_stats-azure-sql-database"></a>sys.database_connection_stats (Azure SQL Database)

[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

  包含 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 資料庫 **連接** 事件的統計資料，提供資料庫連接成功和失敗的總覽。 如需連接事件的詳細資訊，請參閱 [sys.event_log &#40;Azure SQL Database&#41;](../../relational-databases/system-catalog-views/sys-event-log-azure-sql-database.md)中的事件種類。  
  
|統計資料|類型|描述|  
|---------------|----------|-----------------|  
|**database_name**|**sysname**|資料庫的名稱。|  
|**start_time**|**datetime2**|彙總間隔開始的 UTC 日期和時間。 這個時間永遠是 5 分鐘的倍數。 例如：<br /><br /> '2011-09-28 16:00:00'<br />'2011-09-28 16:05:00'<br />'2011-09-28 16:10:00'|  
|**end_time**|**datetime2**|彙總間隔結束的 UTC 日期和時間。 **End_time** 永遠比相同資料列中的對應 **start_time** 晚5分鐘。|  
|**success_count**|**int**|成功連接的數目。|  
|**total_failure_count**|**int**|連接失敗的總數。 這是 **connection_failure_count**、 **terminated_connection_count** 和 **throttled_connection_count** 的總和，不包含鎖死事件。|  
|**connection_failure_count**|**int**|登入失敗的數目。|  
|**terminated_connection_count**|**int**|**_僅適用于 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] v11。_**<br /><br /> 終止的連接數目。|  
|**throttled_connection_count**|**int**|**_僅適用于 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] v11。_**<br /><br /> 節流的連接數目。|  
  
## <a name="remarks"></a>備註  
  
### <a name="event-aggregation"></a>事件彙總

 這個檢視的事件資訊會在 5 分鐘間隔內收集和彙總。 計數資料行代表在給定時間間隔內，特定資料庫發生特殊連接性事件的次數。  
  
 例如，如果使用者在連接到 Database1 資料庫時，於 2012 年 2 月 5 日 11:00 到 11:05 (UTC) 之間失敗七次，這項資訊會在這個檢視的單一資料列中提供：  
  
|**database_name**|**start_time**|**end_time**|**success_count**|**total_failure_count**|**connection_failure_count**|**terminated_connection_count**|**throttled_connection_count**|  
|------------------------|---------------------|-------------------|------------------------|-------------------------------|------------------------------------|---------------------------------------|--------------------------------------|  
|`Database1`|`2012-02-05 11:00:00`|`2012-02-05 11:05:00`|`0`|`7`|`7`|`0`|`0`|  
  
### <a name="interval-start_time-and-end_time"></a>間隔的 start_time 和 end_time

 當事件發生在 **start_time** 之後，或 _在_ 該間隔 **end_time** _之前_，事件 *就會包含* 在匯總間隔內。 例如，正巧發生在 `2012-10-30 19:25:00.0000000` 的事件只會納入到以下所示的第二段間隔：  
  
```  
  
start_time                    end_time  
2012-10-30 19:20:00.0000000   2012-10-30 19:25:00.0000000  
2012-10-30 19:25:00.0000000   2012-10-30 19:30:00.0000000  
```  
  
### <a name="data-updates"></a>資料更新

 這個檢視中的資料會累積一段時間。 通常資料會在彙總間隔開始的 1 小時內累積，但是最長可能需要 24 小時，所有資料才會出現在檢視中。 在這段期間，單一資料列內的資訊會定期更新。  
  
### <a name="data-retention"></a>資料保留

 此視圖中的資料最多會保留30天，或根據資料庫數目和每個資料庫所產生之唯一事件的數目而定，可能會較少。 若要保留這項資訊更長的時間，請將資料複製到另一個資料庫。 在您製作檢視的初始副本之後，檢視中的資料列可能會隨資料累積而更新。 為了讓資料副本保持最新狀態，請定期執行資料列的資料表掃描，查看現有資料列的事件計數是否增加，並且識別新資料列 (您可以使用開始和結束時間識別唯一資料列)，然後用這些變更來更新您的資料副本。  
  
### <a name="errors-not-included"></a>不包括錯誤

 這個檢視可能不會包括所有連接和錯誤資訊：  
  
- 此視圖不包括所有 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 可能發生的資料庫錯誤，只是 [sys.event_log &#40;Azure SQL Database&#41;](../../relational-databases/system-catalog-views/sys-event-log-azure-sql-database.md)中的事件種類所指定的錯誤。  
  
- 如果資料中心內發生機器故障 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] ，則事件資料表中可能會遺失少量的資料。  
  
- 如果已透過 DoSGuard 封鎖 IP 位址，則來自該 IP 位址的連接嘗試事件就無法收集，也不會出現在這個檢視中。  
  
## <a name="permissions"></a>權限

 擁有存取 **master** 資料庫之許可權的使用者，擁有此視圖的唯讀存取權。  
  
## <a name="example"></a>範例

 下列範例會顯示 **sys.database_connection_stats** 的查詢，以傳回在9/25/2011 和中午 9/28/2011 (UTC) 之間發生的資料庫連接摘要。 根據預設，查詢結果會依 **start_time** (遞增順序) 排序。  
  
```sql
SELECT *  
FROM sys.database_connection_stats
WHERE start_time>='2011-09-25 12:00:00' and end_time<='2011-09-28 12:00:00';  
```  

## <a name="see-also"></a>另請參閱

 [針對 Azure SQL Database 連線問題進行疑難排解](/azure/sql-database/sql-database-troubleshoot-common-connection-issues)  
  
  
