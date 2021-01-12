---
description: 位元運算子 (Transact-SQL)
title: 位元運算子 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/04/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- operators [Transact-SQL], bitwise
- bitwise operators
- bit manipulations
ms.assetid: 2b994cf5-2daa-438a-b8c7-4bd8d451ac8d
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e41fff219addb547d9706819a9fd5d9327f24e36
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100952"
---
# <a name="bitwise-operators-transact-sql"></a>位元運算子 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  位元運算子會在整數資料類型類別目錄之任何資料類型的兩個運算式之間，執行位元操作。  
  位元運算子可將兩個整數值轉換成二進位位元、在每個位元上執行 AND、OR 或 NOT 運算、產生結果。 然後將結果轉換成整數。  
  
  例如，整數 170 會轉換成二進位 1010 1010。
整數 75 會轉換成二進位 0100 1011。

|! 運算子之後|位元數學|
|---- |---- |
|AND <br> 如果在任何位置的位元皆為 1，則結果為 1。 |1010 1010 = 170 <br>0100 1011 =  75 <br>-----------------  <br> 0000 1010 =  10 |
|或者 <br> 如果在任何位置的其中一個位元為 1，則結果為 1。 |1010 1010 = 170 <br>0100 1011 =  75 <br>-----------------  <br> 1110 1011 = 235|
|NOT  <br> 反轉每個位元位置的位元值。 |1010 1010 = 170 <br>----------------- <br>  0101 0101 =   85 |
  
請參閱下列主題：   
* [& &#40;位元 AND&#41;](../../t-sql/language-elements/bitwise-and-transact-sql.md)  
* [&= &#40;位元 AND 指派&#41;](../../t-sql/language-elements/bitwise-and-equals-transact-sql.md)   
* [&#124; &#40;位元 OR&#41;](../../t-sql/language-elements/bitwise-or-transact-sql.md)  
* [&#124;= &#40;位元 OR 指派&#41;](../../t-sql/language-elements/bitwise-or-equals-transact-sql.md)   
* [^ &#40;位元排除 OR&#41;](../../t-sql/language-elements/bitwise-exclusive-or-transact-sql.md)  
* [^= &#40;位元排除 OR 指派&#41;](../../t-sql/language-elements/bitwise-exclusive-or-equals-transact-sql.md)  
* [~ &#40;位元 NOT&#41;](../../t-sql/language-elements/bitwise-not-transact-sql.md)  
  
 位元運算子的運算元可以是整數或二進位字串資料類型類別目錄 (**image** 資料類型除外) 的任何資料類型，但不可以兩個運算元都是二進位字串資料類型類別目錄的任何資料類型。 下表顯示支援的運算元資料類型。  
  
|左運算元|右運算元|  
|------------------|-------------------|  
|[binary](../../t-sql/data-types/binary-and-varbinary-transact-sql.md)|**int**、**smallint** 或 **tinyint**|  
|[bit](../../t-sql/data-types/bit-transact-sql.md)|**int**、**smallint**、**tinyint** 或 **bit**|  
|[bigint](../../t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql.md)|**bigint**、**int**、**smallint**、**tinyint**、**binary** 或 **varbinary**|  
|[int](../../t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql.md)|**int**、**smallint**、**tinyint**、**binary** 或 **varbinary**|  
|[smallint](../../t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql.md)|**int**、**smallint**、**tinyint**、**binary** 或 **varbinary**|  
|[tinyint](../../t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql.md)|**int**、**smallint**、**tinyint**、**binary** 或 **varbinary**|  
|[varbinary](../../t-sql/data-types/binary-and-varbinary-transact-sql.md)|**int**、**smallint** 或 **tinyint**|  
  
## <a name="see-also"></a>另請參閱  
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [資料類型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)   
 [複合運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/compound-operators-transact-sql.md)
