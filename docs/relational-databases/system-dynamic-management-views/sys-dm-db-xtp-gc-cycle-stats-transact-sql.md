---
description: sys.dm_db_xtp_gc_cycle_stats (Transact-SQL)
title: sys.dm_db_xtp_gc_cycle_stats (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/29/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_db_xtp_gc_cycle_stats_TSQL
- dm_db_xtp_gc_cycle_stats
- sys.dm_db_xtp_gc_cycle_stats_TSQL
- sys.dm_db_xtp_gc_cycle_stats
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_xtp_gc_cycle_stats dynamic management view
ms.assetid: bbc9704e-158e-4d32-b693-f00dce31cd2f
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: bd6973004f499bfefc950b61382a1d0fe5c6495d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092875"
---
# <a name="sysdm_db_xtp_gc_cycle_stats-transact-sql"></a>sys.dm_db_xtp_gc_cycle_stats (Transact-SQL)
[!INCLUDE[sql-asdb-asdbmi](../../includes/applies-to-version/sql-asdb-asdbmi.md)]

  輸出已刪除一個或多個資料列之已認可交易的目前狀態。 閒置的記憶體回收執行緒會每分鐘喚醒一次，或是在認可的 DML 交易數目超出內部臨界值時喚醒 (從上一次記憶體回收週期之後算起)。 在記憶體回收週期中，它會將已經認可的交易移到與層代有關的一個或多個佇列中。 已經產生過時版本的交易會在 16 個層代中分組在 16 筆交易的單位中，如下所示：  
  
-   層代 0：這會儲存比最舊的作用中交易更早認可的所有交易。 這些交易產生的資料列版本可立即供記憶體回收之用。  
  
-   層代 1-14：儲存時間戳記大於最舊的作用中交易的交易。 資料列版本無法進行記憶體回收。 每個層代最多可以保存 16 筆交易。 這些層代中一共可以有 224 (14 * 16) 筆交易存在。  
  
-   層代 15：時間戳記大於最舊的作用中交易的剩餘交易會前往層代 15。 類似於層代 0，層代 15 中沒有交易數的限制。  
  
 當面臨記憶體不足的壓力時，記憶體回收執行緒會積極地更新最舊的作用中交易提示，這樣會強制記憶體回收。  
  
 如需詳細資訊，請參閱[記憶體內部 OLTP &#40;記憶體內部最佳化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)。  
  
  
|資料行名稱|類型|描述|  
|-----------------|----------|-----------------|  
|cycle_id|**bigint**|記憶體回收循環的唯一識別碼。|  
|ticks_at_cycle_start|**bigint**|循環開始時的時間刻度。|  
|ticks_at_cycle_end|**bigint**|循環結束時的時間刻度。|  
|base_generation|**bigint**|資料庫中目前的基底層代值。 這代表用來識別記憶體回收交易之最舊作用中交易的時間戳記。 最舊的作用中交易識別碼會以 16 的增量更新。 例如，如果您的交易識別碼為124、125、126 .。。139，值會是124。 當您加入另一筆交易時 (例如 140)，此值將會是 140。|  
|xacts_copied_to_local|**bigint**|從交易管線複製到資料庫之層代陣列的交易數目。|  
|xacts_in_gen_0- xacts_in_gen_15|**bigint**|每一層代 (Generation) 的交易數目。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW DATABASE STATE 權限。  
  
## <a name="usage-scenario"></a>使用案例  
 下面是資料行子集的範例輸出，其中顯示 27 個層代：  
  
```  
cycle_id   ticks_at_cycle_start ticks_at_cycle_end   base_generation  xacts_in_gen_0    xacts_in_gen_1  
  
1          123160509            123160509            1                    0                    0  
2          123176822            123176822            1                    0                    1  
3          123236826            123236826            1                    0                    1  
4          123296829            123296829            1                    0                    1  
5          123356832            123356941            129                  0                    0  
6          123357473            123357473            129                  0                    0  
7          123417486            123417486            129                  0                    0  
8          123477489            123477489            129                  0                    0  
9          123537492            123537492            129                  0                    0  
10         123597500            123597500            129                  0                    0  
11         123657504            123657504            129                  0                    0  
12         123717507            123717507            129                  0                    0  
13         123777510            123777510            129                  0                    0  
14         123837513            123837513            129                  0                    0  
15         123897516            123897516            129                  0                    0  
16         123957516            123957516            129                  0                    0  
17         124017516            124017516            129                  0                    0  
18         124077517            124077517            129                  0                    0  
19         124137517            124137517            129                  0                    0  
20         124197518            124197518            129                  0                    0  
21         124257518            124257518            129                  0                    0  
22         124317523            124317523            129                  0                    0  
23         124377526            124377526            129                  0                    0  
24         124437529            124437529            129                  0                    0  
25         124497533            124497533            129                  0                    0  
26         124557536            124557536            129                  0                    0  
27         124617539            124617539            129                  0                    0  
  
```  
  
## <a name="see-also"></a>另請參閱  
 [記憶體最佳化的資料表動態管理檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/memory-optimized-table-dynamic-management-views-transact-sql.md)  
  
  
