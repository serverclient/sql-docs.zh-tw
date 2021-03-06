---
description: 全文檢索搜尋和語意搜尋目錄檢視 (Transact-SQL)
title: 全文檢索搜尋和語義搜尋目錄檢視 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- catalog views [SQL Server], full-text search
- full-text search [SQL Server], catalog views
- full-text indexes [SQL Server], catalog views
ms.assetid: b08ad2fd-e3d8-458f-96f1-678217e0f419
author: pmasl
ms.author: pelopes
ms.reviewer: mikeray
ms.openlocfilehash: 6bd2db217106c1d4a9f12914bd15761388beca86
ms.sourcegitcommit: 04cf7905fa32e0a9a44575a6f9641d9a2e5ac0f8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91809409"
---
# <a name="full-text-search-and-semantic-search-catalog-views-transact-sql"></a>全文檢索搜尋和語意搜尋目錄檢視 (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  本節描述提供有關全文檢索索引和語義索引之資訊的目錄檢視。  
  
## <a name="full-text-search-catalog-views"></a>全文檢索搜尋目錄檢視  
 [sys.fulltext_catalogs](../../relational-databases/system-catalog-views/sys-fulltext-catalogs-transact-sql.md)  
 針對每個全文檢索目錄，各包含一個資料列。  
  
 [sys.fulltext_document_types](../../relational-databases/system-catalog-views/sys-fulltext-document-types-transact-sql.md)  
 針對可用於全文檢索索引作業的每一種文件類型，各傳回一個資料列。 每個資料列都代表 SQL Server 實例中註冊的 **IFilter** 介面。  
  
 [sys.fulltext_index_catalog_usages](../../relational-databases/system-catalog-views/sys-fulltext-index-catalog-usages-transact-sql.md)  
 針對通往全文檢索索引參考的每個全文檢索目錄，各傳回一個資料列。  
  
 [sys.fulltext_index_columns](../../relational-databases/system-catalog-views/sys-fulltext-index-columns-transact-sql.md)  
 屬於全文檢索索引一部分的每個資料行各有一個資料列。  
  
 [sys.fulltext_index_fragments](../../relational-databases/system-catalog-views/sys-fulltext-index-fragments-transact-sql.md)  
 針對每一個資料表內包含全文檢索索引的每一個全文檢索索引片段，各包含一個資料列。  
  
 [sys.fulltext_indexes](../../relational-databases/system-catalog-views/sys-fulltext-indexes-transact-sql.md)  
 針對表格式物件的每個全文檢索索引，各包含一個資料列。  
  
 [sys.fulltext_languages](../../relational-databases/system-catalog-views/sys-fulltext-languages-transact-sql.md)  
 針對其斷詞工具向 SQL Server 註冊的每種語言，各包含一個資料列。 每個資料列都會顯示語言的 LCID 和名稱。  
  
 [sys.fulltext_stoplists](../../relational-databases/system-catalog-views/sys-fulltext-stoplists-transact-sql.md)  
 資料庫中的每個全文檢索停用字詞表都包含一個資料列。  
  
 [sys.fulltext_stopwords](../../relational-databases/system-catalog-views/sys-fulltext-stopwords-transact-sql.md)  
 針對資料庫中所有停用字詞表的每個停用字詞，各包含一個資料列。  
  
 [sys.fulltext_system_stopwords](../../relational-databases/system-catalog-views/sys-fulltext-system-stopwords-transact-sql.md)  
 提供系統停用字詞表的存取權。  
  
 [sys.registered_search_properties &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-registered-search-properties-transact-sql.md)  
 針對目前資料庫上任何搜尋屬性清單所包含的每個搜尋屬性包含一個資料列。  
  
 [sys.registered_search_property_lists &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-registered-search-property-lists-transact-sql.md)  
 針對目前資料庫上的每個搜尋屬性清單，各包含一個資料列。  
  
## <a name="semantic-search-catalog-views"></a>語意搜尋目錄檢視  
 [sys.fulltext_semantic_language_statistics_database &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-fulltext-semantic-language-statistics-database-transact-sql.md)  
 傳回有關目前 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體上安裝之語義語言統計資料庫的資料列。  
  
 [sys.fulltext_semantic_languages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-fulltext-semantic-languages-transact-sql.md)  
 針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體上註冊語義模型的每個語言，各傳回一個資料列。 當語言模型已註冊時，該語言會啟用語意索引。  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;的系統檢視 ](../../t-sql/language-reference.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [&#40;Transact-sql&#41;的全文檢索搜尋和語義搜尋動態管理檢視和函數 ](../../relational-databases/system-dynamic-management-views/full-text-and-semantic-search-dynamic-management-views-functions.md)  
  
