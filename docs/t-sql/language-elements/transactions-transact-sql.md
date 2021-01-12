---
description: 交易 (Transact-SQL)
title: 交易 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/25/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- Transactions
- Transactions_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- transactions [SQL Server]
- transactions [SQL Server], about transactions
- UOW [SQL Server]
- unit of work [SQL Server]
ms.assetid: 1485c375-921a-42af-a871-bb333cc08d3e
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 9c2cbff6c2a5083058e7dccfacbae216bfe0a724
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98083378"
---
# <a name="transactions-transact-sql"></a>交易 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  交易是單一工作單元。 如果交易成功，便會確定交易期間所修改的所有資料，且會成為資料庫中永久的內容。 如果交易發現錯誤，必須取消或回復，便會清除所有的資料修改。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 採用下列交易模式來操作：  
  
 自動認可交易  
 每個個別陳述式都是一項交易。  
  
 明確交易  
 每項交易都是用 BEGIN TRANSACTION 陳述式來明確啟動，用 COMMIT 或 ROLLBACK 陳述式來明確結束。  
  
 隱含交易  
 在上一項交易完成時，隱含地啟動新的交易，但每項交易都用 COMMIT 或 ROLLBACK 陳述式來明確地完成。  
  
 批次範圍的交易  
 僅適用於 Multiple Active Result Sets (MARS)，在 MARS 工作階段下啟動的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 外顯或隱含交易會變成批次範圍的交易。 當批次完成時， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]會自動回復未認可或回復之批次範圍的交易。  

> [!NOTE] 
> 如需有關資料倉儲的特別考量，請參閱[交易 (Azure Synapse Analytics)](transactions-sql-data-warehouse.md)。   

## <a name="in-this-section"></a>本節內容  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 提供下列交易陳述式：  
  
:::row:::
    :::column:::
        [BEGIN DISTRIBUTED TRANSACTION](../../t-sql/language-elements/begin-distributed-transaction-transact-sql.md)
    :::column-end:::
    :::column:::
        [ROLLBACK TRANSACTION](../../t-sql/language-elements/rollback-transaction-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [BEGIN TRANSACTION](../../t-sql/language-elements/begin-transaction-transact-sql.md)
    :::column-end:::
    :::column:::
        [ROLLBACK WORK](../../t-sql/language-elements/rollback-work-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [COMMIT TRANSACTION](../../t-sql/language-elements/commit-transaction-transact-sql.md)
    :::column-end:::
    :::column:::
        [SAVE TRANSACTION](../../t-sql/language-elements/save-transaction-transact-sql.md)
    :::column-end:::
:::row-end:::  
:::row:::
    :::column:::
        [COMMIT WORK](../../t-sql/language-elements/commit-work-transact-sql.md)
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::
  
## <a name="see-also"></a>另請參閱  
 [SET IMPLICIT_TRANSACTIONS &#40;Transact-SQL&#41;](../../t-sql/statements/set-implicit-transactions-transact-sql.md)   
 [@@TRANCOUNT &#40;Transact-SQL&#41;](../../t-sql/functions/trancount-transact-sql.md)  
  
  
