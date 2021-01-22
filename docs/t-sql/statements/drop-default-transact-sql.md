---
description: DROP DEFAULT (Transact-SQL)
title: DROP DEFAULT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/10/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP_DEFAULT_TSQL
- DROP DEFAULT
dev_langs:
- TSQL
helpviewer_keywords:
- DROP DEFAULT statement
- defaults [SQL Server], removing
ms.assetid: d2d3af25-8877-46ba-95d9-1844961d97ee
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 2762305952b7c2e189a9c399f614974e1fc32e11
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98688772"
---
# <a name="drop-default-transact-sql"></a>DROP DEFAULT (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  從目前資料庫移除一或多個使用者自訂的預設值。  
  
> [!IMPORTANT]
>  DROP DEFAULT 會在 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的下一個版本中移除。 請勿在新的開發工作中使用 DROP DEFAULT，並規劃修改目前使用 DROP DEFAULT 的應用程式。 請改用預設定義，您可以利用 [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md) 或 [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md) 的 DEFAULT 關鍵字來建立預設定義。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DROP DEFAULT [ IF EXISTS ] { [ schema_name . ] default_name } [ ,...n ] [ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *IF EXISTS*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。  
  
 只有在預設值已存在時，才能有條件地將其卸除。  
  
 *schema_name*  
 這是預設值所屬的結構描述名稱。  
  
 *default_name*  
 這是現有預設值的名稱。 若要查看存在的預設清單，請執行 **sp_help**。 預設必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。 您可以選擇性地指定預設結構描述名稱。  
  
## <a name="remarks"></a>備註  
 在卸除預設之前，如果預設目前繫結到資料行或別名資料類型，請執行 **sp_unbindefault** 來解除預設的繫結。  
  
 在從允許 Null 值的資料行中卸除預設值之後，當加入資料列且未明確提供值時，會在這個位置插入 NULL。 從 NOT NULL 資料行中卸除預設值之後，當加入資料列且未明確提供值時，會傳回錯誤訊息。 稍後，會做為一般 INSERT 陳述式行為的一部份而加入這些資料列。  
  
## <a name="permissions"></a>權限  
 若要執行 DROP DEFAULT，使用者至少必須有預設值所屬結構描述的 ALTER 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-dropping-a-default"></a>A. 卸除預設值  
 如果預設值尚未繫結到資料行或別名資料類型，只能利用 DROP DEFAULT 來卸除它。 下列範例會移除名稱為 `datedflt` 的使用者建立預設值。  
  
```sql  
USE AdventureWorks2012;  
GO  
IF EXISTS (SELECT name FROM sys.objects  
         WHERE name = 'datedflt'   
            AND type = 'D')  
   DROP DEFAULT datedflt;  
GO  
```  
  
 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，您可以使用下列語法。  
  
```sql  
DROP DEFAULT IF EXISTS datedflt;  
GO  
```  
  
### <a name="b-dropping-a-default-that-has-been-bound-to-a-column"></a>B. 卸除已繫結到資料行的預設值  
 下列範例會將預設值和相關聯之 `EmergencyContactPhone` 資料表的 `Contact` 資料行解除繫結，再卸除名稱為 `phonedflt` 的預設值。  
  
```sql  
USE AdventureWorks2012;  
GO  
   BEGIN   
      EXEC sp_unbindefault 'Person.Contact.Phone'  
      DROP DEFAULT phonedflt  
   END;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE DEFAULT &#40;Transact-SQL&#41;](../../t-sql/statements/create-default-transact-sql.md)   
 [sp_helptext &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptext-transact-sql.md)   
 [sp_help &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-transact-sql.md)   
 [sp_unbindefault &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-unbindefault-transact-sql.md)  
