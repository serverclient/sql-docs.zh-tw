---
description: sys.fn_cdc_get_column_ordinal (Transact-SQL)
title: sys.fn_cdc_get_column_ordinal (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 01/25/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.fn_cdc_get_column_ordinal
- fn_cdc_get_column_ordinal_TSQL
- fn_cdc_get_column_ordinal
- sys.fn_cdc_get_column_ordinal_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- fn_cdc_get_column_ordinal
- sys.fn_cdc_get_column_ordinal
ms.assetid: 4bb21a57-2b94-4208-8bdf-6a3e2681d881
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 4ebab4e1b02b325c5abb7e7b6fbc62b2b970ae50
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094989"
---
# <a name="sysfn_cdc_get_column_ordinal-transact-sql"></a>sys.fn_cdc_get_column_ordinal (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回指定之資料行的資料行序數，與指定之 capture 實例相關聯的 [變更資料表](../../relational-databases/system-tables/cdc-capture-instance-ct-transact-sql.md) 中所顯示的資料行序數。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.fn_cdc_get_column_ordinal ( 'capture_instance','column_name')  
```  
  
## <a name="arguments"></a>引數  
 **'** *capture_instance* **'**  
 這是指定資料行識別為已擷取資料行之擷取執行個體的名稱。 *capture_instance* 為 **sysname**。  
  
 **'** *column_name* **'**  
 這是要回報的資料行。 *column_name* 為 **sysname**。  
  
## <a name="return-type"></a>傳回類型  
 **int**  
  
## <a name="remarks"></a>備註  
 這個函數是用來識別擷取資料行在異動資料擷取更新遮罩中的序數位置。 它主要與函數搭配使用 [sys.fn_cdc_is_bit_set](../../relational-databases/system-functions/sys-fn-cdc-is-bit-set-transact-sql.md) ，以便在查詢變更資料時，從更新遮罩中解壓縮資訊。  
  
## <a name="permissions"></a>權限  
 需要來源資料表之所有擷取資料行的 SELECT 權限。 如果已針對擷取執行個體指定了異動資料擷取元件的資料庫角色，也會需要該角色的成員資格。  
  
## <a name="examples"></a>範例  
 下列範例會取得 `VacationHours` 資料行在 `HumanResources_Employee` 擷取執行個體之更新遮罩中的序數位置。 然後，該值會用於 `sys.fn_cdc_is_bit_set` 的呼叫中，以便從傳回的更新遮罩中擷取資訊。  
  
```  
USE AdventureWorks2012;  
GO  
DECLARE @from_lsn binary(10), @to_lsn binary(10),  @VacationHoursOrdinal int;  
SET @from_lsn = sys.fn_cdc_get_min_lsn('HumanResources_Employee');  
SET @to_lsn = sys.fn_cdc_get_max_lsn();  
SET @VacationHoursOrdinal = sys.fn_cdc_get_column_ordinal   
    ( 'HumanResources_Employee','VacationHours');  
SELECT *, sys.fn_cdc_is_bit_set(@VacationHoursOrdinal,  
    __$update_mask) as 'VacationHours'  
FROM cdc.fn_cdc_get_net_changes_HumanResources_Employee  
    ( @from_lsn, @to_lsn, 'all with mask');  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [異動資料擷取函數 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/change-data-capture-functions-transact-sql.md)   
 [關於異動資料擷取 &#40;SQL Server&#41;](../../relational-databases/track-changes/about-change-data-capture-sql-server.md)   
 [sys.sp_cdc_help_change_data_capture &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sys-sp-cdc-help-change-data-capture-transact-sql.md)   
 [sys.sp_cdc_get_captured_columns &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sys-sp-cdc-get-captured-columns-transact-sql.md)   
 [sys.fn_cdc_is_bit_set &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-cdc-is-bit-set-transact-sql.md)  
  
  
