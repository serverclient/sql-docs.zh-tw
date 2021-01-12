---
description: sys.fn_trace_getfilterinfo (Transact-SQL)
title: sys.fn_trace_getfilterinfo (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- fn_trace_getfilterinfo
- fn_trace_getfilterinfo_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- status information [SQL Server], filters
- sys.fn_trace_getfilterinfo function
- filters [SQL Server], traces
- fn_trace_getfilterinfo function
ms.assetid: 09fe4a28-ff8a-4655-9da1-4654d5bc514d
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 889a10c6ae999a5211120e52d7b903d04c5ec70e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097440"
---
# <a name="sysfn_trace_getfilterinfo-transact-sql"></a>sys.fn_trace_getfilterinfo (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回指定追蹤所套用之篩選的相關資訊。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)] 請改用擴充事件。  
  
 
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
fn_trace_getfilterinfo ( trace_id )  
```  
  
## <a name="arguments"></a>引數  
 *trace_id*  
 這是追蹤的識別碼。 *trace_id* 是 **int**，沒有預設值。  
  
## <a name="tables-returned"></a>傳回的資料表  
 傳回下列資訊。 如需資料行的詳細資訊，請參閱 [sp_trace_setfilter &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-trace-setfilter-transact-sql.md)。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**columnid**|**int**|套用篩選之資料行的識別碼。|  
|**logical_operator**|**int**|指定套用 AND 或 OR 運算子。|  
|**comparison_operator**|**int**|指定比較類型：<br /><br /> 0 = 等於<br /><br /> 1 = 不等於<br /><br /> 2 = 大於<br /><br /> 3 = 小於<br /><br /> 4 = 大於或等於<br /><br /> 5 = 小於或等於<br /><br /> 6 = 類似<br /><br /> 7 = 不類似|  
|**value**|**sql_variant**|指定套用篩選的值。|  
  
## <a name="remarks"></a>備註  
 使用者將 *trace_id* 值設定為識別、修改和控制追蹤。 當傳遞特定追蹤的識別碼時， **fn_trace_getfilterinfo** 會傳回該追蹤之任何篩選準則的相關資訊。 如果指定的追蹤沒有篩選，此函數就會傳回空的資料列集。 當傳遞無效的識別碼時，這個函數會傳回空的資料列集。 如需追蹤的類似資訊，請參閱 [sys.fn_trace_getinfo &#40;transact-sql&#41;](../../relational-databases/system-functions/sys-fn-trace-getinfo-transact-sql.md)。  
  
## <a name="permissions"></a>權限  
 需要伺服器的 ALTER TRACE 權限。  
  
## <a name="examples"></a>範例  
 下列範例會傳回追蹤編號 2 之所有篩選的相關資訊。  
  
```  
SELECT * FROM fn_trace_getfilterinfo(2) ;  
GO  
  
```  
  
## <a name="see-also"></a>另請參閱  
 [建立追蹤 &#40;Transact-SQL&#41;](../../relational-databases/sql-trace/create-a-trace-transact-sql.md)   
 [sp_trace_setfilter &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-trace-setfilter-transact-sql.md)   
 [sp_trace_create &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-trace-create-transact-sql.md)   
 [sp_trace_generateevent &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-trace-generateevent-transact-sql.md)   
 [sp_trace_setevent &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-trace-setevent-transact-sql.md)   
 [sp_trace_setstatus &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-trace-setstatus-transact-sql.md)   
 [sys.fn_trace_geteventinfo &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-trace-geteventinfo-transact-sql.md)   
 [sys.fn_trace_getinfo &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-trace-getinfo-transact-sql.md)   
 [sys.fn_trace_gettable &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-trace-gettable-transact-sql.md)  
  
  
