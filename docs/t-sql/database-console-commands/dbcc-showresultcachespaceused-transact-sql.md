---
description: DBCC SHOWRESULTCACHESPACEUSED (Transact-SQL)
title: DBCC SHOWRESULTCACHESPACEUSED (Transact-SQL)
ms.custom: ''
ms.date: 07/03/2019
ms.prod: sql
ms.technology: data-warehouse
ms.reviewer: ''
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: 73f598cf-b02a-4dba-8d89-9fc0b55a12b8
author: XiaoyuMSFT
ms.author: xiaoyul
monikerRange: = azure-sqldw-latest
ms.openlocfilehash: b3cfbc2117d6c3dd3599443a6f5b6e71a2d3186e
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97637405"
---
# <a name="dbcc-showresultcachespaceused-transact-sql"></a>DBCC SHOWRESULTCACHESPACEUSED (Transact-SQL)

[!INCLUDE [asa](../../includes/applies-to-version/asa.md)]

顯示 Azure [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 資料庫的已使用儲存空間結果集快取。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例 &#40;Transact-SQL&#41;](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DBCC SHOWRESULTCACHESPACEUSED  
[;]  
```  

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="remarks"></a>備註

`DBCC SHOWRESULTCACHESPACEUSED` 命令不接受任何參數，而且會傳回命令執行所在資料庫的已使用空間。

## <a name="permissions"></a>權限

需要 VIEW SERVER STATE 權限。
  
## <a name="result-sets"></a>結果集  
  
|資料行|資料類型|描述|  
|------------|---------------|-----------------|  
|reserved_space|BIGINT|資料庫使用的總空間 (KB)。 當快取的結果集增加時，此數字就會變更。|  
|data_space|BIGINT|資料使用的空間 (KB)。|  
|index_space|BIGINT|索引使用的空間 (KB)。|  
|unused_space|BIGINT|保留未使用的空間 (KB)。|  

## <a name="see-also"></a>另請參閱

[使用結果集快取進行效能微調](/azure/sql-data-warehouse/performance-tuning-result-set-caching)</br>
[ALTER DATABASE SET 選項 &#40;Transact-SQL&#41;](../statements/alter-database-transact-sql-set-options.md?view=azure-sqldw-latest&preserve-view=true)</br>
[ALTER DATABASE &#40;Transact-SQL&#41;](../statements/alter-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)</br>
[SET RESULT SET CACHING &#40;Transact-SQL&#41;](../statements/set-result-set-caching-transact-sql.md)</br>
[DBCC DROPRESULTSETCACHE  &#40;Transact-SQL&#41;](./dbcc-dropresultsetcache-transact-sql.md)