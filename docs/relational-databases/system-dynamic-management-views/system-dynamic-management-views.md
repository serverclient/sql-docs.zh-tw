---
description: '動態管理檢視 (Transact-sql) '
title: 動態管理檢視 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/29/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- database scoped dynamic management objects [SQL Server]
- dynamic management objects [SQL Server], about dynamic management objects
- server state information [SQL Server]
- dynamic management functions [SQL Server]
- metadata [SQL Server], dynamic management objects
- dynamic management views [SQL Server]
- DMVs [SQL Server]
- functions [SQL Server], dynamic management
- server scoped dynamic management objects [SQL Server]
- dynamic management objects [SQL Server]
ms.assetid: cf893ecb-0bf6-4cbf-ac00-8a1099e405b1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 422984471491d402102942304c06f37ef1a14727
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098859"
---
# <a name="system-dynamic-management-views"></a>系統動態管理檢視
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  動態管理檢視和函數傳回伺服器狀態資訊，這項資訊可用來監視伺服器執行個體的健全狀況、診斷問題和調整效能。  
  
> [!IMPORTANT]  
>  動態管理檢視和函數傳回內部實作特定狀態資料。 其結構描述和傳回的資料在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的未來版本中可能會改變。 因此，未來版本中的動態管理檢視和函數不一定與這個版本中的動態管理檢視和函數相容。 例如，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的未來版本中，Microsoft 可能會在資料行清單結尾加入資料行，藉以擴充任何動態管理檢視的定義。 我們建議您不要在實際執行的程式碼中使用 `SELECT * FROM dynamic_management_view_name` 語法，因為傳回的資料行數可能會變更和破壞應用程式。  
  
 動態管理檢視和函數有兩種類型：  
  
-   伺服器範圍的動態管理檢視和函數。 這些都需要伺服器的 VIEW SERVER STATE 權限。  
  
-   資料庫範圍的動態管理檢視和函數。 這些都需要資料庫的 VIEW DATABASE STATE 權限。  
  
## <a name="querying-dynamic-management-views"></a>查詢動態管理檢視  
 動態管理檢視可使用兩部分、三部分或四部分名稱在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式中參考。 另一方面，動態管理函數可使用兩部分或三部分名稱在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式中參考。 動態管理檢視和函數不能使用一部分名稱在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式中參考。  
  
 所有動態管理檢視和函數都存在於 sys 結構描述中，並遵照這個命名慣例 dm_*。 當您使用動態管理檢視或函數時，必須使用 sys 結構描述來加上檢視或函數名稱的前置詞。 例如，若要查詢 dm_os_wait_stats 動態管理檢視，請執行下列查詢：  
  
 ```sql
SELECT wait_type, wait_time_ms  
FROM sys.dm_os_wait_stats;  
```  
  
### <a name="required-permissions"></a>必要權限  
 若要查詢動態管理檢視或函數，需要物件的 SELECT 權限和 VIEW SERVER STATE 或 VIEW DATABASE STATE 權限。 這可讓您選擇性地限制使用者的存取或登入到動態管理檢視和函數。 若要這麼做，先在 master 中建立使用者，然後在您不要他們存取的動態管理檢視或函數上拒絕使用者 SELECT 權限。 在此之後，不論使用者的資料庫內容如何，使用者將無法從這些動態管理檢視或函數中選取。  
  
> [!NOTE]  
>  因為以 DENY 的優先順序為準，如果已授與使用者 VIEW SERVER STATE 權限，但拒絕 VIEW DATABASE STATE 權限，則使用者可以查看伺服器層級資訊，但不能查看資料庫層級資訊。  
  
## <a name="in-this-section"></a>本節內容  
 動態管理檢視和函數已組織成下列類別目錄。  

:::row:::
    :::column:::
        [Always On 可用性群組動態管理檢視和函數 (Transact-sql) ](../../relational-databases/system-dynamic-management-views/always-on-availability-groups-dynamic-management-views-functions.md)

        [異動資料擷取相關的動態管理檢視 &#40;Transact-SQL&#41;](change-data-capture-sys-dm-cdc-errors.md)

        [變更追蹤相關的動態管理檢視](change-tracking-sys-dm-tran-commit-table.md)

        [Common Language Runtime 相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/common-language-runtime-related-dynamic-management-views-transact-sql.md)

        [與資料庫鏡像相關的動態管理檢視 &#40;Transact-sql&#41;](database-mirroring-sys-dm-db-mirroring-auto-page-repair.md)

        [資料庫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/database-related-dynamic-management-views-transact-sql.md)

        [執行相關的動態管理檢視和函式 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)

        [擴充事件動態管理檢視](../../relational-databases/system-dynamic-management-views/extended-events-dynamic-management-views.md)

        [Filestream 和 FileTable 動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/filestream-and-filetable-dynamic-management-views-transact-sql.md)

        [&#40;Transact-sql&#41;的全文檢索搜尋和語義搜尋動態管理檢視和函數 ](../../relational-databases/system-dynamic-management-views/full-text-and-semantic-search-dynamic-management-views-functions.md)

        [異地複寫動態管理檢視和函式 &#40;Azure SQL Database&#41;](../../relational-databases/system-dynamic-management-views/geo-replication-dynamic-management-views-and-functions-azure-sql-database.md)

        [索引相關的動態管理檢視和函式 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/index-related-dynamic-management-views-and-functions-transact-sql.md)

        [I/o 相關的動態管理檢視和函數 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/i-o-related-dynamic-management-views-and-functions-transact-sql.md)
    :::column-end:::
    :::column:::
        [記憶體最佳化的資料表動態管理檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/memory-optimized-table-dynamic-management-views-transact-sql.md)

        [物件相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/object-related-dynamic-management-views-and-functions-transact-sql.md)

        [查詢通知相關的動態管理檢視 &#40;Transact-sql&#41;](query-notifications-sys-dm-qn-subscriptions.md)

        [複寫相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/replication-related-dynamic-management-views-transact-sql.md)

        [Resource Governor 相關動態管理檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/resource-governor-related-dynamic-management-views-transact-sql.md)

        [安全性相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql.md)

        [伺服器相關的動態管理檢視和函式 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/server-related-dynamic-management-views-and-functions-transact-sql.md)

        [Service Broker 相關的動態管理檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/service-broker-related-dynamic-management-views-transact-sql.md)

        [空間資料相關的動態管理檢視和函數 &#40;Transact-sql&#41;](./spatial-data-sys-dm-db-objects-disabled-on-compatibility-level-change.md)

        [Azure Synapse Analytics 和平行處理資料倉儲動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-and-parallel-data-warehouse-dynamic-management-views.md)

        [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)

        [Stretch Database 動態管理檢視 &#40;Transact-sql&#41;]()

        [交易相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)
    :::column-end:::
:::row-end:::

## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;授與伺服器許可權 ](../../t-sql/statements/grant-server-permissions-transact-sql.md)   
 [GRANT 資料庫權限 &#40;Transact-SQL&#41;](../../t-sql/statements/grant-database-permissions-transact-sql.md)   
 [&#40;Transact-sql&#41;的系統檢視 ](../../t-sql/language-reference.md)  
  
