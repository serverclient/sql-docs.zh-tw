---
description: sys.dm_exec_cursors (Transact-SQL)
title: sys.dm_exec_cursors (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_exec_cursors_TSQL
- dm_exec_cursors
- dm_exec_cursors_TSQL
- sys.dm_exec_cursors
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_cursors dynamic management function
ms.assetid: f520b63c-36af-40f1-bf71-6901d6331d3d
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 8ab1ed9e722f2363c0568932e7eb337bc09dbe41
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098996"
---
# <a name="sysdm_exec_cursors-transact-sql"></a>sys.dm_exec_cursors (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回有關在不同資料庫開啟之資料指標的資訊。  
  
## <a name="syntax"></a>語法  
  
```  
  
dm_exec_cursors (session_id | 0 )  
```  
  
## <a name="arguments"></a>引數  
 *session_id* |0  
 工作階段的識別碼。 如果指定 *session_id* ，此函式會傳回指定會話中資料指標的相關資訊。  
  
 如果指定 0，這個函數會傳回有關所有工作階段的所有資料指標的資訊。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**session_id**|**int**|保留這個資料指標的工作階段識別碼。|  
|**cursor_id**|**int**|資料指標物件的識別碼。|  
|**name**|**nvarchar(256)**|由使用者自訂的資料指標名稱。|  
|**properties**|**nvarchar(256)**|指定資料指標的屬性。 下列屬性的值會串連來形成這個資料行的值：<br />宣告介面<br />資料指標類型 <br />資料指標並行<br />資料指標範圍<br />資料指標巢狀層級<br /><br /> 例如，在此資料行中傳回的值可能是 "TSQL &#124; Dynamic &#124; 開放式 &#124; Global (0) "。|  
|**sql_handle**|**varbinary(64)**|宣告資料指標的批次文字控制代碼。|  
|**statement_start_offset**|**int**|目前執行的批次或預存程序中的字元數，目前執行的陳述式即從該處開始。 可以與 **sql_handle**、 **statement_end_offset** 和 [sys.dm_exec_sql_text](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md) 動態管理函數一起使用，以取得要求的目前執行中語句。|  
|**statement_end_offset**|**int**|目前執行的批次或預存程序中的字元數，目前執行的陳述式即在該處結束。 可以與 **sql_handle**、 **statement_start_offset** 和 **sys.dm_exec_sql_text** 動態管理函數一起使用，以取得要求的目前執行中語句。|  
|**plan_generation_num**|**bigint**|可用來在重新編譯之後區分計畫執行個體的序號。|  
|**creation_time**|**datetime**|建立這個資料指標的時間戳記。|  
|**is_open**|**bit**|指定資料指標是否開啟。|  
|**is_async_population**|**bit**|指定背景執行緒是否仍非同步擴展 KEYSET 或 STATIC 資料指標。|  
|**is_close_on_commit**|**bit**|指定資料指標是否使用 CURSOR_CLOSE_ON_COMMIT 宣告。<br /><br /> 1 = 當交易結束時資料指標會關閉。|  
|**fetch_status**|**int**|傳回資料指標的上次提取狀態。 這是最後傳回的 @ @FETCH_STATUS 值。|  
|**fetch_buffer_size**|**int**|傳回有關提取緩衝區大小的資訊。<br /><br /> 1 = Transact-SQL 資料指標。 這可設為 API 資料指標的較高值。|  
|**fetch_buffer_start**|**int**|如果是 FAST_FORWARD 和 DYNAMIC 資料指標，如果該資料指標未開啟，或是位於第一個資料列前面，它會傳回 0。 否則，它會傳回 -1。<br /><br /> 如果是 STATIC 和 KEYSET 資料指標，如果該資料指標未開啟，它會傳回 0，如果資料指標位於最後一個資料列後面，它會傳回 -1。<br /><br /> 否則，它會傳回其所在位置的資料列號碼。|  
|**ansi_position**|**int**|提取緩衝區內的資料指標位置。|  
|**worker_time**|**bigint**|執行這個資料指標之工作者所花的時間 (以百萬分之一秒為單位)。|  
|**讀**|**bigint**|資料指標執行的讀取次數。|  
|**寫**|**bigint**|資料指標執行的寫入次數。|  
|**dormant_duration**|**bigint**|自從這個資料指標上的最後一個查詢 (開啟或提取) 啟動以來的毫秒數。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="remarks"></a>備註  
 下表提供有關資料指標宣告介面的資訊，並包括屬性資料行的可能值。  
  
|屬性|描述|  
|--------------|-----------------|  
|API|資料指標是使用其中一個資料存取 API (ODBC, OLEDB) 宣告。|  
|TSQL|資料指標是使用 Transact-SQL DECLARE CURSOR 語法宣告。|  
  
 下表提供有關資料指標類型的資訊，並包括屬性資料行的可能值。  
  
|類型|描述|  
|----------|-----------------|  
|索引鍵集|資料指標宣告為索引鍵集。|  
|動態|資料指標宣告為動態。|  
|快照式|資料指標宣告為快照集或靜態。|  
|Fast_Forward|資料指標宣告為向前快轉。|  
  
 下表提供有關資料指標並行的資訊，並包括屬性資料行的可能值。  
  
|並行|描述|  
|-----------------|-----------------|  
|唯讀|資料指標宣告為唯讀。|  
|捲動鎖定|資料指標使用捲動鎖定。|  
|開放式|資料指標使用開放式並行存取控制項。|  
  
 下表提供有關資料指標範圍的資訊，並包括屬性資料行的可能值。  
  
|影響範圍|描述|  
|-----------|-----------------|  
|本機|指定已建立資料指標的批次、預存程序或觸發程序，其資料指標的範圍為本機範圍。|  
|全域|指定連接的資料指標範圍為全域。|  
  
## <a name="examples"></a>範例  
  
### <a name="a-detecting-old-cursors"></a>A. 偵測舊資料指標  
 這個範例會針對伺服器上開啟超過 36 小時指定時間的資料指標，傳回相關的資訊。  
  
```  
SELECT creation_time, cursor_id, name, c.session_id, login_name   
FROM sys.dm_exec_cursors(0) AS c   
JOIN sys.dm_exec_sessions AS s ON c.session_id = s.session_id   
WHERE DATEDIFF(hh, c.creation_time, GETDATE()) > 36;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [執行相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)   
 [sys.dm_exec_sessions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)  
  
  

