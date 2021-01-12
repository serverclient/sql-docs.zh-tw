---
description: WAITFOR (Transact-SQL)
title: WAITFOR (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- WAITFOR
- WAITFOR_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- TIME option
- delaying executions [SQL Server]
- batches [SQL Server], execution times
- stored procedures [SQL Server], executing
- DELAY
- time [SQL Server], execution delays
- blocking executions
- transactions [SQL Server], execution times
- WAITFOR statement
- timing executions
ms.assetid: 8e896e73-af27-4cae-a725-7a156733f3bd
author: cawrites
ms.author: chadam
ms.openlocfilehash: 0933c90f711f5c86425c7e2c6b4b350c47612ddc
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095868"
---
# <a name="waitfor-transact-sql"></a>WAITFOR (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  封鎖批次、預存程序或交易的執行，直到經過指定的時間或時間間隔，或直到指定的陳述式修改或傳回至少一個資料列為止。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
WAITFOR   
{  
    DELAY 'time_to_pass'   
  | TIME 'time_to_execute'   
  | [ ( receive_statement ) | ( get_conversation_group_statement ) ]   
    [ , TIMEOUT timeout ]  
}  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 DELAY  
 這是在繼續執行批次、預存程序或交易之前，必須經過的指定時段，最多 24 小時。  
  
 '*time_to_pass*'  
 這是要等待的時間週期。 您可以使用 **datetime** 資料格式來指定 *time_to_pass*，或將它指定成區域變數。 不能指定日期，因此不接受 **datetime** 值的日期部分。 *time_to_pass* 會格式化為 hh:mm[[:ss].mss]。
  
 TIME  
 此時執行批次、預存程序或交易時的指定時間。  
  
 '*time_to_execute*'  
 這是 WAITFOR 陳述式完成的時間。 您可以使用 **datetime** 資料格式來指定 *time_to_execute*，也可以將它指定成區域變數。 不能指定日期，因此不接受 **datetime** 值的日期部分。 *time_to_execute* 會格式化為 hh:mm[[:ss].mss]，且可以選擇性地包含日期 1900-01-01。
  
 *receive_statement*  
 這是有效的 RECEIVE 陳述式。  
  
> [!IMPORTANT]  
>  含 *receive_statement* 的 WAITFOR 只適用於 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 訊息。 如需詳細資訊，請參閱 [RECEIVE &#40;Transact-SQL&#41;](../../t-sql/statements/receive-transact-sql.md)。  
  
 *get_conversation_group_statement*  
 這是有效的 GET CONVERSATION GROUP 陳述式。  
  
> [!IMPORTANT]  
>  含 *get_conversation_group_statement* 的 WAITFOR 只適用於 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 訊息。 如需詳細資訊，請參閱 [GET CONVERSATION GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/get-conversation-group-transact-sql.md)。  
  
 TIMEOUT *timeout*  
 指定等待訊息到達佇列的時段 (以毫秒為單位)。  
  
> [!IMPORTANT]  
>  指定 TIMEOUT 的 WAITFOR 只適用於 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 訊息。 如需詳細資訊，請參閱 [RECEIVE &#40;Transact-SQL&#41;](../../t-sql/statements/receive-transact-sql.md) 和 [GET CONVERSATION GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/get-conversation-group-transact-sql.md)。  
  
## <a name="remarks"></a>備註  
 當執行 WAITFOR 陳述式時，交易在執行中，在相同交易之下，不能執行其他要求。  
  
 實際的時間延遲可能與 *time_to_pass*、*time_to_execute* 或 *timeout* 中所指定時間不同，且會隨著伺服器的活動層級而異。 為 WAITFOR 陳述式執行緒進行排程後，時間計數器便會啟動。 如果伺服器處於忙碌狀態，執行緒可能不會立即排程，因此時間延遲可能比指定的時間還長。  
  
 WAITFOR 不會變更查詢的語意。 如果查詢無法傳回任何資料列，WAITFOR 會永久等待，如果指定了 TIMEOUT，就會等到 TIMEOUT 到期。  
  
 在 WAITFOR 陳述式上，不能開啟資料指標。  
  
 在 WAITFOR 陳述式上，不能定義檢視。  
  
 當查詢超出 query wait 選項時，不需要執行，就能夠完成 WAITFOR 陳述式引數。 如需組態選項的詳細資訊，請參閱[設定 query wait 伺服器組態選項](../../database-engine/configure-windows/configure-the-query-wait-server-configuration-option.md)。 若要查看使用中和等待中的處理序，請使用 [sp_who](../../relational-databases/system-stored-procedures/sp-who-transact-sql.md)。  
  
 每個 WAITFOR 陳述式都有一個相關聯的執行緒。 如果在相同伺服器上指定了許多 WAITFOR 陳述式，可以將許多執行緒繫結起來，以等待執行這些陳述式。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會監視 WAITFOR 陳述式執行緒的數目，如果伺服器感到執行緒資源用盡，它會隨機選取某些要結束的執行緒。  
  
 您可以在也保留了鎖定以防止 WAITFOR 陳述式所存取之資料列集遭到改變的交易內，搭配 WAITFOR 執行查詢來建立死結。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會識別這些狀況，並在這類死結有可能存在時，傳回空的結果集。  
  
> [!CAUTION]  
>  包含 WAITFOR 將會減緩 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序的完成，並在應用程式內產生逾時訊息。 必要時，請在應用程式層級調整連接的逾時設定。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-waitfor-time"></a>A. 使用 WAITFOR TIME  
 下列範例會在晚上 10:20 執行 msdb 資料庫中的預存程序 `sp_update_job`。 (`22:20`).  
  
```sql  
EXECUTE sp_add_job @job_name = 'TestJob';  
BEGIN  
    WAITFOR TIME '22:20';  
    EXECUTE sp_update_job @job_name = 'TestJob',  
        @new_name = 'UpdatedJob';  
END;  
GO  
```  
  
### <a name="b-using-waitfor-delay"></a>B. 使用 WAITFOR DELAY  
 下列範例會在延遲兩小時之後執行預存程序。  
  
```sql  
BEGIN  
    WAITFOR DELAY '02:00';  
    EXECUTE sp_helpdb;  
END;  
GO  
```  
  
### <a name="c-using-waitfor-delay-with-a-local-variable"></a>C. 搭配本機變數來使用 WAITFOR DELAY  
 下列範例顯示如何搭配 `WAITFOR DELAY` 選項來使用本機變數。 這預存程序會等待一陣可變的時段，再將經歷的時、分、秒數資訊傳回給使用者。  
  
```sql  
IF OBJECT_ID('dbo.TimeDelay_hh_mm_ss','P') IS NOT NULL  
    DROP PROCEDURE dbo.TimeDelay_hh_mm_ss;  
GO  
CREATE PROCEDURE dbo.TimeDelay_hh_mm_ss   
    (  
    @DelayLength char(8)= '00:00:00'  
    )  
AS  
DECLARE @ReturnInfo VARCHAR(255)  
IF ISDATE('2000-01-01 ' + @DelayLength + '.000') = 0  
    BEGIN  
        SELECT @ReturnInfo = 'Invalid time ' + @DelayLength   
        + ',hh:mm:ss, submitted.';  
        -- This PRINT statement is for testing, not use in production.  
        PRINT @ReturnInfo   
        RETURN(1)  
    END  
BEGIN  
    WAITFOR DELAY @DelayLength  
    SELECT @ReturnInfo = 'A total time of ' + @DelayLength + ',   
        hh:mm:ss, has elapsed! Your time is up.'  
    -- This PRINT statement is for testing, not use in production.  
    PRINT @ReturnInfo;  
END;  
GO  
/* This statement executes the dbo.TimeDelay_hh_mm_ss procedure. */  
EXEC TimeDelay_hh_mm_ss '00:00:10';  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 `A total time of 00:00:10, in hh:mm:ss, has elapsed. Your time is up.`  
  
## <a name="see-also"></a>另請參閱  
 [流程控制語言 &#40;Transact-SQL&#41;](~/t-sql/language-elements/control-of-flow.md)   
 [datetime &#40;Transact-SQL&#41;](../../t-sql/data-types/datetime-transact-sql.md)   
 [sp_who &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-who-transact-sql.md)  
  
