---
description: CREATE WORKLOAD GROUP (Transact-SQL)
title: CREATE WORKLOAD GROUP (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/27/2020
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- WORKLOAD GROUP
- WORKLOAD_GROUP_TSQL
- CREATE WORKLOAD GROUP
- CREATE_WORKLOAD_GROUP_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE WORKLOAD GROUP statement
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=sql-server-2016||>=sql-server-linux-2017||=azure-sqldw-latest||=azuresqldb-mi-current'
ms.openlocfilehash: 1ad781bd554d7bcf506371ab71e3441e38f91f8c
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643965"
---
# <a name="create-workload-group-transact-sql"></a>CREATE WORKLOAD GROUP (Transact-SQL)

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](create-workload-group-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-workload-group-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server-and-sql-managed-instance"></a>SQL Server 與 SQL 受控執行個體

[!INCLUDE [CREATE WORKLOAD GROUP](../../includes/create-workload-group.md)]
  
::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](create-workload-group-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL 受控執行個體\*_** &nbsp;
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](create-workload-group-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server-and-sql-managed-instance"></a>SQL Server 與 SQL 受控執行個體

[!INCLUDE [CREATE WORKLOAD GROUP](../../includes/create-workload-group.md)]

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](create-workload-group-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](create-workload-group-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_** &nbsp;
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

建立工作負載群組。 工作負載群組是一組要求的容器，而且是在系統上設定工作負載管理的基礎。 工作負載群組提供保留資源以進行工作負載隔離、包含資源、定義每個要求的資源，以及遵守執行規則的能力。 一旦陳述式完成，設定就會生效。

:::image type="icon" source="../../database-engine/configure-windows/media/topic-link.gif"::: [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

```syntaxsql
CREATE WORKLOAD GROUP group_name
 WITH
 (   MIN_PERCENTAGE_RESOURCE = value 
   , CAP_PERCENTAGE_RESOURCE = value 
   , REQUEST_MIN_RESOURCE_GRANT_PERCENT = value
  [ [ , ] REQUEST_MAX_RESOURCE_GRANT_PERCENT = value ]
  [ [ , ] IMPORTANCE = { LOW | BELOW_NORMAL | NORMAL | ABOVE_NORMAL | HIGH } ]
  [ [ , ] QUERY_EXECUTION_TIMEOUT_SEC = value ] )
  [ ; ]

```

*group_name*</br>
指定用來識別工作負載群組的名稱。 *group_name* 是一種 sysname。 它的長度最多可以是 128 個字元，且在執行個體內必須是唯一的。

*MIN_PERCENTAGE_RESOURCE* = 值</br>
針對該工作負載群組指定一個保證的最小資源配置，該資源不會與其他工作負載群組共用。 *value* 為從 0 到 100 的整數範圍。 所有工作負載群組之間的 min_percentage_resource 總和不能超過 100。 min_percentage_resource 的值不能大於 cap_percentage_resource。 每個服務等級允許最小的有效值。 如需詳細資訊，請參閱[有效值](#effective-values)。

*CAP_PERCENTAGE_RESOURCE* = 值</br>
指定工作負載群組中所有要求的資源使用率上限。 允許的整數值範圍為 1 到 100。 cap_percentage_resource 的值必須大於 min_percentage_resource。 如果 min_percentage_resource 在其他工作負載群組中設定為大於零，則可以減小 cap_percentage_resource 的有效值。

*REQUEST_MIN_RESOURCE_GRANT_PERCENT* = 值</br>
設定每個要求配置的最小資源量。 *value* 為必要的參數，其十進位範圍介於 0.75 到 100.00 之間。 request_min_resource_grant_percent 的值必須是 0.25 的倍數，必須是 min_percentage_resource 的因數，而且小於 cap_percentage_resource。 每個服務等級允許最小的有效值。 如需詳細資訊，請參閱[有效值](#effective-values)。

例如：

```sql
CREATE WORKLOAD GROUP wgSample 
WITH
  ( MIN_PERCENTAGE_RESOURCE = 26                -- integer value
    , REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3.25 -- factor of 26 (guaranteed a minimum of 8 concurrency)
    , CAP_PERCENTAGE_RESOURCE = 100 )
```

請考慮用於資源類別的值，作為 request_min_resource_grant_percent 的指導方針。  下表包含 Gen2 的資源配置。

|資源類別|資源百分比|
|---|---|
|Smallrc|3%|
|Mediumrc|10%|
|Largerc|22%|
|Xlargerc|70%|
|||

*REQUEST_MAX_RESOURCE_GRANT_PERCENT* = 值</br>         
設定每個要求配置的資源數量上限。 *value* 為選擇性參數，預設值等於 request_min_resource_grant_percent。 *value* 必須大於或等於 request_min_resource_grant_percent。 當 equest_max_resource_grant_percent 的值大於 request_min_resource_grant_percent 而且有可用的系統資源時，會將其他資源配置給要求。

*IMPORTANCE* = { LOW \| BELOW_NORMAL \| NORMAL \| ABOVE_NORMAL \| HIGH }</br>        
指定要求在工作負載群組中的相對重要性。 下列任一個為其重要性，其中 NORMAL 為預設值：

- LOW
- BELOW_NORMAL
- NORMAL (預設)
- ABOVE_NORMAL
- HIGH

在工作負載群組中設定的重要性，是工作負載群組中所有要求的預設重要性。 使用者也可以在分類器層級設定重要性，該重要性可以覆寫工作負載群組的重要性設定。 這樣可以區分工作負載群組內要求的重要性，以便更快速地存取非保留的資源。 當跨工作負載群組的 min_percentage_resource 總和小於 100 時，會根據重要性來指派非保留的資源。

*QUERY_EXECUTION_TIMEOUT_SEC* = 值</br>     
指定查詢在取消之前可以執行的最長時間 (以秒為單位)。 *value* 必須是 0 或正整數。 值的預設設定為 0，這代表查詢永不逾時。QUERY_EXECUTION_TIMEOUT_SEC 會在查詢處於執行中狀態時計算，而非在查詢排入佇列時。

## <a name="remarks"></a>備註

系統會針對回溯相容性自動建立對應至資源類別的工作負載群組。 這些系統定義的工作負載群組無法卸除。 可以建立額外 8 位使用者定義的工作負載群組。

如果工作負載群組是在 `min_percentage_resource` 大於零的情況下建立，則 `CREATE WORKLOAD GROUP` 陳述式將會排入佇列，直到有足夠的資源可建立工作負載群組為止。

## <a name="effective-values"></a>有效值

`min_percentage_resource`、`cap_percentage_resource`、`request_min_resource_grant_percent` 和 `request_max_resource_grant_percent` 參數具有在目前服務層級內容及其他工作負載群組組態中調整的有效值。

`request_min_resource_grant_percent` 參數具有有效值，因為視服務層級而定，每個查詢都會有所需的最少資源。  例如，在最低服務層級 DW100c，每個要求都需要最少 25% 的資源。  如果工作負載群組設定為 3% 的 `request_min_resource_grant_percent` 和 `request_max_resource_grant_percent`，則當執行個體啟動時，這兩個參數的有效值會調整為 25%。  如果執行個體擴大為 DW1000c，則兩個參數的設定值和有效值為 3%，因為 3% 是該服務層級所支援的最小值。  如果執行個體的擴大至高於 DW1000c，則這兩個參數的設定值和有效值都會保持為 3%。  請參閱下表，以取得不同服務層級之有效值的詳細資料。

|服務等級|REQUEST_MIN_RESOURCE_GRANT_PERCENT 的最低有效值|並行查詢數目上限|
|---|---|---|
|DW100c|25%|4|
|DW200c|12.5%|8|
|DW300c|8%|12|
|DW400c|6.25%|16|
|DW500c|5%|20|
|DW1000c|3%|32|
|DW1500c|3%|32|
|DW2000c|2%|48|
|DW2500c|2%|48|
|DW3000c|1.5%|64|
|DW5000c|1.5%|64|
|DW6000c|0.75%|128|
|DW7500c|0.75%|128|
|DW10000c|0.75%|128|
|DW15000c|0.75%|128|
|DW30000c|0.75%|128|
||||

`min_percentage_resource` 參數必須大於或等於有效 `request_min_resource_grant_percent`。 `min_percentage_resource` 設定為小於有效 `min_percentage_resource` 的工作負載群組，其值在執行階段會調整為零。 發生這種情況時，針對 `min_percentage_resource` 設定的資源可在所有工作負載群組間共用。 例如，`min_percentage_resource` 為 10% 且在 DW1000c 上執行的工作負載群組 `wgAdHoc`，會有 10% 的有效 `min_percentage_resource` (3% 是 DW1000c 所支援的最小值)。 `wgAdhoc` 在 DW100c 上的有效 min_percentage_resource 為 0%。 針對 `wgAdhoc` 設定的 10% 會在所有工作負載群組之間共用。

`cap_percentage_resource` 參數也具有有效值。 如果工作負載群組 `wgAdhoc` 設定為 100% 的 `cap_percentage_resource`，而另一個工作負載群組 `wgDashboards` 是以 25% 的 `min_percentage_resource` 所建立，則 `wgAdhoc` 的有效 `cap_percentage_resource` 會變成 75%。

若要了解工作負載群組的執行階段值，最簡單的方式就是查詢系統檢視 [sys.databases dm_workload_management_workload_groups_stats](../../relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql.md)。

## <a name="permissions"></a>權限

需要 `CONTROL DATABASE` 權限

## <a name="see-also"></a>另請參閱

- [DROP WORKLOAD GROUP &#40;Transact-SQL&#41;](drop-workload-group-transact-sql.md)
- [ALTER WORKLOAD GROUP &#40;Transact-SQL&#41;](alter-workload-group-transact-sql.md)
- [sys.workload_management_workload_groups](../../relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql.md)
- [sys.dm_workload_management_workload_groups_stats](../../relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql.md)
- [快速入門：使用 T-SQL 設定工作負載隔離](/azure/sql-data-warehouse/quickstart-configure-workload-isolation-tsql)

::: moniker-end
