---
description: DROP WORKLOAD GROUP (Transact-SQL)
title: DROP WORKLOAD GROUP (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/10/2020
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP_WORKLOAD_GROUP_TSQL
- DROP WORKLOAD GROUP
dev_langs:
- TSQL
helpviewer_keywords:
- DROP WORKLOAD GROUP statement
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=sql-server-2016||>=sql-server-linux-2017||=azure-sqldw-latest||=azuresqldb-mi-current'
ms.openlocfilehash: f3dd9c4a9b1fde5306846d6008e05d7b6b3d8782
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099314"
---
# <a name="drop-workload-group-transact-sql"></a>DROP WORKLOAD GROUP (Transact-SQL)

[!INCLUDE[select-product](../../includes/select-product.md)]

::: moniker range=">=sql-server-2016||>=sql-server-linux-2017"

:::row:::
    :::column:::
        **_\* SQL Server \*_** &nbsp;
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](drop-workload-group-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](drop-workload-group-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="sql-server-and-sql-managed-instance"></a>SQL Server 與 SQL 受控執行個體

[!INCLUDE [DROP WORKLOAD GROUP](../../includes/drop-workload-group.md)]
  
::: moniker-end
::: moniker range="=azuresqldb-mi-current"

:::row:::
    :::column:::
        [SQL Server](drop-workload-group-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* SQL 受控執行個體\*_** &nbsp;
    :::column-end:::
    :::column:::
        [Azure Synapse<br />Analytics](drop-workload-group-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)
    :::column-end:::
:::row-end:::

&nbsp;

##  <a name="sql-server-and-sql-managed-instance"></a>SQL Server 與 SQL 受控執行個體

[!INCLUDE [DROP WORKLOAD GROUP](../../includes/drop-workload-group.md)]

::: moniker-end
::: moniker range="=azure-sqldw-latest"

:::row:::
    :::column:::
        [SQL Server](drop-workload-group-transact-sql.md?view=sql-server-ver15&preserve-view=true)
    :::column-end:::
    :::column:::
        [SQL 受控執行個體](drop-workload-group-transact-sql.md?view=azuresqldb-mi-current&preserve-view=true)
    :::column-end:::
    :::column:::
        **_\* Azure Synapse<br />Analytics \*_** &nbsp;
    :::column-end:::
:::row-end:::

&nbsp;

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics 

卸除工作負載群組。  一旦陳述式完成，設定就會生效。

:::image type="icon" source="../../database-engine/configure-windows/media/topic-link.gif"::: [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

## <a name="syntax"></a>語法

```syntaxsql
DROP WORKLOAD GROUP group_name  
```

## <a name="arguments"></a>引數

*group_name*  
現有使用者定義之工作負載群組的名稱。

## <a name="remarks"></a>備註

如果工作負載群組有分類器，就無法卸除該工作負載群組。  在卸除工作負載群組之前，請先卸除分類器。  如果有使用中的要求正在使用要卸除的工作負載群組中的資源，則會在其後封鎖該卸除工作負載陳述式。

## <a name="examples"></a>範例

使用下列程式碼範例，確定在卸除工作負載群組之前需要卸除哪些分類器。

```sql
SELECT c.name as classifier_name
      ,'DROP WORKLOAD CLASSIFIER '+c.name as drop_command
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_groups g
    ON c.group_name = g.name
  WHERE g.name = 'wgXYZ' --change the filter to the workload being dropped
```

## <a name="permissions"></a>權限

需要 CONTROL DATABASE 權限

## <a name="see-also"></a>另請參閱

- [CREATE WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/create-workload-group-transact-sql.md)
- [ALTER WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/alter-workload-group-transact-sql.md)
- [sys.workload_management_workload_groups](../../relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql.md)
- [sys.dm_workload_management_workload_groups_stats](../../relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql.md)
- [快速入門：使用 T-SQL 設定工作負載隔離](/azure/sql-data-warehouse/quickstart-configure-workload-isolation-tsql)

::: moniker-end
