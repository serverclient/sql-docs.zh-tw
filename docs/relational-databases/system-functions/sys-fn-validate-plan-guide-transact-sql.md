---
description: sys.fn_validate_plan_guide (Transact-SQL)
title: sys.fn_validate_plan_guide (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.fn_validate_plan_guide
- sys.fn_validate_plan_guide_TSQL
- fn_validate_plan_guide
- fn_validate_plan_guide_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- fn_validate_plan_guide function
- sys.fn_validate_plan_guide function
ms.assetid: 3af8b47a-936d-4411-91d1-d2d16dda5623
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 7c9290f1d638f1eb2cf55c19d70d4e32a773bafe
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093788"
---
# <a name="sysfn_validate_plan_guide-transact-sql"></a>sys.fn_validate_plan_guide (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  確認指定之計畫指南的有效性。 當計畫指南套用到其查詢時，sys.fn_validate_plan_guide 函數會傳回所發生的第一個錯誤訊息。 當計畫指南有效時，會傳回空的資料列集。 當資料庫的實體設計變更後，計畫指南可能變成無效。 例如，如果計畫指南指定特定的索引，而且該索引接著遭到卸除，查詢將無法再使用該計畫指南。  
  
 您可以藉由驗證計畫指南，判斷最佳化工具是否可在不進行任何修改的情況下使用該指南。 根據函數的結果，您可以決定卸除計畫指南，然後藉由諸如重新建立計畫指南中指定之索引的方式，傳回查詢或修改資料庫設計。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
sys.fn_validate_plan_guide ( plan_guide_id )  
```  
  
## <a name="arguments"></a>引數  
 *plan_guide_id*  
 這是 [sys.plan_guides](../../relational-databases/system-catalog-views/sys-plan-guides-transact-sql.md) 目錄檢視中所報告之計劃指南的識別碼。 *plan_guide_id* 是 **int** ，沒有預設值。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|msgnum|**int**|錯誤訊息的識別碼。|  
|severity|**tinyint**|訊息的嚴重性層級，介於 1 至 25 之間。|  
|state|**smallint**|錯誤的狀態碼，可指出程式碼中的錯誤發生點。|  
|message|**nvarchar(2048)**|錯誤的訊息文字。|  
  
## <a name="permissions"></a>權限  
 OBJECT 範圍的計畫指南需要所參考物件的 VIEW DEFINITION 或 ALTER 權限，以及編譯計畫指南中所提供之查詢或批次的權限。 例如，如果批次包含 SELECT 陳述式，就會需要所參考物件的 SELECT 權限。  
  
 SQL 或 TEMPLATE 範圍的計畫指南需要資料庫的 ALTER 權限，以及編譯計畫指南中所提供之查詢或批次的權限。 例如，如果批次包含 SELECT 陳述式，就會需要所參考物件的 SELECT 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-validating-all-plan-guides-in-a-database"></a>A. 驗證資料庫中的所有計畫指南  
 下列範例會檢查目前資料庫中，所有計畫指南的有效性。 如果傳回空的結果集，則所有計畫指南都有效。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT plan_guide_id, msgnum, severity, state, message  
FROM sys.plan_guides  
CROSS APPLY fn_validate_plan_guide(plan_guide_id);  
GO  
```  
  
### <a name="b-testing-plan-guide-validation-before-implementing-a-change-to-the-database"></a>B. 將變更實作至資料庫之前，先測試計畫指南驗證  
 下列範例會使用明確的交易來卸除索引。 `sys.fn_validate_plan_guide`系統會執行此函式，以判斷此動作是否會讓資料庫中的任何計劃指南失效。 根據函數的結果，會認可 `DROP INDEX` 陳述式或回復交易，而且索引不會遭到卸除。  
  
```sql  
USE AdventureWorks2012;  
GO  
BEGIN TRANSACTION;  
DROP INDEX IX_SalesOrderHeader_CustomerID ON Sales.SalesOrderHeader;  
-- Check for invalid plan guides.  
IF EXISTS (SELECT plan_guide_id, msgnum, severity, state, message  
           FROM sys.plan_guides  
           CROSS APPLY sys.fn_validate_plan_guide(plan_guide_id))  
    ROLLBACK TRANSACTION;  
ELSE  
    COMMIT TRANSACTION;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [計劃指南](../../relational-databases/performance/plan-guides.md)   
 [sp_create_plan_guide &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-create-plan-guide-transact-sql.md)   
 [sp_create_plan_guide_from_handle &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-create-plan-guide-from-handle-transact-sql.md)  
  
  
