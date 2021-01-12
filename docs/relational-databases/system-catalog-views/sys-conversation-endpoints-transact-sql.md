---
description: sys.conversation_endpoints (Transact-SQL)
title: sys.conversation_endpoints (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- conversation_endpoints_TSQL
- conversation_endpoints
- sys.conversation_endpoints
- sys.conversation_endpoints_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.conversation_endpoints catalog view
ms.assetid: 2ed758bc-2a9d-4831-8da2-4b80e218f3ea
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: e62cd4fb5c39a8b24ca25f49e381a23a32186643
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100660"
---
# <a name="sysconversation_endpoints-transact-sql"></a>sys.conversation_endpoints (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  [!INCLUDE[ssSB](../../includes/sssb-md.md)] 交談的每一端都是以交談端點來代表。 這份目錄檢視會針對資料庫中的每個交談端點，各包含一資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|conversation_handle|**uniqueidentifier**|這個交談端點的識別碼。 不是 NULLABLE。|  
|conversation_id|**uniqueidentifier**|交談的識別碼。 交談參與者雙方共用這個識別碼。 這和 is_initiator 資料行在資料庫中都是唯一的。 不是 NULLABLE。|  
|is_initiator|**tinyint**|這個端點是交談的起始端或是目標。  不是 NULLABLE。<br /><br /> 1 = 起始端<br /><br /> 0 = 目標|  
|service_contract_id|**int**|這個交談的合約識別碼。 不是 NULLABLE。|  
|conversation_group_id|**uniqueidentifier**|這個交談所屬之交談群組的識別碼。 不是 NULLABLE。|  
|service_id|**int**|交談這一端的服務識別碼。 不是 NULLABLE。|  
|lifetime|**datetime**|這個交談的到期日期/時間。 不是 NULLABLE。|  
|state|**char(2)**|交談的目前狀態。 不是 NULLABLE。 值為下列其中之一：<br /><br /> 開始輸出。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 已處理此交談的 BEGIN CONVERSATION，但尚未傳送任何訊息。<br /><br /> SI：已開始傳出。 另一個執行個體已啟動與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的新交談，但 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 尚未完全收到第一則訊息。 如果第一個訊息被分割，或者 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 收到訊息的順序不正確，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 就可能會建立處於此狀態的交談。 然而，如果收到交談的第一次傳輸包含完整的第一則訊息，則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可能會建立 CO (交談) 狀態的交談。<br /><br /> 共同對話。 已建立交談，且交談兩端可以傳送訊息。 一般服務的大部分通訊都發生在這個狀態的交談中。<br /><br /> DI 已中斷連線輸入。 交談的遠端發出了 END CONVERSATION。 交談會保留在這個狀態中，直到交談的本機端發出 END CONVERSATION 為止。 應用程式可能仍會接收交談的訊息。 由於交談的遠端已經結束交談，所以應用程式無法在此交談中傳送訊息。 當應用程式發出 END CONVERSATION 時，交談會移到 CD (已關閉) 狀態。<br /><br /> DO：已中斷傳出。 交談的本機端發出了 END CONVERSATION。 交談會保留在這個狀態中，直到交談的遠端收到 END CONVERSATION 為止。 應用程式無法傳送或接收交談的訊息。 當交談的遠端認可 END CONVERSATION 時，交談會移到 CD (已關閉) 狀態。<br /><br /> ER：錯誤。 這個端點發生錯誤。 錯誤訊息會放在應用程式佇列中。 如果應用程式佇列是空的，這表示應用程式已耗用錯誤訊息。<br /><br /> CD：已關閉。 交談端點已不在使用中。|  
|state_desc|**nvarchar(60)**|端點交談狀態的描述。 此資料行為 NULLABLE。 值為下列其中之一：<br /><br /> **STARTED_OUTBOUND**<br /><br /> **STARTED_INBOUND**<br /><br /> **交談**<br /><br /> **DISCONNECTED_INBOUND**<br /><br /> **DISCONNECTED_OUTBOUND**<br /><br /> **關閉**<br /><br /> **ERROR**|  
|far_service|**nvarchar(256)**|交談遠端的服務名稱。 不是 NULLABLE。|  
|far_broker_instance|**nvarchar(128)**|交談遠端的 Broker 執行個體。 NULLABLE。|  
|principal_id|**int**|憑證用於對話本機端之主體的識別碼。 不是 NULLABLE。|  
|far_principal_id|**int**|憑證用於對話遠端之使用者的識別碼。 不是 NULLABLE。|  
|outbound_session_key_identifier|**uniqueidentifier**|這個對話之傳出加密金鑰的識別碼。 不是 NULLABLE。|  
|inbound_session_key_identifier|**uniqueidentifier**|這個對話之傳入加密金鑰的識別碼。 不是 NULLABLE。|  
|security_timestamp|**datetime**|本機工作階段金鑰的建立時間。 不是 NULLABLE。|  
|dialog_timer|**datetime**|這個對話之交談計時器傳送 DialogTimer 訊息的時間。 不是 NULLABLE。|  
|send_sequence|**bigint**|傳送順序中下一個訊息編號。 不是 NULLABLE。|  
|last_send_tran_id|**binary(6)**|上一次傳送訊息之交易的內部交易識別碼。 不是 NULLABLE。|  
|end_dialog_sequence|**bigint**|結束對話訊息的序號。 不是 NULLABLE。|  
|receive_sequence|**bigint**|訊息接收順序中預期的下一個訊息編號。 不是 NULLABLE。|  
|receive_sequence_frag|**int**|訊息接收順序中預期的下一個訊息片段編號。 不是 NULLABLE。|  
|system_sequence|**bigint**|這個對話最後一個系統訊息的序號。 不是 NULLABLE。|  
|first_out_of_order_sequence|**bigint**|這個對話在次序不對的訊息中第一則訊息的序號。 不是 NULLABLE。|  
|last_out_of_order_sequence|**bigint**|這個對話在次序不對的訊息中最後一則訊息的序號。 不是 NULLABLE。|  
|last_out_of_order_frag|**int**|這個對話在次序不對的片段中最後一則訊息的序號。 不是 NULLABLE。|  
|is_system|**bit**|如果這是系統對話，便是 1。 不是 NULLABLE。|  
|priority|**tinyint**|指派給這個交談端點的交談優先權。 不是 NULLABLE。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
  
