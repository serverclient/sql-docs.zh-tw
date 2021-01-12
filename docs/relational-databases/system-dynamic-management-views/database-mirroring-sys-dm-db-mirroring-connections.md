---
description: 資料庫鏡像-sys.dm_db_mirroring_connections
title: sys.dm_db_mirroring_connections (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_db_mirroring_connections
- dm_db_mirroring_connections
- sys.dm_db_mirroring_connections_TSQL
- dm_db_mirroring_connections_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_mirroring_connections dynamic management view
ms.assetid: e4df91b6-0240-45d0-ae22-cb2c0d52e0b3
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: e0cd194ef04063bcd1500d4c3be59c352e905114
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101698"
---
# <a name="database-mirroring---sysdm_db_mirroring_connections"></a>資料庫鏡像-sys.dm_db_mirroring_connections
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對為資料庫鏡像建立的每個連接，各傳回一個資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**connection_id**|**uniqueidentifier**|連接的識別碼。|  
|**transport_stream_id**|**uniqueidentifier**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]此連線用來進行 tcp/ip 通訊的網路介面 (SNI) 連接的識別碼。|  
|**state**|**smallint**|連接的目前狀態。 可能的值：<br /><br /> 1 = NEW<br /><br /> 2 = CONNECTING<br /><br /> 3 = CONNECTED<br /><br /> 4 = LOGGED_IN<br /><br /> 5 = 已關閉|  
|**state_desc**|**nvarchar(60)**|連接的目前狀態。 可能的值：<br /><br /> NEW<br /><br /> CONNECTING<br /><br /> CONNECTED<br /><br /> LOGGED_IN<br /><br /> CLOSED|  
|**connect_time**|**datetime**|開啟連接的日期與時間。|  
|**login_time**|**datetime**|連接登入成功的日期和時間。|  
|**authentication_method**|**nvarchar(128)**|Windows 驗證方法的名稱，例如 NTLM 或 KERBEROS。 這個值是來自 Windows。|  
|**principal_name**|**nvarchar(128)**|針對連接權限而驗證的登入名稱。 如果是 Windows 驗證，這個值是遠端使用者名稱。 如果是憑證驗證，則這個值是憑證擁有者。|  
|**remote_user_name**|**nvarchar(128)**|Windows 驗證所用其他資料庫的對等使用者名稱。|  
|**last_activity_time**|**datetime**|前次使用該連接來傳送或接收資訊的日期和時間。|  
|**is_accept**|**bit**|指出連接是否在遠端引發。<br /><br /> 1 = 連接是從遠端執行個體所接受的要求。<br /><br /> 0 = 連接是由本機執行個體所啟動。|  
|**login_state**|**smallint**|這個連接的登入處理序狀態。 可能的值：<br /><br /> 0 = INITIAL<br /><br /> 1 = WAIT LOGIN NEGOTIATE<br /><br /> 2 = ONE ISC<br /><br /> 3 = ONE ASC<br /><br /> 4 = TWO ISC<br /><br /> 5 = TWO ASC<br /><br /> 6 = WAIT ISC Confirm<br /><br /> 7 = WAIT ASC Confirm<br /><br /> 8 = WAIT REJECT<br /><br /> 9 = WAIT PRE-MASTER SECRET<br /><br /> 10 = WAIT VALIDATION<br /><br /> 11 = WAIT ARBITRATION<br /><br /> 12 = 線上<br /><br /> 13 = ERROR|  
|**login_state_desc**|**nvarchar(60)**|遠端電腦登入的目前狀態。 可能的值：<br /><br /> 正在初始化連接交握。<br /><br /> 連接交握正在等候登入交涉訊息。<br /><br /> 連接交握已初始化並傳送用於驗證的安全性內容。<br /><br /> 連接交握已收到並接受用於驗證的安全性內容。<br /><br /> 連接交握已初始化並傳送用於驗證的安全性內容。 沒有可用來驗證對等的選擇性機制。<br /><br /> 連接交握已收到並傳送用於驗證的已接受安全性內容。 沒有可用來驗證對等的選擇性機制。<br /><br /> 連接交握正在等候初始化安全性內容確認訊息。<br /><br /> 連接交握正在等候接受安全性內容確認訊息。<br /><br /> 連接交握正在等待驗證失敗的 SSPI 拒絕訊息。<br /><br /> 連接交握正在等候預備主密碼訊息。<br /><br /> 連接交握正在等候驗證訊息。<br /><br /> 連接交握正在等候仲裁訊息。<br /><br /> 連接交握已完成並上線 (就緒)，可進行訊息交換。<br /><br /> 連線發生錯誤。|  
|**peer_certificate_id**|**int**|驗證遠端執行個體所用憑證的本機物件識別碼。 這個憑證的擁有者，必須對資料庫鏡像端點具備 CONNECT 權限。|  
|**encryption_algorithm**|**smallint**|這個連接所用的加密演算法。 NULLABLE。 可能的值：<br /><br /> **值：** 0<br /><br /> **描述：** 沒有<br /><br /> **DDL 選項：** 禁用<br /><br /> **值：** 1<br /><br /> **描述：** RC4<br /><br /> **DDL 選項：** {必要的 &#124; 必要的演算法 RC4}<br /><br /> **值：** 2<br /><br /> **描述：** Aes<br /><br /> **DDL 選項：** 必要的演算法 AES<br /><br /> **值：** 3<br /><br /> **描述：** 無，RC4<br /><br /> **DDL 選項：** {支援的 &#124; 支援的演算法 RC4}<br /><br /> **值：** 4<br /><br /> **描述：** 無、AES<br /><br /> **DDL 選項：** 支援的演算法 RC4<br /><br /> **值：** 5<br /><br /> **描述：** RC4、AES<br /><br /> **DDL 選項：** 必要的演算法 RC4 AES<br /><br /> **值：** 6<br /><br /> **描述：** AES、RC4<br /><br /> **DDL 選項：** 必要的演算法 AES RC4<br /><br /> **值：** 7<br /><br /> **描述：** NONE、RC4、AES<br /><br /> **DDL 選項：** 支援的演算法 RC4 AES<br /><br /> **值：** 8<br /><br /> **描述：** 無、AES、RC4<br /><br /> **DDL 選項：** 支援的演算法 AES RC4<br /><br /> **注意：** RC4 演算法僅支援回溯相容性。 只有在資料庫相容性層級為 90 或 100 時，才能使用 RC4 或 RC4_128 加密新資料 (不建議使用)。請改用較新的演算法，例如其中一個 AES 演算法。 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本中使用 RC4 或 RC4_128 加密的資料，可以在任何相容性層級進行解密。|  
|**encryption_algorithm_desc**|**nvarchar(60)**|加密演算法的文字表示法。 NULLABLE。 可能的值如下：<br /><br /> **描述：** 沒有<br /><br /> **DDL 選項：** 禁用<br /><br /> **描述：** RC4<br /><br /> **DDL 選項：** {必要的 &#124; 必要的演算法 RC4}<br /><br /> **描述：** Aes<br /><br /> **DDL 選項：** 必要的演算法 AES<br /><br /> **描述：** 無，RC4<br /><br /> **DDL 選項：** {支援的 &#124; 支援的演算法 RC4}<br /><br /> **描述：** 無、AES<br /><br /> **DDL 選項：** 支援的演算法 RC4<br /><br /> **描述：** RC4、AES<br /><br /> **DDL 選項：** 必要的演算法 RC4 AES<br /><br /> **描述：** AES、RC4<br /><br /> **DDL 選項：** 必要的演算法 AES RC4<br /><br /> **描述：** NONE、RC4、AES<br /><br /> **DDL 選項：** 支援的演算法 RC4 AES<br /><br /> **描述：** 無、AES、RC4<br /><br /> **DDL 選項：** 支援的演算法 AES RC4|  
|**receives_posted**|**smallint**|這個連接尚未完成的非同步網路接收數目。|  
|**is_receive_flow_controlled**|**bit**|是否已因流程控制的緣故 (因為網路忙碌) 而延後網路接收。<br /><br /> 1 = True|  
|**sends_posted**|**smallint**|這個連接尚未完成的非同步網路傳送數目。|  
|**is_send_flow_controlled**|**bit**|是否已因網路流程控制的緣故 (因為網路忙碌) 而延後網路傳送。<br /><br /> 1 = True|  
|**total_bytes_sent**|**bigint**|這個連接所傳送的總位元組數。|  
|**total_bytes_received**|**bigint**|這個連接所接收的總位元組數。|  
|**total_fragments_sent**|**bigint**|這個連接所傳送的資料庫鏡像訊息片段總數。|  
|**total_fragments_received**|**bigint**|這個連接所接收的資料庫鏡像訊息片段總數。|  
|**total_sends**|**bigint**|這個連接所發出的網路傳送要求總數。|  
|**total_receives**|**bigint**|這個連接所發出的網路接收要求總數。|  
|**peer_arbitration_id**|**uniqueidentifier**|端點的內部識別碼。 NULLABLE。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="physical-joins"></a>實體聯結  
 ![sys.join_dm_db_mirroring_connections 的聯結](../../relational-databases/system-dynamic-management-views/media/join-dm-db-mirroring-connections.gif "sys.join_dm_db_mirroring_connections 的聯結")  
  
## <a name="relationship-cardinalities"></a>關聯性基數  
  
|寄件者|收件者|關聯性|  
|----------|--------|------------------|  
|**dm_db_mirroring_connections.connection_id**|**dm_exec_connections.connection_id**|一對一|  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [監視資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/monitoring-database-mirroring-sql-server.md)  
  
  
