---
description: sys.sysconstraints (Transact-SQL)
title: sys.sys條件約束 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysconstraints
- sys.sysconstraints
- sysconstraints_TSQL
- sys.sysconstraints_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sysconstraints compatibility view
- sysconstraints system table
ms.assetid: f2b2e2ad-ba24-48a1-913c-8ee4e0895dc4
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 3014430aad20c74d22a72e1756503baadbe7cb68
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099128"
---
# <a name="syssysconstraints-transact-sql"></a>sys.sysconstraints (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含條件約束到擁有資料庫條件約束之物件的對應。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**constid**|**int**|條件約束編號。|  
|**id**|**int**|擁有條件約束的資料表識別碼。|  
|**colid**|**smallint**|定義條件約束的資料行識別碼。<br /><br /> 0 = 資料表條件約束|  
|**spare1**|**tinyint**|保留|  
|**status**|**int**|指出狀態的虛擬位元遮罩。 可能的值如下：<br /><br /> 1 = PRIMARY KEY 條件約束<br /><br /> 2 = UNIQUE KEY 條件約束<br /><br /> 3 = FOREIGN KEY 條件約束<br /><br /> 4 = CHECK 條件約束<br /><br /> 5 = DEFAULT 條件約束<br /><br /> 16 = 資料行層級條件約束<br /><br /> 32 = 資料表層級條件約束|  
|**actions**|**int**|保留|  
|**error**|**int**|保留|  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
