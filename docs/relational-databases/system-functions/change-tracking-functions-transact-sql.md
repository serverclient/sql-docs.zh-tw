---
description: 變更追蹤函數 (Transact-SQL)
title: 變更追蹤函式 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/08/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- functions [SQL Server], change tracking
- change tracking [SQL Server], functions
ms.assetid: 04eb53c4-8b69-414e-9696-185d227fea35
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 5fe7ea18e564c177e2d9b113be29ac62c8193508
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095016"
---
# <a name="change-tracking-functions-transact-sql"></a>變更追蹤函數 (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  異動追蹤會記錄套用至追蹤資料表的插入、更新和刪除活動，並以容易取用的關聯式格式提供變更的詳細資料。 下列函數會傳回變更的相關資訊。  
  
|函式|描述|  
|--------------|-----------------|  
|[CHANGETABLE (變更) ](../../relational-databases/system-functions/changetable-transact-sql.md)|針對資料表在指定之版本之後發生的所有變更，傳回追蹤資訊。|  
|[CHANGETABLE (版本) ](../../relational-databases/system-functions/changetable-transact-sql.md)|針對指定的資料列傳回最新的變更追蹤資訊。|  
|[CHANGE_TRACKING_MIN_VALID_VERSION ( # B1 ](../../relational-databases/system-functions/change-tracking-min-valid-version-transact-sql.md)|當您使用 [CHANGETABLE](../../relational-databases/system-functions/changetable-transact-sql.md) 函數時，傳回可用於從指定資料表取得變更追蹤資訊的最小版本。|  
|[CHANGE_TRACKING_CURRENT_VERSION](../../relational-databases/system-functions/change-tracking-current-version-transact-sql.md)|取得與最後確認之交易相關聯的版本。 下次您使用 CHANGETABLE 列舉變更時，可以使用這個版本。|  
|[CHANGE_TRACKING_IS_COLUMN_IN_MASK](../../relational-databases/system-functions/change-tracking-is-column-in-mask-transact-sql.md)|解讀 CHANGETABLE (CHANGES ) 函數所傳回的 SYS_CHANGE_COLUMNS 值。|  
|[WITH CHANGE_TRACKING_CONTEXT](../../relational-databases/system-functions/with-change-tracking-context-transact-sql.md)|應用程式變更資料時，啟用變更內容的指定，例如，訂閱者識別碼。|  
  
## <a name="see-also"></a>另請參閱  
 [追蹤資料變更 &#40;SQL Server&#41;](../../relational-databases/track-changes/track-data-changes-sql-server.md)  
  
  
