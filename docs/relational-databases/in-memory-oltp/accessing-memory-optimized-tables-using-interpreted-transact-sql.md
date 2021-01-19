---
title: 使用解譯的 T-SQL 存取記憶體最佳化的資料表
description: 了解使用解譯的 Transact-SQL (SQL Server 中的 Transact-SQL 批次或預存程序) 存取經記憶體最佳化的資料表。
ms.custom: seo-dt-2019
ms.date: 05/31/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: 92a44d4d-0e53-4fb0-b890-de264c65c95a
author: MightyPen
ms.author: genemi
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: c88fd07e9af86a3c12561935364514b23bc2ea91
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172280"
---
# <a name="accessing-memory-optimized-tables-using-interpreted-transact-sql"></a>使用解譯的 Transact-SQL 存取記憶體最佳化的資料表
[!INCLUDE[tsql-appliesto-ss2014-asdb-xxxx-xxx_md](../../includes/tsql-appliesto-ss2014-asdb-xxxx-xxx-md.md)]

 您可以使用任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢或 DML 作業 (select、insert、update 或 delete)、特定批次及 SQL 模組 (例如預存程序、資料表值函數、觸發程序和檢視表) 來存取記憶體最佳化的資料表，僅有一些例外狀況。  
  
解譯的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指的是非原生編譯預存程序的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 批次或預存程序。 記憶體最佳化資料表的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 解譯存取指的是 Interop 存取。  

從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]開始，解譯之 [!INCLUDE[tsql](../../includes/tsql-md.md)] 中的查詢可以平行掃描記憶體最佳化資料表，而不是只能以循序模式來進行掃描。

記憶體最佳化的資料表也可以透過原生編譯的預存程序存取。 建議針對效能嚴重不足的 OLTP 作業使用以原生方式編譯的預存程序。  
  
建議在這些案例中使用解譯的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 存取：  
  
- 隨選查詢和管理工作。  
  
- 報告查詢，通常會使用原生編譯的預存程序中未提供的建構 (例如「視窗」函數 (有時稱為 [OVER](../../t-sql/queries/select-over-clause-transact-sql.md) 函數))。  
  
- 為了將應用程式效能嚴重不足部分移轉至記憶體最佳化資料表，應用程式碼變更需求最低或完全不需要。 您可能會從移轉的資料表看到效能提升。 如果您之後將預存程序移轉到以原生方式編譯的預存程序，您可以查看進一步的效能改善情形。  
  
- 當以原生方式編譯的預存程序無法使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式時。  
  
但是，以下的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 建構在可存取記憶體最佳化資料表資料的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 解譯預存程序中不受支援。  
  
|區域|不支援|  
|----------|-----------------|  
|存取資料表|TRUNCATE TABLE<br /><br /> MERGE (做為目標之記憶體最佳化的資料表)。<br /><br /> 動態和索引鍵集資料指標 (這些會自動降級為靜態)。<br /><br /> 使用內容連接從 CLR 模組存取。<br /><br /> 從索引檢視表參考記憶體最佳化資料表。|  
|跨資料庫|跨資料庫查詢<br /><br /> 跨資料庫交易<br /><br /> 連結的伺服器|  
  
## <a name="table-hints"></a>資料表提示

如需有關資料表提示的詳細資訊，請參閱。 [資料表提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-table.md)。 已加入 SNAPSHOT 來支援 [!INCLUDE[hek_2](../../includes/hek-2-md.md)]。  
  
使用解譯的 [!INCLUDE[tsql](../../includes/tsql-md.md)]存取記憶體最佳化的資料表時，不支援下列資料表提示。  

:::row:::
    :::column:::
        HOLDLOCK

        PAGLOCK

        READUNCOMMITTED

        TABLOCKXX
    :::column-end:::
    :::column:::
        IGNORE_CONSTRAINTS

        READCOMMITTED

        ROWLOCK

        UPDLOCK
    :::column-end:::
    :::column:::
        IGNORE_TRIGGERS

        READCOMMITTEDLOCK

        SPATIAL_WINDOW_MAX_CELLS = *integer*

        XLOCK
    :::column-end:::
    :::column:::
        NOWAIT

        READPAST

        TABLOCK
    :::column-end:::
:::row-end:::

當您使用解譯的 [!INCLUDE[tsql](../../includes/tsql-md.md)]從明確或隱含交易存取記憶體最佳化資料表時，您必須至少執行下列其中一項操作：  
  
- 指定 [隔離等級資料表提示](../../relational-databases/in-memory-oltp/transactions-with-memory-optimized-tables.md) ，如 SNAPSHOT、REPEATABLEREAD 或 SERIALIZABLE。  
  
- 將 [MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT](../../t-sql/statements/alter-database-transact-sql-set-options.md) 資料庫選項設定為 [ON]。  
  
在 [自動認可模式](../../odbc/reference/develop-app/auto-commit-mode.md)下執行之查詢所存取的記憶體最佳化資料表不需要隔離等級資料表提示。  
  
## <a name="see-also"></a>另請參閱

[記憶體內部 OLTP 的 Transact-SQL 支援](../../relational-databases/in-memory-oltp/transact-sql-support-for-in-memory-oltp.md)   

[移轉至 In-Memory OLTP](./plan-your-adoption-of-in-memory-oltp-features-in-sql-server.md)