---
description: sys.fn_cdc_is_bit_set (Transact-SQL)
title: sys.fn_cdc_is_bit_set (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- fn_cdc_is_bit_set
- sys.fn_cdc_is_bit_set_TSQL
- sys.fn_cdc_is_bit_set
- fn_cdc_is_bit_set_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.fn_cdc_is_bit_set
- fn_cdc_is_bit_set
ms.assetid: 792fe7cf-b3b8-4f25-8329-78d63f0e6921
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 3c68325c46c275eeb797a4601fa7fd2ea375576f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094978"
---
# <a name="sysfn_cdc_is_bit_set-transact-sql"></a>sys.fn_cdc_is_bit_set (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  透過檢查擷取資料行的序數位置是否設定於提供的位元遮罩內，指出該擷取資料行是否已經更新。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.fn_cdc_is_bit_set ( position , update_mask )  
```  
  
## <a name="arguments"></a>引數  
 *position*  
 這是遮罩中要檢查的序數位置。 *位置* 是 **int**。  
  
 *update_mask*  
 這是識別更新資料行的遮罩。 *update_mask* 是 **Varbinary (128)**。  
  
## <a name="return-type"></a>傳回類型  
 **bit**  
  
## <a name="remarks"></a>備註  
 這個函數通常會當做變更資料查詢的一部分使用，以便指出資料行是否已經變更。 在此案例中，函數 [sys.fn_cdc_get_column_ordinal](../../relational-databases/system-functions/sys-fn-cdc-get-column-ordinal-transact-sql.md) 會在查詢之前用來取得必要的資料行序數。 然後， **sys.fn_cdc_is_bit_set** 會套用至傳回之變更資料的每個資料列，並在傳回的結果集中提供資料行特定資訊。  
  
 當您判斷所傳回結果集的所有資料列是否有變更資料行時，建議使用這個函數，而不是函數 [sys.fn_cdc_has_column_changed](../../relational-databases/system-functions/sys-fn-cdc-has-column-changed-transact-sql.md) 。  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。  
  
## <a name="examples"></a>範例  
 下列範例會使用 `sys.fn_cdc_is_bit_set` 搭配預先計算的資料行序數和 `cdc.fn_cdc_get_all_changes_HR_Department` 的值做為要呼叫的引數，在 `IsGroupNmUpdated` 查詢函數所產生的結果集前面加上 '`__$update_mask`' 資料行。  
  
```  
USE AdventureWorks2012;  
GO  
DECLARE @from_lsn binary(10), @to_lsn binary(10), @GroupNm_ordinal int;  
SET @from_lsn = sys.fn_cdc_get_min_lsn('HR_Department');  
SET @to_lsn = sys.fn_cdc_get_max_lsn();  
SET @GroupNm_ordinal = sys.fn_cdc_get_column_ordinal('HR_Department','GroupName');  
  
SELECT sys.fn_cdc_is_bit_set(@GroupNm_ordinal,__$update_mask) as 'IsGroupNmUpdated', *  
FROM cdc.fn_cdc_get_all_changes_HR_Department( @from_lsn, @to_lsn, 'all')  
WHERE __$operation = 4;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [異動資料擷取函數 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/change-data-capture-functions-transact-sql.md)   
 [sys.fn_cdc_get_column_ordinal &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-cdc-get-column-ordinal-transact-sql.md)   
 [sys.fn_cdc_has_column_changed &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-cdc-has-column-changed-transact-sql.md)   
 [cdc.fn_cdc_get_all_changes_&#60;capture_instance&#62;  &#40;Transact-sql&#41;](../../relational-databases/system-functions/cdc-fn-cdc-get-all-changes-capture-instance-transact-sql.md)   
 [cdc.fn_cdc_get_net_changes_&#60;capture_instance&#62; &#40;Transact-sql&#41;](../../relational-databases/system-functions/cdc-fn-cdc-get-net-changes-capture-instance-transact-sql.md)   
 [關於異動資料擷取 &#40;SQL Server&#41;](../../relational-databases/track-changes/about-change-data-capture-sql-server.md)  
  
  
