---
description: sys.dm_exec_connections (Transact-SQL)
title: sys.dm_exec_connections (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_exec_connections_TSQL
- sys.dm_exec_connections_TSQL
- sys.dm_exec_connections
- dm_exec_connections
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_connections dynamic management view
ms.assetid: 6bd46fe1-417d-452d-a9e6-5375ee8690d8
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 89d36c36c0d1e32bd8da68db6847dde61dc6a7a7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101616"
---
# <a name="sysdm_exec_connections-transact-sql"></a>sys.dm_exec_connections (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回有關與這個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體建立之連接及每一個連接之詳細資料的資訊。 傳回 SQL Server 的全伺服器連接資訊。 傳回 SQL Database 的目前資料庫連接資訊。  
  
> [!NOTE]
> 若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 [sys.dm_pdw_exec_connections &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-connections-transact-sql.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|session_id|**int**|識別這項連接的相關工作階段。 可為 Null。|  
|most_recent_session_id|**int**|代表這項連接最近的相關要求的工作階段識別碼  (SOAP 連接可以由另一個會話重複使用。 ) 可為 null。|  
|connect_time|**datetime**|建立連接的時間戳記。 不可為 Null。|  
|net_transport|**nvarchar(40)**|當連接具有多個使用中結果集 (MARS) 啟用時，一律會傳回 **會話** 。<br /><br /> **注意：** 描述此連接所使用的實體傳輸通訊協定。 不可為 Null。|  
|protocol_type|**nvarchar(40)**|指定裝載的通訊協定類型。 它目前會區分 TDS (TSQL) 和 SOAP。 可為 Null。|  
|protocol_version|**int**|這項連接的相關資料存取通訊協定的版本。 可為 Null。|  
|endpoint_id|**int**|描述其為何種連接類型的識別碼。 這個 endpoint_id 可用來查詢 sys.endpoints 檢視。 可為 Null。|  
|encrypt_option|**nvarchar(40)**|描述這項連接是否啟用加密的布林值。 不可為 Null。|  
|auth_scheme|**nvarchar(40)**|指定搭配這個連接使用的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]/Windows 驗證配置。 不可為 Null。|  
|node_affinity|**smallint**|識別此連接具有相似性的記憶體節點。 不可為 Null。|  
|num_reads|**int**|此連接所發生的位元組讀取數。 可為 Null。|  
|num_writes|**int**|此連接所發生的位元組寫入數目。 可為 Null。|  
|last_read|**datetime**|這項連接期間最後一次讀取的時間戳記。 可為 Null。|  
|last_write|**datetime**|這項連接期間最後一次寫入的時間戳記。 不可設為 Null。|  
|net_packet_size|**int**|用來傳送資訊和資料的網路封包大小。 可為 Null。|  
|client_net_address|**varchar(48)**|連接到這部伺服器之用戶端的主機位址。 可為 Null。<br /><br /> 在 V12 之前的 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，此資料行一律會傳回 NULL。|  
|client_tcp_port|**int**|與這項連接相關聯的用戶端電腦上的通訊埠編號。 可為 Null。<br /><br /> 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，這個資料行一律會傳回 NULL。|  
|local_net_address|**varchar(48)**|代表這項連接的目標伺服器的 IP 位址。 只適用於使用 TCP 傳輸提供者的連接。 可為 Null。<br /><br /> 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，這個資料行一律會傳回 NULL。|  
|local_tcp_port|**int**|當這項連接是使用 TCP 傳輸的連接時，代表這項連接的目標伺服器 TCP 埠。 可為 Null。<br /><br /> 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]，這個資料行一律會傳回 NULL。|  
|connection_id|**uniqueidentifier**|這用來唯一識別各項連接。 不可為 Null。|  
|parent_connection_id|**uniqueidentifier**|這用來識別 MARS 工作階段在使用的主要連接。 可為 Null。|  
|most_recent_sql_handle|**varbinary(64)**|這項連接所執行之前一項要求的 SQL 控制代碼。 most_recent_sql_handle 資料行一律與 most_recent_session_id 資料行同步。 可為 Null。|  
|pdw_node_id|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   

## <a name="physical-joins"></a>實體聯結  
 ![sys.dm_exec_connections 的聯結](../../relational-databases/system-dynamic-management-views/media/join-dm-exec-connections-1.gif "sys.dm_exec_connections 的聯結")  
  
## <a name="relationship-cardinalities"></a>關聯性基數  
  
| 第一個元素 | 第二個元素 | 關聯性 |
| --------------| -------------- | ------------ |  
|dm_exec_sessions.session_id|dm_exec_connections.session_id|一對一|  
|dm_exec_requests.connection_id|dm_exec_connections.connection_id|多對一|  
|dm_broker_connections.connection_id|dm_exec_connections.connection_id|一對一|  
  
## <a name="examples"></a>範例  
 收集查詢自有連接相關資訊的典型查詢。  
  
```sql  
SELECT   
    c.session_id, c.net_transport, c.encrypt_option,   
    c.auth_scheme, s.host_name, s.program_name,   
    s.client_interface_name, s.login_name, s.nt_domain,   
    s.nt_user_name, s.original_login_name, c.connect_time,   
    s.login_time   
FROM sys.dm_exec_connections AS c  
JOIN sys.dm_exec_sessions AS s  
    ON c.session_id = s.session_id  
WHERE c.session_id = @@SPID;  
```  
  
## <a name="see-also"></a>另請參閱  

 [執行相關的動態管理檢視和函式 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)  
  
  


