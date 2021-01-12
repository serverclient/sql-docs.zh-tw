---
description: sys.sysprocesses (Transact-SQL)
title: " (Transact-sql) 的 sys.sys進程 |Microsoft Docs"
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysprocesses_TSQL
- sys.sysprocesses_TSQL
- sys.sysprocesses
- sysprocesses
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sysprocesses compatibility view
- sysprocesses system table
ms.assetid: 60a36d36-54b3-4bd6-9cac-702205a21b16
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: d8272d6759508697512506bcab8df81d65b90b83
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095379"
---
# <a name="syssysprocesses-transact-sql"></a>sys.sysprocesses (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體上執行之處理序的相關資訊。 這些處理序可以是用戶端處理序或系統處理序。 若要存取 sysprocesses，您必須在 master 資料庫內容中，或者，您必須使用 master.dbo.sysprocesses 三部分名稱。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|spid|**smallint**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 工作階段識別碼。|  
|kpid|**smallint**|Windows 執行緒識別碼。|  
|blocked|**smallint**|封鎖要求之工作階段的識別碼。 如果這個資料行是 NULL，表示要求沒有被封鎖，或者封鎖工作階段的工作階段資訊無法使用 (或無法識別)。<br /><br /> -2 = 封鎖資源是由被遺棄的分散式交易所擁有。<br /><br /> -3 = 封鎖資源是由延遲的復原交易所擁有。<br /><br /> -4 = 由於內部閂鎖狀態轉換，而無法判斷封鎖閂鎖擁有者的工作階段識別碼。|  
|waittype|**binary(2)**|保留的。|  
|waittime|**bigint**|目前的等候時間 (以毫秒為單位)。<br /><br /> 0 = 處理序未在等待中。|  
|lastwaittype|**nchar(32)**|一個指出上次或目前等待類型之名稱的字串。|  
|waitresource|**nchar(256)**|鎖定資源的文字表示法。|  
|dbid|**smallint**|處理序目前所使用的資料庫識別碼。|  
|uid|**smallint**|執行命令的使用者識別碼。 如果使用者和角色數目超過 32,767 個，則會造成溢位或傳回 NULL。|  
|cpu|**int**|處理序的累計 CPU 時間。 不論 SET STATISTICS TIME 選項設為 ON 或 OFF，所有處理序的這個項目都會更新。|  
|physical_io|**bigint**|處理序的累計磁碟讀取和寫入。|  
|memusage|**int**|在程序快取中目前配置給此處理序的頁數。 負數表示處理序正在釋放其他處理序配置的記憶體。|  
|login_time|**datetime**|用戶端處理序登入伺服器的時間。|  
|last_batch|**datetime**|上次用戶端處理序執行遠端預存程序呼叫或 EXECUTE 陳述式的時間。|  
|ecid|**smallint**|用來唯一識別代表單一處理序操作之子執行緒的執行內容識別碼。|  
|open_tran|**smallint**|處理序的開啟交易數目。|  
|status|**nchar(30)**|處理序識別碼狀態。 可能的值包括：<br /><br />   =  休眠 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]正在重設會話。<br /><br /> **正在** 執行 = 會話正在執行一或多個批次。 啟用 Multiple Active Result Set (MARS) 之後，工作階段就可以執行多個批次。 如需詳細資訊，請參閱[使用 Multiple Active Result Sets &#40;MARS&#41;](../../relational-databases/native-client/features/using-multiple-active-result-sets-mars.md)。<br /><br /> **背景** = 會話正在執行背景工作，例如鎖死偵測。<br /><br /> **rollback** = 會話正在處理交易回復。<br /><br /> **暫** 止 = 會話正在等候工作者執行緒變成可用。<br /><br /> 可 **執行 = 會話** 中的工作位於排程器的可執行佇列中，並在等候取得時間配量時。<br /><br /> **spinloop** = 會話中的工作正在等待 spinlock 變成可用。<br /><br /> 已 **暫停**= 會話正在等候事件（例如 i/o）完成。|  
|sid|**binary(86)**|使用者的全域唯一識別碼 (GUID)。|  
|hostname|**nchar(128)**|工作站的名稱。|  
|program_name|**nchar(128)**|應用程式的名稱。|  
|hostprocess|**nchar(10)**|工作站處理序識別碼。|  
|cmd|**Nchar (52)**|目前正在執行的命令。|  
|nt_domain|**nchar(128)**|用戶端的 Windows 網域 (如果使用 Windows 驗證的話) 或信任連接。|  
|nt_username|**nchar(128)**|處理序的 Windows 使用者名稱 (如果使用 Windows 驗證的話) 或信任連接。|  
|net_address|**nchar(12)**|在每一個使用者的工作站上指派的網路配接器唯一識別碼。 當使用者登入時，這個識別碼會插入 net_address 資料行中。|  
|net_library|**nchar(12)**|用戶端網路程式庫的儲存資料行。 當網路連接時，每個用戶端處理序都會送入。 網路連接有與其相關聯的網路程式庫，這可使它們進行連接。|  
|loginame|**nchar(128)**|登入名稱。|  
|context_info|**binary(128)**|利用 SET CONTEXT_INFO 陳述式儲存在批次中的資料。|  
|sql_handle|**binary(20)**|代表目前正在執行的批次或物件。<br /><br /> **注意** 此值衍生自物件的批次或記憶體位址。 而不是使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 雜湊式演算法導出。|  
|stmt_start|**int**|開始指定的 sql_handle 之目前 SQL 陳述式的位移。|  
|stmt_end|**int**|結束指定之 sql_handle 的目前 SQL 陳述式的位移。<br /><br /> -1 = 目前陳述式執行至指定 sql_handle 之 fn_get_sql 函數傳回的結果尾端。|  
|request_id|**int**|要求識別碼。 用來識別在特定工作階段中執行的要求。|
|page_resource |**二元 (8)** |**適用於**：[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] <br /><br /> 如果資料行包含頁面，則為頁面資源的8位元組十六進位標記法 `waitresource` 。 |  
  
## <a name="remarks"></a>備註  
 如果使用者具有伺服器的 VIEW SERVER STATE 權限，使用者會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體上看到所有執行中的工作階段；否則，使用者只會看到目前的工作階段。  
  
## <a name="see-also"></a>另請參閱  
 [執行相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)   
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
