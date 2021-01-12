---
description: sys.fn_cdc_get_min_lsn (Transact-SQL)
title: sys.fn_cdc_get_min_lsn (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.fn_cdc_get_min_lsn
- fn_cdc_get_min_lsn
- fn_cdc_get_min_lsn_TSQL
- sys.fn_cdc_get_min_lsn_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- fn_cdc_get_min_lsn
- sys.fn_cdc_get_min_lsn
ms.assetid: bd49e28a-128b-4f6b-8545-6a2ec3f4afb3
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 34f941c959d56e4526a73bd186e18cafeda43660
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099659"
---
# <a name="sysfn_cdc_get_min_lsn-transact-sql"></a>sys.fn_cdc_get_min_lsn (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  從 [cdc.change_tables](../../relational-databases/system-tables/cdc-change-tables-transact-sql.md) 系統資料表傳回指定之 capture 實例的 start_lsn 資料行值。 這個值代表擷取執行個體的有效性間隔低端點。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.fn_cdc_get_min_lsn ( 'capture_instance_name' )  
```  
  
## <a name="arguments"></a>引數  
 **'** *capture_instance_name* **'**  
 這是擷取執行個體的名稱。 *capture_instance_name* 為 **sysname**。  
  
## <a name="return-types"></a>傳回型別  
 **binary(10)**  
  
## <a name="remarks"></a>備註  
 當擷取執行個體不存在，或者呼叫端未經授權，無法存取與擷取執行個體相關聯的變更資料時，便傳回 0x00000000000000000000。  
  
 這個函數通常是用於識別與擷取執行個體相關聯之異動資料擷取時間表的低端點。 您也可以在要求變更資料之前，使用這個函數來驗證查詢範圍的端點是否都位於擷取執行個體時間表之內。 請您務必執行這類檢查，因為在變更資料表上執行清除時，擷取執行個體的低端點就會變更。 如果變更資料要求之間的時間很長，即使是上一次變更資料要求時設定為高端點的低端點也可能位於目前時間表以外。  
  
## <a name="permissions"></a>權限  
 需要系統管理員 (sysadmin) 固定伺服器角色或 db_owner 固定資料庫角色中的成員資格。 若為所有其他使用者，則需要來源資料表中所有擷取資料行的 SELECT 權限，而且如果定義了擷取執行個體的控制角色，便需要該資料庫角色的成員資格。  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-the-minimum-lsn-value-for-a-specified-capture-instance"></a>A. 針對指定的擷取執行個體傳回最小 LSN 值  
 下列範例會針對 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的擷取執行個體 `HumanResources_Employee` 傳回最小 LSN 值。  
  
```  
USE AdventureWorks2-12;  
GO  
SELECT sys.fn_cdc_get_min_lsn ('HumanResources_Employee')AS min_lsn;  
  
```  
  
### <a name="b-verifying-the-low-endpoint-of-a-query-range"></a>B. 驗證查詢範圍的低端點  
 下列範例會使用 `sys.fn_cdc_get_min_lsn` 所傳回的最小 LSN 值來驗證變更資料查詢的建議低端點是否適用於 `HumanResources_Employee` 擷取執行個體的目前時間表。 這則範例會假設擷取執行個體的上一個高端點 LSN 已儲存而且可設定 `@save_to_lsn` 變數。 為了執行此範例，`@save_to_lsn` 會設定為 0x000000000000000000，以便強制執行錯誤處理區段。  
  
```  
USE AdventureWorks2012;  
GO  
DECLARE @min_lsn binary(10), @from_lsn binary(10),@save_to_lsn binary(10), @to_lsn binary(10);  
-- Sets @save_to_lsn to the previous high endpoint saved from the last change data request.  
SET @save_to_lsn = 0x000000000000000000;  
-- Sets the upper endpoint for the query range to the current maximum LSN.  
SET @to_lsn = sys.fn_cdc_get_max_lsn();  
-- Sets the @min_lsn parameter to the current minimum LSN for the capture instance.  
SET @min_lsn = sys.fn_cdc_get_min_lsn ('HumanResources_Employee');  
-- Sets the low endpoint for the query range to the LSN that follows the previous high endpoint.  
SET @from_lsn = sys.fn_cdc_increment_lsn(@save_to_lsn);  
-- Tests to verify the low endpoint is valid for the current capture instance.  
IF (@from_lsn < @min_lsn)  
    BEGIN  
        RAISERROR('Low endpoint of the request interval is invalid.', 16, -1);  
    END  
ELSE  
-- Return the changes occurring within the query range.  
    SELECT * FROM cdc.fn_cdc_get_all_changes_HumanResources_Employee(@from_lsn, @to_lsn, 'all');  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.fn_cdc_get_max_lsn &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-cdc-get-max-lsn-transact-sql.md)   
 [交易記錄 &#40;SQL Server&#41;](../../relational-databases/logs/the-transaction-log-sql-server.md)  
  
  
