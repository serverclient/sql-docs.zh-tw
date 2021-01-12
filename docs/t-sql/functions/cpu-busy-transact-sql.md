---
description: '&#x40;&#x40;CPU_BUSY (Transact-SQL)'
title: CPU_BUSY (Transact-SQL)
ms.custom: ''
ms.date: 09/18/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- '@@CPU_BUSY_TSQL'
- '@@CPU_BUSY'
dev_langs:
- TSQL
helpviewer_keywords:
- CPU [SQL Server]
- status information [SQL Server], CPU
- ticks [SQL Server]
- time [SQL Server], CPU activity
- '@@CPU_BUSY function'
- statistical information [SQL Server], CPU
- CPU [SQL Server], activity
ms.assetid: 81ae0e64-79fa-4a74-9aa5-37045c4cd211
author: cawrites
ms.author: chadam
ms.openlocfilehash: edb5daccc0054ce98d0ebc4bb7568835adc46d80
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98087958"
---
# <a name="x40x40cpu_busy-transact-sql"></a>&#x40;&#x40;CPU_BUSY (Transact-SQL)

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

此函數會傳回 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 自上次開始之後，花費在進行中操作的總時間。 `@@CPU_BUSY` 會傳回以 CPU 時間遞增 (或以「刻度」為單位) 所測量的結果。 這個值是所有 CPU 的累計，所以可能會超出實際的經歷時間。 乘以 [@@TIMETICKS](./timeticks-transact-sql.md) 以轉換成毫秒。
  
> [!NOTE]  
>  若 @@CPU_BUSY 或 @@IO_BUSY 的傳回時間超出大約 49 天的累計 CPU 時間，您會收到算術溢位的警告。 在這個情況下，`@@CPU_BUSY`、`@@IO_BUSY` 和 `@@IDLE` 變數的值並不精確。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
@@CPU_BUSY  
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]


## <a name="return-types"></a>傳回類型
**integer**
  
## <a name="remarks"></a>備註  
若要查看包含多項 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 統計資料的報表，其中包括 CPU 活動，請執行 [sp_monitor](../../relational-databases/system-stored-procedures/sp-monitor-transact-sql.md)。
  
## <a name="examples"></a>範例  
此範例會傳回迄今 (到目前的日期和時間為止) 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] CPU 活動。 此範例會將其中一個值轉換成 `float` 資料類型。 以毫秒為單位計算值時，可避免算術溢位問題。
  
```sql
SELECT @@CPU_BUSY * CAST(@@TIMETICKS AS FLOAT) AS 'CPU microseconds',   
   GETDATE() AS 'As of' ;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
CPU microseconds As of
---------------- -----------------------
18406250         2006-12-05 17:00:50.600
```
  
## <a name="see-also"></a>請參閱
[sys.dm_os_sys_info &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)  
[@@IDLE &#40;Transact-SQL&#41;](../../t-sql/functions/idle-transact-sql.md)  
[@@IO_BUSY &#40;Transact-SQL&#41;](../../t-sql/functions/io-busy-transact-sql.md)  
[sp_monitor &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-monitor-transact-sql.md)  
[系統統計函式 &#40;Transact-SQL&#41;](../../t-sql/functions/system-statistical-functions-transact-sql.md)
  
  
