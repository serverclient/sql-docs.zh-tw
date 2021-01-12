---
description: sys.fn_cdc_get_max_lsn (Transact-SQL)
title: sys.fn_cdc_get_max_lsn (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.fn_cdc_get_max_lsn
- fn_cdc_get_max_lsn
- fn_cdc_get_max_lsn_TSQL
- sys.fn_cdc_get_max_lsn_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- fn_cdc_get_max_lsn
- sys.fn_cdc_get_max_lsn
ms.assetid: 93f3a4c8-b91f-4ebb-8e96-9397bb3a1c43
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 59613b93de2a086bfd4d0bac7fa0f8a8983d8ce5
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094983"
---
# <a name="sysfn_cdc_get_max_lsn-transact-sql"></a>sys.fn_cdc_get_max_lsn (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  從 [cdc.lsn_time_mapping](../../relational-databases/system-tables/cdc-lsn-time-mapping-transact-sql.md) 系統資料表的 start_lsn 資料行，傳回 (LSN) 的最大記錄序號。 您可以使用這個函數，針對任何擷取執行個體傳回異動資料擷取時間表的高端點。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.fn_cdc_get_max_lsn ()  
```  
  
## <a name="return-types"></a>傳回型別  
 **binary(10)**  
  
## <a name="remarks"></a>備註  
 此函數會傳回 [cdc.lsn_time_mapping](../../relational-databases/system-tables/cdc-lsn-time-mapping-transact-sql.md) 資料表的 start_lsn 資料行中的最大 LSN。 因此，它就是變更傳播至資料庫變更資料表時，由擷取處理序所處理的最後一個 LSN。 此外，它會當做所有時間表 (與針對資料庫定義的擷取執行個體相關聯) 的高端點使用。  
  
 此函數通常是用來取得查詢間隔的適當高端點。  
  
## <a name="permissions"></a>權限  
 需要 public 資料庫角色中的成員資格。  
  
## <a name="examples"></a>範例  
  
### <a name="a-returning-the-maximum-lsn-value"></a>A. 傳回最大的 LSN 值  
 下列範例會針對 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中的所有擷取執行個體，傳回最大的 LSN。  
  
```  
USE AdventureWorks2012;  
GO  
SELECT sys.fn_cdc_get_max_lsn()AS max_lsn;  
```  
  
### <a name="b-setting-the-high-endpoint-of-a-query-range"></a>B. 設定查詢範圍的高端點  
 下列範例會使用 `sys.fn_cdc_get_max_lsn` 所傳回的最大 LSN 來設定 `HumanResources_Employee` 擷取執行個體之查詢範圍的高端點。  
  
```  
USE AdventureWorks2012;  
GO  
DECLARE @from_lsn binary(10), @to_lsn binary(10);  
SET @from_lsn = sys.fn_cdc_get_min_lsn(N'HumanResources_Employee');  
SET @to_lsn = sys.fn_cdc_get_max_lsn();  
SELECT * FROM cdc.fn_cdc_get_all_changes_HumanResources_Employee(@from_lsn, @to_lsn, 'all');  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [sys.fn_cdc_get_min_lsn &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-cdc-get-min-lsn-transact-sql.md)   
 [交易記錄 &#40;SQL Server&#41;](../../relational-databases/logs/the-transaction-log-sql-server.md)  
  
  
