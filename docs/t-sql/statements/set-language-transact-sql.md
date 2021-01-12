---
description: SET LANGUAGE (Transact-SQL)
title: SET LANGUAGE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/05/2017
ms.prod: sql
ms.prod_service: sql-data-warehouse, database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SET_LANGUAGE_TSQL
- SET LANGUAGE
dev_langs:
- TSQL
helpviewer_keywords:
- LANGUAGE option
- languages [SQL Server], setting language
- SET LANGUAGE statement
- options [SQL Server], date
- default languages
ms.assetid: 0ec0e5cf-e115-4be9-a0db-e65837d6fa45
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 0f4d0449b0945f833711297dd6e6bcb14a139acd
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093356"
---
# <a name="set-language-transact-sql"></a>SET LANGUAGE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  指定工作階段的語言環境。 工作階段語言決定 **datetime** 格式和系統訊息。  
  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
SET LANGUAGE { [ N ] 'language' | @language_var }   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 [**N**] **'** _language_ **'**  |  **@** _language\_var_  
 這是符合 [sys.syslanguages](../../relational-databases/system-compatibility-views/sys-syslanguages-transact-sql.md) 中所儲存的語言名稱。 這個引數可以是 Unicode 或轉換成 Unicode 的 DBCS。 若要用 Unicode 來指定語言，請使用 **N'** _language_ **'** 。 如果指定為變數，變數必須是 **sysname**。  
  
## <a name="remarks"></a>備註  
 SET LANGUAGE 的設定是在執行階段進行設定，而不是在剖析階段進行設定。  
  
 SET LANGUAGE 會隱含地設定 [SET DATEFORMAT](../../t-sql/statements/set-dateformat-transact-sql.md) 的設定。  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。  
  
## <a name="examples"></a>範例  
 下列範例會將預設語言設為 `Italian`，顯示月份名稱，再切換回 `us_english` 並重新顯示月份名稱。  
  
```sql
DECLARE @Today DATETIME;  
SET @Today = '12/5/2007';  
  
SET LANGUAGE Italian;  
SELECT DATENAME(month, @Today) AS 'Month Name';  
  
SET LANGUAGE us_english;  
SELECT DATENAME(month, @Today) AS 'Month Name' ;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [syslanguages](../../relational-databases/system-compatibility-views/sys-syslanguages-transact-sql.md)   
 [sp_helplanguage &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helplanguage-transact-sql.md)   
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)  
  
  
