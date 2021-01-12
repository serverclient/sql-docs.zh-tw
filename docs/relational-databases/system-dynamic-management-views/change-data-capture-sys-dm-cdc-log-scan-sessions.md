---
description: 變更資料捕獲-sys.dm_cdc_log_scan_sessions
title: sys.dm_cdc_log_scan_sessions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_cdc_log_scan_sessions
- dm_cdc_log_scan_sessions_TSQL
- sys.dm_cdc_log_scan_sessions_TSQL
- sys.dm_cdc_log_scan_sessions
dev_langs:
- TSQL
helpviewer_keywords:
- change data capture [SQL Server], log scan reporting
- sys.dm_cdc_log_scan_sessions dynamic management view
ms.assetid: d337e9d0-78b1-4a07-8820-2027d0b9f87c
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 312a0577d6ac2d092b01b34fcdb2aa034d36995d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100064"
---
# <a name="change-data-capture---sysdm_cdc_log_scan_sessions"></a>變更資料捕獲-sys.dm_cdc_log_scan_sessions
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對目前資料庫中的每個記錄掃描工作階段，各傳回一個資料列。 最後一個傳回的資料列代表目前的工作階段。 您可以使用這個檢視來傳回目前記錄掃描工作階段的相關狀態資訊，或自從上次啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體以來所有工作階段的相關彙總資訊。  
   
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**session_id**|**int**|工作階段的識別碼。<br /><br /> 0 = 這個資料列中傳回的資料是自從上次啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體以來所有工作階段的彙總。|  
|**start_time**|**datetime**|工作階段開始的時間。<br /><br /> 當 **session_id** = 0 時，匯總的資料收集開始時間。|  
|**end_time**|**datetime**|工作階段結束的時間。<br /><br /> NULL = 工作階段使用中。<br /><br /> 當 **session_id** = 0 時，就是上一個會話結束的時間。|  
|**duration**|**bigint**|工作階段的持續時間 (以秒為單位)。<br /><br /> 0 = 工作階段不包含異動資料擷取交易。<br /><br /> 當 **session_id** = 0 時，具有變更資料捕獲交易之所有會話的持續時間 (（以秒為單位）) 。|  
|**scan_phase**|**nvarchar(200)**|工作階段的目前階段。 以下是可能的值及其描述：<br /><br /> 1：正在讀取設定<br />2：第一次掃描，建立雜湊表<br />3：第二次掃描<br />4：第二次掃描<br />5：第二次掃描<br />6：架構版本控制<br />7：上次掃描<br />8：完成<br /><br /> 當 **session_id** = 0 時，這個值一律是 "Aggregate"。|  
|**error_count**|**int**|發生的錯誤數目。<br /><br /> 當 **session_id** = 0 時，就是所有會話中的總錯誤數。|  
|**start_lsn**|**Nvarchar (23)**|工作階段的起始 LSN。<br /><br /> 當 **session_id** = 0 時，就是上一個會話的起始 LSN。|  
|**current_lsn**|**Nvarchar (23)**|目前正在掃描的 LSN。<br /><br /> 當 **session_id** = 0 時，目前的 LSN 為0。|  
|**end_lsn**|**Nvarchar (23)**|工作階段的結束 LSN。<br /><br /> NULL = 工作階段使用中。<br /><br /> 當 **session_id** = 0 時，是最後一個會話的結束 LSN。|  
|**tran_count**|**bigint**|已處理的異動資料擷取交易數目。 此計數器會在第2階段填入。<br /><br /> 當 **session_id** = 0 時，就是所有會話中已處理的交易數目。|  
|**last_commit_lsn**|**Nvarchar (23)**|上一個已處理之認可記錄的 LSN。<br /><br /> 當 **session_id** = 0 時，就是任何會話的最後一個認可記錄 LSN。|  
|**last_commit_time**|**datetime**|處理上一個認可記錄的時間。<br /><br /> 當 **session_id** = 0 時，就是任何會話的最後一個認可記錄檔記錄的時間。|  
|**log_record_count**|**bigint**|已掃描的記錄數目。<br /><br /> 當 **session_id** = 0 時，會掃描所有會話的記錄數目。|  
|**schema_change_count**|**int**|已偵測之資料定義語言 (DDL) 作業的數目。 這個計數器會在第 6 個階段中填入。<br /><br /> 當 **session_id** = 0 時，就是所有會話中已處理的 DDL 作業數目。|  
|**command_count**|**bigint**|已處理的命令數目。<br /><br /> 當 **session_id** = 0 時，就是所有會話中已處理的命令數目。|  
|**first_begin_cdc_lsn**|**Nvarchar (23)**|包含異動資料擷取交易的第一個 LSN。<br /><br /> 當 **session_id** = 0 時，包含變更資料捕獲交易的第一個 LSN。|  
|**last_commit_cdc_lsn**|**Nvarchar (23)**|包含異動資料擷取交易之上一個認可記錄的 LSN。<br /><br /> 當 **session_id** = 0 時，包含變更資料捕獲交易之任何會話的最後一個認可記錄 LSN|  
|**last_commit_cdc_time**|**datetime**|處理包含異動資料擷取交易之上一個認可記錄的時間。<br /><br /> 當 **session_id** = 0 時，即為包含變更資料捕獲交易之任何會話的上次認可記錄檔記錄時間。|  
|**延遲**|**int**|會話中 **end_time** 和 **last_commit_cdc_time** 之間的差異（以秒為單位）。 這個計數器會在第 7 個階段結束時填入。<br /><br /> 當 **session_id** = 0 時，會話所記錄的最後非零延遲值。|  
|**empty_scan_count**|**int**|不包含任何異動資料擷取交易的連續工作階段數目。|  
|**failed_sessions_count**|**int**|失敗的工作階段數目。|  
  
## <a name="remarks"></a>備註  
 每次啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，這個動態管理檢視中的值就會重設。  
  
## <a name="permissions"></a>權限  
 需要 VIEW DATABASE STATE 許可權才能查詢 **sys.dm_cdc_log_scan_sessions** 動態管理檢視。 如需有關動態管理檢視之許可權的詳細資訊，請參閱 [&#40;transact-sql&#41;的動態管理檢視和函數 ](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)。  
  
## <a name="examples"></a>範例  
 下列範例會傳回最新工作階段的資訊。  
  
```  
USE AdventureWorks2012;  
GO  
SELECT session_id, start_time, end_time, duration, scan_phase,  
    error_count, start_lsn, current_lsn, end_lsn, tran_count,  
    last_commit_lsn, last_commit_time, log_record_count, schema_change_count,  
    command_count, first_begin_cdc_lsn, last_commit_cdc_lsn,   
    last_commit_cdc_time, latency, empty_scan_count, failed_sessions_count  
FROM sys.dm_cdc_log_scan_sessions  
WHERE session_id = (SELECT MAX(b.session_id) FROM sys.dm_cdc_log_scan_sessions AS b);  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.dm_cdc_errors &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/change-data-capture-sys-dm-cdc-errors.md)  
  
  

