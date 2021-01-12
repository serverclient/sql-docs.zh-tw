---
description: SET DATEFORMAT (Transact-SQL)
title: SET DATEFORMAT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DATEFORMAT
- SET DATEFORMAT
- SET_DATEFORMAT_TSQL
- DATEFORMAT_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- dates [SQL Server], formats
- dates [SQL Server], ordering date parts
- SET DATEFORMAT option [SQL Server]
- DATEFORMAT option [SQL Server]
- date and time [SQL Server], SET DATEFORMAT
- options [SQL Server], date
- date and time [SQL Server], DATEFORMAT
- dateparts [SQL Server], dateformat
ms.assetid: da217878-7ec4-477e-aa13-604073c948f8
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 51300c851c474b2326de5fe219fe476b5793c2c7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091878"
---
# <a name="set-dateformat-transact-sql"></a>SET DATEFORMAT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  設定月份、日期與年份日期部分的順序，以解譯日期字元字串。 這些字串的類型為 **date**、**smalldatetime**、**datetime**、**datetime2** 或 **datetimeoffset**。  
  
 如需所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間資料類型與函數的概觀，請參閱[日期和時間資料類型與函數 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
SET DATEFORMAT { format | @format_var }   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *format* |  **@** _format_var_  
 這是日期部分的順序。 有效的參數為 **mdy**、**dmy**、**ymd**、**ydm**、**myd** 和 **dym**。 這個引數可以是 Unicode 或轉換成 Unicode 的雙位元組字集 (DBCS)。 美國美國英文的預設值是 **mdy**。 如需所有支援語言的預設 DATEFORMAT，請參閱 [sp_helplanguage &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helplanguage-transact-sql.md)。  
  
## <a name="remarks"></a>備註  
 **date**、**datetime2** 和 **datetimeoffset** 資料類型不支援 DATEFORMAT **ydm**。  
  
 DATEFORMAT 設定會針對日期資料類型以不同方式解譯字元字串，視其字串格式而定。 例如，**datetime** 和 **smalldatetime** 解譯可能不符合 **date**、**datetime2** 或 **datetimeoffset**。 DATEFORMAT 會影響字元字串針對資料庫轉換成日期值的解譯方式。 但是，它並不會影響日期資料類型值的顯示，或其在資料庫中的儲存格式。  
  
 某些字元字串格式 (例如 ISO 8601) 的解譯就與 DATEFORMAT 設定無關。  
  
 SET DATEFORMAT 的設定是在執行階段進行設定，而不是在剖析階段進行設定。  
  
 SET DATEFORMAT 會覆寫 [SET LANGUAGE](../../t-sql/statements/set-language-transact-sql.md) 的隱含日期格式設定。  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。  
  
## <a name="examples"></a>範例  
 下列範例會使用不同的日期字串當做工作階段的輸入，並搭配相同的 `DATEFORMAT` 設定。  
  
```sql
-- Set date format to day/month/year.  
SET DATEFORMAT dmy;  
GO  
DECLARE @datevar DATETIME2 = '31/12/2008 09:01:01.1234567';  
SELECT @datevar;  
GO  
-- Result: 2008-12-31 09:01:01.123  
SET DATEFORMAT dmy;  
GO  
DECLARE @datevar DATETIME2 = '12/31/2008 09:01:01.1234567';  
SELECT @datevar;  
GO  
-- Result: Msg 241: Conversion failed when converting date and/or time -- from character string.  
  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)  

