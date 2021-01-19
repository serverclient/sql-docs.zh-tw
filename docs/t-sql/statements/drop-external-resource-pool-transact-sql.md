---
description: DROP EXTERNAL RESOURCE POOL (Transact-SQL)
title: DROP EXTERNAL RESOURCE POOL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/06/2020
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: machine-learning-services
ms.topic: language-reference
f1_keywords:
- DROP EXTERNAL RESOURCE POOL
- DROP_EXTERNAL_RESOURCE_POOL_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- DROP EXTERNAL RESOURCE POOL statement
ms.assetid: e2fa01bd-96ff-4ea9-bb08-6cb6b6adf68c
author: dphansen
ms.author: davidph
manager: cgronlund
ms.openlocfilehash: 08f25755ca4742b0e14379c40e22e99741eedbda
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172390"
---
# <a name="drop-external-resource-pool-transact-sql"></a>DROP EXTERNAL RESOURCE POOL (Transact-SQL)
[!INCLUDE [SQL Server 2016 and later](../../includes/applies-to-version/sqlserver2016.md)]

刪除 Resource Governor 用來定義外部處理序資源的外部資源集區。 

::: moniker range="=sql-server-2016||>=sql-server-linux-ver15"
若是 [!INCLUDE[sssql15-md](../../includes/sssql16-md.md)] 中的 [!INCLUDE[rsql-productname-md](../../includes/rsql-productname-md.md)]，外部集區會掌管 `rterm.exe`、`BxlServer.exe` 及其衍生的其他處理序。
::: moniker-end

::: moniker range=">=sql-server-2017||>=sql-server-linux-ver15"
若是 [!INCLUDE[rsql-productnamenew-md](../../includes/rsql-productnamenew-md.md)]，外部集區會控管 `rterm.exe`、`python.exe`、`BxlServer.exe` 及其所繁衍的其他處理序。
::: moniker-end

外部資源集區是使用 [CREATE EXTERNAL RESOURCE POOL & #40;TRANSACT-SQL & #41;](../../t-sql/statements/create-external-resource-pool-transact-sql.md) 建立的，並使用 [ALTER EXTERNAL RESOURCE POOL & #40;TRANSACT-SQL & #41;](../../t-sql/statements/alter-external-resource-pool-transact-sql.md) 加以修改。  
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DROP EXTERNAL RESOURCE POOL pool_name  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*pool_name*  
要刪除的外部資源集區名稱。  
  
## <a name="remarks"></a>備註

如果外部資源集區包含工作負載群組，您就無法將其卸除。  

您無法卸除資源管理員的預設或內部集區。  

當您要執行 DDL 陳述式時，建議您先熟悉資源管理員的狀態。 如需詳細資訊，請參閱 [Resource Governor](../../relational-databases/resource-governor/resource-governor.md)。  

## <a name="permissions"></a>權限

需要 `CONTROL SERVER` 權限。  

## <a name="examples"></a>範例

下列範例會卸除名稱為 `ex_pool` 的外部資源集區。  

```sql
DROP EXTERNAL RESOURCE POOL ex_pool;  
GO  
ALTER RESOURCE GOVERNOR RECONFIGURE;  
GO  
```  

## <a name="see-also"></a>另請參閱

+ [外部指令碼已啟用伺服器組態選項](../../database-engine/configure-windows/external-scripts-enabled-server-configuration-option.md)
+ [CREATE EXTERNAL RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-resource-pool-transact-sql.md)
+ [ALTER EXTERNAL RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/alter-external-resource-pool-transact-sql.md)
+ [DROP WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/drop-workload-group-transact-sql.md)
+ [DROP RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/drop-resource-pool-transact-sql.md)
