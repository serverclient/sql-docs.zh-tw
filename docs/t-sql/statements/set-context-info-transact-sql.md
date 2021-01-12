---
description: SET CONTEXT_INFO (Transact-SQL)
title: SET CONTEXT_INFO (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SET_CONTEXT_INFO_TSQL
- SET CONTEXT_INFO
dev_langs:
- TSQL
helpviewer_keywords:
- context information [SQL Server]
- CONTEXT_INFO option
- SET CONTEXT_INFO statement
ms.assetid: a0b7b9f3-dbda-4350-a274-bd9ecd5c0a74
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 6420062352983a45cb1f5f81ddcae83e173b5130
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091845"
---
# <a name="set-context_info-transact-sql"></a>SET CONTEXT_INFO (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  最多可讓 128 個位元組的二進位資訊與目前的工作階段或連接發生關聯。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
SET CONTEXT_INFO { binary_str | @binary_var }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *binary_str*  
 這是要與目前工作階段或連線建立關聯的 **binary** 常數，或能夠隱含地轉換成 **binary** 的常數。  
  
 **@** *binary_var*  
 這是用來存放要與目前工作階段或連線的內容值建立關聯的 **varbinary** 或 **binary** 變數。  
  
## <a name="remarks"></a>備註  
 擷取目前工作階段的內容資訊，最好的方法是使用 CONTEXT_INFO 函數。 工作階段內容資訊也儲存在下列系統檢視的 **context_info** 資料行中：  
  
-   **sys.dm_exec_requests**  
  
-   **sys.dm_exec_sessions**  
  
-   **sys.sysprocesses**  
  
 在使用者自訂函數中，不能指定 SET CONTEXT_INFO。 您不能提供 Null 值給 SET CONTEXT_INFO，因為存放值的檢視不接受 Null 值。  
  
 SET CONTEXT_INFO 不接受常數或變數名稱以外的運算式。 若要將內容資訊設為函式呼叫的結果，您必須先將函式呼叫的結果併入 **binary** 或 **varbinary** 變數中。  
  
 當您在預存程序或觸發程序中發出 SET CONTEXT_INFO 時，和其他 SET 陳述式不同的是，在預存程序或觸發程序完成之後，為內容資訊所設定的新值將保存下來。  
  
## <a name="examples"></a>範例  
  
### <a name="a-setting-context-information-by-using-a-constant"></a>A. 利用常數來設定內容資訊  
 下列範例會示範 `SET CONTEXT_INFO`，它將設定這個值及顯示其結果。 請注意，查詢 `sys.dm_exec_sessions` 需要 SELECT 和 VIEW SERVER STATE 權限，但使用 CONTEXT_INFO 函數便沒有這項需要。  
  
```sql
SET CONTEXT_INFO 0x01010101;  
GO  
SELECT context_info   
FROM sys.dm_exec_sessions  
WHERE session_id = @@SPID;  
GO  
```  
  
### <a name="b-setting-context-information-by-using-a-function"></a>B. 利用函數來設定內容資訊  
 下列範例會示範使用函式的輸出來設定內容值，其中來自函式的值必須先放置在 **binary** 變數中。  
  
```sql
DECLARE @BinVar varbinary(128);  
SET @BinVar = CAST(REPLICATE( 0x20, 128 ) AS varbinary(128) );  
SET CONTEXT_INFO @BinVar;  
  
SELECT CONTEXT_INFO() AS MyContextInfo;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [sys.dm_exec_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)   
 [sys.dm_exec_sessions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)   
 [CONTEXT_INFO  &#40;Transact-SQL&#41;](../../t-sql/functions/context-info-transact-sql.md)  
  
  
