---
description: 'sys.dm_exec_valid_use_hints (Transact-sql) '
title: sys.dm_exec_valid_use_hints (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: conceptual
f1_keywords:
- sys.dm_exec_valid_use_hints
- sys.dm_exec_valid_use_hints_TSQL
- dm_exec_valid_use_hints
- dm_exec_valid_use_hints_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_valid_use_hints management view
ms.assetid: 65d50589-39c2-4046-92b6-0c4587d8c593
author: pmasl
ms.author: pelopes
ms.openlocfilehash: de99c9372846525349df3f9f312222e322bfe751
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171390"
---
# <a name="sysdm_exec_valid_use_hints-transact-sql"></a>sys.dm_exec_valid_use_hints (Transact-sql) 
[!INCLUDE [sqlserver2016](../../includes/applies-to-version/sqlserver2016.md)]

傳回 [使用提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint) 支援的提示名稱。 它會針對每個資料列列出一個提示名稱。  
  
您可以使用此 DMV 來查看使用提示標記法下所有支援提示的清單。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|NAME|**sysname**|提示的名稱。|

如需每個提示的描述，請參閱 [查詢提示](../../t-sql/queries/hints-transact-sql-query.md#use_hint) 。

在 SP1 中引進 [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)] 。
  
## <a name="see-also"></a>另請參閱  
    
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)  

