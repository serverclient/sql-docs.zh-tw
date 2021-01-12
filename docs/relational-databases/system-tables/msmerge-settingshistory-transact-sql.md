---
description: MSmerge_settingshistory (Transact-SQL)
title: MSmerge_settingshistory (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSmerge_settingshistory
- MSmerge_settingshistory_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSmerge_settingshistory system table
ms.assetid: 0bdf2d5f-5502-44cd-aa9d-2d5006ad20ce
author: cawrites
ms.author: chadam
ms.openlocfilehash: 10beb9d65bba15ef0b21acd2242fcbaf1d515dca
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098578"
---
# <a name="msmerge_settingshistory-transact-sql"></a>MSmerge_settingshistory (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **MSmerge_settingshistory** 資料表是用來維護合併式複寫之發行項和發行集屬性的變更歷程記錄，每個對合併式複寫拓撲進行的變更都有一個資料列。 另外，這份資料表也儲存了何時設定初始屬性設定的相關資訊。 這份資料表儲存在發行集和訂閱資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**eventtime**|**datetime**|發生事件的日期時間。|  
|**pubid**|**uniqueidentifier**|給定發行集的唯一識別碼。|  
|**>artid**|**uniqueidentifier**|給定發行項的唯一識別碼。|  
|**事件**|**tinyint**|指定所記錄之事件的類型，它可以是下列項目之一：<br /><br /> **1** -初始發行集層級屬性設定。<br /><br /> **2** -發行集屬性的變更。<br /><br /> **101** -初始發行項屬性設定。<br /><br /> **102** -發行項屬性的變更。|  
|**propertyname**|**sysname**|設定或變更的屬性名稱|  
|**previousvalue**|**sysname**|如果變更了屬性，便是先前的屬性值。|  
|**newvalue**|**sysname**|屬性所改成的值，或建立時的值。|  
|**eventtext**|**Nvarchar (2000)**|描述事件的字元字串。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
