---
description: sys.fn_cdc_has_column_changed (Transact-SQL)
title: sys.fn_cdc_has_column_changed (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.fn_cdc_has_column_changed_TSQL
- sys.fn_cdc_has_column_changed
- fn_cdc_has_column_changed_TSQL
- fn_cdc_has_column_changed
dev_langs:
- TSQL
helpviewer_keywords:
- sys.fn_cdc_has_column_changed
- fn_cdc_has_column_changed
ms.assetid: 2b9e6278-050d-4ffc-8d1a-09606180facc
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 8e835647f387b4980a50a0f49e5c6bd967fe553f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099661"
---
# <a name="sysfn_cdc_has_column_changed-transact-sql"></a>sys.fn_cdc_has_column_changed (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  識別指定的更新遮罩是否表示指定的資料行已經在相關聯的變更資料列中更新。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.fn_cdc_has_column_changed ( 'capture_instance','column_name' , update_mask )  
```  
  
## <a name="arguments"></a>引數  
 **'** *capture_instance* **'**  
 這是擷取執行個體的名稱。 *capture_instance* 為 **sysname**。  
  
 **'** *column_name* **'**  
 這是要回報之指定擷取執行個體的擷取資料行。 *column_name* 為 **sysname**。  
  
 *update_mask*  
 這是在任何相關聯變更資料列中識別已更新資料行的遮罩。 *update_mask* 是 **Varbinary (128)**。  
  
## <a name="return-type"></a>傳回類型  
 **bit**  
  
## <a name="remarks"></a>備註  
 您可以使用這個函數，從在查詢變更資料時傳回的更新遮罩中擷取資訊。 當您需要知道相關聯變更資料列中的特定資料行是否已經修改時，這在後置處理更新遮罩中最有用。 如需詳細資訊，請參閱[關於異動資料擷取 &#40;SQL Server&#41;](../../relational-databases/track-changes/about-change-data-capture-sql-server.md)。  
  
 當這項資訊將作為變更資料查詢的一部分傳回時，建議您使用 [sys.fn_cdc_get_column_ordinal](../../relational-databases/system-functions/sys-fn-cdc-get-column-ordinal-transact-sql.md) 和 [sys.fn_cdc_is_bit_set](../../relational-databases/system-functions/sys-fn-cdc-is-bit-set-transact-sql.md) 的函式，而不是使用此函數。 請在查詢變更資料之前使用 fn_cdc_get_column_ordinal 函數，以便僅計算一次所需的資料行序數。 您可以在查詢中使用 fn_cdc_is_bit_set，以便針對每個傳回的資料列從更新遮罩中擷取資訊。  
  
## <a name="permissions"></a>權限  
 需要系統管理員 (sysadmin) 固定伺服器角色或 db_owner 固定資料庫角色中的成員資格。 若為所有其他使用者，則需要來源資料表中所有擷取資料行的 SELECT 權限，而且如果定義了擷取執行個體的控制角色，便需要該資料庫角色的成員資格。  
  
## <a name="see-also"></a>另請參閱  
 [cdc. &#60;capture_instance&#62;_CT &#40;Transact-sql&#41;](../../relational-databases/system-tables/cdc-capture-instance-ct-transact-sql.md)   
 [cdc.captured_columns &#40;Transact-sql&#41;](../../relational-databases/system-tables/cdc-captured-columns-transact-sql.md)  
  
  
