---
description: sys.fn_MSxe_read_event_stream (Transact-SQL)
title: sys.fn_MSxe_read_event_stream (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- fn_MSxe_read_event_stream_TSQL
- sys.fn_MSxe_read_event_stream_TSQL
- sys.fn_MSxe_read_event_stream
- fn_MSxe_read_event_stream
dev_langs:
- TSQL
helpviewer_keywords:
- sys.fn_MSxe_read_event_stream
- fn_MSxe_read_event_stream
ms.assetid: 5edb1162-625a-41e0-8ec9-1edc8ab9a74a
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 739d2b4298ec91064063acee702d1c4c6df5c4a0
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101338"
---
# <a name="sysfn_msxe_read_event_stream-transact-sql"></a>sys.fn_MSxe_read_event_stream (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  擴充事件提供擴充事件使用者介面 (UI) 內部使用的資料表值函式 (TVF)。 資料表並不包含客戶可用的資料。  
  
 若要檢視事件資料，請使用下列其中一項：  
  
-   擴充事件新增工作階段 UI。  
  
-   fn_xe_file_target_read_file TVF。 如需詳細資訊，請參閱 [sys.fn_xe_file_target_read_file &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql.md)。  
  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.fn_MSxe_read_event_stream ( session_name)  
```  
  
## <a name="arguments"></a>引數  
 *Session_name*  
 伺服器上執行的工作階段名稱。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|type|**Integer (4)**|事件類型。 不可為 Null。|  
|data|**Image (16)**|事件影像資料。 可為 Null。|  
  
## <a name="see-also"></a>另請參閱  
 [擴充的事件動態管理檢視](../../relational-databases/system-dynamic-management-views/extended-events-dynamic-management-views.md)   
 [&#40;Transact-sql&#41;的擴充事件目錄檢視 ](../../relational-databases/system-catalog-views/extended-events-catalog-views-transact-sql.md)   
 [擴充事件](../../relational-databases/extended-events/extended-events.md)  
  
  
