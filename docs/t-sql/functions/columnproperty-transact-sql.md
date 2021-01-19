---
description: COLUMNPROPERTY (Transact-SQL)
title: COLUMNPROPERTY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- COLUMNPROPERTY
- COLUMNPROPERTY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- column properties [SQL Server]
- parameters [SQL Server], properties
- COLUMNPROPERTY function
ms.assetid: 2408c264-6eca-4120-bb71-df043c7c2792
author: cawrites
ms.author: chadam
ms.openlocfilehash: 4bd06800692eb8f27494055dfa391169ced45803
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171360"
---
# <a name="columnproperty-transact-sql"></a>COLUMNPROPERTY (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

此函式會傳回資料行或參數資訊。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
COLUMNPROPERTY ( id , column , property )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*id*  
包含資料表或程序之識別碼 (ID) 的[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。
  
*column*  
包含資料行或參數名稱的運算式。
  
*property*  
針對 *id* 引數，*property* 引數會指定 `COLUMNPROPERTY` 函式所傳回的資訊類型。 *property* 引數可以具有下列任何一個值：
  
|值|描述|傳回的值|  
|---|---|---|
|**AllowsNull**|允許 Null 值|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**ColumnId**|對應於 **sys.columns.column_id** 的資料行識別碼值。|資料行識別碼<br /><br /> **注意：** 當查詢多個資料行時，資料行識別碼值順序可能會有間距。|  
|**FullTextTypeColumn**|資料表中保存 *column* 之文件類型資訊的 TYPE COLUMN。|此函式的第二個參數所傳遞之資料行名稱運算式的全文檢索 TYPE COLUMN 識別碼。|  
|**GeneratedAlwaysType**|是系統產生的資料行值。 對應到 **sys.columns.generated_always_type**|**適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本。<br /><br /> 0：不一律會產生<br /><br /> 1：一律作為資料列開頭產生<br /><br /> 2：一律作為資料列結尾產生|  
|**IsColumnSet**|資料行是資料行集。 如需詳細資訊，請參閱 [使用資料行集](../../relational-databases/tables/use-column-sets.md)。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**IsComputed**|資料行是一個計算資料行。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**IsCursorType**|程序參數的類型是 CURSOR。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**IsDeterministic**|資料行具有決定性。 這個屬性只適用於計算資料行和檢視資料行。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。 不是計算資料行或檢視資料行。|  
|**IsFulltextIndexed**|資料行已完成全文檢索索引的登錄。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**IsHidden**|是系統產生的資料行值。 對應至 **sys.columns.is_hidden**|**適用對象**：[!INCLUDE[ssCurrentLong](../../includes/sscurrent-md.md)] 及更新版本。<br /><br /> 0：不隱藏<br /><br /> 1：隱藏|  
|**IsIdentity**|資料行使用 IDENTITY 屬性。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**IsIdNotForRepl**|資料行會檢查 IDENTITY_INSERT 設定。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**IsIndexable**|資料行可以建立索引。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**IsOutParam**|程序參數是一個輸出參數。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**IsPrecise**|資料行是精確的。 這個屬性只適用於具決定性的資料行。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。 不是具決定性的資料行|  
|**IsRowGuidCol**|資料行具有 **uniqueidentifier** 資料類型，並且使用 ROWGUIDCOL 屬性所定義。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**IsSparse**|資料行是疏鬆資料行。 如需詳細資訊，請參閱 [使用疏鬆資料行](../../relational-databases/tables/use-sparse-columns.md)。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**IsSystemVerified**|[!INCLUDE[ssDE](../../includes/ssde-md.md)] 可以驗證資料行的決定性和有效位數屬性。 這個屬性只適用於計算資料行和檢視資料行。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**IsXmlIndexable**|XML 資料行可用於 XML 索引中。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**有效位數**|資料行或參數的資料類型長度。|指定的資料行資料類型長度<br /><br /> -1：**xml** 或大數值類型<br /><br /> NULL：無效的輸入。|  
|**縮放比例**|資料行或參數資料類型的小數位數。|小數位數值<br /><br /> NULL：無效的輸入。|  
|**StatisticalSemantics**|資料行啟用語意索引。|1：TRUE<br /><br /> 0：FALSE|  
|**SystemDataAccess**|資料行是從存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統目錄或虛擬系統資料表中之資料的函數衍生而來。 這個屬性只適用於計算資料行和檢視資料行。|1：TRUE (表示唯讀存取。)<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**UserDataAccess**|資料行是從存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 本機執行個體所儲存的使用者資料表 (包括檢視和暫存資料表) 中之資料的函數衍生而來。 這個屬性只適用於計算資料行和檢視資料行。|1：TRUE (表示唯讀存取。)<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
|**UsesAnsiTrim**|ANSI_PADDING 已在資料表建立時設定為 ON。 這個屬性只適用對象 **char** 或 **varchar** 類型的資料行或參數。|1：TRUE<br /><br /> 0：FALSE<br /><br /> NULL：無效的輸入。|  
  
## <a name="return-types"></a>傳回類型
 **int**  
  
## <a name="exceptions"></a>例外狀況  
發生錯誤或呼叫端沒有檢視物件的權限時，會傳回 NULL。
  
使用者只能檢視使用者擁有或被授與某些權限之安全性實體的中繼資料。 這表示，如果使用者沒有物件的正確權限，則發出中繼資料的內建函式 (例如 `COLUMNPROPERTY`) 可能會傳回 NULL。 如需詳細資訊，請參閱[中繼資料可見性設定](../../relational-databases/security/metadata-visibility-configuration.md)。
  
## <a name="remarks"></a>備註  
檢查資料行的決定性屬性時，請先測試資料行是否為計算資料行。 如果是非計算資料行，**IsDeterministic** 引數會傳回 NULL。 您可以將計算資料行指定成索引資料行。
  
## <a name="examples"></a>範例  
此範例會傳回 `LastName` 資料行的長度。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT COLUMNPROPERTY( OBJECT_ID('Person.Person'),'LastName','PRECISION')AS 'Column Length';  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```
Column Length
-------------
50
```  
  
## <a name="see-also"></a>請參閱
[中繼資料函式 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)  
[TYPEPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/typeproperty-transact-sql.md)
  
  
