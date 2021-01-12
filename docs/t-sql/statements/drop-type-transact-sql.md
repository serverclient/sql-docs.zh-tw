---
description: DROP TYPE (Transact-SQL)
title: DROP TYPE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/12/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP TYPE
- DROP_TYPE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- user-defined types [SQL Server], deleting
- UDTs [SQL Server], deleting
- alias data types [SQL Server], removing
- DROP TYPE statement
ms.assetid: 11bf83f9-0718-4238-a835-83d2eb14ae7b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f7dc3dcd3f1fa87330bda0865160341eec462a86
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093431"
---
# <a name="drop-type-transact-sql"></a>DROP TYPE (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  從目前資料庫移除別名資料類型或 Common Language Runtime (CLR) 使用者自訂類型。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DROP TYPE [ IF EXISTS ] [ schema_name. ] type_name [ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *IF EXISTS*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 至 [目前版本](https://go.microsoft.com/fwlink/p/?LinkId=299658))。  
  
 只有在類型已存在時，才能有條件地將其卸除。  
  
 *schema_name*  
 這是別名或使用者自訂類型所屬的結構描述名稱。  
  
 *type_name*  
 這是您要卸除的別名資料類型名稱或使用者自訂類型名稱。  
  
## <a name="remarks"></a>備註  
 當出現下列中的任何狀況時，都不會執行 DROP TYPE 陳述式：  
  
-   資料庫中有資料表包含別名資料類型或使用者自訂類型的資料行。 您可以查詢 [sys.columns](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md) 或 [sys.column_type_usages](../../relational-databases/system-catalog-views/sys-column-type-usages-transact-sql.md) 目錄檢視來取得別名或使用者定義類型資料行的相關資訊。  
  
-   計算資料行、CHECK 條件約束、結構描述繫結的檢視以及結構描述繫結的函數之定義會參考別名或使用者自訂類型。 您可以透過查詢 [sys.sql_expression_dependencies](../../relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql.md) 目錄檢視來取得這些參考的資訊。  
  
-   資料庫中建立了若干函數、預存程序或觸發程序，且這些常式使用別名或使用者自訂類型的變數或參數。 您可以查詢 [sys.parameters](../../relational-databases/system-catalog-views/sys-parameters-transact-sql.md) 或 [sys.parameter_type_usages](../../relational-databases/system-catalog-views/sys-parameter-type-usages-transact-sql.md) 目錄檢視，來取得別名或使用者定義類型參數的相關資訊。  
  
## <a name="permissions"></a>權限  
 需要 *type_name* 的 CONTROL 權限或 *schema_name* 的 ALTER 權限。  
  
## <a name="examples"></a>範例  
 下列範例假設目前資料庫中已建立了名稱為 `ssn` 的類型。  
  
```sql  
DROP TYPE ssn ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/create-type-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  
