---
description: Microsoft SQL 資料庫函式是什麼？
title: Microsoft SQL 資料庫函式是什麼？ | Microsoft Docs
ms.custom: ''
ms.date: 06/28/2017
ms.prod: sql
ms.prod_service: sql-data-warehouse, database-engine, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- built-in functions [SQL Server]
- function [SQL Server] See functions [SQL Server]
- functions [Transact-SQL]
- functions [SQL Server], about functions
- scalar functions
- functions [SQL Server]
ms.assetid: 17186213-5ab5-40b0-b470-b660af1ec44c
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b765564b92cad08db5426b653ad816061a6c5dc4
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101133"
---
# <a name="what-are-the-sql-database-functions"></a>SQL 資料庫函式是什麼？
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

深入了解您可以與 SQL 資料庫搭配使用之內建函式的類別。 您可以使用內建函式，或建立您自己的使用者定義函式。
  
## <a name="aggregate-functions"></a>彙總函式

彙總函式會根據一組值來執行計算，再傳回單一值。 它們可在選取清單中或在 SELECT 陳述式的 HAVING 子句中使用。 您可以與 GROUP BY 子句搭配使用彙總來計算資料列類別的彙總。 使用 OVER 子句來計算特定範圍值的彙總。 OVER 子句無法接續在 GROUPING 或 GROUPING_ID 彙總之後。

所有彙總函式都具有確定性，即在相同的輸入值上執行時，它們一律會傳回相同的值。 如需詳細資訊，請參閱[確定性與非確定性函式](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)。|

## <a name="analytic-functions"></a>分析函數
分析函數會根據資料列群組計算出彙總值。 但不同於彙總函式，分析函式可以傳回每個群組的多個資料列。 您可以使用分析函式計算群組中的移動平均、最新總數、百分比或前 N 個結果。

## <a name="ranking-functions"></a>排名函數
排名函數會傳回資料分割中每個資料列的次序值。 根據所用的函數而定，有些資料列可能會收到與其他資料列相同的值。 排名函數不具決定性。

## <a name="rowset-functions"></a>資料列集函式
資料列集函式會傳回可在 SQL 陳述式中，依照資料表參考的方式來使用的物件。

## <a name="scalar-functions"></a>純量函式
處理單一值，再傳回單一值。 凡是運算式有效之處，都能夠使用純量函數。

### <a name="categories-of-scalar-functions"></a>純量函式類別
  
|函數類別目錄|描述|  
|-----------------------|-----------------|  
|[組態函式](configuration-functions-transact-sql.md)|傳回目前組態的詳細資訊。|  
|[轉換函式](conversion-functions-transact-sql.md)|支援資料類型轉型及轉換。|  
|[資料指標函式](cursor-functions-transact-sql.md)|傳回資料指標的詳細資訊。|  
|[日期與時間資料類型與函式](date-and-time-data-types-and-functions-transact-sql.md)|執行作業來處理日期和時間輸入值，以及傳回字串、數值，或日期和時間值。|  
|[JSON 函式](json-functions-transact-sql.md)|驗證、查詢，或變更 JSON 資料。|  
|[邏輯函式](logical-functions-choose-transact-sql.md)|執行邏輯運算。|  
|[數學函式](mathematical-functions-transact-sql.md)|根據函數參數所提供的輸入值來執行計算，以及傳回數值。|  
|[中繼資料函式](metadata-functions-transact-sql.md)|傳回資料庫和資料庫物件的相關資訊。|  
|[安全性函式](security-functions-transact-sql.md)|傳回使用者和角色的相關資訊。|  
|[字串函式](string-functions-transact-sql.md)|執行作業來處理字串 (**char** 或 **varchar**) 輸入值，並傳回字串或數值。|  
|[系統函式](../../relational-databases/system-functions/system-functions-category-transact-sql.md)|執行作業和傳回 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中的值、物件和設定的相關資訊。|  
|[系統統計函式](system-statistical-functions-transact-sql.md)|傳回系統的統計資訊。|  
|[文字和影像函式](./text-and-image-functions-textptr-transact-sql.md)|執行作業來處理文字或影像輸入值或資料行，以及傳回值的相關資訊。|  
  
## <a name="function-determinism"></a>函數決定性  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 內建函數可能具決定性或不具決定性。 如果每當利用一組特定輸入值來呼叫函數時，函數都會傳回相同的值，這些函數便是具決定性。 如果每次呼叫時都可能傳回不同結果，即便使用同一組特定的輸入值也是如此，這些函數便是不具決定性。 如需詳細資訊，請參閱[確定性與非確定性函式](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)  
  
## <a name="function-collation"></a>函數定序  
 採取字元字串輸入、傳回字元字串輸出的函數，輸出會使用輸入字串的定序。  
  
 使用非字元輸入並傳回字元字串的函數，輸出會使用目前資料庫的預設定序。  
  
 採取多重字元字串輸入、傳回單一字元字串的函數，會利用定序優先順序的規則來設定輸出字串的定序。 如需詳細資訊，請參閱[定序優先順序 &#40;Transact-SQL&#41;](../../t-sql/statements/collation-precedence-transact-sql.md)。  
  
## <a name="see-also"></a>另請參閱  
 [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md)   
 [確定性與非確定性函式](../../relational-databases/user-defined-functions/deterministic-and-nondeterministic-functions.md)   
 [使用預存程序 &#40;MDX&#41;](../../mdx/using-stored-procedures-mdx.md)  
  
