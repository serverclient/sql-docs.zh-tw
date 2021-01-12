---
description: sys.dm_os_virtual_address_dump (Transact-SQL)
title: sys.dm_os_virtual_address_dump (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- dm_os_virtual_address_dump
- sys.dm_os_virtual_address_dump_TSQL
- sys.dm_os_virtual_address_dump
- dm_os_virtual_address_dump_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_os_virtual_address_dump dynamic management view
ms.assetid: 7b24ea55-3873-42fd-a86c-441c92eb6175
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b73703774fcee16b5090ebfa9cd1df7d18ed32c0
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095111"
---
# <a name="sysdm_os_virtual_address_dump-transact-sql"></a>sys.dm_os_virtual_address_dump (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  傳回有關在呼叫處理序之虛擬位址空間中頁面範圍的資訊。  
  
> [!NOTE]  
>  **VirtualQuery** Windows API 也會傳回這項資訊。  
  
> [!NOTE]  
>  若要從或呼叫這個 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] ，請使用 **sys.dm_pdw_nodes_os_virtual_address_dump** 名稱。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**region_base_address**|**varbinary(8)**|頁面資料頁面的基底位址指標。 不可為 Null。|  
|**region_allocation_base_address**|**varbinary(8)**|VirtualAlloc Windows API 函數所配置之頁面範圍基底位址的指標。 BaseAddress 成員指向的頁面是包含在這個配置範圍內。 不可為 Null。|  
|**region_allocation_protection**|**varbinary(8)**|第一次配置資料頁區時的保護屬性。 這是下列值之一：<br /><br /> -PAGE_READONLY<br />-PAGE_READWRITE<br />-PAGE_NOACCESS<br />-PAGE_WRITECOPY<br />-PAGE_EXECUTE<br />-PAGE_EXECUTE_READ<br />-PAGE_EXECUTE_READWRITE<br />-PAGE_EXECUTE_WRITECOPY<br />-PAGE_GUARD<br />-PAGE_NOCACHE<br /><br /> 不可為 Null。|  
|**region_size_in_bytes**|**bigint**|資料頁區的大小 (以位元組為單位)，從所有頁面都有相同屬性的基底位址開始。 不可為 Null。|  
|**region_state**|**varbinary(8)**|資料頁區的目前狀態。 這是下列項目之一：<br /><br /> -MEM_COMMIT<br />-MEM_RESERVE<br />-MEM_FREE<br /><br /> 不可為 Null。|  
|**region_current_protection**|**varbinary(8)**|保護屬性。 這是下列值之一：<br /><br /> -PAGE_READONLY<br />-PAGE_READWRITE<br />-PAGE_NOACCESS<br />-PAGE_WRITECOPY<br />-PAGE_EXECUTE<br />-PAGE_EXECUTE_READ<br />-PAGE_EXECUTE_READWRITE<br />-PAGE_EXECUTE_WRITECOPY<br />-PAGE_GUARD<br />-PAGE_NOCACHE<br /><br /> 不可為 Null。|  
|**region_type**|**varbinary(8)**|識別資料頁區中的頁面類型。 這個值可以是下列值之一：<br /><br /> -MEM_PRI加值稅E<br />-MEM_MAPPED<br />-MEM_IMAGE<br /><br /> 不可為 Null。|  
|**pdw_node_id**|**int**|**適用** 于： [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 、 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]<br /><br /> 此散發所在之節點的識別碼。|  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [SQL Server 作業系統相關的動態管理檢視 &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql.md)  
  
  


