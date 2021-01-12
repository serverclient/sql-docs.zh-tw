---
description: sys.fn_get_sql (Transact-SQL)
title: sys.fn_get_sql (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- fn_get_sql
- sys.fn_get_sql_TSQL
- fn_get_sql_TSQL
- sys.fn_get_sql
dev_langs:
- TSQL
helpviewer_keywords:
- fn_get_sql function
- text [SQL Server], SQL handles
- sys.fn_get_sql function
- valid SQL handles [SQL Server]
- SQL handles
ms.assetid: d5fe49b5-0813-48f2-9efb-9187716b2fd4
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 4b3e28e4c66d45f28c6239431e8e6d5440d5d4a0
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093818"
---
# <a name="sysfn_get_sql-transact-sql"></a>sys.fn_get_sql (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回指定 SQL 控制代碼之 SQL 陳述式的文字。  
  
> [!IMPORTANT]  
>  未來的 Microsoft SQL Server 版本將移除這項功能。 請避免在新的開發工作中使用這項功能，並規劃修改目前使用這項功能的應用程式。 請改用 sys.dm_exec_sql_text。 如需詳細資訊，請參閱 [sys.dm_exec_sql_text &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md)。  
  
 
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.fn_get_sql ( SqlHandle )  
```  
  
## <a name="arguments"></a>引數  
 *SqlHandle*  
 控制代碼值。 *SqlHandle* 是 **Varbinary (64)** ，沒有預設值。  
  
## <a name="tables-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|dbid|**smallint**|資料庫識別碼。 對於隨選和準備的 SQL 陳述式而言，則為編譯陳述式的資料庫識別碼。|  
|objectid|**int**|資料庫物件的識別碼。 特定 SQL 陳述式的這個值是 NULL。|  
|number|**smallint**|如果將程序分組的話，便指出群組數目。<br /><br /> 0 = 項目不是程序。<br /><br /> NULL = 特定 SQL 陳述式。|  
|encrypted|**bit**|指出物件是否已經加密。<br /><br /> 0 = 未加密<br /><br /> 1 = 已加密|  
|文字|**text**|這是 SQL 陳述式的文字。 加密物件的這個值是 NULL。|  
  
## <a name="remarks"></a>備註  
 您可以從 [sys.dm_exec_requests &#40;transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md) 動態管理檢視的 sql_handle 資料行取得有效的 SQL 控制碼。  
  
 如果您傳遞的控制碼不再存在於快取中，fn_get_sq **l** 會傳回空的結果集。 如果您傳遞無效的控制代碼，批次會停止，且會傳回錯誤訊息。  
  
 無法快取 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 某些 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語句，例如大量複製語句，以及字串常值大於 8 KB 的語句。 這些陳述式的控制代碼無法利用 fn_get_sql 來擷取。  
  
 結果集的 **text** 資料行會針對可能包含密碼的文字進行篩選。 如需未受監視之安全性相關預存程式的詳細資訊，請參閱 [篩選追蹤](../../relational-databases/sql-trace/filter-a-trace.md)。  
  
 Fn_get_sql 函數會傳回類似于 [DBCC INPUTBUFFER](../../t-sql/database-console-commands/dbcc-inputbuffer-transact-sql.md) 命令的資訊。 以下是何時因無法使用 DBCC INPUTBUFFER 而能夠使用 fn_get_sql 函數的範例：  
  
-   當事件超出 255 個字元時。  
  
-   當您必須傳回預存程序的最高目前巢狀層級時。 例如，有名稱為 sp_1 和 sp_2 的兩個預存程序。 如果 sp_1 呼叫 sp_2，且您在 sp_2 執行時，從 sys.dm_exec_requests 動態管理檢視中取得控制代碼，fn_get_sql 函數會傳回 sp_2 的相關資訊。 另外，fn_get_sql 函數還會傳回最高目前巢狀層級的預存程序完整文字。  
  
## <a name="permissions"></a>權限  
 使用者需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="examples"></a>範例  
 資料庫管理員可以依照下列範例所示，利用 fn_get_sql 函數來協助診斷問題處理序。 在管理員識別問題工作階段識別碼之後，管理員可以擷取該工作階段的 SQL 控制代碼，利用這個控制代碼來呼叫 fn_get_sql，再利用起始和結束位移來判斷問題工作階段識別碼的 SQL 文字。  
  
```  
DECLARE @Handle varbinary(64);  
SELECT @Handle = sql_handle   
FROM sys.dm_exec_requests   
WHERE session_id = 52 and request_id = 0;  
SELECT * FROM sys.fn_get_sql(@Handle);  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [DBCC INPUTBUFFER &#40;Transact-sql&#41;](../../t-sql/database-console-commands/dbcc-inputbuffer-transact-sql.md)   
 [ &#40;Transact-sql&#41;的sys.sys進程 ](../../relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql.md)   
 [sys.dm_exec_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)  
  
  
