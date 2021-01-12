---
description: sys.syslockinfo (Transact-SQL)
title: sys.syslockinfo (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- syslockinfo_TSQL
- sys.syslockinfo_TSQL
- sys.syslockinfo
- syslockinfo
dev_langs:
- TSQL
helpviewer_keywords:
- syslockinfo system table
- sys.syslockinfo compatibility view
ms.assetid: d8cae434-807a-473e-b94f-f7a0e1b2daf0
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: d552fdbb75c4aa1cb786025a8d1c866b89135e12
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099057"
---
# <a name="syssyslockinfo-transact-sql"></a>sys.syslockinfo (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含所有授與、轉換及等待鎖定要求的相關資訊。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
> [!IMPORTANT]  
>  這項功能已變更，與舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不同。 如需詳細資訊，請參閱 [SQL Server 2016 中資料庫引擎功能的重大變更](../../database-engine/breaking-changes-to-database-engine-features-in-sql-server-2016.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**rsc_text**|**nchar(32)**|鎖定資源的文字描述。 包含鎖定資源的一部份。|  
|**rsc_bin**|**binary(16)**|二進位鎖定資源。 包含內含於鎖定管理員的實際鎖定資源。 此資料行包含在工具中，可瞭解用來產生其專屬格式化鎖定資源的鎖定資源格式，以及在 **syslockinfo** 上執行自我聯結的工具。|  
|**rsc_valblk**|**binary(16)**|鎖定值區塊。 某些資源類型可能會將其他資料併入某鎖定資源中，而該鎖定資源並非由鎖定管理員雜湊以決定特定鎖定資源的擁有權。 例如，頁面鎖定不是由特定物件識別碼擁有。 適用於鎖定擴大和其他用途。 不過，頁面鎖定的物件識別碼可以併入鎖定值區塊中。|  
|**rsc_dbid**|**smallint**|資源所關聯的資料庫識別碼。|  
|**rsc_indid**|**smallint**|資源所關聯的索引識別碼 (如果適用的話)。|  
|**rsc_objid**|**int**|資源所關聯的物件識別碼 (如果適用的話)。|  
|**rsc_type**|**tinyint**|資源類型：<br /><br /> 1 = NULL 資源 (未使用)<br /><br /> 2 = 資料庫<br /><br /> 3 = 檔案<br /><br /> 4 = 索引<br /><br /> 5 = 資料表<br /><br /> 6 = 頁面<br /><br /> 7 = 索引鍵<br /><br /> 8 = 範圍<br /><br /> 9 = RID (資料列識別碼)<br /><br /> 10 = 應用程式|  
|**rsc_flag**|**tinyint**|內部資源旗標。|  
|**req_mode**|**tinyint**|鎖定要求模式。 這個資料行是要求者的鎖定模式，且代表授與模式，或者，轉換或等待模式。<br /><br /> 0 = NULL。 未授與資源的任何存取權。 這用來作為預留位置。<br /><br /> 1 = Sch-S (結構描述穩定性)。 確定在任何工作階段持有結構描述元素的結構描述穩定性鎖定時，不卸除結構描述元素，如資料表或索引。<br /><br /> 2 = Sch-M (結構描述修改)。 想要變更指定資源結構描述的任何工作階段都必須持有這個項目。 請確定沒有其他工作階段在參考指示的物件。<br /><br /> 3 = S (共用)。 持有它的工作階段，會取得資源的共用存取權。<br /><br /> 4 = U (更新)。 表示取得最終可能會更新之資源的更新鎖定。 它用來防止當多個工作階段為了未來可能更新資源而鎖定資源時，所常見的死結形式。<br /><br /> 5 = X (獨佔)。 持有它的工作階段，會取得資源的獨佔存取權。<br /><br /> 6 = IS (意圖共用)。 表示在鎖定階層中的某些從屬資源上設定 S 鎖定的意圖。<br /><br /> 7 = IU (意圖更新)。 表示在鎖定階層中的某些從屬資源上設定 U 鎖定的意圖。<br /><br /> 8 = IX (意圖獨佔)。 表示在鎖定階層中的某些從屬資源上設定 X 鎖定的意圖。<br /><br /> 9 = SIU (共用意圖更新)。 表示意圖取得鎖定階層中從屬資源的更新鎖定之共用資源存取權。<br /><br /> 10 = SIX (共用意圖獨佔)。 表示意圖取得鎖定階層中從屬資源的獨佔鎖定之共用資源存取權。<br /><br /> 11 = UIX (更新意圖獨佔)。 表示意圖取得鎖定階層中從屬資源的獨佔鎖定之資源更新鎖定。<br /><br /> 12 = BU。 供大量作業使用。<br /><br /> 13 = RangeS_S (共用索引鍵範圍和共用資源鎖定)。 指出可序列化的範圍掃描。<br /><br /> 14 = RangeS_U (共用索引鍵範圍和更新資源鎖定)。 指出可序列化的更新掃描。<br /><br /> 15 = RangeI_N (插入索引鍵範圍和 NULL 資源鎖定)。 在將新索引鍵插入索引之前，用來測試範圍。<br /><br /> 16 = RangeI_S。 索引鍵範圍轉換鎖定，由 RangeI_N 和 S 鎖定的重疊建立。<br /><br /> 17 = RangeI_U。 索引鍵範圍轉換鎖定，由 RangeI_N 和 U 鎖定的重疊建立。<br /><br /> 18 = RangeI_X。 索引鍵範圍轉換鎖定，由 RangeI_N 和 X 鎖定的重疊建立。<br /><br /> 19 = RangeX_S。 索引鍵範圍轉換鎖定，由 RangeI_N 和 RangeS_S 鎖定的重疊建立 。<br /><br /> 20 = RangeX_U。 索引鍵範圍轉換鎖定，由 RangeI_N 和 RangeS_U 鎖定的重疊建立。<br /><br /> 21 = RangeX_X (獨佔索引鍵範圍和獨佔資源鎖定)。 這是更新範圍中的索引鍵時所用的轉換鎖定。|  
|**req_status**|**tinyint**|鎖定要求的狀態：<br /><br /> 1 = 授與<br /><br /> 2 = 轉換中<br /><br /> 3 = 等待中|  
|**req_refcnt**|**smallint**|鎖定參考計數。 每當交易要求對特定資源的鎖定時，參考計數就會累加。 參考計數等於 0 之前不會釋放鎖定。|  
|**req_cryrefcnt**|**smallint**|保留供日後使用。 一律設為 0。|  
|**req_lifetime**|**int**|鎖定存留期間點陣圖。 在某些查詢處理策略進行期間，必須維持對資源的鎖定，直到查詢處理器已完成特定的查詢階段為止。 查詢處理器和交易管理員會利用鎖定存留期間點陣圖，指出某查詢階段的執行已完成時可以釋放的鎖定群組。 點陣圖中的某些位元用來指出即使其參考計數等於 0 也會一直保留到交易結束為止的鎖定。|  
|**req_spid**|**int**|[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 要求鎖定之會話的內部處理序識別碼。|  
|**req_ecid**|**int**|執行內容識別碼 (ECID)。 用來指出平行作業中的哪個執行緒擁有特定鎖定。|  
|**req_ownertype**|**smallint**|關聯於鎖定的物件類型。<br /><br /> 1 = 交易<br /><br /> 2 = 資料指標<br /><br /> 3 = 工作階段<br /><br /> 4 = ExSession<br /><br /> 請注意，3 和 4 分別代表特殊版本的工作階段鎖定、追蹤資料庫和檔案群組鎖定。|  
|**req_transactionID**|**bigint**|**Syslockinfo** 和 profiler 事件中使用的唯一交易識別碼|  
|**req_transactionUOW**|**uniqueidentifier**|識別 DTC 交易的工作單位識別碼 (UOW)。 如果是非 MS DTC 交易，UOW 設為 0。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
