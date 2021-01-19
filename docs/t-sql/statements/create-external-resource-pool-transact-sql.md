---
description: CREATE EXTERNAL RESOURCE POOL (Transact-SQL)
title: CREATE EXTERNAL RESOURCE POOL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/06/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: machine-learning-services
ms.topic: language-reference
f1_keywords:
- CREATE EXTERNAL RESOURCE POOL
- CREATE EXTERNAL_RESOURCE_POOL_TSQL
- EXTERNAL RESOURCE POOL
- EXTERNAL_RESOURCE_POOL_TSQL
- EXTERNAL RESOURCE
- EXTERNAL_RESOURCE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE EXTERNAL RESOURCE POOL statement
ms.assetid: 8cc798ad-c395-461c-b7ff-8c561c098808
author: dphansen
ms.author: davidph
manager: cgronlund
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15'
ms.openlocfilehash: 7f57b0e829c240813d6f1a453d764e64bce6b279
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171600"
---
# <a name="create-external-resource-pool-transact-sql"></a>CREATE EXTERNAL RESOURCE POOL (Transact-SQL)
[!INCLUDE [SQL Server 2016 and later](../../includes/applies-to-version/sqlserver2016.md)]

建立外部集區，以定義外部處理序的資源。 資源集區代表資料庫引擎執行個體的實體資源 (記憶體和 CPU) 子集。 Resource Governor 可在資源集區間散發伺服器資源，最多可達 64 個集區。

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
CREATE EXTERNAL RESOURCE POOL pool_name  
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
CREATE EXTERNAL RESOURCE POOL pool_name  
[ WITH (  
    [ MAX_CPU_PERCENT = value ]  
    [ [ , ] AFFINITY CPU =    
            {  
                AUTO   
              | ( <cpu_range_spec> )   
              | NUMANODE = ( <NUMA_node_id> )   
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

*pool_name*  
這是外部資源集區的使用者定義名稱。 *pool_name* 為英數字元，最多可有 128 個字元。 此引數在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體內必須是唯一的，且必須滿足[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
MAX_CPU_PERCENT =*value*  
出現 CPU 爭用時，外部資源集區中所有要求可接收的最大平均 CPU 頻寬。 *值* 是整數。 允許的 *value* 範圍為 1 至 100。


MAX_MEMORY_PERCENT =*value*  
指定在此外部資源集區中，可供要求使用的伺服器記憶體總量。 *值* 是整數。 允許的 *value* 範圍為 1 至 100。

MAX_PROCESSES =*value*  
允許外部資源集區使用的處理序數目上限。 0 = 集區的閾值沒有限制，這在之後只會受電腦資源約束。
::: moniker-end

::: moniker range="=sql-server-2016||=sql-server-2017"
MAX_CPU_PERCENT =*value*  
出現 CPU 爭用時，外部資源集區中所有要求可接收的最大平均 CPU 頻寬。 *值* 是整數。 允許的 *value* 範圍為 1 至 100。

AFFINITY {CPU = AUTO | ( <CPU_range_spec>) | NUMANODE = (\<NUMA_node_range_spec>)} 將外部資源集區附加到指定的 CPU。

AFFINITY CPU = **(** <CPU_range_spec> **)** 會將外部資源集區對應到給定之 CPU_ID 所識別的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] CPU。

當您使用 AFFINITY NUMANODE = **(\<NUMA_node_range_spec> **)** 時，外部資源集區會與對應到指定 NUMA 節點或節點範圍的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 實體 CPU 同質化。 

MAX_MEMORY_PERCENT =*value*  
指定在此外部資源集區中，可供要求使用的伺服器記憶體總量。 *值* 是整數。 允許的 *value* 範圍為 1 至 100。

MAX_PROCESSES =*value*  
允許外部資源集區使用的處理序數目上限。 0 = 集區的閾值沒有限制，這在之後只會受電腦資源約束。
::: moniker-end

## <a name="remarks"></a>備註

當您執行 [ALTER RESOURCE GOVERNOR RECONFIGURE](../../t-sql/statements/alter-resource-governor-transact-sql.md) 陳述式時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 將實作資源集區。

如需資源集區的一般資訊，請參閱 [Resource Governor 資源集區](../../relational-databases/resource-governor/resource-governor-resource-pool.md)、[sys.resource_governor_external_resource_pools &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-external-resource-pools-transact-sql.md)及 [sys.dm_resource_governor_external_resource_pool_affinity &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-external-resource-pool-affinity-transact-sql.md)。

如需如何管理用於機器學習之外部資源集區的特定資訊，請參閱 [SQL Server 中的機器學習資源管理](../../machine-learning/administration/resource-governor.md)。 

## <a name="permissions"></a>權限

需要 `CONTROL SERVER` 權限。

## <a name="examples"></a>範例

外部集區的 CPU 使用率限制為百分之 75。 記憶體的最大值為電腦上可用記憶體的百分之 30。

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15"
```sql
CREATE EXTERNAL RESOURCE POOL ep_1
WITH (  
    MAX_CPU_PERCENT = 75
    , MAX_MEMORY_PERCENT = 30
);
GO
ALTER RESOURCE GOVERNOR RECONFIGURE;
GO
```
::: moniker-end

::: moniker range="=sql-server-2016||=sql-server-2017"
```sql
CREATE EXTERNAL RESOURCE POOL ep_1
WITH (  
    MAX_CPU_PERCENT = 75
    , AFFINITY CPU = AUTO
    , MAX_MEMORY_PERCENT = 30
);
GO
ALTER RESOURCE GOVERNOR RECONFIGURE;
GO
```
::: moniker-end

## <a name="see-also"></a>另請參閱

+ [外部指令碼已啟用伺服器組態選項](../../database-engine/configure-windows/external-scripts-enabled-server-configuration-option.md)
+ [sp_execute_external_script &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)
+ [ALTER EXTERNAL RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/alter-external-resource-pool-transact-sql.md)
+ [DROP EXTERNAL RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/drop-external-resource-pool-transact-sql.md)
+ [CREATE RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/create-resource-pool-transact-sql.md)
+ [CREATE WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/create-workload-group-transact-sql.md)
+ [資源管理員資源集區](../../relational-databases/resource-governor/resource-governor-resource-pool.md)
+ [sys.resource_governor_external_resource_pools &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-external-resource-pools-transact-sql.md)
+ [sys.dm_resource_governor_external_resource_pool_affinity &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-external-resource-pool-affinity-transact-sql.md)
+ [ALTER RESOURCE GOVERNOR &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-governor-transact-sql.md)
