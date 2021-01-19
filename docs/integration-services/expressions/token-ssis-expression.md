---
description: TOKEN (SSIS 運算式)
title: TOKEN (SSIS 運算式) | Microsoft Docs
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
ms.assetid: 9fdd06bf-5bc9-445c-95bf-709e0ca5989b
author: chugugrace
ms.author: chugu
ms.openlocfilehash: cc99e599596658019c073b67dbf39b6dd7e573ce
ms.sourcegitcommit: 1f826eb3f73bd4d94bc9638b9cdd60991a2e2fa0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98125599"
---
# <a name="token--ssis-expression"></a>TOKEN (SSIS 運算式)

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]


  依據字串中用來分隔 Token 的指定分隔符號，以及表示要傳回哪個 Token 的 Token 號碼，從字串傳回 Token (子字串)。  
  
## <a name="syntax"></a>語法  
  
```  
TOKEN(character_expression, delimiter_string, occurrence)  
```  
  
## <a name="arguments"></a>引數  
 *character_expression*  
 包含以分隔符號分隔之 Token 的字串。  
  
 *delimiter_string*  
 包含分隔符號字元的字串。 例如，"; ," 包含分號、空格和逗號三個分隔符號字元。  
  
 *occurrence*  
 帶正負號或不帶正負號的整數，指定要傳回的 Token。 例如，若您指定 3 做為此參數的值，則會傳回字串中的第三個 Token。  
  
## <a name="result-types"></a>結果類型  
 DT_WSTR  
  
## <a name="remarks"></a>備註  
 此函式會將 <character_expression> 字串分割成一組以 <delimiter_string> 中所指定分隔符號來分隔的 Token，然後傳回第 N 個 Token (其中 N 是 \<occurrence> 參數指定的 Token 出現編號)。 如需此函數的範例用法，請參閱＜範例＞一節。  
  
 下列備註適用於 TOKEN 函數：  
  
-   分隔符號字串可以包含一個或多個分隔符號字元。  
  
-   如果 \<occurrence> 參數值大於字串中的 Token 總數，則函式就會傳回 NULL。  
  
-   前導分隔符號會予略過。  
  
-   TOKEN 只適用於 DT_WSTR 資料類型。 如果 *character_expression* 引數是字串常值或具有 DT_STR 資料類型的資料行，則該引數會在 TOKEN 執行其作業前隱含轉換成 DT_WSTR 資料類型。 其他資料類型必須明確地轉換為 DT_WSTR 資料類型。  
  
-   如果 character_expression 為 Null，則 TOKEN 會傳回 Null 結果。  
  
-   您可以使用變數和資料行來做為運算式中所有引數的值。  
  
## <a name="expression-examples"></a>運算式範例  
 在下列範例中，TOKEN 函數會傳回 "a"。 "a little white dog" 字串中有 4 個權杖："a"、"little"、"white"、"dog"，並以分隔符號 " " (空格字元) 分隔。 第二個引數 (分隔符號字串) 僅指定一個分隔符號 (空格字元)，以用來將輸入字串分割成 Token。 最後一個引數 1 指定要傳回的第一個 Token。 此範例字串中的第一個權杖是 "a"。  
  
```  
TOKEN("a little white dog"," ",1)  
```  
  
 在下列範例中，TOKEN 函數會傳回 "dog"。 此範例中的分隔符號字串包含 5 個分隔符號。 輸入字串中包含 "a"、"little"、"white"、"dog" 4 個權杖。  
  
```  
TOKEN("a:little|white dog","| ,.:",4)  
```  
  
 在下列範例中，TOKEN 函數會傳回 "" (空字串)，因為字串中沒有 99 個 TOKEN。  
  
```  
TOKEN("a little white dog"," ",99)  
```  
  
 在下列範例中，TOKEN 函數會傳回完整字串。 此函數會剖析輸入字串中的分隔符號，但因為字串中不具分隔符號，所以會直接加入整個字串做為第一個 Token。  
  
```  
TOKEN("a little white dog","|",1)  
```  
  
 在下列範例中，TOKEN 函數會傳回 "a"。 其將忽略所有的前導空格字元。  
  
```  
TOKEN("        a little white dog", " ", 1)  
```  
  
 在下列範例中，TOKEN 函數會傳回日期起始年份 (year from a date) 字串。  
  
```  
TOKEN("2009/01/01", "/", 1)
```  
  
 在下列範例中，TOKEN 函數會從指定路徑傳回檔名。 例如，如果 User::Path 的值為 "c:\program files\data\myfile.txt"，TOKEN 函式就會傳回 "myfile.txt"。 TOKENCOUNT 函式會傳回 4，而 TOKEN 函式會傳回第 4 個權杖 "myfile.txt"。  
  
```  
TOKEN(@[User::Path], "\\", TOKENCOUNT(@[User::Path], "\\"))  
```  
  
## <a name="see-also"></a>另請參閱  
 [函數 &#40;SSIS 運算式&#41;](../../integration-services/expressions/functions-ssis-expression.md)  
  
  
