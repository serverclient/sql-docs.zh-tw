---
description: sp_fulltext_load_thesaurus_file (Transact-SQL)
title: sp_fulltext_load_thesaurus_file (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sp_fulltext_load_thesaurus_file
- sp_fulltext_load_thesaurus_file_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_fulltext_load_thesaurus_file
- full-text indexes [SQL Server], thesaurus files
- thesaurus [full-text search], editing
ms.assetid: 73a309c3-6d22-42dc-a6fe-8a63747aa2e4
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 7fa28cb49c289437bc6c9dea524d1ff9e8b8ac64
ms.sourcegitcommit: dd36d1cbe32cd5a65c6638e8f252b0bd8145e165
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89546158"
---
# <a name="sp_fulltext_load_thesaurus_file-transact-sql"></a>sp_fulltext_load_thesaurus_file (Transact-SQL)

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  造成伺服器執行個體從對應至 LCID 已指定之語言的同義字檔案中剖析並載入資料。 這個預存程序在更新同義字檔案之後很有用。 執行 **sp_fulltext_load_thesaurus_file** 會導致重新編譯使用指定 LCID 之同義字的全文檢索查詢。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
sys.sp_fulltext_load_thesaurus_file lcid [ , @loadOnlyIfNotLoaded  = action ]   
```  
  
## <a name="arguments"></a>引數  
 *lcid*  
 對應您想要載入同義字 XML 定義之語言地區設定識別碼 (LCID) 的整數。 若要取得伺服器實例上可用之語言的 Lcid，請使用 [sys. fulltext_languages &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-fulltext-languages-transact-sql.md) 類別目錄檢視。  
  
 ** \@ loadOnlyIfNotLoaded**  =  *動作*  
 指定同義字檔案是否會載入內部同義字資料表中，即使已經載入也是一樣。 *動作* 是下列其中一項：  
  
|值|定義|  
|-----------|----------------|  
|**0**|載入同義字檔案，不管是否已經載入。 這是 **sp_fulltext_load_thesaurus_file**的預設行為。|  
|1|只有在尚未載入同義字檔案時，才會載入。|  
  
## <a name="return-code-values"></a>傳回碼值  
 無  
  
## <a name="result-sets"></a>結果集  
 None  
  
## <a name="remarks"></a>備註  
 同義字檔案會自動由使用此同義字的全文檢索查詢載入。 若要避免對全文檢索查詢的第一次效能影響，建議您執行 **sp_fulltext_load_thesaurus_file**。  
  
 使用 [sp_fulltext_service](../../relational-databases/system-stored-procedures/sp-fulltext-service-transact-sql.md)'**update_languages**' 以更新使用全文檢索搜尋所註冊的語言清單。  
  
## <a name="permissions"></a>權限  
 只有系統管理員（ **sysadmin** ）固定伺服器角色或系統管理員的成員，才能夠執行 **sp_fulltext_load_thesaurus_file** 預存程式。  
  
 只有系統管理員能夠更新、修改或刪除同義字檔案。  
  
## <a name="examples"></a>範例  
  
### <a name="a-load-a-thesaurus-file-even-if-it-is-already-loaded"></a>A. 載入同義字檔案，即使已經載入也是一樣  
 下列範例會剖析並載入英文同義字檔案。  
  
```sql
EXEC sys.sp_fulltext_load_thesaurus_file 1033;
```  
  
### <a name="b-load-a-thesaurus-file-only-if-it-is-not-yet-loaded"></a>B. 只有在尚未載入同義字檔案時，才會載入  
 下列範例會剖析並載入阿拉伯文同義字檔案 (除非已經載入)。  
  
```sql
EXEC sys.sp_fulltext_load_thesaurus_file 1025, @loadOnlyIfNotLoaded = 1;
```  

## <a name="see-also"></a>另請參閱

[FULLTEXTSERVICEPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/fulltextserviceproperty-transact-sql.md)  
[系統預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
[設定及管理全文檢索搜尋的同義字檔案](../../relational-databases/search/configure-and-manage-thesaurus-files-for-full-text-search.md)
