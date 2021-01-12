---
description: CREATE FULLTEXT CATALOG (Transact-SQL)
title: CREATE FULLTEXT CATALOG (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 09/12/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CATALOG_TSQL
- CREATE_FULLTEXT_TSQL
- FULLTEXT_TSQL
- FULLTEXT CATALOG
- CREATE FULLTEXT CATALOG
- CREATE_FULLTEXT_CATALOG_TSQL
- CATALOG
- FULLTEXT_CATALOG_TSQL
- CREATE FULLTEXT
- FULLTEXT
dev_langs:
- TSQL
helpviewer_keywords:
- full-text catalogs [SQL Server], creating
- CREATE FULLTEXT CATALOG statement
ms.assetid: d7a8bd93-e2d7-4a40-82ef-39069e65523b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 4d3520d2a9d8c1c90507bb52742bd888eb1925cd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095834"
---
# <a name="create-fulltext-catalog-transact-sql"></a>CREATE FULLTEXT CATALOG (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  建立資料庫的全文檢索目錄。 單一全文檢索目錄可以有多個全文檢索索引，但一個全文檢索索引只能屬於單一全文檢索目錄。 每個資料庫都可以有零或多個全文檢索目錄。  
  
 您不能在 **master**、**model** 或 **tempdb** 資料庫中建立全文檢索目錄。  
  
> [!IMPORTANT]  
>  從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 開始，全文檢索目錄是虛擬物件，而且不屬於任何檔案群組。 全文檢索目錄是參考一組全文檢索索引的邏輯概念。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
CREATE FULLTEXT CATALOG catalog_name  
     [ON FILEGROUP filegroup ]  
     [IN PATH 'rootpath']  
     [WITH <catalog_option>]  
     [AS DEFAULT]  
     [AUTHORIZATION owner_name ]  
  
<catalog_option>::=  
     ACCENT_SENSITIVITY = {ON|OFF}  
  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *catalog_name*  
 這是新目錄的名稱。 在目前資料庫的所有目錄名稱之間，目錄名稱必須是唯一的。 另外，在資料庫的所有檔案之間，對應於全文檢索目錄的檔案名稱 (請參閱 ON FILEGROUP) 也必須是唯一的。 如果資料庫中已有另一個目錄在使用這個目錄名稱，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回錯誤。  
  
 目錄名稱的長度不能超出 120 個字元。  
  
 ON FILEGROUP *filegroup*  
 從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 開始，這個子句不會有任何作用。  
  
 IN PATH **'** _rootpath_ **'**  
 > [!NOTE]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
 從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 開始，這個子句不會有任何作用。  
  
 ACCENT_SENSITIVITY = {ON|OFF}  
 指定全文檢索索引的目錄是否區分腔調字。 當這個屬性有了改變時，您必須重建索引。 預設值是使用資料庫定序所指定的區分腔調字屬性。 若要顯示資料庫定序，請使用 **sys.databases** 目錄檢視。  
  
 若要判斷全文檢索目錄目前的區分重音字屬性設定，請針對 *catalog_name*，搭配 **accentsensitivity** 屬性值來使用 FULLTEXTCATALOGPROPERTY 函數。 如果傳回的值是 '1'，全文檢索目錄就會區分腔調字；如果傳回的值是 '0'，目錄就不會區分腔調字。  
  
 AS DEFAULT  
 指定這個全文檢索目錄是預設目錄。 當建立全文檢索索引，卻沒有明確指定全文檢索目錄時，便使用預設目錄。 如果現有的全文檢索目錄已標示了 AS DEFAULT，將這個新目錄設成 AS DEFAULT，會使這個目錄成為預設的全文檢索目錄。  
  
 AUTHORIZATION *owner_name*  
 將全文檢索目錄的擁有者設為資料庫使用者或角色的名稱。 如果 *owner_name* 是某個角色，該角色必須是目前使用者所屬角色的名稱，否則，執行陳述式的使用者必須是資料庫擁有者或系統管理員。  
  
 如果 *owner_name* 是某個使用者名稱，這個使用者名稱必須是下列其中之一：  
  
-   執行陳述式的使用者名稱。  
  
-   執行命令的使用者有其模擬權限的使用者名稱。  
  
-   或者，執行命令的使用者必須是資料庫擁有者或系統管理員。  
  
 *owner_name* 也必須取得指定之全文檢索目錄的 TAKE OWNERSHIP 權限。  
  
## <a name="remarks"></a>備註  
 全文檢索目錄識別碼從 00005 開始，每次新增一個目錄時識別碼便增加一號。  
  
## <a name="permissions"></a>權限  
 使用者必須具有資料庫的 CREATE FULLTEXT CATALOG 權限，或者是 **db_owner** 或 **db_ddladmin** 固定資料庫角色的成員。  
  
## <a name="examples"></a>範例  
 下列範例會建立全文檢索目錄和全文檢索索引。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE FULLTEXT CATALOG ftCatalog AS DEFAULT;  
GO  
CREATE FULLTEXT INDEX ON HumanResources.JobCandidate(Resume) KEY INDEX PK_JobCandidate_JobCandidateID;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.fulltext_catalogs &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-fulltext-catalogs-transact-sql.md)   
 [ALTER FULLTEXT CATALOG &#40;Transact-SQL&#41;](../../t-sql/statements/alter-fulltext-catalog-transact-sql.md)   
 [DROP FULLTEXT CATALOG &#40;Transact-SQL&#41;](../../t-sql/statements/drop-fulltext-catalog-transact-sql.md)   
 [全文檢索搜尋](../../relational-databases/search/full-text-search.md)   
 
  
  
