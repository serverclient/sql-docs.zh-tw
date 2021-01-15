---
description: 'sys.server_resource_stats (Azure SQL Database) '
title: sys.server_resource_stats (Azure SQL Database) Microsoft Docs
ms.custom: ''
ms.date: 06/28/2018
ms.service: sql-database
ms.topic: language-reference
f1_keywords:
- resource_stats
- sys.resource_stats
- sys.resource_stats_TSQL
- resource_stats_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.resource_stats
- resource_stats
ms.assetid: ''
author: jovanpop-msft
ms.author: jovanpop
monikerRange: =azuresqldb-current
ms.openlocfilehash: 8a913c3bf4f01828fcf75df1e3c69dca9149e2de
ms.sourcegitcommit: 23649428528346930d7d5b8be7da3dcf1a2b3190
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98241819"
---
# <a name="sysserver_resource_stats-azure-sql-database"></a>sys.server_resource_stats (Azure SQL Database) 
[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

傳回 Azure SQL 受控執行個體的 CPU 使用量、IO 和儲存體資料。 於五分鐘間隔內收集及彙總資料。 每15秒報告都有一個資料列。 傳回的資料包含 CPU 使用量、儲存體大小、IO 使用量和 SKU。 歷程記錄資料大約會保留 14 天。

**Sys.server_resource_stats** view 具有不同的定義，這取決於與資料庫相關聯的 Azure SQL 受控執行個體版本。 請考慮這些差異，以及升級到新伺服器版本時應用程式所需的任何修改。
 
  
 下表描述 v12 伺服器中可用的資料行：  
  
|資料行|資料類型|描述|  
|----------------------------|---------------|-----------------|  
|start_time|**datetime2**|指出15秒報告間隔開始時間的 UTC 時間|  
|end_time|**datetime**|指出15秒報告間隔結束的 UTC 時間|
|resource_type|Nvarchar(128)|提供計量的資源類型|
|resource_name|nvarchar(128)|資源名稱。|
|sku|nvarchar(128)|實例的受控執行個體服務層級。 以下是可能的值： <br><ul><li>一般用途</li></ul><ul><li>業務關鍵</li></ul>|
|hardware_generation|nvarchar(128)|硬體世代識別碼：例如 Gen 4 或 Gen 5|
|virtual_core_count|int|代表每個實例的虛擬核心數目 (8、16或24（公開預覽）) |
|avg_cpu_percent|decimal (5，2) |實例使用受控執行個體服務層級限制的平均計算使用率（以百分比表示）。 它會計算為實例中所有資料庫之所有資源集區的 CPU 時間總和，並在指定的間隔中除以該層的可用 CPU 時間。|
|reserved_storage_mb|BIGINT|每個實例的保留儲存體 (客戶針對受控實例購買的儲存空間量) |
|storage_space_used_mb|decimal (18，2) |受控實例中所有資料庫檔案所使用的儲存體 (包括使用者和系統資料庫) |
|io_request|BIGINT|間隔內的 i/o 實際作業總數|
|io_bytes_read|BIGINT|間隔內讀取的實體位元組數|
|io_bytes_written|BIGINT|間隔內寫入的實體位元組數|

 
> [!TIP]  
>  如需這些限制和服務層級的詳細內容，請參閱 [受控執行個體服務層級](/azure/sql-database/sql-database-managed-instance#managed-instance-service-tiers)的主題。  
    
## <a name="permissions"></a>權限  
 此視圖適用于具有連接到 **master** 資料庫之許可權的所有使用者角色。  
  
## <a name="remarks"></a>備註  
 **Sys.server_resource_stats** 所傳回的資料會以位元組或 mb (中所述的資料行名稱來表示，) 除了 avg_cpu 以外的資料行名稱中所述，這是以您正在執行之服務層級/效能層級的最大允許限制百分比表示。  
 
## <a name="examples"></a>範例  
下列範例會傳回過去七天的平均 CPU 使用量。  
  
```sql  
DECLARE @s datetime;  
DECLARE @e datetime;  
SET @s= DateAdd(d,-7,GetUTCDate());  
SET @e= GETUTCDATE();  
SELECT AVG(avg_cpu_percent) AS Average_Compute_Utilization   
FROM sys.server_resource_stats   
WHERE start_time BETWEEN @s AND @e  
GO;
```  
    
## <a name="see-also"></a>另請參閱  
 [受控執行個體服務層級](/azure/sql-database/sql-database-managed-instance#managed-instance-service-tiers)
