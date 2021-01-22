---
description: DBCC DBREINDEX (Transact-SQL)
title: DBCC DBREINDEX (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/16/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DBCC DBREINDEX
- DBREINDEX_TSQL
- DBREINDEX
- DBCC_DBREINDEX_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- index rebuilding [SQL Server]
- rebuilding indexes
- dynamic index rebuilding [SQL Server]
- DBCC DBREINDEX statement
ms.assetid: 6e929d09-ccb5-4855-a6af-b616022bc8f6
author: pmasl
ms.author: umajay
ms.openlocfilehash: ed6617453805361b5e8c1ddb888495c0cc5d1cb0
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596671"
---
# <a name="dbcc-dbreindex-transact-sql"></a>DBCC DBREINDEX (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
在指定的資料庫中，重建資料表的一或多個索引。
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)] 請改用 [ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md)。  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DBCC DBREINDEX   
(   
    table_name   
    [ , index_name [ , fillfactor ] ]  
)  
    [ WITH NO_INFOMSGS ]   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *table_name*  
 這是包含要重建之指定索引的資料表名稱。 資料表名稱必須遵照 [識別碼](../../relational-databases/databases/database-identifiers.md)*的規則。*  
  
 *index_name*  
 這是要重建的索引名稱。 索引名稱必須符合識別碼的規則。 如果指定 *index_name*，則必須指定 *table_name*。 如果未指定 *index_name* 或其為 " "，就會重建資料表的所有索引。  
  
 *fillfactor*  
 這是建立或重建索引時，每個索引頁面上用來儲存資料的空間百分比。 當建立索引時，*fillfactor* 會取代填滿因數，而成為索引的新預設值，或成為因重建叢集索引而重建的任何其他非叢集索引的新預設值。  
 當 *fillfactor* 是 0 時，DBCC DBREINDEX 會使用最後指定給索引的填滿因數值。 這個值儲存在 **sys.indexes** 目錄檢視中。   
 如果指定 *fillfactor*，則必須指定 *table_name* 和 *index_name*。 如果未指定 *fillfactor*，就會使用預設填滿因數 100。 如需詳細資訊，請參閱 [指定索引的填滿因素](../../relational-databases/indexes/specify-fill-factor-for-an-index.md)。  
  
 WITH NO_INFOMSGS  
 抑制所有嚴重性層級在 0 到 10 的參考用訊息。  
  
## <a name="remarks"></a>備註  
DBCC DBREINDEX 會重建資料表的索引，或定義給資料表的所有索引。 在允許動態重建索引時，您不需要卸除再重新建立 PRIMARY KEY 或 UNIQUE 條件約束，就可以重建強制執行 PRIMARY KEY 或 UNIQUE 條件約束的索引。 這表示您不需要知道資料表或其條件約束的結構，就能重建索引。 在資料大量複製到資料表時，就可能發生這個情況。

DBCC DBREINDEX 可以在單一陳述式中，重建資料表的所有索引。 這比編寫多個 DROP INDEX 和 CREATE INDEX 陳述式簡單。 由於工作是用單一陳述式來執行的，因此，DBCC DBREINDEX 會自動成為不可部分完成；個別的 DROP INDEX 和 CREATE INDEX 陳述式則必須包括在交易內，才能成為不可部分完成。 另外，DBCC DBREINDEX 提供的最佳化程度超出個別的 DROP INDEX 和 CREATE INDEX 陳述式。

DBCC DBREINDEX 不像 DBCC INDEXDEFRAG 或設定了 REORGANIZE 選項的 ALTER INDEX，它是一項離線作業。 如果重建非叢集索引，在作業期間，會保留相關資料表的共用鎖定。 這可以防止修改資料表。 如果重建叢集索引，就會保留獨佔資料表鎖定。 這會防止任何資料表存取作業，因而可以使資料表有效離線。 請利用設定了 ONLINE 選項的 ALTER INDEX REBUILD 陳述式，在線上重建索引，或在重建索引的作業期間，控制平行處理原則的程度。

如需有關選取方法來重建或重新組織索引的詳細資訊，請參閱[重新組織與重建索引](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)。
  
## <a name="restrictions"></a>限制  
不支援下列物件使用 DBCC DBREINDEX：
-   系統資料表  
-   空間索引  
-   xVelocity 記憶體最佳化的資料行存放區索引  
  
## <a name="result-sets"></a>結果集  
除非指定了 NO_INFOMSGS (必須指定資料表名稱)，否則 DBCC DBREINDEX 一定會傳回：
  
```sql
DBCC execution completed. If DBCC printed error messages, contact your system administrator.  
```  
  
## <a name="permissions"></a>權限  
呼叫端必須擁有資料表，或是系統管理員 **sysadmin** 固定伺服器角色、**db_owner** 固定資料庫角色，或 **db_ddladmin** 固定資料庫角色的成員。
  
## <a name="examples"></a>範例  
### <a name="a-rebuilding-an-index"></a>A. 重建索引  
下列範例會在 `Employee_EmployeeID` 資料庫的 `80` 資料表上，利用填滿因數 `Employee` 來重建 `AdventureWorks` 叢集索引。
  
```sql  
USE AdventureWorks2012;   
GO  
DBCC DBREINDEX ('HumanResources.Employee', PK_Employee_BusinessEntityID,80);  
GO  
```  
  
### <a name="b-rebuilding-all-indexes"></a>B. 重建所有索引  
下列範例利用填滿因數值 `Employee` 來重建 `AdventureWorks` 資料庫的 `70` 資料表的所有索引。
  
```sql
USE AdventureWorks2012;   
GO  
DBCC DBREINDEX ('HumanResources.Employee', ' ', 70);  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
[ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)  
[CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)  
[DBCC &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-transact-sql.md)  
[sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)  
[sys.dm_db_index_physical_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)  
[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)  
  
