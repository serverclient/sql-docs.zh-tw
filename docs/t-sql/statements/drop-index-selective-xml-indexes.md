---
description: DROP INDEX (選擇性 XML 索引)
title: DROP INDEX (選擇性 XML 索引) | Microsoft Docs
ms.custom: ''
ms.date: 08/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP XML INDEX statement
dev_langs:
- TSQL
ms.assetid: 4779ae84-e5f4-4d04-8fc1-e24a6631b428
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 10a8dce89cff5a8201c583b9479a774d44a72ade
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096951"
---
# <a name="drop-index-selective-xml-indexes"></a>DROP INDEX (選擇性 XML 索引)
[!INCLUDE [SQL Server Azure SQL Database ](../../includes/applies-to-version/sql-asdb.md)]

  卸除 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中現有的選擇性 XML 索引或次要選擇性 XML 索引。 如需詳細資訊，請參閱[選擇性 XML 索引 &#40;SXI&#41;](../../relational-databases/xml/selective-xml-indexes-sxi.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DROP INDEX index_name ON <object>  
    [ WITH ( <drop_index_option> [ ,...n ] ) ]  
  
<object> ::=  
{ database_name.schema_name.table_or_view_name | schema_name.table_or_view_name | table_or_view_name }  
  
<drop_index_option> ::=  
{  
    MAXDOP = max_degree_of_parallelism  
    | ONLINE = { ON | OFF }  
}  
```  
  
##  <a name="arguments"></a><a name="Arguments"></a> 引數  
 *index_name*  
 這是要卸除之現有索引的名稱。  
  
 *\< object>* 這是包含 XML 資料行 (已編製索引) 的資料表。 請使用下列其中一個格式：  
  
-   `database_name.schema_name.table_name`  
  
-   `database_name..table_name`  
  
-   `schema_name.table_name`  
  
-   `table_name`  
  
 *\<drop_index_option>* 如需有關卸除索引選項的資訊，請參閱 [DROP INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/drop-index-transact-sql.md)。  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要有資料表或檢視的 ALTER 權限才能執行 DROP INDEX。 根據預設，這項權限會授與系統管理員 (sysadmin) 固定伺服器角色以及 db_ddladmin 和 db_owner 固定資料庫角色。  
  
## <a name="example"></a>範例  
 下列範例顯示 DROP INDEX 陳述式。  
  
```sql  
DROP INDEX sxi_index ON tbl;  
```  
  
## <a name="see-also"></a>另請參閱  
 [選擇性 XML 索引 &#40;SXI&#41;](../../relational-databases/xml/selective-xml-indexes-sxi.md)   
 [建立、修改和卸除選擇性 XML 索引](../../relational-databases/xml/create-alter-and-drop-selective-xml-indexes.md)  
  
  

