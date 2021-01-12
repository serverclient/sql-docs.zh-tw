---
description: DROP FUNCTION (Transact-SQL)
title: DROP FUNCTION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/11/2020
ms.prod: sql
ms.prod_service: sql-data-warehouse, database-engine, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP_FUNCTION_TSQL
- DROP FUNCTION
dev_langs:
- TSQL
helpviewer_keywords:
- user-defined functions [SQL Server], removing
- removing user-defined functions
- DROP FUNCTION statement
- dropping user-defined functions
- deleting user-defined functions
ms.assetid: ee5ad283-9e44-4109-902f-0ce12669ee11
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 655cf93adfd1fbc84d6ab6333fb42e88a66826dd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096937"
---
# <a name="drop-function-transact-sql"></a>DROP FUNCTION (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  從目前資料庫移除一或多個使用者自訂函數。 使用者定義函數是使用 [CREATE FUNCTION](../../t-sql/statements/create-function-transact-sql.md) 建立的，並使用 [ALTER FUNCTION](../../t-sql/statements/alter-function-transact-sql.md) 加以修改。  
  
 DROP 函數支援原生編譯的純量使用者定義函數。 如需詳細資訊，請參閱[記憶體內部 OLTP 的純量使用者定義函數](../../relational-databases/in-memory-oltp/scalar-user-defined-functions-for-in-memory-oltp.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
 -- SQL Server, Azure SQL Database 

DROP FUNCTION [ IF EXISTS ] { [ schema_name. ] function_name } [ ,...n ]   
[;]
```

```syntaxsql
 -- Azure Synapse Analytics, Parallel Data Warehouse 

DROP FUNCTION [IF EXISTS] [ schema_name. ] function_name
[;] 
```  
   
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *IF EXISTS*    
 只有在函數已存在時，才能有條件地將其卸除。 從 [!INCLUDE[ssnoversion_md](../../includes/ssnoversion-md.md)] 2016 和 [!INCLUDE[sssds_md](../../includes/sssds-md.md)] 開始可用。
  
 *schema_name*  
 這是使用者定義函數所屬的結構描述名稱。  
  
 *function_name*  
 這是要移除的使用者自訂函數名稱。 您可以選擇性地指定結構描述名稱。 不能指定伺服器名稱和資料庫名稱。  
  
## <a name="remarks"></a>備註  
 如果資料庫中有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 函數或檢視參考這個函數，並且是利用 SCHEMABINDING 加以建立；或者如果有計算資料行、CHECK 條件約束或 DEFAULT 條件約束參考這個函數，DROP FUNCTION 就不會成功。  
  
 如果有計算資料行參考這個函數，而且已經產生索引，DROP FUNCTION 就不會成功。  
  
## <a name="permissions"></a>權限  
 若要執行 DROP FUNCTION，使用者至少必須對該函數所屬的結構描述具備 ALTER 權限，或是對該函數具備 CONTROL 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-dropping-a-function"></a>A. 卸除函數  
 下列範例會從 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 範例資料庫的 `Sales` 結構描述，卸除 `fn_SalesByStore` 使用者定義的函數。 若要建立此函數，請參閱範例 B 中的 [CREATE FUNCTION & #40;TRANSACT-SQL & #41;](../../t-sql/statements/create-function-transact-sql.md)。  
  
```sql  
DROP FUNCTION Sales.fn_SalesByStore;  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-function-transact-sql.md)   
 [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md)   
 [OBJECT_ID &#40;Transact-SQL&#41;](../../t-sql/functions/object-id-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md)   
 [sys.parameters &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-parameters-transact-sql.md)  
  
  
