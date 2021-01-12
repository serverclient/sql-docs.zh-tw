---
description: sysmergepartitioninfo (Transact-SQL)
title: sysmergepartitioninfo (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sysmergepartitioninfo_TSQL
- sysmergepartitioninfo
dev_langs:
- TSQL
helpviewer_keywords:
- sysmergepartitioninfo system table
ms.assetid: 7429ad2c-dd33-4f7d-89cc-700e083af518
author: cawrites
ms.author: chadam
ms.openlocfilehash: 8a94f14212f2603639c1d272da4d35e9bea9fb29
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100487"
---
# <a name="sysmergepartitioninfo-transact-sql"></a>sysmergepartitioninfo (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  提供每個發行項之資料分割的相關資訊。 針對本機資料庫中所定義的每個合併發行項，各包含一個資料列。 這份資料表儲存在發行集和訂閱資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**>artid**|**uniqueidentifier**|給定發行項的唯一識別碼。|  
|**pubid**|**uniqueidentifier**|這個發行集的唯一識別碼；在加入發行集時產生。|  
|**partition_view_id**|**int**|這份資料表的資料分割檢視識別碼。 這份檢視顯示發行項中的每個資料列到它所屬的不同資料分割識別碼的對應。|  
|**repl_view_id**|**int**|即將加入。|  
|**partition_deleted_view_rule**|**nvarchar(4000)**|在合併式複寫觸發程序內，用來根據舊資料行值擷取每個已刪除或更新的資料列之資料分割識別碼的 SQL 陳述式|  
|**partition_inserted_view_rule**|**nvarchar(4000)**|在合併式複寫觸發程序內，用來根據新資料行值擷取每個已插入或更新的資料列之資料分割識別碼的 SQL 陳述式。|  
|**membership_eval_proc_name**|**sysname**|評估 **MSmerge_contents** 中資料列之目前資料分割識別碼的程式名稱。|  
|**column_list**|**nvarchar(4000)**|發行項中所複寫的資料行清單 (以逗號分隔)。|  
|**column_list_blob**|**nvarchar(4000)**|發行項中所複寫的資料行清單 (以逗號分隔)，其中包括二進位大型物件資料行。|  
|**expand_proc**|**sysname**|這是用來重新評估「新插入的父資料列之所有子資料列的資料分割識別碼」及「已經歷資料分割變更或已被刪除之父資料列的資料分割識別碼」的程序名稱。|  
|**logical_record_parent_nickname**|**int**|邏輯記錄中給定發行項最上層父系的暱稱。|  
|**logical_record_view**|**int**|輸出對應於每個子系 rowguid 之最上層父發行項 rowguid 的檢視。|  
|**logical_record_deleted_view_rule**|**nvarchar(4000)**|類似于 **logical_record_view**，但它會在 update 和 delete 觸發程式中顯示「已刪除」資料表中的子資料列。|  
|**logical_record_level_conflict_detection**|**bit**|指出應該在邏輯記錄層級或資料列或資料行層級偵測衝突。<br /><br /> **0** = 使用資料列或資料行層級的衝突偵測。<br /><br /> **1** = 使用邏輯記錄衝突偵測，其中當發行者端的資料列變更，以及訂閱者端相同的邏輯記錄變更時，會以衝突的方式處理。<br /><br /> 當這個值是 **1** 時，只能使用邏輯記錄層級的衝突解決。|  
|**logical_record_level_conflict_resolution**|**bit**|指出應該在邏輯記錄層級或資料列或資料行層級解決衝突。<br /><br /> **0** = 使用資料列或資料行層級的解析。<br /><br /> **1** = 當發生衝突時，來自優勝者的整個邏輯記錄會覆寫遺失端的整個邏輯記錄。<br /><br /> **1** 值可搭配邏輯記錄層級偵測和資料列或資料行層級偵測使用。|  
|**partition_options**|**tinyint**|定義發行項資料進行資料分割的方式，當所有資料列只屬於單一資料分割或單一訂閱時，能夠使效能最佳化。 *partition_options* 可以是下列其中一個值。<br /><br /> **0** = 發行項的篩選是靜態的，或不產生每個資料分割的唯一資料子集，也就是「重迭」的資料分割。<br /><br /> **1** = 資料分割重迭，在訂閱者端進行的 DML 更新無法變更資料列所屬的資料分割。<br /><br /> **2** = 發行項的篩選會產生非重迭的資料分割，但多個訂閱者可以接收相同的資料分割。<br /><br /> **3** = 發行項的篩選會產生對每個訂閱而言是唯一的非重迭資料分割。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
