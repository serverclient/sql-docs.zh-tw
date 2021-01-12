---
description: CHANGE_TRACKING_MIN_VALID_VERSION (Transact-SQL)
title: CHANGE_TRACKING_MIN_VALID_VERSION (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/08/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- CHANGE_TRACKING_CLEANUP_VERSION
- CHANGE_TRACKING_CLEANUP_VERSION_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CHANGE_TRACKING_MIN_VALID_VERSION
- change tracking [SQL Server], CHANGE_TRACKING_MIN_VALID_VERSION
ms.assetid: 5a43d23f-adcf-4c0b-95ad-07cee03c1f9d
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 81e386ef391283f3a3f3095b7dd321055a676332
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093894"
---
# <a name="change_tracking_min_valid_version-transact-sql"></a>CHANGE_TRACKING_MIN_VALID_VERSION (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  當您使用 [CHANGETABLE](../../relational-databases/system-functions/changetable-transact-sql.md) 函數時，傳回可用於從指定的資料表取得變更追蹤資訊的最小版本。  
    
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
CHANGE_TRACKING_MIN_VALID_VERSION ( table_object_id )  
```  
  
## <a name="arguments"></a>引數  
 *table_object_id*  
 這是資料表的物件識別碼。 *table_object_id* 是 **int**。  
  
## <a name="return-type"></a>傳回類型  
 **bigint**  
  
## <a name="remarks"></a>備註  
 使用此函數可驗證 CHANGETABLE 的 *last_sync_version* 參數值。 如果 *last_sync_version* 小於此函數所報告的值，則稍後對 CHANGETABLE 呼叫所傳回的結果可能會無效。  
  
 CHANGE_TRACKING_MIN_VALID_VERSION 會使用下列資訊判斷傳回值：  
  
-   針對變更追蹤啟用資料表時。  
  
-   執行背景清除工作來移除早於您為資料庫指定之保留期的變更追蹤資訊時。  
  
-   資料表遭到截斷時。 這樣會移除與資料表相關聯的所有變更追蹤資訊。  
  
 如果下列任一條件成立，函數會傳回 NULL：  
  
-   資料庫未啟用變更追蹤。  
  
-   指定的資料表物件識別碼不適用於目前的資料庫。  
  
-   物件識別碼指定的資料表權限不足。  
  
## <a name="examples"></a>範例  
 下列範例會判斷指定的版本是否為有效版本。 此範例會取得 `dbo.Employees` 資料表的最小有效版本，然後將該版本與 `@last_sync_version` 變數的值相比較。 如果 `@last_sync_version` 的值小於 `@min_valid_version` 的值，已變更資料列的清單將無效。  
  
> [!NOTE]  
>  您通常會從您儲存同步資料所使用之最後一個版本號碼所在的資料表或其他位置取得值。  
  
```  
-- The tracked change is tagged with the specified context   
DECLARE @min_valid_version bigint, @last_sync_version bigint;  
  
SET @min_valid_version =   
CHANGE_TRACKING_MIN_VALID_VERSION(OBJECT_ID('dbo.Employees'));  
  
SET @last_sync_version = 11  
IF (@last_sync_version < @min_valid_version)  
-- Error � do not obtain changes  
ELSE  
-- Obtain changes using CHANGETABLE(CHANGES ...)  
```  
  
## <a name="see-also"></a>另請參閱  
 [變更追蹤函數 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/change-tracking-functions-transact-sql.md)   
 [sys.change_tracking_tables &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/change-tracking-catalog-views-sys-change-tracking-tables.md)  
  
  
