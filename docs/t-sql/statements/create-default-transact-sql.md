---
description: CREATE DEFAULT (Transact-SQL)
title: CREATE DEFAULT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/25/2015
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE_DEFAULT_TSQL
- CREATE DEFAULT
dev_langs:
- TSQL
helpviewer_keywords:
- objects [SQL Server], default
- default objects
- CREATE DEFAULT statement
- objects [SQL Server], creating
- DEFAULT definition
ms.assetid: 08475db4-7d90-486a-814c-01a99d783d41
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 7dd06b9daff476b26667c0ef5900f34325b05f7c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100877"
---
# <a name="create-default-transact-sql"></a>CREATE DEFAULT (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

建立一個稱為預設值的物件。 當繫結到某個資料行或別名資料類型，且在插入作業期間未明確提供任何值時，預設值會指定要插入物件所繫結之資料行 (如果是別名資料類型，則是所有資料行) 的值。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]請改為使用透過 ALTER TABLE 或 CREATE TABLE 的 DEFAULT 關鍵字所建立的預設定義。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
CREATE DEFAULT [ schema_name . ] default_name   
AS constant_expression [ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*schema_name*  
 預設值所屬的結構描述名稱。  
  
*default_name*  
 預設值的名稱。 預設名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。 您可以選擇性地指定預設擁有者名稱。  
  
*constant_expression*  
只包含常數值的[運算式](../../t-sql/language-elements/expressions-transact-sql.md) (其中不能有任何資料行或其他資料庫物件的名稱)。 您可以使用任何常數、內建函式或數學運算式，但包含別名資料類型的項目除外。 使用者自訂函式則不能使用。 請用單引號 (**'**) 括住字元和日期常數；貨幣、整數和浮點數常數不需要引號。 二進位資料的前面必須是 0x，貨幣資料的前面必須是錢幣符號 ($)。 預設值必須相容於資料行資料類型。  
  
## <a name="remarks"></a>備註  
 您只能在目前資料庫中建立預設名稱。 在資料庫內，必須藉由結構描述，使預設名稱成為唯一。 當您建立預設值時，請使用 **sp_bindefault**，將它繫結到資料行或別名資料類型。  
  
 如果預設值與所繫結的資料行不相容，則在嘗試插入預設值時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會產生一則錯誤訊息。 例如，N/A 不能作為 **numeric** 資料行的預設值。  
  
 如果預設值對它所繫結的資料行而言太長，系統就會截斷這個值。  
  
 您不能在單一批次中一併使用 CREATE DEFAULT 陳述式與其他 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。  
  
 您必須先卸除預設值，才能建立同名的新預設值。 此外，在卸除預設值之前，您必須先執行 **sp_unbindefault** 來解除預設值的繫結。  
  
 如果資料行有預設值和相關聯的規則，預設值便不能違反規則。 永遠不會插入與規則衝突的預設值，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 每次嘗試插入預設值時，都會產生一則錯誤訊息。  
  
 當繫結到資料行時，在下列情況下，會插入預設值：  
  
-   未明確插入值。  
  
-   搭配 INSERT 使用 DEFAULT VALUES 或 DEFAULT 關鍵字來插入預設值。  
  
 如果您在建立資料行時指定 NOT NULL，但並未建立其預設值，則當使用者無法於該資料行中建立項目時，便會產生錯誤訊息。 下表說明預設值的存在與資料行定義為 NULL 或 NOT NULL 之間的關聯性。 資料表中各項目顯示其結果。  
  
|資料行定義|無項目，無預設值|無項目，有預設值|輸入 NULL，無預設值|輸入 NULL，有預設值|  
|-----------------------|--------------------------|-----------------------|----------------------------|-------------------------|  
|**NULL**|NULL|default|NULL|NULL|  
|**NOT NULL**|錯誤|default|error|error|  
  
 若要重新命名預設值，請使用 **sp_rename**。 如需預設值的報表，請使用 **sp_help**。  
  
## <a name="permissions"></a>權限  
 若要使用 CREATE DEFAULT，使用者必須至少具備目前資料庫中的 CREATE DEFAULT 權限，以及要建立預設值之結構描述的 ALTER 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-a-simple-character-default"></a>A. 建立簡單字元預設值  
 下列範例會建立一個稱為 `unknown` 的字元預設值。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE DEFAULT phonedflt AS 'unknown';  
```  
  
### <a name="b-binding-a-default"></a>B. 繫結預設值  
 下列範例會繫結 A 範例中所建立的預設值。只有在 `Phone` 資料表的 `Contact` 資料行中沒有指定任何項目時，預設值才會生效。 
 
 > [!Note] 
 >  省略任何項目與在 INSERT 陳述式中明確地陳述 NULL 並不相同。  
  
 由於名稱為 `phonedflt` 的預設值不存在，因此下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式會失敗。 這個範例只供說明。  
  
```sql  
USE AdventureWorks2012;  
GO  
sp_bindefault 'phonedflt', 'Person.PersonPhone.PhoneNumber';  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [CREATE RULE &#40;Transact-SQL&#41;](../../t-sql/statements/create-rule-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [DROP DEFAULT &#40;Transact-SQL&#41;](../../t-sql/statements/drop-default-transact-sql.md)   
 [DROP RULE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-rule-transact-sql.md)   
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [sp_bindefault &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-bindefault-transact-sql.md)   
 [sp_help &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-transact-sql.md)   
 [sp_helptext &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptext-transact-sql.md)   
 [sp_rename &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-rename-transact-sql.md)   
 [sp_unbindefault &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-unbindefault-transact-sql.md)  
  
  
