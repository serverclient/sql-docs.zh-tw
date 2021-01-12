---
description: CHANGE_TRACKING_IS_COLUMN_IN_MASK (Transact-SQL)
title: CHANGE_TRACKING_IS_COLUMN_IN_MASK (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/08/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- CHANGE_TRACKING_IS_COLUMN_IN_MASK_TSQL
- CHANGE_TRACKING_IS_COLUMN_IN_MASK
dev_langs:
- TSQL
helpviewer_keywords:
- change tracking [SQL Server], CHANGE_TRACKING_IS_COLUMN_IN_MASK
- CHANGE_TRACKING_IS_COLUMN_IN_MASK
ms.assetid: 649b370b-da54-4915-919d-1b597a39d505
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: a9d5ee6c44e0b0ca9544ee4a32b7cbd455497fe6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095008"
---
# <a name="change_tracking_is_column_in_mask-transact-sql"></a>CHANGE_TRACKING_IS_COLUMN_IN_MASK (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  解讀 CHANGETABLE (CHANGES ) 函數所傳回的 SYS_CHANGE_COLUMNS 值。 這可讓應用程式決定指定的資料行是否要包含在針對 SYS_CHANGE_COLUMNS 傳回的值中。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
CHANGE_TRACKING_IS_COLUMN_IN_MASK ( column_id , change_columns )  
```  
  
## <a name="arguments"></a>引數  
 *column_id*  
 要經過檢查之資料行的識別碼。 您可以使用 [COLUMNPROPERTY](../../t-sql/functions/columnproperty-transact-sql.md) 函數來取得資料行識別碼。  
  
 *change_columns*  
 這是 [CHANGETABLE](../../relational-databases/system-functions/changetable-transact-sql.md) 資料的 SYS_CHANGE_COLUMNS 資料行中的二進位資料。  
  
## <a name="return-type"></a>傳回類型  
 **bit**  
  
## <a name="return-values"></a>傳回值  
 CHANGE_TRACKING_IS_COLUMN_IN_MASK 會傳回下列值。  
  
|傳回值|描述|  
|------------------|-----------------|  
|0|指定的資料行不在 *change_columns* 清單中。|  
|1|指定的資料行位於 *change_columns* 清單中。|  
  
## <a name="remarks"></a>備註  
 CHANGE_TRACKING_IS_COLUMN_IN_MASK 不會執行任何檢查以驗證 *column_id* 值，或從取得 *column_id* 的資料表取得 *change_columns* 參數。  
  
## <a name="examples"></a>範例  
 下列範例可判斷是否更新 `Salary` 資料表的 `Employees` 資料行。 函數會傳回資料 `COLUMNPROPERTY` 行的資料行識別碼 `Salary` 。 `@change_columns` 區域變數必須使用 CHANGETABLE 設定為查詢結果，做為資料來源。  
  
```sql  
SET @SalaryChanged = CHANGE_TRACKING_IS_COLUMN_IN_MASK  
    (COLUMNPROPERTY(OBJECT_ID('Employees'), 'Salary', 'ColumnId')  
    ,@change_columns);  
```  
  
## <a name="see-also"></a>另請參閱  
 [變更追蹤函數 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/change-tracking-functions-transact-sql.md)   
 [CHANGETABLE &#40;Transact-SQL&#41;](../../relational-databases/system-functions/changetable-transact-sql.md)   
 [追蹤資料變更 &#40;SQL Server&#41;](../../relational-databases/track-changes/track-data-changes-sql-server.md)  
  
  
