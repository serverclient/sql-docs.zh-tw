---
description: FORMATMESSAGE (Transact-SQL)
title: FORMATMESSAGE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/12/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FORMATMESSAGE
- FORMATMESSAGE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysmessages system table
- sys.messages catalog view
- FORMATMESSAGE function
- messages [SQL Server], formats
- errors [SQL Server], formats
ms.assetid: 83f18102-2035-4a87-acd0-8d96d03efad5
author: cawrites
ms.author: chadam
ms.openlocfilehash: 27fa2389740a7b2d15cb019d86ba1d02559ce6d4
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172660"
---
# <a name="formatmessage-transact-sql"></a>FORMATMESSAGE (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  從 sys.messages 中現有的訊息或從提供的字串建構訊息。 FORMATMESSAGE 的功能類似於 RAISERROR 陳述式的功能。 不過，RAISERROR 會立即列印訊息，FORMATMESSAGE 則會傳回格式化的訊息，以便進一步處理。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
FORMATMESSAGE ( { msg_number  | ' msg_string ' | @msg_variable} , [ param_value [ ,...n ] ] )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *msg_number*  
 這是 sys.messages 中所儲存之訊息的識別碼。 如果 *msg_number* 是 <= 13000，或 sys.messages 中沒有這則訊息，就會傳回 NULL。  
  
 *msg_string*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](https://go.microsoft.com/fwlink/p/?LinkId=299658))。  
  
 這是括在單引號中的字串，且包含參數值預留位置。 這個錯誤訊息最多可有 2,047 個字元。 如果訊息包含 2,048 個或更多字元，則只會顯示前 2,044 個字元，並且會加上省略符號以表示該訊息已被截斷。 請注意，由於內部儲存行為的緣故，替代參數比輸出顯示耗用更多字元。  如需有關訊息字串結構以及字串中參數用法的資訊，請參閱 [RAISERROR &#40;Transact-SQL&#41;](../../t-sql/language-elements/raiserror-transact-sql.md) 中 *msg_str* 引數的說明。  

 *@msg_variable*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](https://go.microsoft.com/fwlink/p/?LinkId=299658))。  
  
 為 nvarchar 或 varchar 變數，其中包含符合上述 *msg_string* 之準則規範的字串。  
  
 *param_value*  
 這是訊息中所用的參數值。 它可以是多個參數值。 您必須依照預留位置變數在訊息中的出現順序來指定這些值。 最多可有 20 個值。  
  
## <a name="return-types"></a>傳回型別  
 **nvarchar**  
  
## <a name="remarks"></a>備註  
 如同 RAISERROR 陳述式，FORMATMESSAGE 會用所提供的參數值來替代訊息中的預留位置變數，以編輯訊息。 如需有關錯誤訊息所允許之預留位置及編輯處理的詳細資訊，請參閱 [RAISERROR &#40;Transact-SQL&#41;](../../t-sql/language-elements/raiserror-transact-sql.md)。  
  
 FORMATMESSAGE 會用使用者目前的語言來查閱訊息。 針對系統訊息 (*msg_number* <=50000)，如果訊息沒有已當地語系化的版本，便會使用 OS 語言版本。 針對使用者訊息 (*msg_number* >50000)，如果訊息沒有已當地語系化的版本，便會使用英文版本。
  
 如果是當地語系化的訊息，提供的參數值必須對應於 U.S. English 版本中的參數預留位置英文版。 即當地語系化版本中的參數 1 必須對應於 U.S. English 版本中參數 1。參數 2 必須對應於參數 2，依此類推。  
  
## <a name="examples"></a>範例  
  
### <a name="a-example-with-a-message-number"></a>A. 具有訊息編號的範例  
 當「發行項 '%s' 無法加入到發行集 '%s' 中」時，下列範例會使用儲存在 sys.messages 中的複寫訊息 `20009`。FORMATMESSAGE 會將 `First Variable` 和 `Second Variable` 這兩個值代入參數預留位置中。 產生的字串「發行項 'First Variable' 無法加入到發行集 'Second Variable' 中」會儲存在區域變數 `@var1` 中。  
  
```sql
SELECT text FROM sys.messages WHERE message_id = 20009 AND language_id = 1033;  
DECLARE @var1 VARCHAR(200);   
SELECT @var1 = FORMATMESSAGE(20009, 'First Variable', 'Second Variable');   
SELECT @var1;  
```  
  
### <a name="b-example-with-a-message-string"></a>B. 具有訊息字串的範例  
  
**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](https://go.microsoft.com/fwlink/p/?LinkId=299658))。  
  
 下列範例會採用字串作為輸入。  
  
```sql
SELECT FORMATMESSAGE('This is the %s and this is the %s.', 'first variable', 'second variable') AS Result;  
```  
  
 傳回：`This is the first variable and this is the second variable.`  
  
### <a name="c-additional-message-string-formatting-examples"></a>C. 其他訊息字串格式範例  
 下列範例顯示各種格式選項。  
  
```sql
SELECT FORMATMESSAGE('Signed int %i, %d %i, %d, %+i, %+d, %+i, %+d', 5, -5, 50, -50, -11, -11, 11, 11);
SELECT FORMATMESSAGE('Signed int with up to 3 leading zeros %03i', 5);  
SELECT FORMATMESSAGE('Signed int with up to 20 leading zeros %020i', 5);  
SELECT FORMATMESSAGE('Signed int with leading zero 0 %020i', -55);  
SELECT FORMATMESSAGE('Bigint %I64d', 3000000000);
SELECT FORMATMESSAGE('Unsigned int %u, %u', 50, -50);  
SELECT FORMATMESSAGE('Unsigned octal %o, %o', 50, -50);  
SELECT FORMATMESSAGE('Unsigned hexadecimal %x, %X, %X, %X, %x', 11, 11, -11, 50, -50);  
SELECT FORMATMESSAGE('Unsigned octal with prefix: %#o, %#o', 50, -50);  
SELECT FORMATMESSAGE('Unsigned hexadecimal with prefix: %#x, %#X, %#X, %X, %x', 11, 11, -11, 50, -50);  
SELECT FORMATMESSAGE('Hello %s!', 'TEST');  
SELECT FORMATMESSAGE('Hello %20s!', 'TEST');  
SELECT FORMATMESSAGE('Hello %-20s!', 'TEST');  
```  
  
## <a name="see-also"></a>另請參閱  
 [RAISERROR &#40;Transact-SQL&#41;](../../t-sql/language-elements/raiserror-transact-sql.md)  
 [THROW &#40;Transact-SQL&#41;](../../t-sql/language-elements/throw-transact-sql.md)   
 [sp_addmessage &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addmessage-transact-sql.md)   
 [sys.messages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)   
 [CONCAT &#40;Transact-SQL&#41;](../../t-sql/functions/concat-transact-sql.md)  
 [CONCAT_WS &#40;Transact-SQL&#41;](../../t-sql/functions/concat-ws-transact-sql.md)  
 [QUOTENAME &#40;Transact-SQL&#41;](../../t-sql/functions/quotename-transact-sql.md)  
 [REPLACE &#40;Transact-SQL&#41;](../../t-sql/functions/replace-transact-sql.md)  
 [REVERSE &#40;Transact-SQL&#41;](../../t-sql/functions/reverse-transact-sql.md)  
 [STRING_AGG &#40;Transact-SQL&#41;](../../t-sql/functions/string-agg-transact-sql.md)  
 [STRING_ESCAPE &#40;Transact-SQL&#41;](../../t-sql/functions/string-escape-transact-sql.md)  
 [STUFF &#40;Transact-SQL&#41;](../../t-sql/functions/stuff-transact-sql.md)  
 [TRANSLATE &#40;Transact-SQL&#41;](../../t-sql/functions/translate-transact-sql.md)  
 [系統函數 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)   
  
  
