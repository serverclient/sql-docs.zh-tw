---
description: sys.dm_filestream_file_io_requests (Transact-SQL)
title: sys.dm_filestream_file_io_requests (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_filestream_file_io_requests
- dm_filestream_file_io_requests
- sys.dm_filestream_file_io_requests_TSQL
- dm_filestream_file_io_requests_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_filestream_file_io_requests catalog view
ms.assetid: d41e39a5-14d5-4f3d-a2e3-a822b454c1ed
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 69b34fd2a8ec5adf393994399011cf1b2df6ea03
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097651"
---
# <a name="sysdm_filestream_file_io_requests-transact-sql"></a>sys.dm_filestream_file_io_requests (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  顯示命名空間擁有者 (NSO) 在給定時間處理之 I/O 要求的清單。  
  
|Column|類型|描述|  
|------------|----------|-----------------|  
|**request_context_address**|**varbinary(8)**|顯示 NSO 記憶體區塊的內部位址，該記憶體區塊包含來自驅動程式的 I/O 要求。 不可為 Null。|  
|**current_spid**|**smallint**|顯示目前 SQL Server 連接 (SPID) 的系統處理序識別碼。 不可為 Null。|  
|**request_type**|**nvarchar(60)**|顯示 I/O 要求封包 (IRP) 類型。 可能的要求類型是 REQ_PRE_CREATE、REQ_POST_CREATE、REQ_RESOLVE_VOLUME、REQ_GET_VOLUME_INFO、REQ_GET_LOGICAL_NAME、REQ_GET_PHYSICAL_NAME、REQ_PRE_CLEANUP、REQ_POST_CLEANUP、REQ_CLOSE、REQ_FSCTL、REQ_QUERY_INFO、REQ_SET_INFO、REQ_ENUM_DIRECTORY、REQ_QUERY_SECURITY 和 REQ_SET_SECURITY。 不可為 Null|  
|**request_state**|**nvarchar(60)**|顯示 I/O 要求在 NSO 中的狀態。 可能的值為 REQ_STATE_RECEIVED、REQ_STATE_INITIALIZED、REQ_STATE_ENQUEUED、REQ_STATE_PROCESSING、REQ_STATE_FORMATTING_RESPONSE、REQ_STATE_SENDING_RESPONSE、REQ_STATE_COMPLETING 和 REQ_STATE_COMPLETED。 不可為 Null。|  
|**request_id**|**int**|顯示驅動程式指派給此要求的唯一要求識別碼。 不可為 Null。|  
|**irp_id**|**int**|顯示唯一 IRP 識別碼。 這在識別與給定 IRP 相關的所有 I/O 要求時相當實用。 不可為 Null。|  
|**handle_id**|**int**|表示命名空間控制代碼識別碼。 這是 NSO 專用的識別碼，在執行個體中是唯一的。 不可為 Null。|  
|**client_thread_id**|**varbinary(8)**|顯示源自要求的用戶端應用程式執行緒識別碼。<br /><br /> 警告只有在用戶端應用程式與 SQL Server 在同一部電腦上執行時，才有意義。 **\* \* \* \*** 當用戶端應用程式在遠端執行時， **client_thread_id** 會顯示代表遠端用戶端運作之某個系統進程的執行緒識別碼。<br /><br /> 可為 Null。|  
|**client_process_id**|**varbinary(8)**|如果用戶端應用程式在與 SQL Server 相同的電腦上執行，顯示用戶端應用程式的處理序識別碼。 若是遠端用戶端，這會顯示代表用戶端應用程式運作的系統處理序識別碼。 可為 Null。|  
|**handle_context_address**|**varbinary(8)**|顯示與用戶端控制碼相關聯之內部 NSO 結構的位址。 可為 Null。|  
|**filestream_transaction_id**|**varbinary(128)**|顯示與給定控制代碼相關聯之交易的識別碼，以及與此控制代碼相關聯的所有要求。 這是 **get_filestream_transaction_coNtext** 函數所傳回的值。 可為 Null。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [Filestream 和 FileTable 動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/filestream-and-filetable-dynamic-management-views-transact-sql.md)  
  
  
