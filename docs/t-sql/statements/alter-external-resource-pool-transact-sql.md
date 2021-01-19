---
description: ALTER EXTERNAL RESOURCE POOL (Transact-SQL)
title: ALTER EXTERNAL RESOURCE POOL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/06/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: machine-learning-services
ms.topic: language-reference
f1_keywords:
- ALTER_EXTERNAL_RESOURCE_POOL_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER EXTERNAL RESOURCE POOL statement
ms.assetid: 634c327d-971b-49ba-b8a2-e243a04040db
author: dphansen
ms.author: davidph
manager: cgronlund
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15'
ms.openlocfilehash: feaf2da954465b17fb9beb948e47af9905c91512
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170230"
---
# <a name="alter-external-resource-pool-transact-sql"></a>ALTER EXTERNAL RESOURCE POOL (Transact-SQL)
[!INCLUDE [SQL Server 2016 and later](../../includes/applies-to-version/sqlserver2016.md)]

變更 Resource Governor 外部集區，其指定外部處理序可以使用的資源。 

::: moniker range="=sql-server-2016"
若是 [!INCLUDE[sssql15-md](../../includes/sssql16-md.md)] 中的 [!INCLUDE[rsql-productname-md](../../includes/rsql-productname-md.md)]，外部集區會掌管 `rterm.exe`、`BxlServer.exe` 及其衍生的其他處理序。
::: moniker-end

::: moniker range=">=sql-server-2017||>=sql-server-linux-ver15"
若是 [!INCLUDE[rsql-productnamenew-md](../../includes/rsql-productnamenew-md.md)]，外部集區會控管 `rterm.exe`、`python.exe`、`BxlServer.exe` 及其所繁衍的其他處理序。
::: moniker-end

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。

## <a name="syntax"></a>語法
::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
```syntaxsql
ALTER EXTERNAL RESOURCE POOL { pool_name | "default" }
[ WITH (
    [ MAX_CPU_PERCENT = value ]
    [ [ , ] MAX_MEMORY_PERCENT = value ]
    [ [ , ] MAX_PROCESSES = value ]
    )
]
[ ; ]
  
<CPU_range_spec> ::=
{ CPU_ID | CPU_ID  TO CPU_ID } [ ,...n ]
```  
::: moniker-end
::: moniker range="=sql-server-2016||=sql-server-2017"
 ```syntaxsql

ALTER EXTERNAL RESOURCE POOL { pool_name | "default" }
[ WITH (
    [ MAX_CPU_PERCENT = value ]
    [ [ , ] AFFINITY CPU =
            {
                AUTO
              | ( <cpu_range_spec> )
              | NUMANODE = (( <NUMA_node_id> )
            } ]   
    [ [ , ] MAX_MEMORY_PERCENT = value ]
    [ [ , ] MAX_PROCESSES = value ]
    )
]
[ ; ]
  
<CPU_range_spec> ::=
{ CPU_ID | CPU_ID  TO CPU_ID } [ ,...n ]
```  
::: moniker-end 

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

{ *pool_name* | "default" }  
這是現有的使用者定義外部資源集區名稱，或是安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時建立的預設外部資源集區名稱。
搭配 `ALTER EXTERNAL RESOURCE POOL` 使用時，"default" 必須加上引號 ("") 或方括號 ([]) 才能避免與系統保留字 `DEFAULT` 產生衝突。

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
MAX_CPU_PERCENT =*value*  
在出現 CPU 競爭時，指定外部資源集區中所有要求可接收的最大平均 CPU 頻寬。 *值* 是整數。 允許的 *value* 範圍為 1 至 100。

MAX_MEMORY_PERCENT =*value*  
指定在此外部資源集區中，可供要求使用的伺服器記憶體總量。 *值* 是整數。 允許的 *value* 範圍為 1 至 100。

MAX_PROCESSES =*value*  
指定允許外部資源集區使用的處理序數目上限。 指定 0 來為集區設定無限的閾值，這在之後只有電腦資源繫結會對其建立繫結。
::: moniker-end

::: moniker range="=sql-server-2016||=sql-server-2017"
MAX_CPU_PERCENT =*value*  
在出現 CPU 競爭時，指定外部資源集區中所有要求可接收的最大平均 CPU 頻寬。 *值* 是整數。 允許的 *value* 範圍為 1 至 100。

AFFINITY {CPU = AUTO | ( \<CPU_range_spec> ) | NUMANODE = (\<NUMA_node_range_spec>)}  
將外部資源集區附加到指定的 CPU。

AFFINITY CPU = **(** \<CPU_range_spec> **)** 會將外部資源集區對應到由指定 CPU_ID 所識別的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] CPU。 當使用 AFFINITY NUMANODE = **(** \<NUMA_node_range_spec> **)** 時，外部資源集區會與對應到指定 NUMA 節點或節點範圍的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 實體 CPU 同質化。

MAX_MEMORY_PERCENT =*value*  
指定在此外部資源集區中，可供要求使用的伺服器記憶體總量。 *值* 是整數。 允許的 *value* 範圍為 1 至 100。

MAX_PROCESSES =*value*  
指定允許外部資源集區使用的處理序數目上限。 指定 0 來為集區設定無限的閾值，這在之後只有電腦資源繫結會對其建立繫結。
::: moniker-end
## <a name="remarks"></a>備註

當您執行 [ALTER RESOURCE GOVERNOR RECONFIGURE](../../t-sql/statements/alter-resource-governor-transact-sql.md) 陳述式時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 將實作資源集區。

如需資源集區的一般資訊，請參閱 [Resource Governor 資源集區](../../relational-databases/resource-governor/resource-governor-resource-pool.md)、[sys.resource_governor_external_resource_pools &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-external-resource-pools-transact-sql.md)及 [sys.dm_resource_governor_external_resource_pool_affinity &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-external-resource-pool-affinity-transact-sql.md)。  

如需使用外部資源集區來管理機器學習工作的特定資訊，請參閱 [SQL Server 中的機器學習資源管理](../../machine-learning/administration/resource-governor.md)...
## <a name="permissions"></a>權限

需要 `CONTROL SERVER` 權限。

## <a name="examples"></a>範例

下列陳述式會變更外部集區，其限制 CPU 使用量為 50%，電腦中可用記憶體的最大記憶體為 25%。
::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
```sql
ALTER EXTERNAL RESOURCE POOL ep_1
WITH (
    MAX_CPU_PERCENT = 50
    , MAX_MEMORY_PERCENT = 25
);
GO
ALTER RESOURCE GOVERNOR RECONFIGURE;
GO
```
::: moniker-end

::: moniker range="=sql-server-2016||=sql-server-2017"
```sql
ALTER EXTERNAL RESOURCE POOL ep_1
WITH (
    MAX_CPU_PERCENT = 50
    , AFFINITY CPU = AUTO
    , MAX_MEMORY_PERCENT = 25
);
GO
ALTER RESOURCE GOVERNOR RECONFIGURE;
GO
```
::: moniker-end

## <a name="see-also"></a>另請參閱

+ [SQL Server 中的機器學習資源管理](../../machine-learning/administration/resource-governor.md)
+ [外部指令碼已啟用伺服器組態選項](../../database-engine/configure-windows/external-scripts-enabled-server-configuration-option.md)
+ [CREATE EXTERNAL RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-resource-pool-transact-sql.md)
+ [DROP EXTERNAL RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/drop-external-resource-pool-transact-sql.md)
+ [ALTER RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-pool-transact-sql.md)
+ [CREATE WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/create-workload-group-transact-sql.md)
+ [資源管理員資源集區](../../relational-databases/resource-governor/resource-governor-resource-pool.md)
+ [ALTER RESOURCE GOVERNOR &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-governor-transact-sql.md) 
