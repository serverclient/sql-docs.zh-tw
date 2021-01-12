---
description: ALTER QUEUE (Transact-SQL)
title: ALTER QUEUE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/01/2016
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_QUEUE_TSQL
- ALTER QUEUE
dev_langs:
- TSQL
helpviewer_keywords:
- number of queue readers
- modifying queues
- ALTER QUEUE statement
- queue readers [SQL Server]
- queues [Service Broker], modifying
- unavailable queues [SQL Server]
- activation stored procedures [Service Broker]
ms.assetid: d54aa325-8761-4cd4-8da7-acf33df12296
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 385a84a0f92c41cbf661abe327d244a2e9b6ebd3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092073"
---
# <a name="alter-queue-transact-sql"></a>ALTER QUEUE (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  變更佇列屬性。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER QUEUE <object>   
   queue_settings  
   | queue_action  
[ ; ]  
  
<object> : :=  
{ database_name.schema_name.queue_name | schema_name.queue_name | queue_name }
  
<queue_settings> : :=  
WITH  
   [ STATUS = { ON | OFF } [ , ] ]  
   [ RETENTION = { ON | OFF } [ , ] ]  
   [ ACTIVATION (  
       { [ STATUS = { ON | OFF } [ , ] ]   
         [ PROCEDURE_NAME = <procedure> [ , ] ]  
         [ MAX_QUEUE_READERS = max_readers [ , ] ]  
         [ EXECUTE AS { SELF | 'user_name'  | OWNER } ]  
       |  DROP }  
          ) [ , ]]  
         [ POISON_MESSAGE_HANDLING (  
          STATUS = { ON | OFF } )  
         ]   
  
<queue_action> : :=  
   REBUILD [ WITH <query_rebuild_options> ]  
   | REORGANIZE [ WITH (LOB_COMPACTION = { ON | OFF } ) ]  
   | MOVE TO { file_group | "default" }  
  
<procedure> : :=  
{ database_name.schema_name.stored_procedure_name | schema_name.stored_procedure_name | stored_procedure_name }
  
<queue_rebuild_options> : :=  
{  
   ( MAXDOP = max_degree_of_parallelism )  
}  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *database_name* (object)  
 這是要變更之佇列所在的資料庫名稱。 若未提供 *database_name*，預設為目前的資料庫。  
  
 *schema_name* (object)  
 這是新佇列所屬的結構描述名稱。 若未提供 *schema_name*，預設為目前使用者的預設結構描述。  
  
 *queue_name*  
 這是要變更的佇列名稱。  
  
 STATUS (Queue)  
 指定佇列是可以使用 (ON) 或無法使用 (OFF)。 當佇列無法使用時，不能將訊息加入佇列或從佇列中移除。  
  
 RETENTION  
 指定佇列的保留設定。 如果 RETENTION = ON，使用這個佇列的交談所傳送或接收的所有訊息都會保留在佇列中，直到交談結束為止。 這個選項可讓您保留訊息來進行稽核，或在錯誤發生時用來執行補償交易。  
  
> [!NOTE]  
>  設定 RETENTION = ON 會降低效能。 只有在必須符合應用程式的服務等級合約時，才應該使用這項設定。  
  
 ACTIVATION  
 指定為了處理到達這個佇列的訊息而啟動之預存程序的相關資訊。  
  
 STATUS (Activation)  
 指定佇列是否啟用預存程序。 當 STATUS = ON 時，如果目前執行的程序數目低於 MAX_QUEUE_READERS，且訊息到達佇列的速度比預存程序接收訊息快，佇列便會啟動 PROCEDURE_NAME 所指定的預存程序。 當 STATUS = OFF 時，佇列不會啟用預存程序。  
  
 REBUILD [ WITH \<queue_rebuild_options> ]  
 **適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本。  
  
 重建佇列內部資料表的所有索引。 當您遇到因為高負載而產生的片段問題時，請使用此功能。 MAXDOP 是唯一支援的佇列重建選項。 REBUILD 一律是離線作業。  
  
 REORGANIZE [ WITH ( LOB_COMPACTION = { ON | OFF } ) ]  
 **適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本。  
  
 重新組織佇列內部資料表上的所有索引。   
不同於使用者資料表上的 REORGANIZE，佇列上的 REORGANIZE 一律會以離線作業執行，因為佇列上會明確停用頁面層級鎖定。  
  
> [!TIP]  
>  如需有關索引片段的一般指引，當片段介於 5% 和 30% 之間時重新組織索引。 當片段高於 30% 重建索引。 不過，這些數字僅是作為您環境起點的一般指引。 若要判斷索引片段的數量，請使用 [sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md) - 如需範例，請參閱該文章中的範例 G。  
  
 MOVE TO { *file_group* | "default" }  
 **適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本。  
  
 將佇列內部資料表 (包含其索引) 移動至使用者指定的檔案群組。  新的檔案群組不得為唯讀。  
  
 PROCEDURE_NAME = \<procedure>  
 指定在佇列包含要處理的訊息時，將啟動的預存程序名稱。 這個值必須是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼。  
  
 *database_name* (procedure)  
 這是包含預存程序的資料庫名稱。  
  
 *schema_name* (procedure)  
 這是擁有預存程序的結構描述名稱。  
  
 *stored_procedure_name*  
 這是預存程序的名稱。  
  
 MAX_QUEUE_READERS =*max_reader*  
 指定佇列同時啟動的啟用預存程序的最大執行個體數目。 *max_readers* 的值必須是在 0 和 32767 之間的數字。  
  
 EXECUTE AS  
 指定啟用預存程序執行所使用的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫使用者帳戶。 當佇列啟用預存程序時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 必須能夠檢查這個使用者的權限。 如果是 Windows 網域使用者，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 必須連接這個網域，且在程序啟用或啟用失敗時，必須能夠驗證指定使用者的權限。 如果是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用者，伺服器一律可以檢查權限。  
  
 SELF  
 指定以目前使用者的身分來執行預存程序。 (執行此 ALTER QUEUE 陳述式的資料庫主體)。  
  
 '*user_name*'  
 這是指定用來執行預存程序的使用者名稱。 *user_name* 必須是指定為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼的有效 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用者。 目前的使用者必須擁有指定之 *user_name* 的 IMPERSONATE 權限。  
  
 OWNER  
 指定以佇列擁有者的身分來執行預存程序。  
  
 DROP  
 刪除與佇列相關聯的所有啟用資訊。  
  
 POISON_MESSAGE_HANDLING  
 指定是否啟用有害訊息處理。 預設值是 ON。  
  
 有害訊息處理設定為 OFF 的佇列將不會在五個連續的交易復原後停用。 這可讓應用程式定義自訂有害訊息處理系統。  
  
## <a name="remarks"></a>備註  
 當含有指定之啟用預存程序的佇列包含訊息時，將啟用狀態 OFF 改成 ON 會立即啟動這個啟用預存程序。 將啟用狀態 ON 改成 OFF 會使 Broker 停止啟用預存程序的執行個體，但並不會停止目前在執行中的預存程序之執行個體。  
  
 變更佇列來加入啟用預存程序，並不會變更佇列的啟用狀態。 變更佇列的啟用預存程序，並不會影響目前在執行中的啟用預存程序的執行個體。  
  
 在啟用過程中，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會檢查佇列的最大佇列讀取器數目。 因此，變更佇列來增加最大佇列讀取器數目，可讓 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 立即啟用更多啟用預存程序的執行個體。 變更佇列來減少最大佇列讀取器數目，並不會影響目前在執行中啟用預存程序的執行個體。 不過，在啟用預存程序的執行個體數目低於設定的最大數目之前，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 並不會啟動新的預存程序執行個體。  
  
 當佇列無法使用時，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會保存使用資料庫傳輸佇列中的佇列之服務的訊息。 [sys.transmission_queue](../../relational-databases/system-catalog-views/sys-transmission-queue-transact-sql.md) 目錄檢視會提供傳輸佇列的檢視。  
  
 如果 RECEIVE 陳述式或 GET CONVERSATION GROUP 陳述式指定無法使用的佇列，陳述式會因 [!INCLUDE[tsql](../../includes/tsql-md.md)] 錯誤而失敗。  
  
## <a name="permissions"></a>權限  
 變更佇列的權限預設為佇列的擁有者、db_ddladmin 或 db_owner 固定資料庫角色的成員，以及系統管理員 (sysadmin) 固定伺服器角色的成員。  
  
## <a name="examples"></a>範例  
  
### <a name="a-making-a-queue-unavailable"></a>A. 使佇列無法使用  
 下列範例使 `ExpenseQueue` 佇列無法接收訊息。  
  
```sql  
ALTER QUEUE ExpenseQueue WITH STATUS = OFF ;  
```  
  
### <a name="b-changing-the-activation-stored-procedure"></a>B. 變更啟用預存程序  
 下列範例會變更佇列啟動的預存程序。 這個預存程序是以執行 `ALTER QUEUE` 陳述式的使用者身分來執行。  
  
```sql  
ALTER QUEUE ExpenseQueue  
    WITH ACTIVATION (  
        PROCEDURE_NAME = new_stored_proc,  
        EXECUTE AS SELF) ;  
```  
  
### <a name="c-changing-the-number-of-queue-readers"></a>C. 變更佇列讀取器的數目  
 下列範例會將 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 為了這個佇列而啟動的最大預存程序執行個體數目設為 `7`。  
  
```sql  
ALTER QUEUE ExpenseQueue WITH ACTIVATION (MAX_QUEUE_READERS = 7) ;  
```  
  
### <a name="d-changing-the-activation-stored-procedure-and-the-execute-as-account"></a>D. 變更啟用預存程序和 EXECUTE AS 帳戶  
 下列範例會變更 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 啟動的預存程序。 這個預存程序以 `SecurityAccount` 使用者的身分來執行。  
  
```sql  
ALTER QUEUE ExpenseQueue  
    WITH ACTIVATION (  
        PROCEDURE_NAME = AdventureWorks2012.dbo.new_stored_proc ,  
        EXECUTE AS 'SecurityAccount') ;  
```  
  
### <a name="e-setting-the-queue-to-retain-messages"></a>E. 設定佇列來保留訊息  
 下列範例會設定佇列來保留訊息。 佇列會保留使用這個佇列的服務所傳送和接收的所有訊息，直到包含訊息的交談結束為止。  
  
```sql  
ALTER QUEUE ExpenseQueue WITH RETENTION = ON ;  
```  
  
### <a name="f-removing-activation-from-a-queue"></a>F. 從佇列中移除啟用  
 下列範例會從佇列中移除所有啟用資訊。  
  
```  
ALTER QUEUE ExpenseQueue WITH ACTIVATION (DROP) ;  
```  
  
### <a name="g-rebuilding-queue-indexes"></a>G. 重建佇列索引  
  
**適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本。  
  
 下列範例會重建佇列索引  
  
```sql  
ALTER QUEUE ExpenseQueue REBUILD WITH (MAXDOP = 2)   
```  
  
### <a name="h-reorganizing-queue-indexes"></a>H. 重新組織佇列索引  
  
**適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本。  
  
 下列範例會重新組織佇列索引  
  
```sql  
ALTER QUEUE ExpenseQueue REORGANIZE   
```  
  
### <a name="i-moving-queue-internal-table-to-another-filegroup"></a>I.將佇列內部資料表移至另一個檔案群組  
  
**適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 及更新版本。  
  
```sql  
ALTER QUEUE ExpenseQueue MOVE TO [NewFilegroup]   
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE QUEUE &#40;Transact-SQL&#41;](../../t-sql/statements/create-queue-transact-sql.md)   
 [DROP QUEUE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-queue-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)  
  
  
