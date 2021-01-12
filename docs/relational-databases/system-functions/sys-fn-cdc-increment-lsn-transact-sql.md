---
description: sys.fn_cdc_increment_lsn (Transact-SQL)
title: sys.fn_cdc_increment_lsn (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- fn_cdc_increment_lsn_TSQL
- sys.fn_cdc_increment_lsn_TSQL
- sys.fn_cdc_increment_lsn
- fn_cdc_increment_lsn
dev_langs:
- TSQL
helpviewer_keywords:
- fn_cdc_increment_lsn
- sys.fn_cdc_increment_lsn
ms.assetid: e53b6703-358b-4c9a-912a-8f7c7331069b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: ee5d44cbe53d31609a454eb5acbed52a53ae194e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099658"
---
# <a name="sysfn_cdc_increment_lsn-transact-sql"></a>sys.fn_cdc_increment_lsn (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  根據指定的記錄序號 (LSN)，傳回序列中的下一個 LSN。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.fn_cdc_increment_lsn ( lsn_value )  
```  
  
## <a name="arguments"></a>引數  
 *lsn_value*  
 LSN 值。 *lsn_value* 是 **二進位 (10)**。  
  
## <a name="return-type"></a>傳回類型  
 **binary(10)**  
  
## <a name="remarks"></a>備註  
 此函數傳回的 LSN 值永遠大於指定的值，而且這兩個值之間不存在任何 LSN 值。  
  
 若要有系統地查詢一段時間內變更資料的資料流，您可以定期重複查詢函數呼叫，而且每次都指定新的查詢間隔，以便限定查詢所傳回的變更。 為了協助確保不會遺失任何資料，通常會使用上一個查詢的上限來產生後續查詢的下限。 由於查詢間隔是封閉的間隔，因此新的下限必須大於先前的上限，但是必須夠小，才能確保沒有任何變更的 LSN 值介於這個值與舊的上限之間。 sys.fn_cdc_increment_lsn 函數可用於取得這個值。  
  
## <a name="permissions"></a>權限  
 需要 public 資料庫角色中的成員資格。  
  
## <a name="examples"></a>範例  
 下列範例會根據上一個查詢和儲存在變數 `sys.fn_cdc_increment_lsn` 中的上限，使用 `@save_to_lsn` 來產生異動資料擷取查詢的新下限值。  
  
```  
USE AdventureWorks2012;  
GO  
DECLARE @from_lsn binary(10), @to_lsn binary(10), @save_to_lsn binary(10);  
SET @save_to_lsn = <previous_upper_bound_value>;  
SET @from_lsn = sys.fn_cdc_increment_lsn(@save_to_lsn);  
SET @to_lsn = sys.fn_cdc_get_max_lsn();  
SELECT * from cdc.fn_cdc_get_all_changes_HumanResources_Employee( @from_lsn, @to_lsn, 'all' );  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.fn_cdc_decrement_lsn &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-cdc-decrement-lsn-transact-sql.md)   
 [cdc.fn_cdc_get_all_changes_&#60;capture_instance&#62;  &#40;Transact-sql&#41;](../../relational-databases/system-functions/cdc-fn-cdc-get-all-changes-capture-instance-transact-sql.md)   
 [cdc.fn_cdc_get_net_changes_&#60;capture_instance&#62; &#40;Transact-sql&#41;](../../relational-databases/system-functions/cdc-fn-cdc-get-net-changes-capture-instance-transact-sql.md)   
 [交易記錄 &#40;SQL Server&#41;](../../relational-databases/logs/the-transaction-log-sql-server.md)   
 [關於異動資料擷取 &#40;SQL Server&#41;](../../relational-databases/track-changes/about-change-data-capture-sql-server.md)  
  
  
