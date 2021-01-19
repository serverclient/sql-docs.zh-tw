---
description: DROP RULE (Transact-SQL)
title: DROP RULE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/11/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP_RULE_TSQL
- DROP RULE
dev_langs:
- TSQL
helpviewer_keywords:
- rules [SQL Server], removing
- deleting roles
- DROP RULE statement
- removing roles
- dropping roles
ms.assetid: 8370b730-7fd5-43fe-a7f6-8300b3caa16d
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 81dd188416fd57274dbfd2ba587ddd9fdde1f51c
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172600"
---
# <a name="drop-rule-transact-sql"></a>DROP RULE (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  從目前資料庫移除一或多個使用者自訂的規則。  
  
> [!IMPORTANT]
>  DROP DEFAULT 會在 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的下一個版本中移除。 請勿在新的開發工作中使用 DROP RULE，並規劃修改目前使用 DROP RULE 的應用程式。 請改用 CHECK 條件約束，您可以利用 [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md) 或 [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md) 的 CHECK 關鍵字加以建立。 如需詳細資訊，請參閱 [Unique Constraints and Check Constraints](../../relational-databases/tables/unique-constraints-and-check-constraints.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DROP RULE [ IF EXISTS ] { [ schema_name . ] rule_name } [ ,...n ] [ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *IF EXISTS*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](https://go.microsoft.com/fwlink/p/?LinkId=299658))。  
  
 只有在規則已存在時，才能有條件地將其卸除。  
  
 *schema_name*  
 這是規則所屬的結構描述名稱。  
  
 *rule*  
 這是要移除的規則。 規則名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。 您可以選擇性地指定規則結構描述名稱。  
  
## <a name="remarks"></a>備註  
 若要卸除規則，且規則目前繫結了資料行或別名資料類型，請先將它解除繫結。 若要解除繫結規則，請使用 **sp_unbindrule**。 如果您試圖卸除規則時，規則仍是繫結的，便會出現一則錯誤訊息，且會取消 DROP RULE 陳述式。  
  
 在卸除規則之後，會輸入規則先前管理的資料行中所輸入的新資料，但不會有規則的條件約束。 現有資料完全不會受到影響。  
  
 DROP RULE 陳述式不適用於 CHECK 條件約束。 如需有關卸除 CHECK 條件約束的詳細資訊，請參閱 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)。  
  
## <a name="permissions"></a>權限  
 若要執行 DROP RULE，使用者至少必須有規則所屬結構描述的 ALTER 權限。  
  
## <a name="examples"></a>範例  
 下列範例會解除繫結再卸除名稱為 `VendorID_rule` 的規則。 
  
```sql  
sp_unbindrule 'Production.ProductVendor.VendorID'  
DROP RULE VendorID_rule  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE RULE &#40;Transact-SQL&#41;](../../t-sql/statements/create-rule-transact-sql.md)   
 [sp_bindrule &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-bindrule-transact-sql.md)   
 [sp_help &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-transact-sql.md)   
 [sp_helptext &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptext-transact-sql.md)   
 [sp_unbindrule &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-unbindrule-transact-sql.md)   
 [USE &#40;Transact-SQL&#41;](../../t-sql/language-elements/use-transact-sql.md)  

