---
description: CREATE RULE (Transact-SQL)
title: CREATE RULE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- RULE_TSQL
- CREATE RULE
- CREATE_RULE_TSQL
- RULE
dev_langs:
- TSQL
helpviewer_keywords:
- list rules [SQL Server]
- unbinding rules
- pattern rules [SQL Server]
- range rules [SQL Server]
- alias data types [SQL Server], rules
- CREATE RULE statement
- reports [SQL Server], rules
- status information [SQL Server], rules
- rules [SQL Server], precedence
- binding rules [SQL Server]
- rules [SQL Server], creating
ms.assetid: b016a289-3a74-46b1-befc-a13183be51e4
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 9fc137ea5a6c2b48a523f758619c129133b6a4eb
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091980"
---
# <a name="create-rule-transact-sql"></a>CREATE RULE (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  建立一個稱為規則的物件。 當繫結到資料行或別名資料型別時，規則會指定能夠插入這個資料行的可接受的值。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]我們建議您改用檢查條件約束。 CHECK 條件約束是利用 CREATE TABLE 或 ALTER TABLE 的 CHECK 關鍵字來建立的。 如需詳細資訊，請參閱 [Unique Constraints and Check Constraints](../../relational-databases/tables/unique-constraints-and-check-constraints.md)。  
  
 資料行或別名資料型別只能有一個繫結的規則。 不過，資料行可以同時有一個規則以及一個或多個相關聯的檢查條件約束。 在這個情況下，會評估所有限制。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE RULE [ schema_name . ] rule_name   
AS condition_expression  
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *schema_name*  
 這是規則所屬的結構描述名稱。  
  
 *rule_name*  
 這是新規則的名稱。 規則名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。 您可以選擇性地指定規則擁有者名稱。  
  
 *condition_expression*  
 這是定義規則的一個或多個條件。 規則可以是 WHERE 子句中任何有效的運算式，可以包括算術運算子、關係運算子和述詞 (如 IN、LIKE、BETWEEN) 之類的元素。 規則不能參考資料行或其他資料庫物件。 未參考資料庫物件的內建函數可以包括在內。 無法使用使用者自訂函數。  
  
 *condition_expression* 包括一個變數。 每個區域變數前面都會有 @ 記號 ( **@** )。 這個運算式參考 UPDATE 或 INSERT 陳述式所輸入的值。 當建立規則時，您可以使用任何名稱或符號來代表值，但第一個字元必須是 @ 記號 ( **@** )。  
  
> [!NOTE]  
>  請避免建立使用別名資料型別之運算式的規則。 雖然您可以建立使用別名資料型別之運算式的規則，但在規則繫結到資料行或別名資料型別之後，當參考運算式時，會無法編譯運算式。  
  
## <a name="remarks"></a>備註  
 CREATE RULE 無法在單一批次中，與其他 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式結合起來。 規則不適用於建立規則時已在資料庫中的資料，規則無法繫結到系統資料類型。  
  
 規則只能建立在目前資料庫中。 建立好規則之後，請執行 **sp_bindrule**，將規則繫結到資料行或別名資料類型。 規則必須相容於資料行資料類型。 例如， "\@value LIKE A%" 不能用來作為數值資料行的規則。 規則無法繫結到 **text**、**ntext****image**、**varchar (max)**、**nvarchar(max)**、**varbinary(max)**、**xml**、CLR 使用者定義型別或 **timestamp** 資料行。 規則無法繫結到計算資料行。  
  
 請用單引號 (') 括住字元和日期常數，二進位常數前面要附加 0x。 如果規則與所繫結的資料行不相容，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會在插入值時 (而不是繫結規則時)，傳回一則錯誤訊息。  
  
 只有在您試圖將值插入別名資料型別的資料庫資料行或更新這個資料行時，才會啟動繫結到別名資料型別的規則。 由於規則不會測試變數，因此，不會將值指派給繫結到相同資料類型之規則所拒絕的別名資料型別變數。  
  
 若要取得規則的報表，請使用 **sp_help**。 若要顯示規則的文字，請將規則名稱設為參數來執行 **sp_helptext**。 若要重新命名規則，請使用 **sp_rename**。  
  
 在建立同名的新規則之前，您必須先使用 DROP RULE 來卸除舊規則，在卸除舊規則之前，必須先使用 **sp_unbindrule** 來解除它的繫結。 若要解除規則和資料行的繫結，請使用 **sp_unbindrule**。  
  
 您可以在未解除先前繫結的情況下，將新規則繫結到資料行或資料類型；新規則會覆寫先前的規則。 繫結到資料行的規則，一律優先於繫結到別名資料型別的規則。 將規則繫結到資料行，會取代已繫結到這個資料行的別名資料型別之規則。 但將規則繫結到資料類型，並不會取代繫結到這個別名資料型別之資料行的規則。 下表顯示將規則繫結到資料行，或繫結到規則已存在的別名資料型別時，所採用的優先順序。  
  
|新規則繫結到|舊規則繫結到<br /><br /> 別名資料型別|舊規則繫結到<br /><br /> 資料行|  
|-----------------------|-------------------------------------------|----------------------------------|  
|別名資料型別|取代舊規則|沒有變更|  
|資料行|取代舊規則|取代舊規則|  
  
 如果資料行有預設值及其相關聯的規則，預設值必須在規則所定義的網域內。 永遠不會插入與規則衝突的預設值。 SQL Server Database Engine 每次嘗試插入這類預設值時，都會產生一則錯誤訊息。  
  
## <a name="permissions"></a>權限  
 若要執行 CREATE RULE，使用者至少必須有目前資料庫中的 CREATE RULE 權限，以及正在建立規則之結構描述的 ALTER 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-a-rule-with-a-range"></a>A. 利用範圍來建立規則  
 下列範例會建立一個規則來限制插入這個規則所繫結之一個或多個資料行的整數範圍。  
  
```sql  
CREATE RULE range_rule  
AS   
@range>= $1000 AND @range <$20000;  
```  
  
### <a name="b-creating-a-rule-with-a-list"></a>B. 利用清單來建立規則  
 下列範例會建立一個規則，將輸入 (這個規則所繫結的) 資料行之實際值限制在規則所列出的值。  
  
```sql  
CREATE RULE list_rule  
AS   
@list IN ('1389', '0736', '0877');  
```  
  
### <a name="c-creating-a-rule-with-a-pattern"></a>C. 利用模式建立規則  
 下列範例會建立一個遵照下列模式的規則：任兩個字元後面接著連字號 (`-`)，再接著任意數目的字元或不接任何字元，再以 `0` 至 `9` 的整數做為結尾。  
  
```sql  
CREATE RULE pattern_rule   
AS  
@value LIKE '__-%[0-9]'  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [CREATE DEFAULT &#40;Transact-SQL&#41;](../../t-sql/statements/create-default-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [DROP DEFAULT &#40;Transact-SQL&#41;](../../t-sql/statements/drop-default-transact-sql.md)   
 [DROP RULE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-rule-transact-sql.md)   
 [運算式 &#40;Transact-SQL&#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [sp_bindrule &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-bindrule-transact-sql.md)   
 [sp_help &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-transact-sql.md)   
 [sp_helptext &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helptext-transact-sql.md)   
 [sp_rename &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-rename-transact-sql.md)   
 [sp_unbindrule &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-unbindrule-transact-sql.md)   
 [WHERE &#40;Transact-SQL&#41;](../../t-sql/queries/where-transact-sql.md)  
  
  
