---
description: sys.dm_os_performance_counters (Transact-SQL)
title: sys.dm_os_performance_counters (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/13/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_os_performance_counters
- sys.dm_os_performance_counters_TSQL
- dm_os_performance_counters_TSQL
- sys.dm_os_performance_counters
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_performance_counters dynamic management view
ms.assetid: a1c3e892-cd48-40d4-b6be-2a9246e8fbff
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 68d8c4eb6b0ee1da0427894821886f98129f6585
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094050"
---
# <a name="sysdm_os_performance_counters-transact-sql"></a>sys.dm_os_performance_counters (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對伺服器所維護的每個效能計數器，各傳回一個資料列。 如需每個效能計數器的詳細資訊，請參閱 [使用 SQL Server 物件](../../relational-databases/performance-monitor/use-sql-server-objects.md)。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_performance_counters** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**object_name**|**nchar(128)**|這個計數器所屬的類別目錄。|  
|**counter_name**|**nchar(128)**|計數器的名稱。 為了取得計數器的詳細資訊，這是要從使用中的計數器清單中選取的主題名稱 [SQL Server 物件](../../relational-databases/performance-monitor/use-sql-server-objects.md)。 |  
|**instance_name**|**nchar(128)**|計數器的特定執行個體名稱。 通常包含資料庫名稱。|  
|**cntr_value**|**bigint**|計數器的目前值。<br /><br /> **注意：** 針對每秒的計數器，這個值是累計的。 必須以不連續時間間隔取樣值來計算該速率值。 任何兩個連續取樣值之間的差等於所使用的時間間隔速率。|  
|**cntr_type**|**int**|Windows 效能架構所定義的計數器類型。 如需效能計數器類型的詳細資訊，請參閱檔中的 [WMI 效能計數器類型](/windows/desktop/WmiSdk/wmi-performance-counter-types) 或您的 Windows Server 檔。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="remarks"></a>備註  
 如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的安裝執行個體無法顯示 Windows 作業系統的效能計數器，請使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢來確認效能計數器是否已停用。  
  
```sql  
SELECT COUNT(*) FROM sys.dm_os_performance_counters;  
```  
  
如果傳回值為 0 個資料列，這就表示效能計數器已停用。 接著，您應該查看安裝記錄檔並搜尋錯誤3409， `Reinstall sqlctr.ini for this instance, and ensure that the instance login account has correct registry permissions.` 這表示未啟用效能計數器。 緊接在 3409 錯誤前面的錯誤應該會指出效能計數器啟用失敗的根本原因。 如需安裝程式記錄檔的詳細資訊，請參閱 [View 和 Read SQL Server 安裝程式記錄](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)檔。  

當資料 `cntr_type` 行值為65792、272696320和537003264時，效能計數器會顯示即時快照計數器值。

效能計數器，其中資料 `cntr_type` 行值為272696576、1073874176和1073939712會顯示累計計數器值，而不是立即快照。 因此，若要取得類似快照集的讀取，您必須比較兩個收集點之間的差異。

## <a name="permission"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
 
## <a name="examples"></a>範例  
 下列範例會傳回顯示快照計數器值的所有效能計數器。  
  
```sql  
SELECT object_name, counter_name, instance_name, cntr_value, cntr_type  
FROM sys.dm_os_performance_counters
WHERE cntr_type = 65792 OR cntr_type = 272696320 OR cntr_type = 537003264;  
```  
  
## <a name="see-also"></a>另請參閱  
  [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)   
 [sys.sysperfinfo &#40;Transact-SQL&#41;](../../relational-databases/system-compatibility-views/sys-sysperfinfo-transact-sql.md)  
  
