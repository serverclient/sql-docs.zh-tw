---
description: CREATE WORKLOAD 分類器 (Transact-SQL)
title: CREATE WORKLOAD 分類器 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/11/2020
ms.prod: sql
ms.prod_service: sql-data-warehouse
ms.reviewer: jrasnick
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- WORKLOAD CLASSIFIER
- WORKLOAD_CLASSIFIER_TSQL
- CREATE WORKLOAD CLASSIFIER
- CREATE_WORKLOAD_CLASSIFIER_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE WORKLOAD CLASSIFIER statement
ms.assetid: ''
author: ronortloff
ms.author: rortloff
monikerRange: =azure-sqldw-latest
ms.openlocfilehash: 6a81c3164e183bb8a202e4ac537298c093e6d873
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644007"
---
# <a name="create-workload-classifier-transact-sql"></a>CREATE WORKLOAD CLASSIFIER (Transact-SQL)

[!INCLUDE [asa](../../includes/applies-to-version/asa.md)]

建立用於工作負載管理的分類器物件。  分類器會根據分類器陳述式定義中指定的參數，將連入要求指派給工作負載群組。  每個提交的要求都會評估分類器。  如果要求與分類器不相符，則會將其指派給預設的工作負載群組。  預設工作負載群組為 smallrc 資源類別。

> [!NOTE]
> 工作負載分類器會取代 sp_addrolemember 資源類別指派。  建立工作負載分類器之後，請執行 sp_droprolemember 以移除任何多餘的資源類別對應。

 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md).  
  
## <a name="syntax"></a>語法

```syntaxsql
CREATE WORKLOAD CLASSIFIER classifier_name  
WITH  
    (   WORKLOAD_GROUP = 'name'  
    ,   MEMBERNAME = 'security_account' 
[ [ , ] WLM_LABEL = 'label' ]  
[ [ , ] WLM_CONTEXT = 'context' ]  
[ [ , ] START_TIME = 'HH:MM' ]  
[ [ , ] END_TIME = 'HH:MM' ]  
  
[ [ , ] IMPORTANCE = { LOW | BELOW_NORMAL | NORMAL | ABOVE_NORMAL | HIGH }]) 
[;]
```
[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]

## <a name="arguments"></a>引數

 *classifier_name*  
 指定用來識別工作負載分類器的名稱。  classifier_name 是一種 sysname。  它的長度最多可以是 128 個字元，且在執行個體內必須是唯一的。

 *WORKLOAD_GROUP* =  *'name'*    
 當分類器規則符合條件時，name 會將要求對應至工作負載群組。  name 是一種 sysname。  它的長度最多可以是 128 個字元，且在分類器建立時必須是有效的工作負載群組名稱。

 可用的工作負載群組可以在 [sys.workload_management_workload_groups](../../relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql.md) 目錄檢視中找到。

 *MEMBERNAME* =  *'security_account'*     
 用來分類的安全性帳戶。  Security_account 是一種 sysname，沒有預設值。 Security_account 可以是資料庫使用者、資料庫角色、Azure Active Directory 登入或 Azure Active Directory 群組。
 
 *WLM_LABEL*   
 指定可分類要求的標籤值。  Label 是 nvarchar(255) 類型的選擇性參數。  在要求中使用 [OPTION (LABEL)](/azure/sql-data-warehouse/sql-data-warehouse-develop-label)，以符合分類器設定。

範例：

```sql
CREATE WORKLOAD CLASSIFIER wcELTLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'ELTRole'  
 ,WLM_LABEL      = 'dimension_loads' )

SELECT COUNT(*) 
  FROM DimCustomer
  OPTION (LABEL = 'dimension_loads')
```

*WLM_CONTEXT*  
指定可分類要求的工作階段內容值。  context 是 nvarchar(255) 類型的選擇性參數。  在提交設定工作階段內容的要求之前，使用變數名稱等於 [ 的 ](../../relational-databases/system-stored-procedures/sp-set-session-context-transact-sql.md?view=azure-sqldw-latest&preserve-view=true)sys.sp_set_session_context`wlm_context`。

範例：

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoad WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'ELTRole'
 ,WLM_CONTEXT    = 'dim_load' )
 
--set session context
EXEC sys.sp_set_session_context @key = 'wlm_context', @value = 'dim_load'

--run multiple statements using the wlm_context setting
SELECT COUNT(*) FROM stg.daily_customer_load
SELECT COUNT(*) FROM stg.daily_sales_load

--turn off the wlm_context session setting
EXEC sys.sp_set_session_context @key = 'wlm_context', @value = null
```

*START_TIME* 和 *END_TIME*  
指定要求可以分類的 start_time 和 end_time。  start_time 和 end_time 都是 UTC 時區的 HH:MM 格式。  start_time and end_time 必須同時指定。

範例：

```sql
CREATE WORKLOAD CLASSIFIER wcELTLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME     = 'ELTRole'  
 ,START_TIME     = '22:00'
 ,END_TIME       = '02:00' )
```

*IMPORTANCE* = { LOW \| BELOW_NORMAL \| NORMAL \| ABOVE_NORMAL \| HIGH }  
指定要求的相對重要性。  重要性為下列其中一項：

- LOW
- BELOW_NORMAL
- NORMAL (預設)
- ABOVE_NORMAL
- HIGH  

如果未指定重要性，則會使用工作負載群組的重要性設定。  預設工作負載群組重要性是 NORMAL。  重要性會影響所排定要求的順序，進而授與資源和鎖定的優先存取權。

## <a name="classification-parameter-weighting"></a>分類參數加權

要求可以符合多個分類器。  分類器參數具有加權。  加權較高的比對分類器會用於指派工作負載群組和重要性。  加權如下所示：

|分類器參數 |Weight   |
|---------------------|---------|
|USER                 |64       |
|ROLE                 |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

請考慮下列分類器設定。

```sql
CREATE WORKLOAD CLASSIFIER classifierA WITH  
( WORKLOAD_GROUP = 'wgDashboards'  
 ,MEMBERNAME     = 'userloginA'
 ,IMPORTANCE     = HIGH
 ,WLM_LABEL      = 'salereport' )

CREATE WORKLOAD CLASSIFIER classifierB WITH  
( WORKLOAD_GROUP = 'wgUserQueries'  
 ,MEMBERNAME     = 'userloginA'
 ,IMPORTANCE     = LOW
 ,START_TIME     = '18:00'
 ,END_TIME       = '07:00' )
```

使用者 `userloginA` 是針對這兩個分類器所設定。  如果 userloginA 在 UTC 下午 6 點和上午 7 點之間執行帶有等於 `salesreport` 的標籤的查詢，則該要求將被分類為具有高重要性的 wgDashboards 工作負載群組。  預期可能會針對非工作時間報告將要求分類為重要性較低的 wgUserQueries，但是 WLM_LABEL 的加權高於 START_TIME/END_TIME。  分類器 A 的加權為 80 (使用者 64，加上 WLM_LABEL 的 16)。  分類器 B 的加權為 68 (使用者 64，加上 START_TIME/END_TIME 的 4)。  在本例中，您可以將 WLM_LABEL 新增至分類器 B。

 如需詳細資訊，請參閱[工作負載加權](/azure/sql-data-warehouse/sql-data-warehouse-workload-classification#classification-weighting)。

## <a name="permissions"></a>權限

 需要 CONTROL DATABASE 權限。  
  
## <a name="examples"></a>範例

 下列範例會顯示如何建立名為 `wgcELTRole` 的工作負載分類器。 它會使用 staticrc20 工作負載群組、使用者 `ELTRole`，並將重要性設定為 `above_normal`。

```sql
CREATE WORKLOAD CLASSIFIER wgcELTRole
  WITH (WORKLOAD_GROUP = 'staticrc20'
       ,MEMBERNAME = 'ELTRole'
      ,IMPORTANCE = above_normal);
```

## <a name="see-also"></a>另請參閱

[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] 分類](/azure/sql-data-warehouse/sql-data-warehouse-workload-classification)</br>
[DROP WORKLOAD CLASSIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/drop-workload-classifier-transact-sql.md)</br>
[sys.workload_management_workload_classifiers](../../relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql.md)</br>
[sys.workload_management_workload_classifier_details](../../relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql.md)

