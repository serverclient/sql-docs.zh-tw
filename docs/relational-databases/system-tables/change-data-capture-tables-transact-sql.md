---
description: 異動資料擷取資料表 (Transact-SQL)
title: 變更 Data Capture 資料表 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: a4372d0b-50ca-4e58-80f6-2ed3cb52a84a
author: cawrites
ms.author: chadam
ms.openlocfilehash: 6ef80682d8d95188aa764c6e04e768c57298715a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094921"
---
# <a name="change-data-capture-tables-transact-sql"></a>異動資料擷取資料表 (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  異動資料擷取可在資料表上啟用變更追蹤，如此對資料表所做的資料操作語言 (DML) 和資料定義語言 (DDL) 變更就可以累加地載入資料倉儲中。 本節中的主題將說明儲存異動資料擷取作業所用之資訊的系統資料表。  
  
## <a name="in-this-section"></a>本節內容  
 [cdc. <capture_instance>_CT](../../relational-databases/system-tables/cdc-capture-instance-ct-transact-sql.md)  
 針對在相關聯之來源資料表中對擷取資料行所做的每個變更，各傳回一個資料列。  
  
 [cdc.captured_columns](../../relational-databases/system-tables/cdc-captured-columns-transact-sql.md)  
 針對在擷取執行個體中追蹤的每個資料行，各傳回一個資料列。  
  
 [cdc.change_tables](../../relational-databases/system-tables/cdc-change-tables-transact-sql.md)  
 針對資料庫中的每個變更資料表，各傳回一個資料列。  
  
 [cdc.ddl_history](../../relational-databases/system-tables/cdc-ddl-history-transact-sql.md)  
 針對在啟用異動資料擷取之資料表中所做的每個資料定義語言 (DDL) 變更，各傳回一個資料列。  
  
 [cdc.lsn_time_mapping](../../relational-databases/system-tables/cdc-lsn-time-mapping-transact-sql.md)  
 針對在變更資料表中具有資料列的每筆交易，各傳回一個資料列。 這個資料表是用來在記錄序號 (LSN) 認可值與交易認可的時間之間進行對應。  
  
 [cdc.index_columns](../../relational-databases/system-tables/cdc-index-columns-transact-sql.md)  
 針對每一個與變更資料表相關聯的索引資料行，各傳回一個資料列。  
  
 [dbo.cdc_jobs &#40;Transact-sql&#41;](../../relational-databases/system-tables/dbo-cdc-jobs-transact-sql.md)  
 傳回異動資料擷取代理程式作業的組態參數。  
  
## <a name="see-also"></a>另請參閱  
 [異動資料擷取預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/change-data-capture-stored-procedures-transact-sql.md)   
 [異動資料擷取函數 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/change-data-capture-functions-transact-sql.md)  
  
  
