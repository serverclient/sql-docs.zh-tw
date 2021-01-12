---
description: sys.dm_repl_traninfo (Transact-SQL)
title: sys.dm_repl_traninfo (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_repl_traninfo
- dm_repl_traninfo
- sys.dm_repl_traninfo_TSQL
- dm_repl_traninfo_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_repl_traninfo dynamic management view
ms.assetid: 5abe2605-0506-46ec-82b5-6ec08428ba13
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f8a0613d83cebfed56ba5202a6c3535ef31c8a84
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098836"
---
# <a name="sysdm_repl_traninfo-transact-sql"></a>sys.dm_repl_traninfo (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回每一項複寫交易或異動資料擷取交易的資訊。  

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**fp2p_pub_exists**|**tinyint**|如果交易是在資料庫中，則使用點對點異動複寫進行發行。 若為 True，該值為 1；否則為 0。|  
|**db_ver**|**int**|資料庫版本。|  
|**comp_range_address**|**varbinary(8)**|定義必須略過的部分回復範圍。|  
|**textinfo_address**|**varbinary(8)**|快取文字資訊結構的記憶體中位址。|  
|**fsinfo_address**|**varbinary(8)**|快取檔案資料流資訊結構的記憶體中位址。|  
|**begin_lsn**|**Nvarchar (64)**|交易之開始記錄的記錄序號 (LSN)。|  
|**commit_lsn**|**Nvarchar (64)**|交易之認可記錄的 LSN。|  
|**dbid**|**smallint**|資料庫識別碼。|  
|**rows**|**int**|交易內的複寫命令識別碼。|  
|**xdesid**|**Nvarchar (64)**|交易識別碼。|  
|**artcache_table_address**|**varbinary(8)**|上次用於這項交易之快取發行項資料表結構的記憶體中位址。|  
|**伺服器**|**Nvarchar (514)**|伺服器名稱。|  
|**server_len_in_bytes**|**smallint**|伺服器名稱的字元長度 (以位元組為單位)。|  
|**database**|**Nvarchar (514)**|資料庫名稱。|  
|**db_len_in_bytes**|**smallint**|資料庫名稱的字元長度 (以位元組為單位)。|  
|**鼻祖**|**Nvarchar (514)**|引發交易的伺服器名稱。|  
|**originator_len_in_bytes**|**smallint**|引發交易之伺服器的字元長度 (以位元組為單位)。|  
|**orig_db**|**Nvarchar (514)**|引發交易的資料庫名稱。|  
|**orig_db_len_in_bytes**|**smallint**|引發交易之資料庫的字元長度 (以位元組為單位)。|  
|**cmds_in_tran**|**int**|目前交易的複寫命令數目，用來決定何時要認可邏輯交易。|  
|**is_boundedupdate_singleton**|**tinyint**|指定唯一資料行更新是否只影響單一資料列。|  
|**begin_update_lsn**|**Nvarchar (64)**|用於唯一資料行更新的 LSN。|  
|**delete_lsn**|**Nvarchar (64)**|刪除作為更新的一部分的 LSN。|  
|**last_end_lsn**|**Nvarchar (64)**|邏輯交易的最後一個 LSN。|  
|**fcomplete**|**tinyint**|指定命令是否為部分更新。|  
|**fcompensated**|**tinyint**|指定交易是否涉及部分回復。|  
|**fprocessingtext**|**tinyint**|指定交易是否包含二進位大型資料類型資料行。|  
|**max_cmds_in_tran**|**int**|邏輯交易中的命令數目上限，如記錄讀取器代理程式所指定。|  
|**begin_time**|**datetime**|交易開始的時間。|  
|**commit_time**|**datetime**|認可交易的時間。|  
|**session_id**|**int**|異動資料擷取記錄檔掃描工作階段的識別碼。 這個資料行會對應到 [sys.dm_cdc_logscan_sessions](../../relational-databases/system-dynamic-management-views/change-data-capture-sys-dm-cdc-log-scan-sessions.md)中的 **session_id** 資料行。|  
|**session_phase**|**int**|指出發生錯誤時工作階段所處階段的編號。 這個資料行會對應到 [sys.dm_cdc_errors](../../relational-databases/system-dynamic-management-views/change-data-capture-sys-dm-cdc-errors.md)中的 **phase_number** 資料行。|  
|**is_known_cdc_tran**|**bit**|指出交易是由異動資料擷取所追蹤。<br /><br /> 0 = 交易複寫交易。<br /><br /> 1 = 異動資料擷取交易。|  
|**error_count**|**int**|發生的錯誤數目。|  
  
## <a name="permissions"></a>權限  
 需要發行集資料庫的 VIEW DATABASE STATE 權限，或針對異動資料擷取啟用之資料庫的 VIEW DATABASE STATE 權限。  
  
## <a name="remarks"></a>備註  
 只對目前載入發行項快取中的複寫資料庫物件或針對異動資料擷取啟用的資料表傳回這項資訊。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [複寫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/replication-related-dynamic-management-views-transact-sql.md)   
 [異動資料擷取相關的動態管理檢視 &#40;Transact-SQL&#41;](./system-dynamic-management-views.md)  
  
