---
description: DBCC DROPRESULTSETCACHE  (Transact-SQL)
title: DBCC DROPRESULTSETCACHE (Transact-SQL)
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
ms.openlocfilehash: 22ebd9f7d52fd15d9dbd8479962312387857b233
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641254"
---
# <a name="dbcc-dropresultsetcache--transact-sql"></a>DBCC DROPRESULTSETCACHE  (Transact-SQL)

[!INCLUDE [asa](../../includes/applies-to-version/asa.md)]

從 Azure [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] 資料庫移除所有結果集快取項目。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例 &#40;Transact-SQL&#41;](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DBCC DROPRESULTSETCACHE
[;]  
```  

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="permissions"></a>權限

需要 DB_OWNER 固定資料庫角色中的成員資格。

## <a name="remarks"></a>備註

- 此命令會清空所有查詢的結果集快取。  

- 關閉資料庫的結果集快取功能也會刪除所有已快取的結果。  

- 暫停已啟用結果集快取的資料庫將不會刪除已快取的結果。  

## <a name="see-also"></a>另請參閱

- [使用結果集快取進行效能微調](/azure/sql-data-warehouse/performance-tuning-result-set-caching)</br>
- [ALTER DATABASE SET 選項 &#40;Transact-SQL&#41;](../statements/alter-database-transact-sql-set-options.md?view=azure-sqldw-latest&preserve-view=true)</br>
- [ALTER DATABASE &#40;Transact-SQL&#41;](../statements/alter-database-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)</br>
- [SET RESULT SET CACHING &#40;Transact-SQL&#41;](../statements/set-result-set-caching-transact-sql.md)</br>
- [DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-SQL&#41;](./dbcc-showresultcachespaceused-transact-sql.md)