---
description: sys.dm_db_xtp_merge_requests (Transact-SQL)
title: sys.dm_db_xtp_merge_requests (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 02/01/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
ms.assetid: c1224e88-af74-4c99-ae32-d5d2c552a1f5
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 7a7f8f4a38bffb8abc6be1380a2225077130f143
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099845"
---
# <a name="sysdm_db_xtp_merge_requests-transact-sql"></a>sys.dm_db_xtp_merge_requests (Transact-SQL)

[!INCLUDE[sqlserver](../../includes/applies-to-version/sqlserver.md)]

追蹤資料庫合併要求。 合併要求可能是由 SQL Server 所產生，或者要求可能已由具有 [sys.sp_xtp_merge_checkpoint_files (transact-sql) ](../../relational-databases/system-stored-procedures/sys-sp-xtp-merge-checkpoint-files-transact-sql.md)的使用者提出。

> [!NOTE]
> 這個動態管理檢視 (DMV) sys.dm_db_xtp_merge_requests）存在，直到 Microsoft SQL Server 2014 為止。
> 但從 SQL Server 2016 開始，此 DMV 不再適用。

## <a name="columns-in-the-report"></a>報表中的資料行

| 資料行名稱 | 資料類型 | 描述 |
| :-- | :-- | :-- |
| request_state | TINYINT | 合併要求的狀態：<br/>0 = Requested<br/>1 = Pending<br/>2 = 已安裝<br/>3 = Abandoned |
| request_state_desc | nvarchar(60) | 要求目前狀態的意義：<br/><br/>要求-存在合併要求。<br/>暫止-正在處理合併。<br/>已安裝-合併已完成。<br/>已放棄-合併無法完成，可能是因為儲存體不足。 |
| destination_file_id | GUID | 用於合併來源檔案之目的地檔案的唯一識別碼。 |
| lower_bound_tsn | BIGINT | 目標合併檔案的最小時間戳記。 要合併之所有來源檔案的最低交易時間戳記。 |
| upper_bound_tsn | BIGINT | 目標合併檔案的最大時間戳記。 要合併之所有來源檔案的最高交易時間戳記。 |
| collection_tsn | BIGINT | 可收集目前資料列的時間戳記。<br/><br/>當 checkpoint_tsn 大於 collection_tsn 時，就會移除處於 Installed 狀態的資料列。<br/><br/>當 checkpoint_tsn 小於 collection_tsn 時，就會移除處於 Abandoned 狀態的資料列。 |
| checkpoint_tsn | BIGINT | 檢查點啟動的時間。<br/><br/>由時間戳記低於此數的交易所完成的所有刪除都會計算在新的資料檔案中。 剩餘的刪除則會移到目標差異檔案。 |
| sourcenumber_file_id | GUID | 最多16個內部檔案識別碼，可唯一識別合併中的原始程式檔。 |

## <a name="permissions"></a>權限

需要目前資料庫的 VIEW DATABASE STATE 權限。

## <a name="see-also"></a>另請參閱

[記憶體最佳化的資料表動態管理檢視 (Transact-SQL)](../../relational-databases/system-dynamic-management-views/memory-optimized-table-dynamic-management-views-transact-sql.md)
