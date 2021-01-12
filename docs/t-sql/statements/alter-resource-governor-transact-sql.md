---
description: ALTER RESOURCE GOVERNOR (Transact-SQL)
title: ALTER RESOURCE GOVERNOR (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/01/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER RESOURCE GOVERNOR
- ALTER_RESOURCE_GOVERNOR_TSQL
- ALTER RESOURCE GOVERNOR RECONFIGURE
- ALTER_RESOURCE_GOVERNOR_RECONFIGURE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER RESOURCE GOVERNOR
- RECONFIGURE, ALTER RESOURCE GOVERNOR
ms.assetid: 442c54bf-a0a6-4108-ad20-db910ffa6e3c
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 087b03fe9fab6e505295121cdd34b07dbf9db2c2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092050"
---
# <a name="alter-resource-governor-transact-sql"></a>ALTER RESOURCE GOVERNOR (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  這個陳述式會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中用來執行下列 Resource Governor 動作：  
  
-   當發出 CREATE|ALTER|DROP WORKLOAD GROUP 或 CREATE|ALTER|DROP RESOURCE POOL 或 CREATE|ALTER|DROP EXTERNAL RESOURCE POOL 陳述式時，套用指定的設定變更。  
  
-   啟用或停用資源管理員。  
  
-   設定傳入之要求的分類。  
  
-   重設工作負載群組和資源集區統計資料。  
  
-   設定每一個磁碟區的最大 I/O 作業量。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER RESOURCE GOVERNOR   
      { DISABLE | RECONFIGURE }  
    | WITH ( CLASSIFIER_FUNCTION = { schema_name.function_name | NULL } )  
    | RESET STATISTICS  
    | WITH ( MAX_OUTSTANDING_IO_PER_VOLUME = value )   
[ ; ]  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 DISABLE  
 停用資源管理員。 停用資源管理員會產生下列結果：  
  
-   系統不會執行分類函數。  
  
-   所有新的連接都會自動分類至預設群組中。  
  
-   系統起始的要求會分類至內部工作負載群組中。  
  
-   所有現有的工作負載群組和資源集區設定都會重設為預設值。 在此情況下，如果到達限制，系統將不會引發任何事件。  
  
-   一般的系統監視不會受到影響。  
  
-   您可以變更組態，但這些變更要在資源管理員啟用之後才會生效。  
  
-   一旦重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之後，資源管理員將不會載入其組態，而只會具有預設和內部群組與集區。  
  
 RECONFIGURE  
 當資源管理員未啟用時，RECONFIGURE 會啟用資源管理員。 啟用資源管理員會產生下列結果：  
  
-   系統會針對新的連接執行分類函數，以便將其工作負載指派給工作負載群組。  
  
-   系統會接受並強制執行資源管理員組態中指定的資源限制。  
  
-   啟用資源管理員之前存在的要求會受到停用資源管理員時所做的任何組態變更影響。  
  
 當 Resource Governor 在執行時，RECONFIGURE 會在執行 CREATE|ALTER|DROP WORKLOAD GROUP 或 CREATE|ALTER|DROP RESOURCE POOL 或 CREATE|ALTER|DROP EXTERNAL RESOURCE POOL 陳述式時套用所要求的任何設定變更。  
  
> [!IMPORTANT]  
>  若要讓任何組態變更生效，必須先發出 ALTER RESOURCE GOVERNOR RECONFIGURE。  
  
 CLASSIFIER_FUNCTION = { _schema_name_ **.** _function_name_ | NULL }  
 註冊 *schema_name.function_name* 所指定的分類函數。 這個函數會將每個新的工作階段分類，然後將工作階段要求和查詢指派到工作負載群組。 使用 NULL 時，會將新的工作階段自動指派到預設的工作負載群組。  
  
 RESET STATISTICS  
 針對所有工作負載群組與資源集區重設統計資料。 如需詳細資訊，請參閱 [sys.dm_resource_governor_workload_groups &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql.md) 和 [sys.dm_resource_governor_resource_pools &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql.md)。  
  
 MAX_OUTSTANDING_IO_PER_VOLUME = *value*  
 **適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。  
  
 設定每一個磁碟區已排入佇列的最大 I/O 作業量。 這些 I/O 作業可以是任何規模的讀取或寫入。  MAX_OUTSTANDING_IO_PER_VOLUME 的最大值為 100。 其值並非百分比。 這項設定是設計來針對磁碟區的 IO 特性調整 IO 資源管理。 建議您試驗不同的值，並考慮使用 IOMeter、[DiskSpd](https://gallery.technet.microsoft.com/DiskSpd-a-robust-storage-6cd2f223) 或 SQLIO (已淘汰) 等校正工具識別儲存體子系統的最大值。 此設定提供系統層級安全性檢查，即使其他集區的 MAX_IOPS_PER_VOLUME 設為無限制，仍可讓 SQL Server 達到資源集區的 IOPS 下限。 如需有關 MAX_IOPS_PER_VOLUME 的詳細資訊，請參閱 [CREATE RESOURCE POOL](../../t-sql/statements/create-resource-pool-transact-sql.md)。  
  
## <a name="remarks"></a>備註  
 ALTER RESOURCE GOVERNOR DISABLE、ALTER RESOURCE GOVERNOR RECONFIGURE 和 ALTER RESOURCE GOVERNOR RESET STATISTICS 無法用於使用者交易內。  
  
 RECONFIGURE 參數是 Resource Governor 語法的一部分，不應該與屬於個別 DDL 陳述式的 [RECONFIGURE](../../t-sql/language-elements/reconfigure-transact-sql.md) 混淆。  
  
 建議您在執行 DDL 陳述時前，先熟悉資源管理員的狀態。 如需詳細資訊，請參閱 [Resource Governor](../../relational-databases/resource-governor/resource-governor.md)。  
  
## <a name="permissions"></a>權限  
 需要 CONTROL SERVER 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-starting-the-resource-governor"></a>A. 啟動資源管理員  
 首次安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時，會停用資源管理員。 下列範例會啟動資源管理員。 陳述式執行後，資源管理員會執行，而且可能會使用預先定義的工作負載群組和資源集區。  
  
```sql  
ALTER RESOURCE GOVERNOR RECONFIGURE;  
```  
  
### <a name="b-assigning-new-sessions-to-the-default-group"></a>B. 指派新的工作階段到預設檔案群組  
 下列範例會從資源管理員組態移除任何現有的分類函數，以將所有新增的工作階段指派到預設的工作負載群組。 當沒有任何函數被指定為分類函數時，所有新的工作階段都會指派到預設的工作負載群組。 這項變更僅會套用至新的工作階段。 現有的工作階段不會受到影響。  
  
```sql  
ALTER RESOURCE GOVERNOR WITH (CLASSIFIER_FUNCTION = NULL);  
GO  
ALTER RESOURCE GOVERNOR RECONFIGURE;  
```  
  
### <a name="c-creating-and-registering-a-classifier-function"></a>C. 建立和註冊分類函數  
 下列範例會建立名為 `dbo.rgclassifier_v1` 的分類函數。 這個函數會根據使用者名稱或應用程式名稱將每個新的工作階段分類，然後將工作階段要求和查詢指派到特定的工作負載群組。 沒有對應到指定的使用者或應用程式名稱的工作階段會指派到預設的工作負載群組。 接著會註冊此分類函數並套用組態變更。  
  
```sql  
-- Store the classifier function in the master database.  
USE master;  
GO  
SET ANSI_NULLS ON;  
GO  
SET QUOTED_IDENTIFIER ON;  
GO  
CREATE FUNCTION dbo.rgclassifier_v1() RETURNS sysname   
WITH SCHEMABINDING  
AS  
BEGIN  
-- Declare the variable to hold the value returned in sysname.  
    DECLARE @grp_name AS sysname  
-- If the user login is 'sa', map the connection to the groupAdmin  
-- workload group.   
    IF (SUSER_NAME() = 'sa')  
        SET @grp_name = 'groupAdmin'  
-- Use application information to map the connection to the groupAdhoc  
-- workload group.  
    ELSE IF (APP_NAME() LIKE '%MANAGEMENT STUDIO%')  
        OR (APP_NAME() LIKE '%QUERY ANALYZER%')  
            SET @grp_name = 'groupAdhoc'  
-- If the application is for reporting, map the connection to  
-- the groupReports workload group.  
    ELSE IF (APP_NAME() LIKE '%REPORT SERVER%')  
        SET @grp_name = 'groupReports'  
-- If the connection does not map to any of the previous groups,  
-- put the connection into the default workload group.  
    ELSE  
        SET @grp_name = 'default'  
    RETURN @grp_name  
END;  
GO  
-- Register the classifier user-defined function and update the   
-- the in-memory configuration.  
ALTER RESOURCE GOVERNOR WITH (CLASSIFIER_FUNCTION=dbo.rgclassifier_v1);  
GO  
ALTER RESOURCE GOVERNOR RECONFIGURE;  
GO  
```  
  
### <a name="d-resetting-statistics"></a>D. 重設統計資料  
 下列範例會重設所有的工作負載群組和資源集區統計資料。  
  
```sql 
ALTER RESOURCE GOVERNOR RESET STATISTICS;  
```  
  
### <a name="e-setting-the-max_outstanding_io_per_volume-option"></a>E. 設定 MAX_OUTSTANDING_IO_PER_VOLUME 選項  
 下列範例會將 MAX_OUTSTANDING_IO_PER_VOLUME 選項設為 20。  
  
```sql  
ALTER RESOURCE GOVERNOR  
WITH (MAX_OUTSTANDING_IO_PER_VOLUME = 20);   
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/create-resource-pool-transact-sql.md)   
 [ALTER RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-pool-transact-sql.md)   
 [DROP RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/drop-resource-pool-transact-sql.md)   
 [CREATE EXTERNAL RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/create-external-resource-pool-transact-sql.md)   
 [DROP EXTERNAL RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/drop-external-resource-pool-transact-sql.md)   
 [ALTER EXTERNAL RESOURCE POOL &#40;Transact-SQL&#41;](../../t-sql/statements/alter-external-resource-pool-transact-sql.md)   
 [CREATE WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/create-workload-group-transact-sql.md)   
 [ALTER WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/alter-workload-group-transact-sql.md)   
 [DROP WORKLOAD GROUP &#40;Transact-SQL&#41;](../../t-sql/statements/drop-workload-group-transact-sql.md)   
 [資源管理員](../../relational-databases/resource-governor/resource-governor.md)   
 [sys.dm_resource_governor_workload_groups &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql.md)   
 [sys.dm_resource_governor_resource_pools &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql.md)  
  
  
