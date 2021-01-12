---
description: ALTER FULLTEXT STOPLIST (Transact-SQL)
title: ALTER FULLTEXT STOPLIST (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER FULLTEXT STOPLIST
- ALTER_FULLTEXT_STOPLIST_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- stoplists [full-text search]
- full-text search [SQL Server], stoplists
- ALTER FULLTEXT STOPLIST statement
- full-text search [SQL Server], stopwords
- stopwords [full-text search]
ms.assetid: f6ad87d5-6a34-435a-8456-8244947c5c83
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 12e90f8d8b2d0b35ecd406af54f55b6ea96cb4c6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98082360"
---
# <a name="alter-fulltext-stoplist-transact-sql"></a>ALTER FULLTEXT STOPLIST (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  在目前資料庫的預設全文檢索停用字詞表中插入或刪除停用字詞。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER FULLTEXT STOPLIST stoplist_name  
{   
        ADD [N] 'stopword' LANGUAGE language_term    
  | DROP   
    {  
        'stopword' LANGUAGE language_term   
      | ALL LANGUAGE language_term   
      | ALL  
     }  
;  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *stoplist_name*  
 這是要改變的停用字詞表名稱。 *stoplist_name* 最多可有 128 個字元。  
  
 **'** *stopword* **'**  
 這是可能在指定語言中具有語言含意之文字的字串，或是沒有語言含意的 Token。 *stopword* 受限為 Token 長度的最大值 (64 個字元)。 停用字詞可以指定為 Unicode 字串。  
  
 LANGUAGE *language_term*  
 指定要與新增或卸除之 *stopword* 建立關聯的語言。  
  
 *language_term* 可以指定為對應於語言地區設定識別碼 (LCID) 的字串、整數或十六進位值，如下所示：  
  
|格式|描述|  
|------------|-----------------|  
|String|*language_term* 會對應到 [sys.syslanguages (Transact-SQL)](../../relational-databases/system-compatibility-views/sys-syslanguages-transact-sql.md) 相容性檢視中的 **alias** 資料行值。 字串必須以單引號括住，如下： **'** _language_term_*_'_*。|  
|整數|*language_term* 是語言的 LCID。|  
|十六進位|*language_term* 是 0x 後接 LCID 的十六進位值。 十六進位值不能超出 8 位數，開頭的零也包括在內。 如果這個值是雙位元組字集 (DBCS) 格式，SQL Server 會將它轉換成 Unicode。|  
  
 ADD **'** _stopword_*_'_* LANGUAGE *language_term*  
 將停用字詞新增到 LANGUAGE *language_term* 指定之語言的停用字詞表。  
  
 如果語言的關鍵字和 LCID 值的指定組合在 STOPLIST 中不是唯一的，將會傳回錯誤。  如果此 LCID 值未對應到註冊的語言，將會產生錯誤。  
  
 DROP { **'** _stopword_*_'_* LANGUAGE *language_term* | ALL LANGUAGE *language_term* | ALL }  
 卸除停用字詞表中的停用字詞。  
  
 **'** *stopword* **'** LANGUAGE *language_term*  
 卸除 *language_term* 指定之語言的指定停用字詞。  
  
 ALL LANGUAGE *language_term*  
 卸除 *language_term* 指定之語言的所有停用字詞。  
  
 ALL  
 卸除停用字詞表中的所有停用字詞。  
  
## <a name="remarks"></a>備註  
 CREATE FULLTEXT STOPLIST 僅支援相容性層級 100 和更高層級。 若為相容性層級 80 和 90，系統停用字詞表就一定會指派給資料庫。  
  
## <a name="permissions"></a>權限  
 若要將停用字詞表指定為資料庫的預設停用字詞表，則需要 ALTER DATABASE 權限。 若要變更停用字詞表，則必須是停用字詞表的擁有者，或是具有 **db_owner** 或 **db_ddladmin** 固定資料庫角色中的成員資格。  
  
## <a name="examples"></a>範例  
 下列範例會變更名為 `CombinedFunctionWordList` 的停用字詞表，加入 'en' 一字 (先針對西班牙文，再針對法文)。  
  
```sql  
ALTER FULLTEXT STOPLIST CombinedFunctionWordList ADD 'en' LANGUAGE 'Spanish';  
ALTER FULLTEXT STOPLIST CombinedFunctionWordList ADD 'en' LANGUAGE 'French';  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE FULLTEXT STOPLIST &#40;Transact-SQL&#41;](../../t-sql/statements/create-fulltext-stoplist-transact-sql.md)   
 [DROP FULLTEXT STOPLIST &#40;Transact-SQL&#41;](../../t-sql/statements/drop-fulltext-stoplist-transact-sql.md)   
 [設定及管理全文檢索搜尋的停用字詞與停用字詞表](../../relational-databases/search/configure-and-manage-stopwords-and-stoplists-for-full-text-search.md)   
 [sys.fulltext_stoplists &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-fulltext-stoplists-transact-sql.md)   
 [sys.fulltext_stopwords &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-fulltext-stopwords-transact-sql.md)   
 [設定及管理全文檢索搜尋的停用字詞與停用字詞表](../../relational-databases/search/configure-and-manage-stopwords-and-stoplists-for-full-text-search.md)  
  
  
