---
description: 'sys.masked_columns (Transact-sql) '
title: sys.masked_columns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 02/25/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.masked_columns
- masked_columns_tsql
- sys.masked_columns_tsql
- masked_columns
helpviewer_keywords:
- sys.masked_columns catalog view
ms.assetid: 671577e4-d757-4b8d-9aa9-0fc8d51ea9ca
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: e5096f57c8e0b06c1f0403c446eb5417cd13b6f1
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095539"
---
# <a name="sysmasked_columns-transact-sql"></a>sys.masked_columns (Transact-sql) 

[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

  使用 **sys.masked_columns** view 來查詢已套用動態資料遮罩函數的資料表資料行。 此檢視繼承自 **sys.columns** 檢視。 它會傳回 **sys.columns** 檢視中的所有資料行，加上 **is_masked** 和 **masking_function** 資料行，指出資料行是否已遮罩，若已遮罩，則指出定義了哪個遮罩函數。 此檢視只會顯示已套用遮罩函數的資料行。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|這個資料行所屬的物件識別碼。|  
|NAME|**sysname**|資料行的名稱。 在物件中，這是唯一的。|  
|column_id|**int**|資料行的識別碼。 在物件中，這是唯一的。<br /><br /> 資料行識別碼不一定會循序排列。|  
|**sys.masked_columns** 會傳回繼承自 **sys. 資料行** 的更多資料行。|各種|如需更多資料行定義，請參閱 [sys. columns &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md) 。|  
|is_masked|**bit**|指出資料行是否為遮罩。 1表示遮罩。|  
|masking_function|**nvarchar(4000)**|資料行的遮罩函數。|  
  
## <a name="remarks"></a>備註  
  
## <a name="permissions"></a>權限  
 此視圖會傳回資料表的相關資訊，其中使用者在資料表上具有某種許可權，或使用者具有 VIEW ANY DEFINITION 許可權。  
  
## <a name="example"></a>範例  
 下列查詢會將 **sys.masked_columns** 聯結至 **sys. 資料表** ，以傳回所有遮罩資料行的相關資訊。  
  
```  
SELECT tbl.name as table_name, c.name AS column_name, c.is_masked, c.masking_function  
FROM sys.masked_columns AS c  
JOIN sys.tables AS tbl   
    ON c.object_id = tbl.object_id  
WHERE is_masked = 1;  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態資料遮罩](../../relational-databases/security/dynamic-data-masking.md)   
 [sys.columns &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)  
  
  
