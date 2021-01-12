---
description: 查詢通知-sys.dm_qn_subscriptions
title: sys.dm_qn_subscriptions (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_qn_subscriptions
- dm_qn_subscriptions_TSQL
- sys.dm_qn_subscriptions
- sys.dm_qn_subscriptions_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_qn_subscriptions dynamic management view
ms.assetid: a3040ce6-f5af-48fc-8835-c418912f830c
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 62953d7f80a8d0fa327f37eadcc7a8dafd6fa734
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100038"
---
# <a name="query-notifications---sysdm_qn_subscriptions"></a>查詢通知-sys.dm_qn_subscriptions
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回有關伺服器之使用中查詢通知訂閱的資訊。 您可以使用這個檢視，檢查伺服器或指定資料庫中是否有使用中的訂閱，或檢查指定的伺服器主體。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**id**|**int**|訂閱的識別碼。|  
|**database_id**|**int**|執行通知查詢的資料庫識別碼。 這個資料庫會儲存與這項訂閱有關的資訊。|  
|**希**|**varbinary(85)**|建立和擁有這項訂閱之伺服器主體的安全性識別碼。|  
|object_id|**int**|儲存訂閱參數相關資訊的內部資料表識別碼。|  
|**created**|**datetime**|建立訂閱的日期和時間。|  
|**timeout**|**int**|訂閱的逾時 (以秒為單位)。 通知會標示為在過了這個時間之後引發。<br /><br /> 注意：實際引發時間可能大於指定的超時時間。但是，如果在指定的超時時間之後，但在引發訂用帳戶之前，會使訂閱失效的變更發生，就會 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 確保在進行變更時引發。|  
|**status**|**int**|指出訂閱的狀態。 如需狀態碼的清單，請參閱＜備註＞底下的表格。|  
  
## <a name="relationship-cardinalities"></a>關聯性基數  
  
|寄件者|收件者|開啟|類型|  
|----------|--------|--------|----------|  
|**sys.dm_qn_subscriptions**|**sys.databases**|**database_id**|多對一|  
|**sys.dm_qn_subscriptions**|**sys.internal_tables**|object_id|多對一|  
  
## <a name="remarks"></a>備註  
 狀態碼 0 表示未定義的狀態。  
  
 下列狀態碼表示因為變更所引發的訂閱：  
  
|程式碼|次要狀態|Info|  
|----------|------------------|----------|  
|65798|因為資料變更所以引發訂閱|插入觸發的訂閱|  
|65799|因為資料變更所以引發訂閱|刪除|  
|65800|因為資料變更所以引發訂閱|更新|  
|65801|因為資料變更所以引發訂閱|合併|  
|65802|因為資料變更所以引發訂閱|截斷資料表|  
|66048|因為已超過逾時的設定所以引發訂閱|未定義的資訊模式|  
|66315|因為物件變更所以引發訂閱|已經卸除物件或使用者|  
|66316|因為物件變更所以引發訂閱|物件已經更改|  
|66565|因為已經卸離或卸除資料庫所以引發訂閱|重新啟動伺服器或資料庫|  
|66571|因為已經卸離或卸除資料庫所以引發訂閱|已經卸除物件或使用者|  
|66572|因為已經卸離或卸除資料庫所以引發訂閱|物件已經更改|  
|67341|因為缺少伺服器上的 od 資源所以引發訂閱|因為缺少伺服器上的 od 資源所以引發訂閱|  
  
 下列狀態碼表示無法建立訂閱：  
  
|程式碼|次要狀態|Info|  
|----------|------------------|----------|  
|132609|因為不支援陳述式所以訂閱建立失敗|查詢太複雜|  
|132610|因為不支援陳述式所以訂閱建立失敗|訂閱陳述式無效|  
|132611|因為不支援陳述式所以訂閱建立失敗|訂閱集合選項無效|  
|132612|因為不支援陳述式所以訂閱建立失敗|隔離等級無效|  
|132622|因為不支援陳述式所以訂閱建立失敗|內部使用|  
|132623|因為不支援陳述式所以訂閱建立失敗|超過每個資料表的範本限制|  
  
 下列狀態碼用於內部，而且分類為檢查終止和初始化模式：  
  
|程式碼|次要狀態|Info|  
|----------|------------------|----------|  
|198656|用於內部：檢查終止和初始化模式|未定義的資訊模式|  
|198928|已終結訂閱|因為已附加資料庫所以引發訂閱|  
|198929|已終結訂閱|因為已卸除使用者所以引發訂閱|  
|198930|已終結訂閱|因為重新訂閱所以已卸除訂閱|  
|198931|已終結訂閱|已終止訂閱|  
|199168|訂閱使用中|未定義的資訊模式|  
|199424|訂閱已初始化但尚未使用中|未定義的資訊模式|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
> [!NOTE]  
>  如果使用者沒有 VIEW SERVER STATE 權限，這份檢視便會傳回目前使用者所擁有的訂閱相關資訊。  
  
## <a name="examples"></a>範例  
  
### <a name="a-return-active-query-notification-subscriptions-for-the-current-user"></a>A. 傳回目前使用者的使用中查詢通知訂閱  
 下列範例會傳回目前使用者的使用中查詢通知訂閱。 如果使用者有 VIEW SERVER STATE 權限，會傳回伺服器中的所有使用中訂閱。  
  
```  
SELECT id, database_id, sid, object_id, created, timeout, status  
FROM sys.dm_qn_subscriptions;  
GO  
```  
  
### <a name="b-returning-active-query-notification-subscriptions-for-a-specified-user"></a>B. 傳回指定使用者的使用中查詢通知訂閱  
 下列範例會傳回登入 `Ruth0` 所訂閱的使用中查詢通知訂閱。  
  
```  
SELECT id, database_id, sid, object_id, created, timeout, status  
FROM sys.dm_qn_subscriptions  
WHERE sid = SUSER_SID('Ruth0');  
GO  
```  
  
### <a name="c-returning-internal-table-metadata-for-query-notification-subscriptions"></a>C. 傳回查詢通知訂閱的內部資料表中繼資料  
 下列範例會傳回查詢通知訂閱的內部資料表中繼資料。  
  
```  
SELECT qn.id AS query_subscription_id  
    ,it.name AS internal_table_name  
    ,it.object_id AS internal_table_id  
FROM sys.internal_tables AS it  
JOIN sys.dm_qn_subscriptions AS qn ON it.object_id = qn.object_id  
WHERE it.internal_type_desc = 'QUERY_NOTIFICATION';  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [查詢通知相關的動態管理檢視 &#40;Transact-sql&#41;](./system-dynamic-management-views.md)   
 [KILL QUERY NOTIFICATION SUBSCRIPTION &#40;Transact-SQL&#41;](../../t-sql/language-elements/kill-query-notification-subscription-transact-sql.md)  
  
