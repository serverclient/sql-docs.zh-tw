---
description: sys.numbered_procedure_parameters (Transact-SQL)
title: sys.numbered_procedure_parameters (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- numbered_procedure_parameters_TSQL
- sys.numbered_procedure_parameters_TSQL
- numbered_procedure_parameters
- sys.numbered_procedure_parameters
dev_langs:
- TSQL
helpviewer_keywords:
- sys.numbered_procedure_parameters catalog view
ms.assetid: a441d46d-1f30-41c2-8d94-e9442f59786e
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 6d716d8b08d479043bfb592b743d987ddd7bf73f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095490"
---
# <a name="sysnumbered_procedure_parameters-transact-sql"></a>sys.numbered_procedure_parameters (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對編號程序的每個參數，各包含一個資料列。 當您建立編號的預存程序時，基本程序的編號為 1。 後續所有程序則為 2 號、3 號等，依此類推。 **sys.numbered_procedure_parameters** 包含所有後續程式（編號為2以上）的參數定義。 這份檢視不會顯示基本預存程序 (編號 = 1) 的參數。 基本預存程序類似於未編號的預存程序。 因此，其參數會以 [sys. 參數表示 (transact-sql) ](../../relational-databases/system-catalog-views/sys-parameters-transact-sql.md)。  
  
> [!IMPORTANT]  
>  編號程序已被取代。 不再使用編號程序。 當編譯使用這份目錄檢視的查詢時，會引發 DEPRECATION_ANNOUNCEMENT 事件。  
  
> [!NOTE]  
>  編號程序並不支援 XML 和 CLR 參數。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|這個參數所屬物件的識別碼。|  
|**procedure_number**|**smallint**|這個程序在物件內的編號，大於或等於 2。|  
|**name**|**sysname**|參數的名稱。 在 **procedure_number** 中是唯一的。|  
|**parameter_id**|**int**|參數的識別碼。 在 **procedure_number** 中是唯一的。|  
|**system_type_id**|**tinyint**|參數系統類型的識別碼。|  
|**user_type_id**|**int**|參數的類型識別碼 (如使用者所定義)。|  
|**max_length**|**smallint**|參數的最大長度 (以位元組為單位)。<br /><br /> -1 = 資料行的資料類型是 varchar(max)、nvarchar(max) 或 varbinary(max)。|  
|**有效位數**|**tinyint**|如果是以數值為基礎，便是參數的有效位數；否則，便是 0。|  
|**scale**|**tinyint**|如果是以數值為基礎，便是參數的小數位數；否則，便是 0。|  
|**is_output**|**bit**|1 = 參數是輸出 (或傳回)；否則，便是 0。|  
|**is_cursor_ref**|**bit**|1 = 參數是一個資料指標參考參數。|  
  
> [!NOTE]  
>  編號程序並不支援 XML 和 CLR 參數。  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
