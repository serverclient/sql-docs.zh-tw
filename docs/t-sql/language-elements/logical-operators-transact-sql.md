---
description: 邏輯運算子 (Transact-SQL)
title: 邏輯運算子 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- operators [Transact-SQL], logical
- testing truth
- truth testing
- "TRUE"
- "FALSE"
- logical operators [SQL Server], Transact-SQL
ms.assetid: edd92f08-76fb-4fd7-a4b6-8520d6a81df1
author: cawrites
ms.author: chadam
ms.openlocfilehash: d30bc8ac77518a3568567214bccd8c4788fa88ba
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98083595"
---
# <a name="logical-operators-transact-sql"></a>邏輯運算子 (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  邏輯運算子會測試是否符合某些狀況。 如同比較運算子，邏輯運算子會傳回含 TRUE、FALSE 或 UNKNOWN 值的 **Boolean** 資料類型。  
  
|運算子|意義|  
|--------------|-------------|  
|[ALL](../../t-sql/language-elements/all-transact-sql.md)|如果一組比較全為 TRUE，便是 TRUE。|  
|[AND](../../t-sql/language-elements/and-transact-sql.md)|如果兩個布林運算式都是 TRUE 時，便是 TRUE。|  
|[ANY](../../t-sql/language-elements/any-transact-sql.md)|如果一組比較中的任何一項是 TRUE，便是 TRUE。|  
|[BETWEEN](../../t-sql/language-elements/between-transact-sql.md)|如果運算元在範圍內，便是 TRUE。|  
|[EXISTS](../../t-sql/language-elements/exists-transact-sql.md)|如果子查詢包含任何資料列，便是 TRUE。|  
|[IN](../../t-sql/language-elements/in-transact-sql.md)|如果運算元等於運算式清單中的某個運算式，便是 TRUE。|  
|[LIKE](../../t-sql/language-elements/like-transact-sql.md)|如果運算元符合某個模式，便是 TRUE。|  
|[NOT](../../t-sql/language-elements/not-transact-sql.md)|反轉任何其他布林運算子的值。|  
|[OR](../../t-sql/language-elements/or-transact-sql.md)|如果任一個布林運算式是 TRUE，便是 TRUE。|  
|[SOME](../../t-sql/language-elements/some-any-transact-sql.md)|如果一組比較部分為 TRUE，便是 TRUE。|  
  
## <a name="see-also"></a>另請參閱  
 [運算子優先順序 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operator-precedence-transact-sql.md)  
  
  
