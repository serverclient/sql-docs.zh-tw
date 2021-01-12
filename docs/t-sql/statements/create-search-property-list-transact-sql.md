---
description: CREATE SEARCH PROPERTY LIST (Transact-SQL)
title: CREATE SEARCH PROPERTY LIST (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE_SEARCH_PROPERTY_LIST_TSQL
- CREATE SEARCH
- CREATE_SEARCH_TSQL
- CREATE_SEARCH_PROPERTY_TSQL
- CREATE SEARCH PROPERTY
- CREATE SEARCH PROPERTY LIST
- sql13.swb.spl.newsearchpropertylist.f1
dev_langs:
- TSQL
helpviewer_keywords:
- full-text search [SQL Server], search property lists
- search property lists [SQL Server], creating
- CREATE SEARCH PROPERTY LIST statement
ms.assetid: 5440cbb8-3403-4d27-a2f9-8e1f5a1bc12b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: afd220a9893f77c79d5677af135c9a640611abf2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091982"
---
# <a name="create-search-property-list-transact-sql"></a>CREATE SEARCH PROPERTY LIST (Transact-SQL)
[!INCLUDE [SQL Server Azure SQL Database ](../../includes/applies-to-version/sql-asdb.md)]

  建立新的搜尋屬性清單。 搜尋屬性清單用來指定您想要包含在全文檢索索引中的一個或多個搜尋屬性。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
CREATE SEARCH PROPERTY LIST new_list_name  
   [ FROM [ database_name. ] source_list_name ]  
   [ AUTHORIZATION owner_name ]  
;  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *new_list_name*  
 這是新搜尋屬性清單的名稱。 *new_list_name* 是最多 128 個字元的識別碼。 *new_list_name* 在目前資料庫的所有屬性清單中必須是唯一的，且符合識別碼的規則。 在建立全文檢索索引時，會使用 *new_list_name*。  
  
 *database_name*  
 這是 *source_list_name* 所指定之屬性清單所在的資料庫名稱。 如果未指定，*database_name* 會預設為目前的資料庫。  
  
 *database_name* 必須指定現有資料庫的名稱。 目前連線的登入必須與 *database_name* 所指定資料庫中的現有使用者識別碼建立關聯。 您也必須有資料庫的必要[權限](#Permissions)。  
  
 *source_list_name*  
 指定要從 *database_name* 複製現有的屬性清單來建立新的屬性清單。 如果 *source_list_name* 不存在，CREATE SEARCH PROPERTY LIST 會失敗並出現錯誤。 *new_list_name* 會繼承 *source_list_name* 中的搜尋屬性。  
  
 AUTHORIZATION *owner_name*  
 指定擁有屬性清單的使用者或角色的名稱。 *owner_name* 必須是目前使用者所屬的角色名稱，或者目前使用者必須具有 *owner_name* 的 IMPERSONATE 權限。 若未指定，擁有權便歸目前使用者。  
  
> [!NOTE]  
>  您可以使用 [ALTER AUTHORIZATION](../../t-sql/statements/alter-authorization-transact-sql.md)[!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式來變更擁有者。  
  
## <a name="remarks"></a>備註  
  
> [!NOTE]  
>  如需屬性清單的一般資訊，請參閱[使用搜尋屬性清單搜尋文件屬性](../../relational-databases/search/search-document-properties-with-search-property-lists.md)。  
  
 根據預設，新的搜尋屬性清單是空的，而且您必須改變它，才能手動加入一個或多個搜尋屬性。 或者，您也可以複製現有的搜尋屬性清單。 在此情況下，新的清單會繼承其來源的搜尋屬性，但是您可以改變新的清單以加入或移除搜尋屬性。 下次完整母體擴展時搜尋屬性清單中的任何屬性都會包含在全文檢索索引中。  
  
 在下列任何情況下，CREATE SEARCH PROPERTY LIST 陳述式會失敗：  
  
-   如果 *database_name* 所指定的資料庫不存在。  
  
-   如果 *source_list_name* 所指定的清單不存在。  
  
-   如果您沒有正確的權限。  
  
 **新增或移除清單的屬性**  
  
-   [ALTER SEARCH PROPERTY LIST &#40;Transact-SQL&#41;](../../t-sql/statements/alter-search-property-list-transact-sql.md)  
  
-   **卸除屬性清單**  
  
-   [DROP SEARCH PROPERTY LIST &#40;Transact-SQL&#41;](../../t-sql/statements/drop-search-property-list-transact-sql.md)  
  
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要目前資料庫的 CREATE FULLTEXT CATALOG 權限，以及從中複製來源屬性清單的任何資料庫的 REFERENCES 權限。  
  
> [!NOTE]  
>  需要 REFERENCES 權限才能將清單與全文檢索索引產生關聯。 需要 CONTROL 權限才能新增和移除屬性或卸除清單。 屬性清單擁有者可以授與清單的 REFERENCES 或 CONTROL 權限。 具有 CONTROL 權限的使用者也可以將 REFERENCES 權限授與其他使用者。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-an-empty-property-list-and-associating-it-with-an-index"></a>A. 建立空白屬性清單，並將它與索引建立關聯  
 下列範例會建立名為 `DocumentPropertyList` 的新搜尋屬性清單。 然後此範例使用 [ALTER FULLTEXT INDEX](../../t-sql/statements/alter-fulltext-index-transact-sql.md) 陳述式，將新屬性清單與 `AdventureWorks` 資料庫中 `Production.Document` 資料表的全文檢索索引建立關聯，但不啟動母體擴展。  
  
> [!NOTE]  
>  如需將數個預先定義的已知搜尋屬性新增至這個搜尋屬性清單的範例，請參閱 [ALTER SEARCH PROPERTY LIST &#40;Transact-SQL&#41;](../../t-sql/statements/alter-search-property-list-transact-sql.md)。 在將搜尋屬性加入至清單之後，資料庫管理員需要使用另一個 ALTER FULLTEXT INDEX 陳述式搭配 START FULL POPULATION 子句。  
  
```sql 
CREATE SEARCH PROPERTY LIST DocumentPropertyList;  
GO  
USE AdventureWorks2012;  
ALTER FULLTEXT INDEX ON Production.Document   
   SET SEARCH PROPERTY LIST DocumentPropertyList  
   WITH NO POPULATION;   
GO   
```  
  
### <a name="b-creating-a-property-list-from-an-existing-one"></a>B. 從現有屬性清單建立屬性清單  
 下列範例會從範例 A 所建立的清單 `JobCandidateProperties` (此清單與 `DocumentPropertyList` 資料庫中的全文檢索索引相關聯) 建立新的搜尋屬性清單 `AdventureWorks2012`。 然後此範例使用 ALTER FULLTEXT INDEX 陳述式，將新屬性清單與 `HumanResources.JobCandidate` 資料庫中 `AdventureWorks2012` 資料表的全文檢索索引產生關聯。 這個 ALTER FULLTEXT INDEX 陳述式會啟動完整母體擴展，這是 SET SEARCH PROPERTY LIST 子句的預設行為。  
  
```sql  
CREATE SEARCH PROPERTY LIST JobCandidateProperties 
FROM AdventureWorks2012.DocumentPropertyList;  
GO  
ALTER FULLTEXT INDEX ON HumanResources.JobCandidate   
   SET SEARCH PROPERTY LIST JobCandidateProperties;  
GO
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER SEARCH PROPERTY LIST &#40;Transact-SQL&#41;](../../t-sql/statements/alter-search-property-list-transact-sql.md)   
 [DROP SEARCH PROPERTY LIST &#40;Transact-SQL&#41;](../../t-sql/statements/drop-search-property-list-transact-sql.md)   
 [sys.registered_search_properties &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-registered-search-properties-transact-sql.md)   
 [sys.registered_search_property_lists &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-registered-search-property-lists-transact-sql.md)   
 [sys.dm_fts_index_keywords_by_property &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-fts-index-keywords-by-property-transact-sql.md)   
 [使用搜索屬性清單搜索文件屬性](../../relational-databases/search/search-document-properties-with-search-property-lists.md)   
 [尋找搜尋屬性的屬性集 GUID 與屬性整數識別碼](../../relational-databases/search/find-property-set-guids-and-property-integer-ids-for-search-properties.md)  
  
  
