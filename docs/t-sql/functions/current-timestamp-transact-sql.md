---
description: CURRENT_TIMESTAMP (Transact-SQL)
title: CURRENT_TIMESTAMP (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CURRENT_TIMESTAMP
- CURRENT_TIMESTAMP_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], functions
- niladic functions
- current date and time [SQL Server]
- time [SQL Server], current
- date and time [SQL Server], CURRENT_TIMESTAMP
- functions [SQL Server], time
- system date and time [SQL Server]
- system time [SQL Server]
- functions [SQL Server], date and time
- time [SQL Server], functions
- dates [SQL Server], current date and time
- dates [SQL Server], system date and time
- CURRENT_TIMESTAMP function [SQL Server]
- time [SQL Server], system
ms.assetid: c724d9cc-7b1f-4c71-bdf5-08bc52b33afc
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8725fa782ef5918ac952e927097721d2cbf14afa
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093715"
---
# <a name="current_timestamp-transact-sql"></a>CURRENT_TIMESTAMP (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

此函式將目前資料庫的系統時間戳記以 **datetime** 值傳回 (不含資料庫時區位移)。 `CURRENT_TIMESTAMP` 會從執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之電腦的作業系統衍生這個值。
  
> [!NOTE]  
>  `SYSDATETIME` 和 `SYSUTCDATE` 比 `GETDATE` 和 `GETUTCDATE` 具有更高的精確度，以小數秒數有效位數來度量。 `SYSDATETIMEOFFSET` 函式包含系統時區位移。 您可以將 `SYSDATETIME`、`SYSUTCDATE` 和 `SYSDATETIMEOFFSET` 指派給任何日期和時間類型的變數。  
  
這個函式是相當於 [GETDATE](../../t-sql/functions/getdate-transact-sql.md) 的 ANSI SQL。
  
如需所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間資料類型與函式的概觀，請參閱[日期和時間資料類型與函式](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CURRENT_TIMESTAMP  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
這個函數沒有引數。
  
## <a name="return-type"></a>傳回類型  
**datetime**
  
## <a name="remarks"></a>備註  
只要是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式可以參考 **datetime** 運算式的任何位置，它們就可以參考 `CURRENT_TIMESTAMP`。
  
`CURRENT_TIMESTAMP` 是非決定性函數。 參考這個資料行的檢視和運算式，是無法編製索引的。
  
## <a name="examples"></a>範例  
這些範例會使用六個可傳回目前日期和時間值的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統函數來傳回日期、時間或這兩者。 由於這些範例會依序傳回值，因此其小數秒數可能會不同。 請注意，傳回的實際值會反映實際執行日期/時間。
  
### <a name="a-get-the-current-system-date-and-time"></a>A. 取得目前的系統日期和時間  
  
```sql
SELECT SYSDATETIME()  
    ,SYSDATETIMEOFFSET()  
    ,SYSUTCDATETIME()  
    ,CURRENT_TIMESTAMP  
    ,GETDATE()  
    ,GETUTCDATE();  
/* Returned:  
SYSDATETIME()      2007-04-30 13:10:02.0474381  
SYSDATETIMEOFFSET()2007-04-30 13:10:02.0474381 -07:00  
SYSUTCDATETIME()   2007-04-30 20:10:02.0474381  
CURRENT_TIMESTAMP  2007-04-30 13:10:02.047  
GETDATE()          2007-04-30 13:10:02.047  
GETUTCDATE()       2007-04-30 20:10:02.047  
*/
```  
  
### <a name="b-get-the-current-system-date"></a>B. 取得目前的系統日期  
  
```sql
SELECT CONVERT (DATE, SYSDATETIME())  
    ,CONVERT (DATE, SYSDATETIMEOFFSET())  
    ,CONVERT (DATE, SYSUTCDATETIME())  
    ,CONVERT (DATE, CURRENT_TIMESTAMP)  
    ,CONVERT (DATE, GETDATE())  
    ,CONVERT (DATE, GETUTCDATE());  
  
/* Returned   
SYSDATETIME()      2007-05-03  
SYSDATETIMEOFFSET()2007-05-03  
SYSUTCDATETIME()   2007-05-04  
CURRENT_TIMESTAMP  2007-05-03  
GETDATE()          2007-05-03  
GETUTCDATE()       2007-05-04  
*/  
```  
  
### <a name="c-get-the-current-system-time"></a>C. 取得目前的系統時間  
  
```sql
SELECT CONVERT (TIME, SYSDATETIME())  
    ,CONVERT (TIME, SYSDATETIMEOFFSET())  
    ,CONVERT (TIME, SYSUTCDATETIME())  
    ,CONVERT (TIME, CURRENT_TIMESTAMP)  
    ,CONVERT (TIME, GETDATE())  
    ,CONVERT (TIME, GETUTCDATE());  
  
/* Returned  
SYSDATETIME()      13:18:45.3490361  
SYSDATETIMEOFFSET()13:18:45.3490361  
SYSUTCDATETIME()   20:18:45.3490361  
CURRENT_TIMESTAMP  13:18:45.3470000  
GETDATE()          13:18:45.3470000  
GETUTCDATE()       20:18:45.3470000  
*/  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
```sql
SELECT CURRENT_TIMESTAMP;  
```  
  
## <a name="see-also"></a>另請參閱
[CAST 和 CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)
  
  

