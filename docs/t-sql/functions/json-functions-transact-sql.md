---
description: JSON 函式 (Transact-SQL)
title: JSON 函式 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/03/2020
ms.prod: sql
ms.technology: t-sql
ms.topic: language-reference
helpviewer_keywords:
- JSON functions
ms.assetid: ec97d451-06af-44a3-8304-305d410cfc8e
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: chadam
monikerRange: = azuresqldb-current||= azure-sqldw-latest||>= sql-server-2016||>= sql-server-linux-2017
ms.openlocfilehash: bf8d6d2516f31e5e5f40e5a2f418e5da11d9b879
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101096"
---
# <a name="json-functions-transact-sql"></a>JSON 函式 (Transact-SQL)

[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

使用此章節頁面上描述的函式來驗證或變更 JSON 文字，或擷取簡單或複雜的值。  
  
|函式|描述|  
|--------------|-----------------|  
|[ISJSON](../../t-sql/functions/isjson-transact-sql.md)|測試字串是否包含有效的 JSON。|  
|[JSON_VALUE](../../t-sql/functions/json-value-transact-sql.md)|從 JSON 字串擷取純量值。|  
|[JSON_QUERY](../../t-sql/functions/json-query-transact-sql.md)|從 JSON 字串擷取物件或陣列。|  
|[JSON_MODIFY](../../t-sql/functions/json-modify-transact-sql.md)|更新 JSON 字串中的屬性值，並傳回更新後的 JSON 字串。|

 如需 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中內建 JSON 支援的詳細資訊，請參閱 [JSON 資料 &#40;SQL Server&#41;](../../relational-databases/json/json-data-sql-server.md)。  

## <a name="see-also"></a>另請參閱

 - [使用內建函數，驗證、查詢以及變更 JSON 資料 &#40;SQL Server&#41;](../../relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server.md)
 - [JSON 路徑運算式 &#40;SQL Server&#41;](../../relational-databases/json/json-path-expressions-sql-server.md)
 - [JSON 資料 &#40;SQL Server&#41;](../../relational-databases/json/json-data-sql-server.md)  
