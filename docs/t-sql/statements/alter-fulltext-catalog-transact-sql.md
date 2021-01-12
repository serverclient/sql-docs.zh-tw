---
description: ALTER FULLTEXT CATALOG (Transact-SQL)
title: ALTER FULLTEXT CATALOG (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_FULLEXT_CATALOG_TSQL
- ALTER FULLEXT CATALOG
dev_langs:
- TSQL
helpviewer_keywords:
- modifying full-text catalogs
- full-text catalogs [SQL Server], rebuilding
- accent sensitivity
- ALTER FULLTEXT CATALOG statement
- full-text catalogs [SQL Server], modifying
- full-text catalogs [SQL Server], reorganizing
ms.assetid: 31a47aaf-6c7f-48a4-a86a-d57aec66c9cb
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: a694ce465b53f74e3139a017548e7bfecd52b88c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98082739"
---
# <a name="alter-fulltext-catalog-transact-sql"></a>ALTER FULLTEXT CATALOG (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  變更全文檢索目錄的屬性。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql 
ALTER FULLTEXT CATALOG catalog_name   
{ REBUILD [ WITH ACCENT_SENSITIVITY = { ON | OFF } ]  
| REORGANIZE  
| AS DEFAULT   
}  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *catalog_name*  
 指定要修改的目錄名稱。 如果含指定名稱的目錄不存在，則 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回錯誤，且不會執行 ALTER 作業。  
  
 REBUILD  
 通知 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 重建整個目錄。 重建目錄時，會刪除現有的目錄，並就地建立新的目錄。 具有全文檢索索引參考的所有資料表都會與新目錄產生關聯。 重建會重設資料庫系統資料表中的全文檢索中繼資料。  
  
 WITH ACCENT_SENSITIVITY = {ON|OFF}  
 指定全文檢索索引和查詢要改變的目錄是否區分腔調字。  
  
 若要判斷全文檢索目錄目前的區分重音字屬性設定，請針對 *catalog_name*，搭配 **accentsensitivity** 屬性值來使用 FULLTEXTCATALOGPROPERTY 函數。 如果函數傳回 '1'，全文檢索目錄就會區分腔調字；如果函數傳回 '0'，目錄就不會區分腔調字。  
  
 目錄和資料庫區分腔調字的預設值相同。  
  
 REORGANIZE  
 通知 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行「主要合併」，其中包括將索引作業過程所建立的較小索引合併到單一大型索引中。 合併全文檢索索引片段可以改善效能，並釋出磁碟和記憶體資源。 如果全文檢索目錄經常變更，請定期利用這個命令來重新組織全文檢索目錄。  
  
 REORGANIZE 也會將內部索引和目錄結構最佳化。  
  
 請記住，主要合併可能要花一些時間才能完成，這會隨著索引資料量而不同。 主要合併大量資料可能會建立長時間執行的交易，並在檢查點期間延遲截斷交易記錄。 在此情況下，交易記錄可能會在完整復原模式下明顯成長。 最佳作法是，確認您的交易記錄包含足夠的空間供長時間執行的交易使用，然後在資料庫中識別使用完整復原模式的大型全文檢索索引。 如需詳細資訊，請參閱 [管理交易記錄檔的大小](../../relational-databases/logs/manage-the-size-of-the-transaction-log-file.md)。  
  
 AS DEFAULT  
 指定這個全文檢索目錄是預設目錄。 當建立全文檢索索引，卻沒有指定目錄時，會使用預設目錄。 如果有現存的全文檢索目錄，將這個目錄設為 AS DEFAULT 會置換現有的預設值。  
  
## <a name="permissions"></a>權限  
 使用者必須具備全文檢索目錄的 ALTER 權限，或是 **db_owner**、**db_ddladmin** 固定資料庫角色或 sysadmin 固定伺服器角色的成員。  
  
> [!NOTE]  
>  若要使用 ALTER FULLTEXT CATALOG AS DEFAULT，使用者必須具備全文檢索目錄的 ALTER 權限，以及資料庫的 CREATE FULLTEXT CATALOG 權限。  
  
## <a name="examples"></a>範例  
 下列範例會變更區分腔調字之預設全文檢索目錄 `accentsensitivity` 的 `ftCatalog` 屬性。  
  
```sql  
--Change to accent insensitive  
USE AdventureWorks2012;  
GO  
ALTER FULLTEXT CATALOG ftCatalog   
REBUILD WITH ACCENT_SENSITIVITY=OFF;  
GO  
-- Check Accentsensitivity  
SELECT FULLTEXTCATALOGPROPERTY('ftCatalog', 'accentsensitivity');  
GO  
--Returned 0, which means the catalog is not accent sensitive.  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.fulltext_catalogs &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-fulltext-catalogs-transact-sql.md)   
 [CREATE FULLTEXT CATALOG &#40;Transact-SQL&#41;](../../t-sql/statements/create-fulltext-catalog-transact-sql.md)   
 [DROP FULLTEXT CATALOG &#40;Transact-SQL&#41;](../../t-sql/statements/drop-fulltext-catalog-transact-sql.md)   
 [全文檢索搜尋](../../relational-databases/search/full-text-search.md)  
  
  
