---
description: FULLTEXTCATALOGPROPERTY (Transact-SQL)
title: FULLTEXTCATALOGPROPERTY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FULLTEXTCATALOGPROPERTY_TSQL
- FULLTEXTCATALOGPROPERTY
dev_langs:
- TSQL
helpviewer_keywords:
- full-text catalogs [SQL Server], properties
- FULLTEXTCATALOGPROPERTY function
- status information [SQL Server], full-text catalogs
ms.assetid: f841dc79-2044-4863-aff0-56b8bb61f250
author: cawrites
ms.author: chadam
ms.openlocfilehash: 57e53c26b7f1797daf6a1ccc53f7578eabda734a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98087280"
---
# <a name="fulltextcatalogproperty-transact-sql"></a>FULLTEXTCATALOGPROPERTY (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

傳回 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中全文檢索目錄屬性的相關資訊。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
FULLTEXTCATALOGPROPERTY ('catalog_name' ,'property')  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
  
> [!NOTE]  
>  下列屬性會從未來的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中移除：**LogSize** 和 **PopulateStatus**。 請避免在新的開發工作中使用這些屬性，並規劃修改目前使用任何這些屬性的應用程式。  
  
_catalog\_name_  
這是一個包含全文檢索目錄名稱的運算式。  
  
_property_  
這是一個包含全文檢索目錄屬性名稱的運算式。 下表列出各個屬性，並提供傳回資訊的描述。  
  
|屬性|描述|  
|--------------|-----------------|  
|**AccentSensitivity**|區分腔調字設定。<br /><br /> 0 = 不區分腔調字<br /><br /> 1 = 區分腔調字|  
|**IndexSize**|顯示全文檢索目錄的邏輯大小，以 MB 為單位。 包括語意關鍵片語和文件相似度索引的大小。<br /><br /> 如需詳細資訊，請參閱此主題稍後的「備註」。|  
|**ItemCount**|索引項目的數目，包括目錄中的所有全文檢索、關鍵片語和文件相似度索引|  
|**LogSize**|支援這個項目的目的，只是為了與舊版相容。 永遠傳回 0。<br /><br />  搜尋服務全文檢索目錄的相關錯誤記錄組合集大小 (以位元組為單位)。|  
|**MergeStatus**|指出主要合併是否正在進行中。<br /><br /> 0 = 主要合併不在進行中<br /><br /> 1 = 主要合併正在進行中|  
|**PopulateCompletionAge**|前次全文檢索索引擴展完成和 01/01/1990 00:00:00 之間的時差 (以秒為單位)。<br /><br /> 只更新完整和累加搜耙的這個項目。 如果未進行擴展，便傳回 0。|  
|**PopulateStatus**|0 = 閒置<br /><br /> 1 = 完整擴展進行中<br /><br /> 2 = 已暫停<br /><br /> 3 = 調整執行速度<br /><br /> 4 = 復原中<br /><br /> 5 = 已關閉<br /><br /> 6 = 累加擴展進行中<br /><br /> 7 = 正在建立索引<br /><br /> 8 = 磁碟已滿， 已暫停。<br /><br /> 9 = 變更追蹤|  
|**UniqueKeyCount**|全文檢索目錄中唯一索引鍵的數目。|  
|**ImportStatus**|指出是否正在匯入全文檢索目錄。<br /><br /> 0 = 沒有正在匯入全文檢索目錄。<br /><br /> 1 = 正在匯入全文檢索目錄。|  
  
## <a name="return-types"></a>傳回型別  
**int**  
  
## <a name="exceptions"></a>例外狀況  
發生錯誤或呼叫端沒有檢視物件的權限時，會傳回 NULL。  
  
在 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中，使用者只能檢視安全性實體的中繼資料。 這些安全性實體是使用者所擁有或使用者被授與某些權限的安全性實體。 因此，發出中繼資料的內建函式 (例如，FULLTEXTCATALOGPROPERTY) 會在使用者不具有該物件任何權限時傳回 NULL。 如需詳細資訊，請參閱 [sp_help_fulltext_catalogs &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-fulltext-catalogs-transact-sql.md)。  
  
## <a name="remarks"></a>備註  
FULLTEXTCATALOGPROPERTY ('_catalog\_name_','**IndexSize**') 只會尋找具有狀態 4 或 6 的片段，如 [sys.fulltext_index_fragments](../../relational-databases/system-catalog-views/sys-fulltext-index-fragments-transact-sql.md) 中所示。 這些片段是邏輯索引的一部分。 因此，**IndexSize** 屬性只會傳回邏輯索引大小。 

不過，在索引合併期間，實際的索引大小可能會是其邏輯大小的兩倍。 若要找出全文檢索索引在合併期間所使用的實際大小，請使用 [sp_spaceused](../../relational-databases/system-stored-procedures/sp-spaceused-transact-sql.md) 系統預存程序。 該程序會查看所有與全文檢索索引相關聯的片段。 

全文檢索母體擴展可能會失敗。 如果您限制全文檢索目錄檔案的成長，且未提供足夠的空間給合併處理使用，則它可能會失敗。 在此情況下，FULLTEXTCATALOGPROPERTY ('_catalog\_name_','**IndexSize**') 會傳回 0，且下列錯誤會寫入全文檢索記錄中：  
  
`Error: 30059, Severity: 16, State: 1. A fatal error occurred during a full-text population and caused the population to be cancelled. Population type is: FULL; database name is FTS_Test (id: 13); catalog name is t1_cat (id: 5); table name t1 (id: 2105058535). Fix the errors that are logged in the full-text crawl log. Then, resume the population. The basic Transact-SQL syntax for this is: ALTER FULLTEXT INDEX ON table_name RESUME POPULATION.`  
  
請務必不要讓應用程式在緊湊迴圈中等待，並檢查 **PopulateStatus** 屬性是否進入閒置狀態。 進入閒置狀態表示母體擴展已完成。 這項檢查會使 CPU 週期背離資料庫和全文檢索搜尋處理序，並造成逾時。 在資料表層級上檢查對應的 **PopulateStatus** 屬性 (OBJECTPROPERTYEX 系統函式中的 **TableFullTextPopulateStatus**) 通常是比較好的選擇。 OBJECTPROPERTYEX 中的這個屬性及其他新的全文檢索屬性，可提供更精細的全文檢索索引資料表相關資訊。 如需詳細資訊，請參閱 [OBJECTPROPERTYEX &#40;Transact-SQL&#41;](../../t-sql/functions/objectpropertyex-transact-sql.md)。  
  
## <a name="examples"></a>範例  
下列範例會傳回名稱為 `Cat_Desc` 的全文檢索目錄中之全文檢索索引項目數。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT fulltextcatalogproperty('Cat_Desc', 'ItemCount');  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
[FULLTEXTSERVICEPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/fulltextserviceproperty-transact-sql.md)   
[中繼資料函數 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)   
[sp_help_fulltext_catalogs &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-fulltext-catalogs-transact-sql.md)  
  
  
