---
description: ALTER WORKLOAD GROUP (Transact-SQL)
title: ALTER WORKLOAD GROUP (Transact-SQL)
ms.custom: ''
ms.date: 05/05/2020
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_WORKLOAD_GROUP_TSQL
- ALTER WORKLOAD GROUP
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER WORKLOAD GROUP statement
ms.assetid: 957addce-feb0-4e54-893e-5faca3cd184c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=sql-server-2016||>=sql-server-linux-2017||=azure-sqldw-latest||=azuresqldb-mi-current'
ms.openlocfilehash: 2c9c90cb1107d33f6ecbb42e0974371555d9b9aa
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098418"
---
# <a name="alter-workload-group-transact-sql"></a>ALTER WORKLOAD GROUP (Transact-SQL)

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-workload-group-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-workload-group-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server-and-sql-managed-instance"></a>SQL Server 與 SQL 受控執行個體

[!INCLUDE [ALTER WORKLOAD GROUP](../../includes/alter-workload-group.md)]
  
::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](alter-workload-group-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL 受控執行個體\*_** &nbsp;
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](alter-workload-group-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server-and-sql-managed-instance"></a>SQL Server 與 SQL 受控執行個體

[!INCLUDE [ALTER WORKLOAD GROUP](../../includes/alter-workload-group.md)]

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](alter-workload-group-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](alter-workload-group-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_** &nbsp;
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

變更現有的工作負載群組。

請參閱以下的 `ALTER WORKLOAD GROUP` 行為一節，進一步了解 `ALTER WORKLOAD GROUP` 如何在具有執行和佇列要求的系統上運作。 

對 [CREATE WORKLOAD GROUP](create-workload-group-transact-sql.md) 的限制也適用於 `ALTER WORKLOAD GROUP`。  在修改參數之前，請查詢 [sys.workload_management_workload_groups](../../relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql.md) 以確保值位於可接受的範圍內。

## <a name="syntax"></a>語法

```syntaxsql
ALTER WORKLOAD GROUP group_name
 WITH
 ([       MIN_PERCENTAGE_RESOURCE = value ]
  [ [ , ] CAP_PERCENTAGE_RESOURCE = value ]
  [ [ , ] REQUEST_MIN_RESOURCE_GRANT_PERCENT = value ]
  [ [ , ] REQUEST_MAX_RESOURCE_GRANT_PERCENT = value ] 
  [ [ , ] IMPORTANCE = { LOW | BELOW_NORMAL | NORMAL | ABOVE_NORMAL | HIGH }]
  [ [ , ] QUERY_EXECUTION_TIMEOUT_SEC = value ] )
  [ ; ]
  ```

## <a name="arguments"></a>引數

group_name  
是否已變更現有使用者定義的工作負載群組名稱。  group_name 不可變更。 

MIN_PERCENTAGE_RESOURCE = 值  
值是 0 到 100 的整數範圍。  變更 min_percentage_resource 時，所有工作負載群組的 min_percentage_resource 總和不能超過 100。  如要變更 min_percentage_resource，則必須先完成工作負載群組中正在執行的所有查詢，命令才會完成。  如需進一步的詳細資料，請參閱本文件的＜ALTER WORKLOAD GROUP 行為＞一節。

CAP_PERCENTAGE_RESOURCE = 值  
值是 1 到 100 的整數範圍。  cap_percentage_resource 的值必須大於 min_percentage_resource。  若要變更 cap_percentage_resource，則必須先完成工作負載群組中正在執行的所有查詢，命令才會完成。  如需進一步的詳細資料，請參閱本文件的＜ALTER WORKLOAD GROUP 行為＞一節。 

REQUEST_MIN_RESOURCE_GRANT_PERCENT = 值  
值是介於 0.75 到 100.00 之間的小數。  request_min_resource_grant_percent 其值必須是 min_percentage_resource 的因數，且小於 cap_percentage_resource。 
  
REQUEST_MAX_RESOURCE_GRANT_PERCENT = 值  
值是小數，且必須大於 request_min_resource_grant_percent。

IMPORTANCE = { LOW \|  BELOW_NORMAL \| NORMAL \| ABOVE_NORMAL \| HIGH }  
變更工作負載群組要求的預設重要性。

QUERY_EXECUTION_TIMEOUT_SEC = 值  
變更查詢在取消之前可執行的最長時間 (以秒為單位)。 值必須是 0 或正整數。 值的預設設定為 0，這代表沒有限制。   

## <a name="permissions"></a>權限

需要 CONTROL DATABASE 權限

## <a name="example"></a>範例

下例會檢查目錄檢視中的 wgDataLoads 值，並變更這些值。

```sql
SELECT *
FROM sys.workload_management_workload_groups  
WHERE [name] = 'wgDataLoads'

ALTER WORKLOAD GROUP wgDataLoads WITH
( MIN_PERCENTAGE_RESOURCE            = 40
 ,CAP_PERCENTAGE_RESOURCE            = 80
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 10 )
 ```

## <a name="alter-workload-group-behavior"></a>ALTER WORKLOAD GROUP 行為

系統無論何時都有 3 個要求類型
- 尚未分類的要求。
- 已分類並正在等待物件鎖定或系統資源的要求。
- 已分類且正在執行的要求。

設定的生效時間會隨著工作負載群組屬性變更而不同。

**重要性或 query_execution_timeout** 未分類的要求會針對重要性和 query_execution_timeout 屬性挑選新組態值。  正在等待和執行的要求會按舊組態執行。  無論工作負載群組中是否有正在執行的查詢，`ALTER WORKLOAD GROUP` 要求都會立即執行。

**Request_min_resource_grant_percent 或 request_max_resource_grant_percent** 針對 request_min_resource_grant_percent 和 request_max_resource_grant_percent，正在執行的要求會按舊組態執行。  正在等待的要求和未分類要求會挑選新組態值。  無論工作負載群組中是否有正在執行的查詢，`ALTER WORKLOAD GROUP` 要求都會立即執行。

**Min_percentage_resource 或 cap_percentage_resource** 針對 min_percentage_resource 和 cap_percentage_resource，正在執行的要求會按舊組態執行。  正在等待的要求和未分類要求會挑選新組態值。 

變更 min_percentage_resource 和 cap_percentage_resource 需要清空正在變更之工作負載群組中正在執行的要求。  減少 min_percentage_resource 時，釋放的資源會傳回共用集區，讓其他工作負載群組中的要求能夠利用。  相反地，增加 min_percentage_resource 則會等候直到只使用共用集區所需資源的要求完成。  變更工作負載群組作業將具有共用資源的優先存取權，在共用集區中等待執行的其他要求則往後排。  如果 min_percentage_resource 的總和超過 100%，則 ALTER WORKLOAD GROUP 要求立即失敗。 

**鎖定行為** 變更工作負載群組需要對所有工作負載群組執行全域鎖定。  變更工作負載群組的要求會排入已提交的 create 或 drop 工作負載群組要求的後面。  如果一次提交一批 alter 陳述式，則會依其提交順序處理。  

## <a name="see-also"></a>另請參閱

- [CREATE WORKLOAD GROUP &#40;Transact-SQL&#41;](create-workload-group-transact-sql.md)
- [DROP WORKLOAD GROUP &#40;Transact-SQL&#41;](drop-workload-group-transact-sql.md)
- [sys.workload_management_workload_groups](../../relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql.md)
- [sys.dm_workload_management_workload_groups_stats](../../relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql.md)
- [快速入門：使用 T-SQL 設定工作負載隔離](/azure/sql-data-warehouse/quickstart-configure-workload-isolation-tsql)

::: moniker-end
