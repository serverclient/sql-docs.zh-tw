---
description: SET RESULT_SET_CACHING (Transact-SQL)
title: SET RESULT_SET_CACHING (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/16/2020
ms.prod: sql
ms.prod_service: sql-data-warehouse
ms.reviewer: jrasnick
ms.technology: t-sql
ms.topic: language-reference
f1_keywords: ''
dev_langs:
- TSQL
helpviewer_keywords: ''
author: XiaoyuMSFT
ms.author: xiaoyul
monikerRange: =azure-sqldw-latest
ms.openlocfilehash: d938f8523284c1c814573e70ebf2e20a50a4d089
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644376"
---
# <a name="set-result-set-caching-transact-sql"></a>SET RESULT SET CACHING (Transact-SQL) 

[!INCLUDE [asa](../../includes/applies-to-version/asa.md)]

控制目前用戶端工作階段的結果集快取行為。  

適用於 [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)]  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法

```syntaxsql
SET RESULT_SET_CACHING { ON | OFF };
```  

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="remarks"></a>備註  

在連線到您要設定其 result_set_caching 設定的使用者資料庫時執行此命令。

**開啟**   
啟用目前用戶端工作階段的結果集快取。  若在資料庫層級關閉結果集快取，便無法為工作階段開啟此功能。

**關閉**   
停用目前用戶端工作階段的結果集快取。

## <a name="examples"></a>範例

使用查詢的 request_id 來查詢 [sys.dm_pdw_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql.md) 中的 result_cache_hit 資料行以判斷此查詢的執行結果是結果快取命中或錯過。

```sql
SELECT result_cache_hit
FROM sys.dm_pdw_exec_requests
WHERE request_id = 'QID58286'
```

## <a name="permissions"></a>權限

需要 public 角色中的成員資格

## <a name="see-also"></a>另請參閱

- [使用結果集快取進行效能微調](/azure/sql-data-warehouse/performance-tuning-result-set-caching)
- [ALTER DATABASE SET 選項 &#40;Transact-SQL&#41;](./alter-database-transact-sql-set-options.md?preserve-view=true&view=azure-sqldw-latest&preserve-view=true)
- [ALTER DATABASE &#40;Transact-SQL&#41;](./alter-database-transact-sql.md?preserve-view=true&view=azure-sqldw-latest&preserve-view=true)
- [DBCC SHOWRESULTCACHESPACEUSED (Transact-SQL)](../database-console-commands/dbcc-showresultcachespaceused-transact-sql.md)
- [DBCC DROPRESULTSETCACHE (Transact-SQL)](../database-console-commands/dbcc-dropresultsetcache-transact-sql.md)